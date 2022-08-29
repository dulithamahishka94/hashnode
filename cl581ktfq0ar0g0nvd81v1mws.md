## Decorator Design Pattern

Hello everyone! If you are new to the blog and eager to learn about the design patterns, hop on to the series from [here](https://dulitharajapaksha.hashnode.dev/series/design-patterns-101). I'll be breaking down all the design patterns and make it simple as possible to understand, even for a baby (Of course that is a slang. Babies can't understand anything. They barely even talk 🙄 Back to reading. Chop! Chop!!)

Today I am going to teach you how to make bubble tea (What the heck? I am just kidding). Let's jump into the story, shall we?

I'll start off with the name today. This is called **The Decorator Pattern**.

![Thumbs-Up-From-Spongebob-whatsupbugs-43071255-498-280.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1656440308752/-9tQgkl1qB.gif align="left")

## The story

One day, the president of Banana Republic (Mr. Ban Ana) went on a trip to Taiwan for a diplomatic mission. When he was in a meeting with the country folks, a young waiter came to him and served him a iced tea with some Jell-O ish bubbles. He was surprised by the taste and he asked the waiter, what that drink was called. It's "Bubble Tea", the waiter replied. That was the first time Mr. Ban Ana heard that name. Then he turned to his secretary and told "I need a bubble tea shop opened immediately in Banana Republic".

The newly opened bubble tea joint had a very good customer base. But it took only two days to them to get into trouble (Typical situation in Banana Republic). Customers had different selections for bubble flavors. The making cost was different, so the bubble tea joint could not charge a fixed amount for every flavor. Some bubbles were cheap and some were expensive. Some customers asked for two sugar packs. Some asked milk to be added. Some did not need any milk or sugar. The shop was on fire like the sky on an independence day. The only option they had was, to have a discussion among the managers of the bubble tea joint and find a solution. As usual, they found one.

> WHAT WAS THAT?

One manager raised his voice. "It is very difficult to manage the prices of the different tea flavors like this. Specially when they ask for extra sugar and milk. Shall we have a base price for a bubble tea and add the cost on top of that for different flavors and added sugar and milk?". That method looks feasible but due to the big customer base, they have agreed upon to talk with the best software developer in town and make a system.

![Mhw4.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1656443065506/WxTqgWQMs.gif align="left")

The developer started to put on some diagrams together.

![Blank diagram - Page 4 (5).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1656846360048/UP-2k-0VT.png align="left")

As per the requirement he got from the bubble tea joint, anyone can request any amount of milk, sugar and bubble type to be added into the bubble tea. It is totally up to the customer to decide, and the joint will cost them according to the number of items their customers request to put inside the tea.

> Simple yet awesome!

If the customer asks for 1 x milk, 2 x sugar with cola bubbles, the total would be 1.60 ( (1 x 0.75) + (2 x 0.15) + 0.55 ). Then that amount will be added to the base price of the bubble tea which is 2.25 and the total would be 3.85.

After creating a diagram to understand the situation and the requirements clearly, the developer decided to make a class diagram for the implementation.

For this implementation, basically he needed two things.

1. Bubble Tea
2. Addons (milk, sugar, bubbles)

But, unfortunately, there are more!

![UML class diagram - UML class diagram.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1656847033842/QRSi6W3ZB.png align="left")

Oops! That looks complicated. Is it? Everything is complicated when you don't understand. When you understand the concept, there is nothing special about it. Let's dig, let's dig!

On the top we have an interface called `Bubble Tea`. This is where we keep the functions as a blueprint. So, whenever we implement this interface, we need to override the functions. For the sake of Banana Republic, it contains the description and the cost (Yes! No nuclear codes 😉)

Below that, we have the class `Basic Bubble Tea`. This base class implements the interface that we have created before. So that, it overrides all the abstract functions of `Bubble Tea` interface. This bubble tea is the basic one. So, all the other things should be added on top of that. Again, the customers have the choice of getting this basic bubble tea without adding any of the special bubbles (I really don't know what the bubbles are called. So pathetic.), additional milk and sugar.

As I said, since the customers can order this independently, this basic bubble tea has a price of its own (BR 2.25/=).

Now, to the stage of the magician. This is where we are going to learn the concept of the **decorator**. What this class going to do is, it is going to mimic the functionalities of `Bubble Tea` interface. Okay, hold up. WHAT ? 

![783UTM7.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1656848307348/cesODscG4.png align="left")

The decorator class will impersonate bubble tea interface. And all the addons (bubbles, milk and sugar) will get wrapped around the decorator dynamically. By doing that you will be able to add new functions into the existing class (add addons into the bubble tea) without altering its structure. It is like adding a 4x scope to a gun. You don't have to change the gun structure to add the scope. By adding the scope, the gun will have new, enhanced or added functionality to it.

So, as the base class, the decorator will also implements the Bubble Tea interface. This decorator will not have any interesting properties, instead it will be used to do a clever type-match in future.

Now, to the concept of addons. They are just simple classes. If you have many addons, you can add multiple classes in here. They have different costs inside. Because each addon costs differently.

This can be little bit confusing when looking at the overall class diagram. But, I can assure you, there is nothing special about it when you get to the bottom of the bubble tea.

Everything is setup. Now it's time to do some developments.

(Before getting your hopes up, below code is based on PHP. It is almost same with other languages, only the syntaxes are different)

Lets make the bubble tea interface first. Nothing special about it. Just an ordinary interface.

```php
interface BubbleTea {

    public function description();
    public function cost();

}
```

Next stop, Base Bubble Tea. Since it implements the interface, the functions inside the interface should get overridden.

```php
class BasicBubbleTea implements BubbleTea {

    public function description() {
        return "Basic Bubble Tea";
    }

    public function cost() {
        return 2.25;
    }

}
```
Here I have updated both overridden functions by adding a simple return statement. Everything is straightforward and seems okay so far.

Now we are halfway through. It is the time for the *decorator* to come and save the bubble tea joint. As I said earlier, the decorator is a trick to make a clever type-match and to make ingredients to work as objects.

```php
abstract class BubbleTeaDecorator implements BubbleTea {

}
```

`BubbleTeaDecorator` is a abstract class that implements the `BubbleTea` interface. Hence, `BubbleTeaDecorator` is a `BubbleTea`. That is all about the decorator. That is everything the decorator does. It just sits there, and make the type match possible on the way down of the road.

But remember, in the `BubbleTea` interface we have the abstract `cost()` function. That means anyone extends the abstract class `BubbleTeaDecorator` needs to override the `cost()` function in their class (*If you are wondering why I haven't override the `cost()` function inside `BubbleTeaDecorator` abstract class, it is not needed if we are planning to keep the function abstract inside the abstract class. You MUST override the same function in a concrete class because you cannot keep abstract functions inside a concrete class*).

Now, to the ingredients. This is where the magic happens by leveraging the decorator class. Lets make the code step by step. First, we create the `Milk` class and extend it from the abstract decorator class we have created earlier. When that happens, the abstract method `cost()` needs to be overridden in the Milk class.

```php
class Milk extends BubbleTeaDecorator {
    public function description() {
        // TODO: Implement description() method.
    }

    public function cost() {
        // TODO: Implement cost() method.
    }
}
```
Okay cool! Now, just think like this. What are we going to do with milk? Are we adding the milk to an empty cup? Or, are we adding them into the basic bubble tea?

Just remember, Milk, Sugar, Cola and Jell-O bubbles are addons. Addons are the things that you put on something. Or something you purchase additionally, apart from the main item. In this case, addons are things that customers can purchase to put on the basic bubble tea. If the customer purchase milk as an addon, they'll put milk into the bubble tea. Simple as that.

So, in our Milk class, we need a basic bubble tea to add the milk. How do we add the basic bubble tea into the Milk class?

![awkward-silence-silence.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1656951599686/dBmfHxfqy.gif align="left")

Yes! You are correct. From dependency injection. So, how do we inject the dependency? Absolutely. Through the constructor of the Milk class. Lets inject the dependency.

```php
class Milk extends BubbleTeaDecorator {

    private $bubble_tea;

    public function __construct(BubbleTea $bubble_tea) {
        $this->bubble_tea = $bubble_tea;
    }

    public function description() {
        // TODO: Implement description() method.
    }

    public function cost() {
        // TODO: Implement cost() method.
    }
}
```

Basically what we do is, we say, whenever we call the Milk class, take an existing Basic Bubble Tea object. It is like "Please whatever you do with milk, please put it into a bubble tea that we have already made". Since the class `BasicBubbleTea` is an instance of `BubbleTea` we can inject it without any issue on runtime.

Now only thing we have is, to update the `cost()` and `description()` functions. Addon is an additionally purchased item or a service. So, whenever you purchase that, it needs to be added to the amount.

```php
class Milk extends BubbleTeaDecorator {

    private $bubble_tea;

    public function __construct(BubbleTea $bubble_tea) {
        $this->bubble_tea = $bubble_tea;
    }

    public function description() {
        return $this->bubble_tea->description() . ' With 1 x Milk.';
    }

    public function cost() {
        return $this->bubble_tea->cost() + 0.75; // 0.75 will be added to the cost.
    }
}
```

We have created one addon successfully. Lets also create Sugar, Cola and Jell-O bubble addons.

```php
class Sugar extends BubbleTeaDecorator {

    private $bubble_tea;

    public function __construct(BubbleTea $bubble_tea) {
        $this->bubble_tea = $bubble_tea;
    }

    public function description() {
        return $this->bubble_tea->description() . ' With 1 x Sugar.';
    }

    public function cost() {
        return $this->bubble_tea->cost() + 0.15;
    }
}

class ColaBubbles extends BubbleTeaDecorator {

    private $bubble_tea;

    public function __construct(BubbleTea  $bubble_tea) {
        $this->bubble_tea = $bubble_tea;
    }

    public function description() {
        return $this->bubble_tea->description() . ' With 1 x Cola Bubbles.';
    }

    public function cost() {
        return $this->bubble_tea->cost() + 0.55;
    }
}

class JelloBubbles extends BubbleTeaDecorator {

    private $bubble_tea;

    public function __construct(BubbleTea  $bubble_tea) {
        $this->bubble_tea = $bubble_tea;
    }

    public function description() {
        return $this->bubble_tea->description() . ' With 1 x Jell-O Bubbles.';
    }

    public function cost() {
        return $this->bubble_tea->cost() + 0.50;
    }
}
```
Every addon has the same implementation. But the cost and description functions are different. Because every addon has a different name and a different cost to be added.

Everything looks good. Lets make a bubble tea shall we?

For starters, make the basic bubble tea.

```php
$basicBubbleTea = new BasicBubbleTea();
echo $basicBubbleTea->cost(); // 2.25
```
We have the basic bubble tea and the cost is BR 2.25/= (If you didn't know, BR is the currency of Banana Republic. Please make a note.)

> Customer : "Hi, can I have one Bubble Tea with one Milk and two sugar?"

> Cashier : "Yes, of course sir. Do you need any special bubbles to be added?"

> Customer : "Hmm. I think Jell-O bubbles will be good for my health. Please add Jell-O bubbles".

> Cashier : "Sure, let me add those and tell you the amount"

```php
// Make Basic Bubble Tea.
$basicBubbleTea = new BasicBubbleTea();

// Add 1 x Milk.
$basicBubbleTea = new Milk($basicBubbleTea);

// Add 2 x Sugar.
$basicBubbleTea = new Sugar($basicBubbleTea);
$basicBubbleTea = new Sugar($basicBubbleTea);

// Add healthy Jell-O Bubbles.
$basicBubbleTea = new JelloBubbles($basicBubbleTea);

// Addons details.
echo 'Order Details : ' . $basicBubbleTea->description();

// Total amount.
echo 'Total Amount : BR ' . $basicBubbleTea->cost();
```

The final result got printed as below on the cashiers screen.

***Order Details : Basic Bubble Tea With 1 x Milk. With 1 x Sugar. With 1 x Sugar. With 1 x Jell-O Bubbles.***

***Total Amount : BR 3.80***

> Cashier : "Sir, the total is BR 3.80/=. Would you like to pay by cash or a banana card?"

Did you see how we have solved a practical issue with the help of a design pattern. 

THE END!

Here is the complete codebase.

```php
interface BubbleTea {
    public function description();
    public function cost();
}

class BasicBubbleTea implements BubbleTea {

    public function description() {
        return "Basic Bubble Tea";
    }

    public function cost() {
        return 2.25;
    }
}

abstract class BubbleTeaDecorator implements BubbleTea {

}

class Milk extends BubbleTeaDecorator {

    private $bubble_tea;

    public function __construct(BubbleTea $basic_bubble_tea) {
        $this->bubble_tea = $basic_bubble_tea;
    }

    public function description() {
        return $this->bubble_tea->description() . ' With 1 x Milk.';
    }

    public function cost() {
        return $this->bubble_tea->cost() + 0.75;
    }
}

class Sugar extends BubbleTeaDecorator {

    private $bubble_tea;

    public function __construct(BubbleTea $basic_bubble_tea) {
        $this->bubble_tea = $basic_bubble_tea;
    }

    public function description() {
        return $this->bubble_tea->description() . ' With 1 x Sugar.';
    }

    public function cost() {
        return $this->bubble_tea->cost() + 0.15;
    }
}

class ColaBubbles extends BubbleTeaDecorator {

    private $bubble_tea;

    public function __construct(BubbleTea $basic_bubble_tea) {
        $this->bubble_tea = $basic_bubble_tea;
    }

    public function description() {
        return $this->bubble_tea->description() . ' With 1 x Cola Bubbles.';
    }

    public function cost() {
        return $this->bubble_tea->cost() + 0.55;
    }
}

class JelloBubbles extends BubbleTeaDecorator {

    private $bubble_tea;

    public function __construct(BubbleTea $basic_bubble_tea) {
        $this->bubble_tea = $basic_bubble_tea;
    }

    public function description() {
        return $this->bubble_tea->description() . ' With 1 x Jell-O Bubbles.';
    }

    public function cost() {
        return $this->bubble_tea->cost() + 0.50;
    }
}

// Make Basic Bubble Tea.
$basicBubbleTea = new BasicBubbleTea();

// Add 1 x Milk.
$basicBubbleTea = new Milk($basicBubbleTea);

// Add 2 x Sugar.
$basicBubbleTea = new Sugar($basicBubbleTea);
$basicBubbleTea = new Sugar($basicBubbleTea);

// Add healthy Jell-O Bubbles.
$basicBubbleTea = new JelloBubbles($basicBubbleTea);

// Addons details.
echo 'Order Details : ' . $basicBubbleTea->description();

// Total amount.
echo 'Total Amount : BR ' . $basicBubbleTea->cost();
```

## Usage of this design pattern

- You can use this to assign extra behaviors or to extend a current behavior on runtime.

- You can also use this to extend functionality of places where extending (inheritance) looks inappropriate.

- You can also use this when you want to modify the functionality of a single object of class and leave others unchanged.

## Real life examples of the pattern

- Of course, above scenario itself is a real life example.

- Just think of a pizza order. When you make the order, they ask you what to put for the topping. It is a decorator. Whatever you request, they put. And the price gets changed along with the topping. It is not always about adding values or functionalities. It can also be used to substract or to remove the functionalities.

- Have you seen I have used bold and italic fonts in this article? Sometimes I also used headings. Those are decorators. The words you type are the base ones. Italic, bold and headings are the decorators.

- A Christmas tree decorated with little Santas and light bulbs? Also following the decorator pattern.

> See how you have already used these concepts without knowing?

## What can you see in a design pattern like this?

- A subject and its decorators are separate entities. The subject's author does not need to do anything special to have it decorated. Decorators, similarly, do not need to prepare to be decorated.

- Any combination of capabilities can be easily added. The same feature can even be added twice. With inheritance, this is difficult.

- The same object can be decorated in multiple ways at the same time. Clients can request specific capabilities by sending messages to the appropriate decorator.

Now, whenever you go to a pizza joint, or a bubble tea shop or even a cake shop, you can simply say "Ha ha, I know you are using decorator pattern. I already read Simple Yet Awesome". They'll really be surprised 😅

Okay, this is the end of another design pattern. So far I have completed 4 design patterns. Like this I have planned to cover all the design patterns in this series. Take your time. Read slowly. If you did not understand, read it again. If you have any questions, ask in the comments. I'll be happy to answer.

Until we meet again, have a good day.

![cf710ee522c506bda4cdf29a3f57c5c4.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1656956688210/KWqQ500Do.gif align="left")









