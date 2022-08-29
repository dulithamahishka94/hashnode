## SOLID Design Principles : Open/ Closed Principle

We have learned what the **S** means in SOLID principles. If you haven't read it yet, you can read it from [here](https://dulitharajapaksha.hashnode.dev/solid-design-principles-single-responsibility-principle). 

Now, for the second letter in SOLID. It stands for **Open/ Closed Principle**. 

# What is open closed principle?

The principle says "Software entities (classes, modules, functions, etc.) should be open for extension, but closed for modification". That is nice! But what does it really mean? 

![confused-icegif.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1660109119950/GEY05NWwk.gif align="left")

The general idea about this is, you should be able to add new functionality to entities (classes, modules, functions, etc.) without changing it's existing codebase.

Let's discuss it with a real world example. Imagine you have a system with a notification system.

```php
class Application {
    private $notificationType = null;

    public function __construct($type) {
        $this->notificationType = $type;
    }

    public function sendNotification() {
        $notification = new Notification;
        if ($this->notificationType == 'text') {
            // do work related to structuring the notification.
            $notification->sendTextNotification();
        } else {
            // do work related to structuring the notification.
            $notification->sendEmailNotification();
        }
    }
}

class Notification {
    /*
     * Send text notifications.
     */
    public function sendTextNotification() {
    }

    /*
     * Send email notifications.
     */
    public function sendEmailNotification() {
    }
}
```
Right now, you do not have any problem and this does not break the OCP (Open/ Closed Principle). But the problem occurs when you get another notification type that needs to be added.

```php
class Application {
    private $notificationType = null;

    public function __construct($type) {
        $this->notificationType = $type;
    }

    public function sendNotification() {
        $notification = new Notification;
        if ($this->notificationType == 'text') {
            // do work related to structuring the notification.
            $notification->sendTextNotification();
        } else if ($this->notificationType == 'toast') {
            // do work related to structuring the notification.
            $this->sendToastNotification();
        } else {
            // do work related to structuring the notification.
            $notification->sendEmailNotification();
        }
    }
}

class Notification {
    /*
     * Send text notifications.
     */
    public function sendTextNotification() {
    }

    /*
     * Send email notifications.
     */
    public function sendEmailNotification() {
    }

    /*
     * Send toast notifications.
     */
    public function sendToastNotification() {
    }
}
```
Even though this seemed fine, this breaks OCP. Why? You have modified the existing class. By doing this, your development might break an existing functionality. This is not good. 

So, how do we add functionalities to a class without breaking OCP? How do you extend the functionalities of a class without modifying it. Seems impossible? Let's discuss. 

First things first, why do we need to extend without modifying a class? Let's say you are doing a complex code. If you are modifying an already working production sent code, you have to be 100% certain it does not break the existing functionality. I know there can be unit tests which you can test before deploying the new code. But there is a chance we should not take lightly. 

So, how do we do this? Let's move step by step. 

For this, you can use a design pattern called *Factory Design Pattern* (My next article will be about the Factory Pattern. [Subscribe](https://dulitharajapaksha.hashnode.dev/newsletter) to my newsletter. So, you'll be notified once I publish it). 

To resolve this issue, we are creating a separate factory class where we hold the notification details. If we have any more notification types in future, we can easily update the factory class without updating the existing `Application` class. 

First, create an interface for the notifications. You'll get to know soon why we need the interface. 

```php
interface NotificationInterface {
    public function send();
}
```

Now create separate classes for each notification type and implement the interface on the classes.

```php
class TextNotification implements NotificationInterface {

    public function send() {
        // TODO: Implement send() method.
    }
}

class ToastNotification implements NotificationInterface {

    public function send() {
        // TODO: Implement send() method.
    }
}

class EmailNotification implements NotificationInterface {

    public function send() {
        // TODO: Implement send() method.
    }
}
```

By implementing the interface, you also need to override the `send()` abstract function of the interface inside concrete notification classes. 

Next stop is the factory class.

```php
class NotificationFactory {

    /*
     * Select the notification type.
     */
    public function selectNotification($notificationType) {
        if ($notificationType == 'text') {
            return new TextNotification;
        } else if ($notificationType == 'toast') {
            return new ToastNotification;
        } else {
            return new EmailNotification;
        }
    }
}
```

When you pass the correct argument into the `selectNotification($notificationType)` function, it will return the notification class accordingly. Whenever we need to add more notification types, we can do so without needing to change the existing code. All we have to do now is initialize the notification type from our `NotificationFactory ` class and create a new notification class by implementing our `NotificationInterface` . That is all. 

Finally, you can simply call the function inside the factory class with the appropriate parameters from the `application` class. 

```php
class Application {
    private $notificationType = null;

    public function __construct($type) {
        $this->notificationType = $type;
    }

    public function sendNotification() {
        $notificationFactory = new NotificationFactory();
        $notification = $notificationFactory->selectNotification($this->notificationType);
        $notification->send();
    }
}
```
To summarize, the Open/ Closed principle is very simple to apply; it aids in the addition of new features by extending existing code rather than modifying it. To follow the Open Closed principle correctly, all you have to care about is whether you will modify your code in the future to add new features or functionality. 

The reason we add the interface is, mainly you can generalize the `send()` function in concrete classes. So, you do not need to worry about the class you need to call from the base application class.

> It is not over yet!

# Advantages of Open/ Closed Principle

- Code is easily maintainable
The fundamental advantage of this principle is that an interface adds another degree of abstraction, allowing for loose coupling. An interface's implementations are independent of one another and do not need to interact any code.

- Code is more flexible
In the case of plugins, there is a basic or core module that may be supplemented with additional features and capabilities via a common gateway interface. Web browser extensions are a nice illustration of this.

That is about it for the day. Let's meet with the next article on SOLID Principles. That is **Liskov Substitution Principle**.

Cheerio!

![breechesapeakeshores-cheerio.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1660132945751/_T42C-9_A.gif align="left")







