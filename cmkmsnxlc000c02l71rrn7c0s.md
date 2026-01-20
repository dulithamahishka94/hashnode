---
title: "Redis Caching in Laravel: Why I Ditched Cache Tags (And You Might Want To)"
seoTitle: "Redis Caching In Laravel"
seoDescription: "Discover why Laravel cache tags may not be scalable and how explicit keys can improve performance in Redis caching"
datePublished: Tue Jan 20 2026 16:14:18 GMT+0000 (Coordinated Universal Time)
cuid: cmkmsnxlc000c02l71rrn7c0s
slug: redis-caching-in-laravel
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1768409575241/e8c23a77-e825-458b-b88f-48941ce6f4b2.png
tags: laravel, redis, cache, cache-invalidation

---

Recently, I noticed unexpected latency issues with our Redis instance. So, I put my “Debugging Hat” on, and went for a dive.

The culprit? Cache tags. Those innocent-looking Laravel helpers that promise easy cache management. Turns out, "easy" and "scalable" aren't always the same thing.

Here's what I learned rebuilding the caching layer from scratch.

Every story has to start somewhere. So, get ready and keep your eyes on the road ahead!

## First, The Disaster

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1767865146407/93729035-48ec-46d4-9ca5-27341115e3b5.gif align="left")

Our messaging app was doing great in the early days. A few hundred users and queries per page seemed manageable at first. After all, we were already caching it, so what could go wrong? Those were, indeed, famous last words.

When I actually stress-tested it:

* **User A** sends a message
    
* **Users B, C, and D** need their unread badges updated
    
* The conversation list for **everyone** needs to reflect the new message
    
* Cache tags start creating orphaned data
    
* Redis memory climbs
    
* Everything slows down
    
* I start questioning my career choices
    

The "*just flush the tag*" approach was killing us. Time to actually understand what was happening under the hood.

> **🍵 Coffee Break Reality Check**
> 
> If you're here because your cache is misbehaving in production, skip to the "Explicit Keys" section. You can come back for the theory later. I won't judge.

## Laravel's Cache Facade: Quick Refresher

You probably know this already, but let's make sure we're on the same page:

```php
// Basic stuff
Cache::put('user_123_profile', $userData, now()->addMinutes(30));
$profile = Cache::get('user_123_profile');

// The pattern you'll use everywhere
$profile = Cache::remember('user_123_profile', 3600, function () {
    return User::with('profile')->find(123);
});
```

Simple. Clean. Works great until you need to invalidate a bunch of related stuff at once.

That's when most tutorials tell you: "**Just use cache tags!**"

***Yeah, about that...***

![Burning House Girl Meme Generator - Piñata Farms - The best ...](https://a.pinatafarm.com/459x308/d13547c6b6/burning-house-girl.jpg align="left")

## Cache Tags: What They Don't Tell You in the Docs

Cache tags in Laravel let you group related cache items together so you can invalidate them as a group. It's useful when you have interconnected cached data that needs to be cleared together.

Cache tags look amazing in examples:

```php
Cache::tags(['users', 'user_123'])->put('profile_data', $data, 3600);

// Later, flush everything for this user
Cache::tags(['user_123'])->flush();
```

Clean, right? Here's what's actually happening in your Redis instance.

### The Ugly Truth: How Tags Actually Work

When you write that innocent line of code, Laravel does this behind the scenes:

**Step 1:** Creates a random "version" for each tag

```plaintext
tag:users:key -> "abc123"
tag:user_123:key -> "def456"
```

**Step 2:** Your actual key becomes a Frankenstein monster

```plaintext
abc123|def456|profile_data -> {your actual data}
```

**Step 3:** Tracks membership in Redis Sets

```plaintext
tag:users:entries -> SET["abc123|def456|profile_data", ...]
tag:user_123:entries -> SET["abc123|def456|profile_data", ...]
```

Three extra Redis operations. For every. Single. Cache write.

### The Real Kicker: What `flush()` Does

Here's the part that got me. When you call `Cache::tags(['user_123'])->flush()`:

1. Laravel generates a NEW random version for the tag
    
2. Updates the version key
    
3. Deletes the entries set
    

Notice what's missing? **It doesn't delete your actual cached data.**

Your data is now orphaned. It sits there, taking up memory, until the TTL expires. The next lookup generates a different composite key and misses the old data.

```php
// Before flush: looks for "abc123|def456|profile_data"
// After flush: looks for "abc123|xyz789|profile_data" (cache miss!)
// Meanwhile: "abc123|def456|profile_data" is still chilling in Redis
```

In a busy system with frequent flushes? You end up with tons of zombie data.

> **🚨 War Story**
> 
> We were flushing tags on every new message. With 1000 messages per day and 30-day TTLs on some keys, we had accumulated gigabytes of orphaned cache entries. Redis was sweating harder than me during that incident call.

## The Four Horsemen of Cache Tag Problems

### 1\. Memory Bloat (The Silent Killer)

```plaintext
Monday morning:  1,000 cache entries
                 Tag flushed 10 times throughout the day
                 Result: Up to 10,000 orphaned entries

Friday evening:  You're wondering why Redis is using 4GB
```

### 2\. The Entries Set Grows Forever

Every tagged cache entry adds to `tag:users:entries`. In a busy app:

```plaintext
tag:users:entries -> SET with 100,000+ members
```

When you flush? Laravel deletes this entire set. That's O(N). Your Redis blocks.

### 3\. Race Conditions That'll Make You Question Reality

```plaintext
T1: Request A starts, gets tag version "abc123"
T2: Request B flushes, tag version becomes "xyz789"
T3: Request A writes cache with old version "abc123"
T4: Request A's data is immediately orphaned
```

This one took me hours to debug. The cache was "working" but data kept disappearing randomly.

### 4\. Debugging Is Basically Impossible

Try finding a specific cached item in production:

```bash
redis-cli KEYS "*profile*"
# Returns: "abc123|def456|profile_data"
# Cool, but... which user? Which tag version? Is this orphaned?
```

Compare that to explicit keys:

```bash
redis-cli KEYS "*user:123*"
# Returns: "myapp:user:123:profile"
# Ah, user 123's profile. Got it.
```

> **💡 Pro Tip**
> 
> If you're already using tags and can't migrate immediately, at least set shorter TTLs. 5 minutes of orphaned data is way better than 24 hours.

## Explicit Keys: The "Boring" Solution That Actually Works

I know, I know. Manually managing cache keys sounds tedious. **But hear me out.**

### A Simple Key Builder

```php
class CacheKey
{
    private const PREFIX = 'myapp';

    public static function userProfile(int $userId): string
    {
        return self::PREFIX . ":user:{$userId}:profile";
    }

    public static function conversation(string $id): string
    {
        return self::PREFIX . ":conv:{$id}:data";
    }

    public static function userConversationState(int $userId, string $convId): string
    {
        return self::PREFIX . ":user:{$userId}:conv:{$convId}:state";
    }
}
```

Boring? Maybe. But now:

* Every key is **predictable**, given the inputs, you know exactly what the key is
    
* No mystery composite keys
    
* No orphaned data
    
* `redis-cli` actually works for debugging
    

### The "But I Have To Know What To Invalidate" Objection

Yes, you do. And honestly? That's a feature, not a bug.

With tags, you're thinking: *"I'll just flush the tag and magic happens!"*

With explicit keys, you're forced to think: *"What exactly needs to be invalidated when X happens?"*

> That second mindset leads to better cache design. Trust me on this one.

## Let's Talk Redis Operations (The Fun Part)

Alright, now that we've established explicit keys are the way to go, let's talk about how to actually use Redis efficiently. Because there's more to life than `Cache::get()` and `Cache::put()`.

> **🎯 Quick Reference**
> 
> * **Strings**: Simple values, full read/write
>     
> * **Hashes**: Structured data, partial updates
>     
> * **INCR/DECR**: Counters without race conditions
>     
> * **Pipeline**: Batch operations, one network trip
>     
> * **UNLINK**: Delete without blocking
>     

### Hashes: For When You Don't Want to Serialize Everything

Laravel's `Cache::put()` serializes your entire object. Every time. Even if you just want to update one field.

Redis Hashes let you store structured data and update individual fields:

```php
// Store user state as a hash
Redis::hset('user:123:state', 'online', '1');
Redis::hset('user:123:state', 'last_seen', now()->timestamp);
Redis::hset('user:123:state', 'theme', 'dark');

// Update just one field (no serialization overhead)
Redis::hset('user:123:state', 'last_seen', now()->timestamp);

// Get everything
$state = Redis::hgetall('user:123:state');
// ['online' => '1', 'last_seen' => '1699123456', 'theme' => 'dark']

// Or just one field
$theme = Redis::hget('user:123:state', 'theme');
```

Under the hood, Redis stores small hashes in a "ziplist", a super compact format. One hash with 10 fields uses less memory than 10 separate keys.

**When I use this:** User preferences, session data, feature flags, any structured data with frequent partial updates.

### INCR/DECR: Counters That Don't Lie

Here's a bug I've seen in codebases more times than I'd like to admit:

```php
// The wrong way (race condition waiting to happen)
$count = Cache::get('page_views');
Cache::put('page_views', $count + 1);
```

Two requests hit this simultaneously, both read `100`, both write `101`. You just lost a page view.

Redis's atomic counters fix this:

```php
Redis::incr('page_views');  // Always correct, even with 1000 concurrent requests
Redis::incrby('user:123:points', 50);
Redis::decr('inventory:item_456');
```

These are atomic because Redis is single-threaded for command execution. The increment happens in one uninterruptible operation.

**Example on how to properly cache the unread count:**

```php
public function onNewMessage(string $recipientId): void
{
    $key = "user:{$recipientId}:unread";
    Redis::incr($key);
    Redis::expire($key, 3600);  // Reset TTL
}

public function onMessageRead(string $userId): void
{
    $key = "user:{$userId}:unread";
    if ((int) Redis::get($key) > 0) {
        Redis::decr($key);
    }
}
```

No locks. No race conditions. Just works.

> **⚡ Performance Note**
> 
> Atomic operations are not just correct, they're fast. One `INCR` command vs. GET + calculation + SET. That's 3x fewer network round trips.

### Pipeline: Because Network Latency Adds Up

Every Redis command is a network round trip. If your latency is 1ms (which is pretty good), 100 commands = 100ms of just waiting.

```php
// Slow: 50 round trips
foreach ($userIds as $userId) {
    Redis::del("user:{$userId}:conv:{$convId}:state");
}

// Fast: 1 round trip
Redis::pipeline(function ($pipe) use ($userIds, $convId) {
    foreach ($userIds as $userId) {
        $pipe->del("user:{$userId}:conv:{$convId}:state");
    }
});
```

With pipelining, all commands get sent together, Redis processes them in order, and all responses come back together.

**Heads up though:** Pipelines aren't atomic. Other clients' commands can interleave between yours. If you need atomicity, look into **Redis transactions** or **Lua scripts**.

### DEL vs UNLINK: The Optimization That Saved Our Bacon

This one's subtle but important.

`DEL` is synchronous. When you delete a key, Redis:

1. Removes it from the hash table
    
2. Frees the memory
    
3. *Then* moves on to the next command
    

For big values (large strings, hashes with thousands of fields), that memory deallocation takes time. And during that time, Redis can't do anything else. Your fast reads are stuck waiting behind a slow delete.

`UNLINK` (Redis 4.0+) is the async version:

1. Removes the key from the hash table immediately
    
2. Queues the memory for background cleanup
    
3. Moves on to the next command instantly
    

```php
// Might block if the value is large
Redis::del('big_cached_object');

// Returns immediately, always
Redis::unlink('big_cached_object');
```

**The difference in practice:**

| Scenario | DEL | UNLINK |
| --- | --- | --- |
| Small string (1KB) | ~0.001ms | ~0.001ms |
| Large string (1MB) | ~1ms | ~0.01ms |
| Hash with 10K fields | ~10ms | ~0.01ms |

When we switched our bulk invalidation from `DEL` to `UNLINK` in pipelines, our P99 latency dropped significantly. It's one of those changes that costs nothing and helps a lot.

> **🔥 Hot Take**
> 
> Just use `UNLINK` everywhere. I can't think of a single case where you actually need the synchronous memory-freeing behavior of `DEL`. If you know of one, I'd genuinely love to hear it.

### EXPIRE: How Redis Actually Handles TTL

Quick detour into Redis internals because this tripped me up once.

Redis doesn't have a background process constantly checking "is this key expired yet?" That would be expensive.

*Instead, it uses two strategies:*

**Passive expiration:** When you try to access a key, Redis checks if it's expired. If yes, it deletes it right there and returns null.

**Active expiration:** 10 times per second, Redis samples 20 random keys that have a TTL. If more than 25% are expired, it samples again immediately.

```plaintext
The cleanup loop:
1. Pick 20 random keys with TTL
2. Delete the expired ones
3. If >25% were expired, go to step 1
4. Otherwise, wait 100ms and start over
```

**Why this matters:** Keys might live slightly past their expiration until they're accessed or randomly sampled. Don't rely on TTL for precise timing, use a proper scheduler for that.

> 💡 **Keep In Mind**
> 
> Use `Cache::rememberForever` carefully. Having too many `rememberForever` keys can fill up Redis memory. Instead of just considering how rarely the data changes, think about how often it is accessed. Using `rememberForever` on data that rarely accessed over time isn't a good idea.

## Putting It Together: What Our Cache Service Looks Like Now

Here's a simplified version of what we ended up with:

```php
class ConversationCache
{
    private const CONV_TTL = 1800;      // 30 min (data rarely changes)
    private const STATE_TTL = 900;       // 15 min (changes often)
    private const UNREAD_TTL = 3600;     // 1 hour (expensive to compute)

    public function getConversation(string $id): ?array
    {
        $key = "conv:{$id}:data";

        try {
            $cached = Cache::get($key);
            if ($cached !== null) {
                return $cached;
            }

            $data = $this->loadFromDatabase($id);
            if ($data) {
                Cache::put($key, $data, self::CONV_TTL);
            }
            return $data;

        } catch (RedisException $e) {
            // Redis down? Just hit the database.
            Log::warning('Redis unavailable', ['error' => $e->getMessage()]);
            return $this->loadFromDatabase($id);
        }
    }

    public function getUserState(string $userId, string $convId): array
    {
        $key = "user:{$userId}:conv:{$convId}:state";

        $state = Redis::hgetall($key);

        if (empty($state)) {
            $state = $this->loadStateFromDb($userId, $convId);
            Redis::hset($key, 'read', $state['read'] ? '1' : '0');
            Redis::hset($key, 'archived', $state['archived'] ? '1' : '0');
            Redis::expire($key, self::STATE_TTL);
        }

        return [
            'read' => ($state['read'] ?? '0') === '1',
            'archived' => ($state['archived'] ?? '0') === '1',
        ];
    }

    public function invalidateForParticipants(string $convId, array $userIds): void
    {
        if (empty($userIds)) return;

        Redis::pipeline(function ($pipe) use ($userIds, $convId) {
            foreach ($userIds as $userId) {
                $pipe->unlink("user:{$userId}:conv:{$convId}:state");
            }
        });
    }
}
```

Notice the patterns:

* **Explicit, predictable keys**
    
* **Redis Hashes for structured state**
    
* **Pipeline + UNLINK for bulk invalidation**
    
* **Graceful degradation**, if Redis dies, we fall back to the database
    

> **📊 The Numbers**
> 
> Before and after the refactor:
> 
> | **Metric** | **Cache Tags** | **Explicit Keys** |
> | --- | --- | --- |
> | Queries per page | 300+ | 3-5 |
> | Orphaned cache data | Large | Zero |
> | Redis memory growth | Unbounded | Stable |
> | Time to debug cache issues | Hours | Minutes |
> | My stress levels | High | Manageable |

## Wrapping Up

Look, cache tags aren't evil. For a small app with low traffic, they're fine. Convenient, even.

But if you're building something that needs to scale, or you're debugging mysterious cache inconsistencies at 2am, explicit keys are worth the extra thought upfront.

The cache design that saved me:

1. **Deterministic keys** via a builder class
    
2. **Layered TTLs** based on how often data changes
    
3. **Redis Hashes** for structured data with partial updates
    
4. **Atomic counters** for anything that increments
    
5. **Pipeline + UNLINK** for bulk operations
    
6. **Graceful fallbacks** when Redis misbehaves
    

It's not glamorous. It won't get you Twitter famous. But it works, it scales, and it's debuggable. In production, that's what matters.

*Got questions? Found a mistake? Built something cool with these patterns? Hit me up, always happy to chat about making things faster and optimized.*

See you again with another war story. Till then, then.

![YARN | Bye, Bye. | Minions (2015) | Video gifs by quotes | 5e100432 | 紗](https://y.yarn.co/5e100432-7b39-4e65-bff9-e4c24b36753c_text.gif align="left")