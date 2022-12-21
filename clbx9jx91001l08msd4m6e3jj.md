# SOLID Design Principles - Interface Segregation Principle

So, today we are going to talk about the fourth principle in SOLID design principles. It is called **Interface Segregation Principle**.

First of all, let's see what is an Interface.

# What is an Interface?

The interface is a blueprint of a class which has all the elements that a class has to implement. Methods or functions inside an interface are called abstract functions. Those have just the function name, and not a body or functionality inside. It is just to say like "Hey! If you are implementing this interface in your class, you should override all the functions of this interface".

Let's look at the below example.

```php
interface AnimalInterface
{
    public function move();
    public function eat();
}

class Animal implements AnimalInterface
{

    /*
        Overriding move()
    */
    public function move()
    {
        // functionalities related to move should be here.
    }

    /*
        Overriding eat()
    */
    public function eat()
    {
        // functionalities related to eat should be here
    }
}
```

As you can see in the above code interfaces are binding to a class through the keyword `implements`. Unlike `extends` (inheritance), multiple interfaces can be `implements` in a class.

# What does it mean by segregation?

So, what does segregation means? Google says "the action or state of setting someone or something apart from others". It is true you know, that is exactly what we are trying to do here.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1671602797029/pRLJ6HbjT.gif align="left")

# What is Interface Segregation?

The Interface Segregation Concept (ISP) is an object-oriented design SOLID principle that argues that clients should not be compelled to rely on interfaces they do not utilize. In other words, rather than having a huge and complex interface with numerous methods that may or may not be useful to the client, interfaces should be built such that customers only have access to the methods they require.

# How can we break the Interface Segregation Principle? (Of course unintentionally)

Let's see what breaks the interface segregation principle. Imagine you are creating a game with three characters. A hero, a tank and a turret (Yes I know tanks and turrets are not characters. But I am creating the game. So be it).

First, let's make an interface for these characters. This contains the functions these characters can do.

```php
/**
 * Character Interface
 */
interface Character
{
    /*
    Character moves
    */
    public function move();

    /*
    Character shoots
    */
    public function shoot();

    /*
    Charcter speaks
    */
    public function speak();
}
```

Now let's implement the classes to use the interface.

```php
/**
 * Hero character
 */
class Hero implements Character
{
    /*
    @override Character moves
    */
    public function move()
    {
        echo "Move";
    }

    /*
    @override Character shoots
    */
    public function shoot()
    {
        echo "Shoot";
    }

    /*
    @override Charcter speaks
    */
    public function speak()
    {
        echo "Speak";
    }
}

/**
 * Tank character
 */
class Tank implements Character
{
    /*
    @override Character moves
    */
    public function move()
    {
        echo "Move";
    }

    /*
    @override Character shoots
    */
    public function shoot()
    {
        echo "Shoot";
    }

    /*
    @override Charcter speaks
    */
    public function speak()
    {
        throw new Exception("Cannot Speak");
    }
}

/**
 * Turret character
 */
class Turret implements Character
{
    /*
    @override Character moves
    */
    public function move()
    {
        throw new Exception("Cannot Move");
    }

    /*
    @override Character shoots
    */
    public function shoot()
    {
        echo "Shoot";
    }

    /*
    @override Charcter speaks
    */
    public function speak()
    {
        throw new Exception("Cannot Speak");
    }
}
```

Now, here I have created an Interface with 3 abstract functions for `move`, `shoot` and `speak`. A hero can do all these three, a tank can only move and shoot but cannot speak, and a turret can only shoot. This code works perfectly without any issues. But, **how does it break Interface Segregation Principle?**

As you can see in the code, there are lots of exceptions inside a class. Having exceptions inside a class is totally fine. But here, as you can see, there are many unwanted functions for some characters.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1671603062518/o_sxMuvQj.gif align="left")

# How can we make a class and an interface adhere to Interface Segregation Principle?

Now let's see how we can adhere to the principle with the same code.

First, what I would do is, instead of creating a single interface for all three functions, I will create three separate interfaces.

```php
/**
 * CharacterMoves Interface
 */
interface CharacterMoves
{
    public function move();
}

/**
 * CharacterShoots Interface
 */
interface CharacterShoots
{
    public function shoot();
}

/**
 * CharacterSpeaks Interface
 */
interface CharacterSpeaks
{
    public function speak();
}
```

Then I will `implements` only the needed interfaces in the classes.

```php
/**
 * Hero character
 */
class Hero implements CharacterMoves, CharacterShoots, CharacterSpeaks
{
    /*
    @override Character moves
    */
    public function move()
    {
        echo "Move";
    }

    /*
    @override Character shoots
    */
    public function shoot()
    {
        echo "Shoot";
    }

    /*
    @override Charcter speaks
    */
    public function speak()
    {
        echo "Speak";
    }
}

/**
 * Tank character
 */
class Tank implements CharacterMoves, CharacterShoots
{
    /*
    @override Character moves
    */
    public function move()
    {
        echo "Move";
    }

    /*
    @override Character shoots
    */
    public function shoot()
    {
        echo "Shoot";
    }
}

/**
 * Turret character
 */
class Turret implements CharacterShoots
{
    /*
    @override Character shoots
    */
    public function shoot()
    {
        echo "Shoot";
    }
}
```

As you can see here, there is no need to add the exceptions. Because the class only contains the functions that it is supposed to use. Turrets cannot move nor speak, so it does not have a need to use those functions. So, we kept the interfaces apart from each other so we do not need to use unwanted functions inside a class.

This is the base of the *Interface Segregation Principle*. Keeping interfaces simple and apart from each other.

# Advantages of Interface Segregation Principle

1.  Improved flexibility: By building smaller, more focused interfaces, you can update or extend the interfaces more readily without hurting customers that rely on them. This makes it easy to make changes to your codebase without affecting current functionality.
    
2.  Better cohesion: Interfaces created with the ISP in mind are more coherent since they only offer methods relevant to a certain client. This results in code that is easier to maintain and comprehend.
    
3.  Improved readability: It might be difficult to comprehend the relationships between different components when dealing with a huge codebase. Following the ISP can help you understand how various components communicate with one another because each interface only provides methods that are relevant to a given client.
    
4.  Reduced complexity: You may minimize the complexity of your codebase and make it easier to understand and maintain by building interfaces that are suited to individual clients.
    

Overall, following the **Interface Segregation Principle** can help you design more flexible, maintainable, and understandable code in PHP.

It is for the day then. Hope you enjoyed and learned something from this article. Keep in touch with the blog to see the next article on the final SOLID principle. That is **Dependency Inversion Principle**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1671603135380/0bAs9Tv6T.gif align="left")