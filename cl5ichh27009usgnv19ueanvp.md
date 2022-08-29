## Singleton Design Pattern

Today I am going to explain about the simplest design pattern in this series. This is called **Singleton Design Pattern**. Without further ado lets jump in, shall we?

Before starting, I hope you have enjoyed the previous articles in the [Design Pattern Series](https://dulitharajapaksha.hashnode.dev/series/design-patterns-101) and learned something as well.

![giphy.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1657377787749/oZFEx7OaW.gif align="left")

# The story

The general hospital of the Banana Republic has a high amount of patient files. With the current patient count, it was very hard to keep everything written on files. Specially when they needed to search something. The director board of the hospital has agreed to hire a software developer to make them a software which can easily store and access files. But, they were on a tight budget. So, they have decided to hire a low-cost developer to make their application and the developer promised to make a top-notch application for the hospital. He strictly kept his promise and delivered a application for the hospital within few days. The directors were very happy, they have paid more than they have agreed and they put the developers name of the hospitals "Hall of fame".

After three days, the users of the application felt some slowness of the application. But they did not care about that and thought it was some kind of an internet issue. However, on the fourth day, the entire hospital data storing application went down with the error "Too many connections to the database!".

The director board was furious about the incident and tried to call the developer, but his phone was unreachable. The developer has fled the country. The directors did not have any other option other than hiring a seasoned developer to do the debugging and fix the system. Within 5 minutes of the time the developer found the issue and it took only one hour to fix the entire software system.

> WHAT WAS THAT?

The developer raised his voice. "The previous developer did not make this application properly. He just made many database connections that he was not supposed to. As an example, as you see this database connection, he made a connection object each an every time when a database calls happens. He could just used the previous object for the call. So, there will be no any new connections which eventually flooded your systems connection pool. But, he did not! What I did was, I created a single connection object when the system starts, and used that same object for every call. So, there will be no flooding in the connection pool. Because whenever you want to make a request, you just use the connection you already have. It will create a new connection only when you do not have an already created connection instance."

What did he really mean? In an application there can be instances, where the same object can be used to do work. As in the above example, the database connection instance. You don't need to create something new, if you already have it. Instead of creating a new instance, you can use the same previously created instance. You need to create one, only if the previous one gets destroyed or when the previous one does not exist. It will stop creating unnecessary instances and connection that the application does not need. Otherwise the unnecessary objects will consume more resources and make the application slow or crashed.

![oh-wow-the-office.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1657638828669/ua_piILQO.gif align="left")

> Simple yet awesome!

Imagine we end up in this situation. How do we handle this?
1. First we need a class to create an object or an instance.
2. Then we need some mechanism to check whether the object does exist and if not, create a new one.

Lets see how a class diagram looks like. As I said earlier, it is fairly simple.

![Singleton Design Patterns - Simple Yet Awesome.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1657542239115/VZeFQgHSA.png align="left")

There is a secret in this implementation. You will realize it very soon.

![6e1a483e-448c-43a3-9c09-4e17980dfe4f_text (1).gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1657638962033/JHsswnxBB.gif align="left")

Lets jump into the code.

First we create the singleton class, aka. `DatabaseConnection` class.

```php
class DatabaseConnection {
    private $connection;

    // The db connection is established in the private constructor.
    private function __construct() {
        $this->connection = new PDO(); // Think of this PDO as the database connection.
    }
}
```

This class is self explanatory. It has a constructor. When it gets called, a [PDO](https://www.php.net/manual/en/book.pdo.php) object will get assigned to a private class variable (Just imagine this PDO is the database connection. I BEG YOU 😥).

Next comes the interesting part. We need a way to get the instance first. I order to do something, first you need to grab it. Let's create the `getInstance()` function.

```php
class DatabaseConnection {
    // Hold the class instance.
    private static $instance = null;
    private $connection;

    // The db connection is established in the private constructor.
    private function __construct() {
        $this->connection = new PDO();
    }

    public static function getInstance() {
        if (!self::$instance) {
            self::$instance = new DatabaseConnection();
        }

        return self::$instance;
    }
}
```

Now, we have added this static function `getInstance()`. Why static? Before explaining that, did you see the access modifier of the constructor? 

Yes, it is private! What is the purpose of a constructor? The constructor is the function that has the ability to create an object of a class. Since it is a function, it has the same behavior as functions. So, if you make it private, it will only be accessible within the class. What if you make a constructor private? You will **not be able to** create instances (i.e. objects) of the class from outside. Then my previous question. Why static? So the function can be called without creating an object of the class.

Hmm! Makes sense.

Okay, what is happening inside `getInstance()` function? It checks whether the variable `private static $instance` is null, if not, it creates an object of its own class (It is possible since the function is inside the class. If the function was in a separate class, it will not be possible since the modifier of the constructor is private), and assigns it to the variable `private static $instance`. Whenever you call the same function again, value for `self::$instance` will return true, because you have already assigned a value to the variable. With the *Not Operator (!)*, the condition will be considered as false and it will prevent creating another object. 

Finally, lets create a simple function to retrieve the value of `private $conn`.

```php
class DatabaseConnection {
    // Hold the class instance.
    private static $instance = null;
    private $connection;

    // The db connection is established in the private constructor.
    private function __construct() {
        $this->connection = new PDO();
    }

    public static function getInstance() {
        if (!self::$instance) {
            self::$instance = new DatabaseConnection(); // Think of this PDO as the database connection.
        }

        return self::$instance;
    }

    public function getConnection() {
        return $this->connection;
    }
}
```

How do we check whether this is correct?

```php
$connection1 = DatabaseConnection::getInstance();
$connection2 = DatabaseConnection::getInstance();
$connection3 = DatabaseConnection::getInstance();

if (($connection1 === $connection2) && ($connection2 === $connection3)) {
    echo "Connections are same";
} else {
    echo "Connections are different";
}
```

The output returns as **Connections are same**.

THE END!

Final codebase as follows.

```php
class DatabaseConnection {
    // Hold the class instance.
    private static $instance = null;
    private $connection;

    // The db connection is established in the private constructor.
    private function __construct() {
        $this->connection = new PDO();
    }

    public static function getInstance() {
        if (!self::$instance) {
            self::$instance = new DatabaseConnection(); // Think of this PDO as the database connection.
        }

        return self::$instance;
    }

    public function getConnection() {
        return $this->conn;
    }
}

$connection1 = DatabaseConnection::getInstance();
$connection2 = DatabaseConnection::getInstance();
$connection3 = DatabaseConnection::getInstance();

if (($connection1 === $connection2) && ($connection2 === $connection3)) {
    echo "Connections are same";
} else {
    echo "Connections are different";
}
```

Yay! We have successfully fixed the bug of the hospital application using the Singleton Pattern.

![minions-despicable-me-excited-applause.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1657639377296/BlO8bhrcm.gif align="left")

## Usage of this design pattern

Singleton pattern is used when you need to use a single instance of for all your needs. Mostly, this is used to make database connections because, database calls happen very frequently. If you create multiple connection instances for each request, the instance pool will get flooded very soon.

Another scenario is to implement a logging mechanism for an application. A single log instance will be used to all the application wide logging purposes.

Basically, singleton is used when you need to manage your resources.

That is about it. Hope you have learned when to use singleton pattern and how to use it. So, if you are a beginner and you are making the same mistake as the hospital application developer has done, it is time to change your codebase.

See you soon with another episode of Simple Yet Awesome. Until then, feel free to explore Banana Republic.

![7wD7.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1657639606973/6uCUVVfJt.gif align="left")
