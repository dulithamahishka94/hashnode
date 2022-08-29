## Observer Design Pattern

Design patterns! Hmm!! An interesting subject among the software engineers. How frequent do you think a developer gets frustrated with a hard-to-maintain code which was written by a previous developer? Surprisingly, quite a lot. "How could you say that? Developers are also humans". Yes! I agree. But developers who take over another developer's tasks are also human. So if you do code, make sure you code in a manner where another developer can easily understands and easily maintainable. That is where the design patterns come in.

I will start off my blog with a simple design pattern. I will tell the name at the end of the post (I know it is in the title of the post. 'abracadabra'! Okay now you cannot remember it. Keep on reading. Hop hop. ), so you have an idea why they call the pattern that way.

> Buckle up! This is going to be a bumpy ride!!

## The story
Let's imagine this situation which happened in [Banana Republic](https://en.wikipedia.org/wiki/Banana_republic). The Banana Republic has quite a good development rate. So, they built a bank (Let's call it as Banana Bank) and a Central Bank to control the Banana Bank. After that, the central bank has made an agreement with Uncle Sam and they have decided to float their currency with USD (Uncle Sam's Dollar).

Now the people have started to go for the bank to exchange their local currency with USD. As the parent bank, the Central Bank of Banana Republic is deciding the exchange rate for the current day. The Banana Bank calls them at midnight, and get the current exchange rate and exchange the money of the people according to the given rate by the central bank. People have started to come in, the Banana Bank got overwhelmed, so the Central Bank of Banana Republic has decided to open another bank. They also started to do the same. Calling the Central Bank of Banana Republic at midnight and get the exchange rates. Again the same happened. More people have started to come in and again Central Bank of Banana Republic has opened more banks to facilitate people's needs. Suddenly, there was this incident, the Central Bank of Banana Republic decided to change the exchange rate at the midday. Unfortunately, the other banks did not know that and they have exchanged the money as per the exchange rate they received yesterday midnight. That introduced a chaos and people have started complaining to the Central Bank of Banana Republic.

The Central Bank of Banana Republic got irritated by these late night calls and they have decided to change the process of informing these exchange rates. But there was a little hiccup. There were some banks who registered under the Central Bank of Banana Republic, but they did not want the USD exchange rates. Hmm! The managers of Central Bank of Banana Republic got together and started to find a solution. And they have found a solution where they can meet all the needs.

WHAT WAS THAT?

They have appointed person as a registry (A banana man). Through the banana man, the banks can register themselves with a mobile number. So, whenever the exchange rate changes, the banana man can send a text message to all the registered banks with the new information. And upon receiving the text message, the banks can change their exchange rate accordingly. If anyone wants to opt-out later, they can also inform the banana man and he will take the necessary steps to opt-out. No late night calls. A simple yet a perfectly working solution.

Let's make some diagrams, it will be helpful to understand the situation.

![Blank diagram - Page 1 (2).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653841711208/YQl3QYovY.png align="left")

In here, the Central Bank of Banana Republic is called a *Subject * or a *Publisher*. It's job is to send out the information required for the *Dependents* or the *Subscribers*. There is always **one publisher** and **one or multiple subscribers**. Hence, the relationship is called **one-to-many**. The publisher does not care about the subscribers functionality. The subscriber may or may not have additional functionalities. In this context, the Banana Banks may or may not have the option of fixed deposits or current accounts. It depends on the bank. The Central Bank of Banana Republic has nothing to do with the dependents functionalities. It simply sends out the information for the registered subscribers.

> Simple yet awesome!

If we are to make a sample code for the above scenario, how do we do it? Let's breakdown the situation.

1. We need the Central Bank of Banana Republic.
2. We need some other banks.
3. There should be a place inside the central bank to register/unregister the banks.
4. A functionality needed to inform the other banks whenever the exchange rate gets changed.
5. Other banks also should have a place to receive the exchange rate information.

First let's see how the class diagram should be with an optimum solution.

![Blank diagram - Page 1 (3).png](https://cdn.hashnode.com/res/hashnode/image/upload/v1653842424705/MC3CE4r91.png align="left")

In above class diagram, the *subject* has an interface. It has defined the functions that needs to be in the class ConcreteSubject (aka. Central Bank of Banana Republic).

The ConcreteObserver (aka. Banana Bank) needs to have a function which can be called upon the state changes (Exchange rate changes of the Central Bank of Banana Republic).

As I said earlier, the subject does not care or know about the functionality of the *observer*. But, the subject should know where to call on the observer. Hence we create another interface for the observer. All the observers that follows the pattern should *implement* the observer interface and by doing that, the caller function inside the observer interface will get automatically overridden on the ConcreteObserver class. So, the subject knows where to call.

Lets jump into the code!

(Before getting your hopes up, below code is based on PHP. It is almost same with other languages, only the syntaxes are different)

Let's go with the points and the class diagram I have mentioned earlier.

First, create a class for Central Bank of Banana Republic.

```php
// CBBR - Central Bank of Banana Republic
class CBBR {

}
``` 

We have created the ConcreteSubject. Now we need to create the Subject interface and *implement* it on ConcreteSubject. By implementing the interface, all the abstract functions will get overridden inside the class.

```php
interface ExchangeRate {
    function notifyBanks();
    function registerBank($bank);
    function removeBank($bank);
}

class CBBR implements ExchangeRate {
    private $dollarRate = 100; // Decided to keep the base rate as 100. Nothing to concern.
    private $banksList = [];

    public function setDollarRate($rate) {
        $this->dollarRate = $rate;
        $this->notifyBanks();
    }

    public function registerBank($bank) {
        $this->banksList[] = $bank;
    }

    public function removeBank($bank) {
        // remove bank..
    }

    public function notifyBanks() {
        foreach ($this->banksList as $key => $bank) {
            $bank->updateRate($this->dollarRate);
        }
    }
}
``` 
All the three functions have been overridden inside the concrete class (aka. CBBR). Also, I have updated the contents of the functions. Let's break it down shall we?

The variable `private $dollarRate` is used to keep the value of the dollar exchange rate. It is private because only the Central Bank has the ability to change it. Since that, I have added another function called `setDollarRate($rate)` which can be used to update the `$dollarRate` variable when a value is passed (If you are not aware, this mechanism is called encapsulation).

All the banks should be registered prior to receive the information about the exchange rates. Hence the function `registerBank($bank)` comes into the play. Whenever you call the function with an instance of a bank, it will get registered. I have used a simple array in here, but you can use any kind of data structure or a data storing mechanism to store these data. Whenever the `registerBank($bank)` gets called, the parsed instance will get added to the array ` private $banksList`. The modifier `private` is used because it can be only accessed within the class.

I took the pleasure of not adding the code inside `removeBank($bank)` (I may be lazy or I may not know how to remove an element from an array. It is up to you to decide).

Finally, whenever we set a exchange rate, `notifyBanks()` function will get called from `setDollarRate($rate)` and it will iterate through the array and call a function named `updateRate()` (Where is that `updateRate()` function? Wait, the show is not over yet!).

> Popcorn time!
> ![tumblr_f890edc6fd80ba0b95e9acef493b868f_f2c397ac_500.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1653844219838/L6btmUJlM.gif align="left")

Back to business.

Now we are finished with the *Publisher*'s part. Let's go and see about the subscribers.

If you look at the class diagram again, you will see there is an interface for the publisher too. I have already explained why we need an interface for this.

Lets make the interface.

```php
interface Banks {
    function updateRate($rate);
}
```

Now is the time to shine for the Banana Banks.

```php
class BananaBankOne implements Banks {

    private $rate = 0;

    function __construct(CBBR $centralBank) {
        $centralBank->registerBank($this);
    }

    function updateRate($rate) {
        $this->rate = $rate;
        $this->getRate();
    }

    function getRate()  {
        echo 'USD rate in Banana Bank One is ' . $this->rate . "<br>";
    }
}
```

> It's break down time!

Right after implementing the interface on the `BananaBankOne` class, the abstract method should get overridden. In there we can use whatever the method we prefer to capture the passing exchange rate, and I have assigned it into a variable called `private $rate` in here. When it gets updated, the function `getRate()` will get called and it is used to show the current exchange value to the bank.

End of the pattern. So, how does it work?

```php
$centralBank= new CBBR();
new BananaBankOne($centralBank); // To register your bank as a subscriber.
```

When you need to register the bank, you just need to call the `registerBank($bank)` function. In this scenario, I took the liberty of calling the function from the Banana Bank's constructor. Once you call the function, it will get stored in the publisher and will call the registered subscriber upon updating the state (In this case, the exchange rate).

Now you have registered the bank. Only thing needed is to change the exchange rate of the Central Bank.

```php
$centralBank->setDollarRate(200); // Change the dollar rate and subscribers will get automatically notified.
```
The result should get printed as ***USD rate in Banana Bank One is 356***

THE END!

Now you should have an idea about how this pattern works and the usage. Yay!!

The final codebase will look like follows.

```php
interface ExchangeRate {
    function notifyBanks();
    function registerBank($bank);
    function removeBank($bank);
}

class CBBR implements ExchangeRate {
    private $dollarRate = 100;
    private $banksList = [];

    public function setDollarRate($rate) {
        $this->dollarRate = $rate;
        $this->notifyBanks();
    }

    public function registerBank($bank) {
        $this->banksList[] = $bank;
    }

    public function removeBank($bank) {
        // remove bank..
    }

    public function notifyBanks() {
        foreach ($this->banksList as $key => $bank) {
            $bank->updateRate($this->dollarRate);
        }
    }
}

interface Banks {
    function updateRate($rate);
}

class BananaBankOne implements Banks {

    private $rate = 0;

    function __construct(CBBR $centralBank) {
        $centralBank->registerBank($this);
    }

    function updateRate($rate) {
        $this->rate = $rate;
        $this->getRate();
    }

    function getRate()  {
        echo 'USD rate in Banana Bank One is ' . $this->rate . "<br>";
    }
}

$centralBank= new CBBR();
new BananaBankOne($centralBank);

$centralBank->setDollarRate(356);
```

## Usage of this design pattern

This design pattern is widely used among the systems that use subscriber architecture.
Such as,
- News sites
- RSS feeds
- Notification delivery

Just think of Twitter. The process of twitter is, you can follow someone and when they post a tweet, you'll get a notification. You do not know at what time the person you follows will tweet something. So, you cannot be on top of your follower's profile 24/7 to check whether they have posted a tweet. Imagine that with many followers. That would be disastrous (Like what happened with the Banana Bank). Instead, they have given you this simple solution. You'll get a notification when the person you follow posted a tweet. When you receive a notification you know that the person you follow has posted something. In there, you are a subscriber. And the person you follow, is a publisher. Seems familiar huh? 

## What can you see in a design pattern like this?

- Subjects and observers are loosely coupled
- They have little knowledge of each other
- Subject knows only that the observer implements a specific interface
- Observers can be moved freely. They can be added or removed at any time.

Let's again take twitter again. You and the person you follow are loosely connected (loosely coupled). It means you may or may not know about the person you follow. He does the same. You two may have different functionalities. But it does not matter to deliver your follower's tweet to you. You are free to move. It means you can easily follow/ unfollow the person you follow.

The technical aspect is more deeper than this. But this will help you to get a clear understanding about how this pattern works and what are the characteristics.

Oh! I forgot to tell you the name of this pattern. Hope you already guessed it (and seen it on the post title). Yes! This is called the **Observer Design Pattern**.

That's about it. Hope you learned something today. See you soon with another episode of Simple yet awesome!

Till then,

![bye-felicia-hasta-la-vista-baby.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1653845572433/of3gxijP2.gif align="left")





