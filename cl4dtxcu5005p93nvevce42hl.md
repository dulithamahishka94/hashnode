## Strategy Design Pattern

Hello hello! So far I have completed two design patterns in this blog and if you haven't read any, go see the series about design patterns from [here](https://dulitharajapaksha.hashnode.dev/series/design-patterns-101).

Today I brought you another design pattern which developers usually get confused. Sometimes this pattern looks very hard to understand, but I assure you there is nothing complex in this pattern.

So, have you packed your luggage? Are you ready to visit Banana Republic?

> Good morning passengers. This is the pre-boarding announcement for Flight Banana 007B to Banana Republic. We are now inviting those passengers who are afraid of design patterns or like to learn about them, to begin boarding at this time. Please have your boarding pass and identification ready. Regular boarding will begin in approximately ten minutes time. Thank you.

![VTsh1y.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1654797935067/e8RBv2aQe.gif align="left")

## The Story

Do you like sandwiches? (If not, please start liking sandwiches. I mean, who doesn't like sandwiches?) The capital of the Banana Republic has a great sandwich bar called BananaWiches. It has a large kitchen with the best chefs on the island. At the start, the chefs got overwhelmed by the orders and they could not deliver some on time. As usual there were some angry customers who complained about the chefs delay to the higher management. Then the higher management decided to check the sandwich processing flow to get an idea about why the delay happens. After watching the process for a few moments one manager figured out the reason for the delay.

WHAT WAS THAT?

The chefs usually start the preparation of two bread slices on two sides only after they received the order from the customer. It takes a significant amount of time to prepare those bread slices. But what the manager realized was, even though there are many sandwiches, the bread slices were the same. So, he asked one of the chefs to confirm what he saw. "Do you use the same grilled bread slice for every sandwich?", the manager asked. "Yes", the chef replied.

"Let's change your strategy of making the sandwiches", the manager said again. What the manager proposed was to make all the needed bread slices prior making the sandwich. "So, you only need to change the middle filling on the fly", the manager said.

Let's recap what the manager has suggested. Even though the chefs make different sandwiches, they use the same bread slices. In very simple terms, for chees sandwiches, chicken sandwiches or whatever the sandwich type you can imagine, the chefs use the same type of grilled bread slices as top and bottom of the sandwich. Now, the chefs can make those slices before start making the sandwiches. So, they can save time on preparing the bread slices. Whatever the filling they intended to put, can be put when the order comes in. A simple yet a perfectly working solution (And time saving too 😉).

Okay! Let's simplify things more. A sandwich consists of three layers. Top and bottom two bread slices, and a filling in the middle).

![6j88mc.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1654864178210/aVpIvCxlz.jpg align="left")

Now, the type of the sandwich is defined with the type of the filling in the middle. If you use cheese, it will be a cheese sandwich. You can use chicken, then it will be a chicken sandwich.

I NEED A SANDWICH STAT!

![200.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1655137404005/0LfhBNW0D.gif align="left")

So, I was telling you that, the middle layer of the sandwich can be changed according to the order. There is no way that the chef can predict what the next customer wants. So, he has to put the filling in the middle of the process of making the sandwich (in other words, at runtime). Also, he can swap out the middle filling according to the customers needs. Because whatever the chef does, the bread slices will remain the same.

> Simple yet awesome!

Let's make a simple application that follows the same practice. What do we need first?

1. We obviously need a sandwich
2. Then we need some fillings (that can be changed according to the order)
3. We need some logic to change them accordingly without touching the sandwich (The fillings has no dependency with the sandwich)

A class diagram for a solution like above will look like follows.

![Blank diagram - Page 3.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1654866081677/vjqEpmX54.png align="left")

What do we have here?

First there should be a main strategy interface. That defines the common functions that needed by the supported strategies (or algorithms). We call Chicken, Cheese and Vege classes as *concrete strategy classes* that implements the *Strategy Interface*. These classes get called from the context according to the arguments it passes.

Context is the one who decides which strategy to call upon the arguments it gets. It is basically the decision maker of the application. It this story, the chef. He must decide what filling he has to put by looking at the order (the arguments).

Let's see how the code will look like.

(Please be noted that these are php syntaxes. The strategy and coding structure will almost be the same in other languages other than the syntaxes)

First, as usual we will be creating the interface which holds the function that needs to be implemented inside strategies.

```php
interface FillingType {
    function addFilling();
}
```

Now, we have to create each sandwich class (strategy classes) implementing the above interface. So, the context knows where to call in the strategy.

```php
class CheeseSandwich implements FillingType {
    function addFilling() {
        echo "Cheese filling has been added to the sandwich";
    }
}
```
One sandwich class is created. After implementing the interface into the sandwich class, the function `addFilling()` should get overridden. Now it is time to make the other two sandwich classes. It follows the same pattern as the above.

```php
class ChickenSandwich implements FillingType {
    function addFilling() {
        echo "Chicken filling has been added to the sandwich";
    }
}

class VegeSandwich implements FillingType {
    function addFilling() {
        echo "Vegetable filling has been added to the sandwich";
    }
}
```
As you can see, each an every class has a `addFilling()` function that holds a different functionality. It depends on the strategy that the context uses. In this case, depends on the sandwich type.

Let's create the chef class now.

```php
class Chef {

    private $fillingType = null;

    function __construct(FillingType $filling_type) {
        $this->fillingType = $filling_type;
    }

    function useFilling() {
        $this->fillingType->addFilling();
    }
}
```
What have we done here?

It is chef's functionality to use the appropriate filling for his order. To choose the correct filling he has to identify which strategy he has to use. That is when the constructor comes into play. When creating the chef's object we can pass the strategy type onto the chef (order onto the chef). Right after the constructor gets called, the argument will get assigned to the variable `private $fillingType`. In the `useFilling()` function, the chef calls the `addFilling()` method by using the passed object that has already been assigned to `private $fillingType`. If the CheeseSandwich class is passed into the Chef class, the Chef will call the `addFilling()` method on the CheeseSandwich class.

Have you noticed that we used `FillingType` interface as a parameter to the constructor? Do you know the reason why?

If we use a concrete class as the argument, only that class will be able send through as a parameter. By using an interface, we simply say "the class we are passing can be of this type", without strictly mentioning the class name. So, any class that implements the interface, can be injected through the constructor of the context class.

Finally, as I told you before, we just need to call the Chef class with the FillingType instance.

```php
$filling = new Chef(new ChickenSandwich());
$filling->useFilling();
```

The result should get printed as **Chicken filling has been added to the sandwich**.

THE END!

Here is the final codebase.

```php

interface FillingType {
    function addFilling();
}

class CheeseSandwich implements FillingType {
    function addFilling() {
        echo "Cheese filling has been added to the sandwich";
    }
}

class ChickenSandwich implements FillingType {
    function addFilling() {
        echo "Chicken filling has been added to the sandwich";
    }
}

class VegeSandwich implements FillingType {
    function addFilling() {
        echo "Vegetable filling has been added to the sandwich";
    }
}

class Chef {

    private $fillingType = null;

    function __construct(FillingType $filling_type) {
        $this->fillingType = $filling_type;
    }

    function useFilling() {
        $this->fillingType->addFilling();
    }
}

$filling = new Chef(new ChickenSandwich());
$filling->useFilling();
```

## What happens in here? (More of a technical explanation)

In this pattern, we depend on composition rather than inheritance. Context is made up of a Strategy. Context delegates a behavior to Strategy rather than implementing it. The context would be the class in which new behaviors would be required. We have the ability to change our behavior on the fly. Strategy is implemented as an interface to allow us to change our behavior without changing our context.

This pattern is all about composition. The main reason for that is, the strategy needs to be applied on the runtime. In the beginning of the code, it has no idea what would be the strategy for making the order. It automatically finds the correct strategy on runtime.

This pattern has both advantages and disadvantages.

As advantages we can take,

1. Code is very clean since it follows the principle SoC (Separation of Concerns). That means each strategy has a different class. Everything is separated.
2. It follows polymorphism. It means it can easily interchange the strategies on runtime.
3. Strategies will be applied automatically without long queue of if-else statements.

Disadvantages are,

1. The application should be aware of the strategies it contains.
2. Some strategies may not use any additional functions the strategy interface has (But that can be mitigated using Interface Segregation Principle more deeply. It is a talk for another day.)

This pattern follows below *design principles*.

1. Favor composition over inheritance.
2. Interface Segregation Principle.
3. Encapsulate what varies.
4. Separation of Concerns (SoC)

Do not worry if you do not know what the design principles are, and what those terms mean. I'll bring a separate series on design principles in near future. Stay tuned!

If you still cannot figure out the name of this pattern (The name is the most used word in this article), it is called **Strategy Design Pattern**.

The most common use of this pattern is for the sorting algorithms (quick sort, bubble sort etc.). You just need to pass the algorithm type and the data that needs to be sorted. It will sort your data according to the *strategy* you choose.

That's about it. Another design pattern is checked. See you soon with another episode of Simple yet awesome!

(Hit on subscribe. So, you will not miss my future articles. Nothing to think. Just put the email and press subscribe 🤗)

Cheerio!

![cheers-james-spader.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1655136700982/G8Wg7dU25.gif align="left")






