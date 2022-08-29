## SOLID Design Principles : Single Responsibility Principle

If you read my article about adapter design pattern, at the end I said the pattern followed some solid design principles. Actually the word SOLID does not mean that the principles are solid (you have some SOLID contents in here mate). SOLID belongs to five design principles that was put together into one group. Each word represents a design principle that can be used to produce good code.

Before jumping into the SOLID principles, let's have some learning about the design principles.

![teach-me-spongebob.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1654766146323/yJrBoJYEh.gif align="left")

# What is a principle?

If you google the meaning of principle, it will show you many meanings that is either hard to understand or too lazy to read. So, I'll give you a simple meaning for a principle. Principle is *a rule of conduct based on beliefs of what is right and wrong*.

Can you add a line between right and wrong, and say "Okay this side is right and this side is wrong" ? No you can't. So, if you can't say that, how do you say we should follow principles? I DID NOT SAY THAT! There is nothing as you should follow principles, but if you do, it will be good for you and others. Okay enough on the religious lessons!

# What is a design principle?

So what I was trying to say is design principles can be considered as a guide to design. It is just a guide, there is nothing as you must do. But its better if you *could* do.

Even though the design principles and design patterns sounds so similar, they have no connection in between them, or a similarity. As I said earlier, design principles are a guide to design, while design patterns helps you to give solutions to a recurring issue. By following a design pattern, sometimes you will be able to achieve a design principle (more focus is on the word 'sometimes'. Should have bolded the word. Anyways now it is too late to bold, isn't it?).

# Why should we follow design principles?

1. Clean Code
2. Easy to maintain
3. Easily understandable

Design principles will help you to do clean code. Clean code is easily understandable which can lead to higher maintainability.

There is a famous saying "Always code as if the guy who ends up maintaining your code will be a violent psychopath who knows where you live". Unless you want to die by a hand of an angry developer, I suggest you to keep this on your mind.

There are many design principles in the universe of software engineering. Today, I am going to talk about five design principles that are heavily used and heavily asked around in the interviews and surprisingly, **most fail to answer**. 

It is called **SOLID** principles.

# What is SOLID?

SOLID is an acronym of five design principles introduced by Robert C. Martin (aka. Uncle Bob). Those are,
- **S** - Single Responsibility Principle 
- **O** - Open Closed Principle
- **L** - Liskov's Substitution Principle (that looks scary. Actually, it is not)
- **I** - Interface Segregation Principle
- **D** - Dependency Inversion Principle

I will be covering each principle of SOLID in separate articles. Otherwise this article will get forever to read. In here, I will be talking about the first principle of SOLID. Which is  *Single Responsibility Principle*.

## Single Responsibility Principle

A class should have one, and only one, reason to change. Could you elaborate more?

Just think of an ATM. What would you do with an ATM? You simply withdraw money. Then it will print a receipt for you.

```php
class Atm {

	public $deposit = 0.00;

	function __construct($deposit) {
		$this->deposit = $deposit;
	}

	public function withdrawMoney($money) {
        if ($money <= $this->deposit) {
		    $this->deposit = $this->deposit - $money;
		    $this->printReceipt();
        }
	}

	public function printReceipt() {
		if ($this->deposit <= 0) {
			echo "You are broke";
		} else {
			echo $this->deposit . " remaining. <br>";
		}
	}
}

$atm = new Atm(1000);
$atm->withdrawMoney(500);
$atm->withdrawMoney(400);
```

In above example, you can deposit some money when you make the ATM object. Then you can withdraw the amount you prefer. Perfectly working example. But, what if I told you, this breaks the Single Responsibility Principle?

> What does it mean actually?

This principle says a class should have only one purpose. ATM's purpose is to withdraw money. Printing a receipt belongs to the printer. Even though the printer is inside the ATM, it is not the ATM's job to print the receipt. How should we fix above class to adhere with the Single Responsibility Principle?

```php
class Atm {

	public $deposit = 0.00;

	function __construct($deposit) {
		$this->deposit = $deposit;
	}

	public function withdrawMoney($money) {
		if ($money <= $this->deposit) {
		    $this->deposit = $this->deposit - $money;
		    $this->printReceipt();
        }
	}

	public function printReceipt() {
		$receipt = new Receipt($this->deposit);
		$receipt->printReceipt();
	}
}

class Receipt {

	public $moneyAvailable = 0.0;

	function __construct($money) {
		$this->moneyAvailable = $money;
	}

	public function printReceipt() {
		if ($this->moneyAvailable < 0) {
			echo "You are broke";
		} else {
			echo $this->moneyAvailable . " remaining. <br>";
		}
	}
}
```
Now, if you have something to do with the receipt, you only have to edit the receipt class. You can assure whatever happens inside the Receipt class, the ATM class works independently. Because you never touch the ATM class if you do not have to. When you do not follow the Single Responsibility Principle, there is a high chance you break another functionality by making changes to a completely new functionality.

> It is not over yet!

## Advantages of Single Responsibility Principle

- Easy to understand

When a class has only one purpose, it contains very little number of functions and logics. Hence, it will be smaller in size and it will eventually make the class more understandable.

However, avoid oversimplifying your code. Some programmers take the single responsibility principle to its logical conclusion by developing classes with only one function. Subsequently, whenever they wish to create actual code, they must inject numerous dependencies, making the code unreadable and unclear.

- Easy to maintain

Changes are segregated, minimizing the possibility of disrupting other unrelated portions of the software. Because programming faults are inversely related to complexity, making the code more understandable makes it less vulnerable to defects.

- Easy to reuse

If a class has multiple responsibilities and just one of those tasks is required in another portion of the software, the other unneeded responsibilities impede reusability. Because the class only has one responsibility, it should be reusable without alteration.

That is about it for the day. Let's meet with the next article on SOLID Principles. That is **Open Closed Principle**.

Adios!

![d38767bea06936e854b7190269138851.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1659864618173/xtSvJzscH.gif align="left")
