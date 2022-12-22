# SOLID Design Principles: Dependency Inversion Principle

Hey people! Today we are going to learn about the final design principle of SOLID principles. That is the **Dependency Inversion Principle**.

# Dependency Inversion Vs. Dependency Injection

Hmm sounds the same right? Yes sounds the same. But they have two different meanings. Actually, dependency injection is a technique that we can use to do dependency inversion. It involves providing the dependencies of a class through its constructor or methods, rather than the class creating or accessing them directly. This allows the dependencies to be easily swapped out or mocked for testing, and makes the class more modular and easier to test and maintain.

Something like this

```php
class Foo {
  private final Bar bar;
  
  public Foo(Bar bar) {
    this.bar = bar;
  }
  
  public void doSomething() {
    bar.doSomethingElse();
  }
}
```

`public foo(Bar bar)` is the constructor of the class `Foo` . With dependency injection, `Foo` would accept an instance of `Bar` as a constructor parameter or method argument. This allows the caller to pass in a specific instance of `Bar` when creating an instance of `Foo`, making it easier to test `Foo` and swap out different implementations of `Bar`.

Now you know what is Dependency Injection. But we still did not get an answer for Dependency Inversion.

# What is Dependency Inversion?

In software engineering, the dependency inversion principle is a design principle that states:

*   High-level modules should not depend on low-level modules. Both should depend on abstractions.
    
*   Abstractions should not depend on details. Details should depend on abstractions.
    

This principle can be applied in the context of PHP to help you design more flexible and maintainable software.

One way to implement dependency inversion in PHP is to use dependency injection. This involves creating a class that has dependencies on abstractions (e.g., interfaces) rather than concrete implementations. The concrete implementations can then be injected into the class via its constructor or setter methods, allowing the class to be more flexible and easier to test.

I hate to explain something without a code example. Let's jump into a code, shall we?

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1671624547621/JU6w2DUtl.gif align="left")

# Dependency Inversion Principle explained with code

Let's assume you have to create a system to send a WhatsApp notification when a user is registered. One way to do it is as below.

```php
class UserController {
  protected $notification;

  public function __construct() {
    $this->notification = new WhatsAppNotification();
  }

  public function register($email, $password) {
    // Register user functionality in here

    // Send notification
    $this->notification->send($email, 'Welcome!', 'Thank you for registering.');
  }
}

class WhatsAppNotification
{
    public function send($to, $subject, $body)
    {
        // Send a notification in here
    }
}
```

This is very cool for a while. Then suddenly your angry manager pops up and says "I also need to add Slack Notification to the system". You, the pro-developer say, "Yo! I got this man!" and change the `UserController` as below.

```php
class UserController {
    protected $notification;

    public function __construct($notificationType) {
        if ("WhatsApp" == $notificationType) {
            $this->notification = new WhatsAppNotification();
        } else {
            $this->notification = new SlackNotification();
        }
    }

    public function register($email, $password) {
        // Register user functionality in here

        // Send notification
        $this->notification->send($email, 'Welcome!', 'Thank you for registering.');
    }
}
```

Again after a month, your manager asks you to add email notifications as well. Now you are frustrated because you have to change the class over and over again when you get a new requirement. This is a very bad practice and this can be easily solved via the Dependency Inversion Principle.

I'll remind you again what this principle says.

> High-level modules should not depend on low-level modules. Both should depend on abstractions.

In this example, `UserController` is the high-level module. And `WhatsAppNotification` and `SlackNotification` classes are low-level modules. It basically says, when you add the functionality of these notifications, it should not get affected to the `UserController` class. Simple as that. So when you implement the principle, you will not have to re-change the `UserController` class over and over again.

Let's jump into the code and restructure it.

So, if I am doing this, what I will do is, I will (I am saying "I will" like I am the person who found this principle. Actually, that would be nice. But I did not. So, 😅😂) create an interface for these notifications and add the `send()` there. So, all the notification classes that implement this interface will have to override the send function.

Can I create just the classes without the interface? Then it would be hard to implement the principle. You will see why. Stay with the article.

```php
interface NotificationInterface {
    public function send($to, $subject, $body);
}
```

Now the three notification classes.

```php
class WhatsAppNotification implements NotificationInterface
{
    public function send($to, $subject, $body)
    {
        // send email using WhatsAppNotification library
    }
}

class SlackNotification implements NotificationInterface
{
    public function send($to, $subject, $body)
    {
        // send email using SlackNotification library
    }
}

class WhatEverTheHellYouNeedToImplementNotification implements NotificationInterface
{
    public function send($to, $subject, $body)
    {
        // send email using WhatEverTheHellYouNeedToImplementNotification library
    }
}
```

Now to the `UserController`. Remember we have to structure this like it has nothing to do with adding low-level classes.

```php
class UserController
{
    protected $notification;

    public function __construct(NotificationInterface $notification)
    {
        $this->notification = $notification;
    }

    public function register($email, $password)
    {
        // Register user functionality in here

        $this->notification->send($email, 'Welcome!', 'Thank you for registering.');
    }
}
```

Now, what has happened? Do you remember the Dependency Injection? Injecting a class into another class's constructor. THAT HAPPENED. But, instead of a class, we have injected an interface.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1671624489089/OTAcP1faT.gif align="left")

> WHY THE HELL IS THAT?

```plaintext
public function __construct(WhatsAppNotification $notification)
{
    $this->notification = $notification;
}
```

If we inject a class object as the above code, you will only be able to pass an object of the mentioned class. As an example, since I have mentioned `WhatsAppNotification` in here, I will not be able to send a `SlackNotification` class to the `UserController` 's constructor.

But, if I mention an interface instead of a class, I will be able to pass any object that `implements` the interface. So here (not the white-coloured code, above that), I will be able to pass `WhatsAppNotification` , `SlackNotification` or `WhatEverTheHellYouNeedToImplementNotification` classes into the `UserController`'s constructor (Because all those classes `implements` the same interface).

I'll add the complete script again here so it is easy to explain.

```php
interface NotificationInterface {
    public function send($to, $subject, $body);
}

class UserController
{
    protected $notification;

    public function __construct(NotificationInterface $notification)
    {
        $this->notification = $notification;
    }

    public function register($email, $password)
    {
        // Register user functionality in here

        // Send notification
        $this->notification->send($email, 'Welcome!', 'Thank you for registering.');
    }
}

class WhatsAppNotification implements NotificationInterface
{
    public function send($to, $subject, $body)
    {
        // send email using WhatsAppNotification library
    }
}

class SlackNotification implements NotificationInterface
{
    public function send($to, $subject, $body)
    {
        // send email using SlackNotification library
    }
}

class WhatEverTheHellYouNeedToImplementNotification implements NotificationInterface
{
    public function send($to, $subject, $body)
    {
        // send email using WhatEverTheHellYouNeedToImplementNotification library
    }
}
```

In the above code, whatever notification class you add, you only need to implement the `NotificationInterface` . And that notification class will easily be able to pass to the `UserController` class. Does not matter how many notification types you add, you do not need to change the `UserController` class. So, as the principle says "High-level module (aka. `UserController` class) will not depend on lower-level modules (aka. `WhatsAppNotification` , `SlackNotification`, `WhatEverTheHellYouNeedToImplementNotification` classes)".

See how you made a disastrous code into a nice maintainable one. This is the main advantage of these principles. Easily maintainable classes.

By following the dependency inversion principle, it is easier to create modular, maintainable, and testable code that is less prone to breaking when changes are made.

Okay, here it goes with the **SOLID Design Principles**. It took a little longer than I thought with the break I took. My bad 😥.

> *Complete list of SOLID principles that I have explained in this series.*
> 
> [Single Responsibility Principle](https://dulitharajapaksha.hashnode.dev/solid-design-principles-single-responsibility-principle)
> 
> [Open/Closed Principle](https://dulitharajapaksha.hashnode.dev/solid-design-principles-open-closed-principle)
> 
> [Liskov Substitution Principle](https://dulitharajapaksha.hashnode.dev/solid-design-principles-the-liskov-substitution-principle)
> 
> [Interface Segregation Principle](https://dulitharajapaksha.hashnode.dev/solid-design-principles-interface-segregation-principle)
> 
> [Dependency Inversion Principle](https://dulitharajapaksha.hashnode.dev/solid-design-principles-dependency-inversion-principle)

See you again soon then!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1671624730008/dAeDiH2Je.gif align="left")