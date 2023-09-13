---
title: "How inheritance can complex your code in OOP design (If you use it wrong)"
seoTitle: "Inheritance or Composition"
seoDescription: "Inheritance can make your codebase complicated if you do not know when or how to use it. You should consider using composition over inheritance."
datePublished: Tue Jun 21 2022 05:09:00 GMT+0000 (Coordinated Universal Time)
cuid: cl4npi9s50311venv0lspc6kn
slug: how-inheritance-can-complex-your-code-in-oop-design-if-you-use-it-wrong
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1655734797117/fng1iRZwH.png
tags: software-development, inheritance, object-oriented-programming, composition, simple-yet-awesome

---

How was your life when you started programming? Was it good? Or was it bad? For me, it was both. Good because it felt like a nice vibe to be a programmer, and also kind of bad because I had no idea what I was doing. There is this, there is that and there is this that. What are you talking about?

So, if you remember, all of you started programming by coding something on a file from top to bottom. No classes, no methods, NOTHING. As someone who was born in 1600 BC, I know the feeling.

![200 (1).gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1655283928999/GR2KRHt99.gif align="left")

Then the concept of Object Oriented Programming (OOP) came into action around 1966-1967. Everything was separated into pieces and called them classes and functions (methods in some languages). They were treated as objects to ease the interaction in between them.

There are four core object-oriented concepts.

1. Encapsulation
    
2. Abstraction
    
3. Inheritance
    
4. Polymorphism
    

Among these, the concept inheritance plays a major role. But if you do not know when to use inheritance, you'll be in a big trouble. Even though inheritance looks best, it can complex your solutions and codebases like no other. Let's talk about inheritance a bit more.

> First of all, what does it mean by inheritance?

In general English, inheritance means the process of something that passing from a parent to a child. In software engineering, it means the same.

![6jqgs5.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1655285768596/c8wSmKGah.jpg align="left")

It is the behavior. When you think about classes, a child class can inherit behaviors from a parent class. The behaviors are called functions or methods. If a child class is extended from a parent class, it inherits parent class's functions. But there is no rule like "The child class must do the same as the parent". If a child inherits the eye color of the parent, it does not mean that the child can never change his/ her eye color again.

> Then how it can complex the codebase?

Just imagine a situation like this. You have a parent class.

```php
class Animal {
    function eat() {

    }
    
    function speak() {
        
    }
}
```

Now you have two sub classes.

```php
class Mammal extends Animal {
    
}

class Bird extends Animal {
    function fly() {
        
    }
}
```

Above has no issues because mammals and birds are animals. So they can inherit the parent class functionalities.

Now, let's take the Bird class. Bird has a function called `fly()`. Because birds can fly. But could everyone? Can Ostrich fly? Nope! So, can I extend Bird class and make a child class called Ostrich? Can. But it is wrong. Hence I need another class above Ostrich.

```php
class Bird extends Animal {
    
}

class BirdsCanFly extends Bird {
    function fly() {
        
    }
}

class BirdsCannotFly extends Bird {
    
}
```

Hmm, now it looks good. But, even though Ostrich cannot fly, it can run . Penguins cannot fly or run, they swim. Again, I have to make another two classes for both animals.

```php
class BirdsCannotFlyButRun extends BirdsCannotFly {
    
}

class BirdsCannotFlyButSwim extends BirdsCannotFly {
    
}
```

See how complicated it gets when the requirement changes?

Some languages such as C++ supports *multiple inheritance* (Two or more parent classes can inherit behaviors into a subclass). It can get much more complicated than the above example.

Let's see a practical scenario where developers use inheritance.

Assume you are creating some kind of a invoice payment application which needs a sales tax calculation.

```php
class Invoice {

    public function calculateInvoiceValue() {
        $invoiceAmount = 1000;

        $salesTaxOnInvoice = $this->calculateSalesTax($invoiceAmount);
    }

    public function calculateSalesTax($amount) {
        return $amount + ($amount * 8 / 100);
    }
}
```

This has no issue. This is totally acceptable. Now, imagine you need to add a payment class which also needs a sales tax calculation.

```php
class Payment {
    public function calculatePayment() {
        $paymentAmount = 5000;
        $additionalPayments = 600;
        
        $totalPayment = $paymentAmount + $additionalPayments;

        $salesTaxOnInvoice = $this->calculateSalesTax($totalPayment);
    }

    public function calculateSalesTax($amount) {
        return $amount + ($amount * 8 / 100);
    }
}
```

Now you see the issue. Function `calculateSalesTax($amount)` that calculates the sales tax is duplicated. There is a design principle called DRY (Don't Repeat Yourself), which ultimately says "Do not repeat the codes".

> If you encounter something like this, what would be the first thing that comes into your mind?

***Create a separate class for tax calculation and extend it on the child class***. Yes!

Let's change the code a bit.

```php
class Invoice extends SalesTax {

    public function calculateInvoiceValue() {
        $invoiceAmount = 1000;

        $salesTaxOnInvoice = $this->calculateSalesTax($invoiceAmount);
    }
}

class Payment extends SalesTax {
    public function calculatePayment() {
        $paymentAmount = 5000;
        $additionalPayments = 600;

        $totalPayment = $paymentAmount + $additionalPayments;

        $salesTaxOnInvoice = $this->calculateSalesTax($totalPayment);
    }
}

class SalesTax {
    public function calculateSalesTax($amount) {
        return $amount + ($amount * 8 / 100);
    }
}
```

Yey!! We did use inheritance and solved our problem in a correct way. **Or did we?**

Even though this works properly, it does not mean that it is implemented in a correct way. **Most of us use inheritance to reduce the code duplication. AND THAT IS WRONG!**.

![2faa16ab-b0f3-493f-9779-8f040ebe5ef5_text.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1655447137132/vp6eAHxu_.gif align="left")

Inheritance is **never meant to be used to reduce code duplication**. Even though the code works perfectly, it can arise other issues. By inheriting a class, it automatically exposes the public and protected functions to the child class. However, the `Invoice` and `Payment` classes only care about the `calculateSalesTax($amount)` method. They do not care about the other functions in the `SalesTax` class. So it is basically a waste.

And also, inheritance makes a **IS-A** relationship between two classes. So in this case, if we say *Invoice is-a sales tax*, does it sound correct to you? Is invoice a sales tax? Doesn't it sounds fishy? Yes! This is the first red flag that you get when you do something like this. You are trying to make a relationship between two things that do not belong. Like apples and poop 😂

Is there any other way to do this? That is where the **composition** comes into action. Composition has a different relationship when comparing with inheritance. Composition relates with "HAS-A" relationship while inheritance introduces a "IS-A" relationship. So, you can say, Invoice has a Sales Tax Calculator instead of Invoice is a Sales Tax Calculator.

How can we solve the above issue with composition? It is fairly simple.

```php
class Invoice {

    private $salesTax = null;

    function __construct(SalesTax $salesTax) {
        $this->salesTax = $salesTax;
    }

    public function calculateInvoiceValue() {
        $invoiceAmount = 1000;

        $salesTaxOnInvoice = $this->salesTax->calculateSalesTax($invoiceAmount);
    }
}

class SalesTax {
    public function calculateSalesTax($amount) {
        return $amount + ($amount * 8 / 100);
    }
}
```

For starters, we can get rid of the inheritance relationship of the class. Hence we can remove `extends SalesTax` from the invoice class. So it no longer supports inheritance. Then we can add a constructor in the Invoice class with a argument of the `SalesTax` class. And inside that constructor we can assign the passed argument into a variable, so later it can be used from a function inside Invoice class. This way, you can only access the functions that you need (You can set the access modifier as `protected`, if you do not want to let other classes access the functions through composition).

We have successfully converted an inheritance flow into a composition flow.

Does it mean that we can totally forget inheritance and go with composition. NOPE! I know what you are going to ask next.

## How do we decide when to use inheritance and when to use composition?

First thing that can come to your mind is, if we can see IS-A relationship, we can use inheritance. Even though it looks correct, there could be scenarios where it will make issues in future. Let's look at below example.

Let's assume we are creating a game. What is the thing that almost every game has?

![6k9enr.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1655727322046/vq-wtFfY4n.jpg align="left")

Okay okay, you got points. But, in this case I was going to say characters (I am the owner of this, so lets move with my option yeah 😉?). Now the game creator says "Okay we need two characters. A hero and a villain who can move and attack."

```php
class Hero {
    function move() {
        // do exercises;
    }

    function attack() {
        // drinking power potions and attack;
    }
}

class Villain {
    function move() {
        // do exercises;
    }

    function attack() {
        // drinking power potions and attack;
    }
}
```

As you can see in here, `move()` and `attack()` functions are duplicated in both classes. Hence, we'll create another class called `Characters`. It totally makes sense because Hero and Villain both are characters. So, Hero IS-A character and Villain IS-A character segment is fulfilled.

```php
class Character {
    function move() {
        // do exercises;
    }

    function attack() {
        // drinking power potions and attack;
    }
}

class Hero extends Character {
    function move() {
        // override the move function.
    }

    function attack() {
        // override the attack function.
    }
}

class Villain extends Character {
    function move() {
        // override the move function.
    }

    function attack() {
        // override the attack function.
    }
}
```

Now it looks cool. Unfortunately, game creator had a dream of a Turret that night and he tells his idea of a Turret as a Character to the game developers. It should work with inheritance because as the creator said "Turret is a character" in the game. So, the developers have started to write the turret class.

```php
class Turret extends Character {
    function move() {
        throw new \RuntimeException("Turrets cannot move");
    }

    function attack() {
        // override the attack function.
    }
}
```

Turrets can attack. They might have multiple attack options too. But a turret cannot move. Since the turret is a character and it has already extended by the character class, the `move()` function is already inherited to the turret class regardless of its ability to move. But, as the turret cannot move, the developers added a exception inside the function. So it will not move.

As you can see, even though the relationship of 'IS-A' makes sense here, the inheritance will make the code complicated when multiple characters come in and have different functions. If you have a newer function which can be applied to most of your characters, again you need to add it to the parent class and also, override it in the classes that do not want the functionality.

> The problem with inheritance is, that it encourages you to go predict the future

I am not saying that we do not want inheritance. Inheritance is a very powerful core concept in Object Oriented Programming. But we have to make sure we use the inheritance only when it is needed and only where it is applicable. Otherwise you will end up with complex codebase that is very hard to maintain.

Before you start on inheritance, ask these questions about the code from yourself.

* Is the relationship between these two objects 'IS-A' or 'HAS-A'?
    
* Are there any methods that is useless for the subclasses (Such as `move()` to a turret class)?
    

If you get answers from yourself that you can agree with, you can go ahead with the implementation with inheritance. Otherwise it is safe to go with composition.

Hope you folks got an idea about inheritance and composition. Also, when and how to use what.

There is also a design principle called "Composition over inheritance" (I will be covering this in a different post under [Design Principles 101](https://dulitharajapaksha.hashnode.dev/series/design-principles-101)).

Okay then. Let's meet again soon.

![brooklyn-nine-nine-jake-peralta.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1655731419089/SMulJ-U1m.gif align="left")