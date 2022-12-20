# SOLID Design Principles - The Liskov Substitution Principle

Hey folks! After a long break, I am continuing my design principles series with the next SOLID principle. That is **Liskov Substitution Principle**.

The name of this principle sounds very serious. But I assure you, there is nothing to be scared of. This is a very simple principle with a scary name. The person who found this principle was Barbara Liskov. Hence the name Liskovs Principle.

# What is Liskov Substitution Principle?

The Liskov Substitution Principle (LSP) is a principle in object-oriented programming that states that objects of a subclass should be able to be used in the same way as objects of the superclass, without the user of the objects needing to be aware of the difference. This means that any subclass of a class should be able to be used in the same way as the superclass, without affecting the correctness of the program.

Okay, that does not help. I know. Let's see what it really means.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1671553085356/OItaj5Jgk.gif align="left")

It means when you have sub-classes, it should be able to use as the parent class. I know, I know. That does not help too. Let's jump into a code, so you can understand better.

In PHP, this principle can be applied by following some best practices when designing your class hierarchy. Here is an example of how you can apply the Liskov Substitution Principle in your PHP code:

Consider a simple example where we have a `Shape` class and two subclasses: `Rectangle` and `Square`.

```php
class Shape {
    public function area() {
        // code to calculate the area of the shape
    }
}

class Rectangle extends Shape {
    public $width;
    public $height;

    public function __construct($width, $height) {
        $this->width = $width;
        $this->height = $height;
    }

    public function area() {
        return $this->width * $this->height;
    }
}

class Square extends Shape {
    public $sideLength;

    public function __construct($sideLength) {
        $this->sideLength = $sideLength;
    }

    public function area() {
        return $this->sideLength * $this->sideLength;
    }
}
```

In this example, we have a `Shape` class with a single method, `area()`, which calculates the area of the shape. We also have two subclasses: `Rectangle` and `Square`. The `Rectangle` class has two properties: `width` and `height`, and the `Square` class has a single property: `sideLength`.

To apply the Liskov Substitution Principle in this example, we need to ensure that the `Rectangle` and `Square` classes can be used in the same way as the `Shape` class. This means that the `area()` method of the `Rectangle` and `Square` classes should have the same signature and return type as the `area()` method of the `Shape` class.

We can see that this is the case in the example above. The `area()` method of the `Rectangle` class takes no arguments and returns an integer, which is the same as the `area()` method of the `Shape` class. Similarly, the `area()` method of the `Square` class also takes no arguments and returns an integer.

By following this approach, we can ensure that our `Rectangle` and `Square` objects can be used in the same way as `Shape` objects, without the user of the objects needing to be aware of the difference. This makes our code more flexible and easier to maintain over time.

# **Advantages** of Liskov Substitution Principle

The Liskov Substitution Principle (LSP) is important because it helps to ensure that a program is well-structured and easy to understand and maintain. By adhering to the LSP, developers can be confident that objects of a subclass can be used in the same way as objects of the superclass, which helps to reduce the risk of introducing bugs into the program.

Additionally, the LSP can help to make it easier to extend and modify a program. For example, if a program is designed to use a superclass and its subclasses in a way that adheres to the LSP, it will be easier to add new subclasses or modify existing ones without breaking the program. This can save time and effort when developing and maintaining software and can help to reduce the overall cost of ownership of the program.

Overall, the Liskov Substitution Principle is an important principle to consider when designing and implementing object-oriented software, as it can help to ensure that the program is robust, easy to understand, and easy to maintain.

That is about it for the day. Let's meet with the next article on SOLID Principles. That is **Interface Segregation Principle**.

Have a good day!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1671553592005/z1oKt5iyk.gif align="center")