# 5 PHP Interview Questions That Will Hit You Hard

Today I am going to talk about kind of a different topic. So, you have faced at least one interview, right? How did it go? As per your experience, was it easy or hard?

In this article, I am going to talk about some PHP Interview Questions that will hit you hard until you fell. If you are going to face an interview for a senior or above position, you should have to have an understanding of the below questions.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672044515011/bf8c7df0-b907-4509-ae69-f850cd98a037.gif align="left")

> Ready to get hit? Let's go!

# What is Late Static Binding?

Late static binding in PHP refers to the ability to reference a class using the `static` keyword. When you use `static` to refer to something like a class, PHP will utilize the class that was called at *runtime* rather than the class that was called at *compile* time. This allows you to write methods in a base class that may be overridden by derived classes and have the right method called depending on the situation.

In PHP, here's an example of late static binding:

Imagine you have a class called `Animal`, which has a method called `makeNoise()`. This method simply outputs the sound that the animal makes:

```php
class Animal {
    public static function makeNoise() {
        echo "Grrrr...";
    }
}
```

Now, let's say you want to create a subclass of `Animal` called `Dog`, which should override the `makeNoise()` method to output a different sound. You can do this using late static binding:

```php
class Dog extends Animal {
    public static function makeNoise() {
        echo "Woof woof!";
    }
}
```

Now, whenever you call `Dog::makeNoise()`, it will output "Woof woof!", because the `makeNoise()` method of the `Dog` class is called at runtime.

But let's say you also have a class called `Cat`, which should make a different noise when it's `makeNoise()` method is called:

```php
class Cat extends Animal {
    public static function makeNoise() {
        echo "Meow!";
    }
}
```

If you call `Cat::makeNoise()`, it will output "Meow!", because the `makeNoise()` method of the `Cat` class is called at runtime.

So, in this example, the late static binding allows different animals to make different noises, depending on which class was called at runtime.

Reasons to use Late Static Binding (LSB).

* *Overriding static methods*: LSB allows you to override static methods in a subclass while still utilizing the `parent::` syntax to call the parent method. This is handy if you have a parent class with a static method that should be overridden in a child class, but you want to make sure that the child class method gets called even if the parent method is accessed via a static context.
    
* *Polymorphism*: With LSB, you may write polymorphic static methods that behave differently based on the class that is called at runtime. This is handy if your base class has a static function that should be implemented differently in each subclass.
    
* *Dynamic context*: In a static context, you can use the `static::` syntax to refer to the called class rather than the class where the method is declared. This is handy if you wish to utilize a static method in a dynamic situation where the class being called is unknown until runtime.
    
* *Code simplification*: LSB may help you simplify your code by avoiding the need for conditional statements to establish the context of a static method. Instead, you may use the `static::` syntax to automatically reference the called class.
    

> You already knew it! I know. But were you able to explain it?

# What is a ReflectionClass in PHP?

This is where you get the headshot immediately. Did you hear about this class before? Did you know that this class was there since PHP 5?

In PHP, the `ReflectionClass` class is a part of the Reflection API, which allows you to introspect classes, interfaces, and traits in your PHP code.

Using the `ReflectionClass` class, you can get information about a class, such as its name, the names of its methods and properties, and its parent class. You can also use it to instantiate an object of a class, or to check if a class is abstract or final.

```php
class TheNewGameThatIamBuilding {
    public $ammo;
    protected $health;
    private $lives;

    public function shootOnTheSpot() {}
    protected function runWhenSeeAnEnemy() {}
    private function getPumpedUp() {}
}

$reflector = new ReflectionClass('TheNewGameThatIamBuilding');

// Get the class name
echo $reflector->getName() . "\n";

// Check if the class is abstract
echo $reflector->isAbstract() . "\n";

// Get a list of properties
$properties = $reflector->getProperties();
foreach ($properties as $property) {
    echo $property->getName() . "\n";
}

// Get a list of methods
$methods = $reflector->getMethods();
foreach ($methods as $method) {
    echo $method->getName() . "\n";
}

// Create new instance
$instance = $reflection->newInstance();
```

In this example, the `ReflectionClass` object is created for the `TheNewGameThatIamBuilding` class, and then various details about the class are extracted using various methods of the `ReflectionClass` class.

The `ReflectionClass` class is useful for creating class introspection tools, for debugging, or for creating generic code that can work with any class.

There are many more functions in this `ReflectionClass`, you can check from [php.net](https://www.php.net/manual/en/class.reflectionclass.php)

# What is the difference between empty, isset and array\_key\_exists?

## empty

As the name suggests, `empty` checks whether the variable is empty. The below values are acceptable in PHP as empty.

* NULL
    
* FALSE
    
* array()
    
* 0.0
    
* "0"
    
* 0
    
* ""
    

## isset

`isset` can be used when you want to determine if a *key* exists and is associated with a *value*. When the array key exists but the value is `null`, `isset` will return `false` .

## array\_key\_exists

array\_key\_exists will tell you when a certain key exists inside an array. When the array key exists but the value is `null`, `array_key_exists` will return `true`.

> When comparing the performance, `isset` is faster than `array_key_exists` because it is actually a language construct, not a function. But the performance is negligible unless you are doing some Jimmy Neutron stuff.

# What are variables passing as values and passing as references?

In PHP, when you pass a value (such as a scalar value or an object) to a function or method as an argument, it is passed by value by default. This means that a copy of the value is passed to the function, and any changes made to the value inside the function have no effect on the original value outside the function.

Here is an example of passing a value by value in PHP:

```php
function incrementValue($value) {
    $value++;
}

$x = 10;
incrementValue($x);
echo $x; // Outputs: 10
```

The value of `$x` is supplied as an argument to the `incrementValue()` method in this example. The value of $value is increased by one throughout the function. This modification, however, has no effect on the original value of `$x` since `$value` is a copy of `$x` and the original value of `$x` remains unchanged.

If you wish to provide a value to a function via reference, however, you may use the & operator in the function specification. This instructs PHP to send a reference to the original value rather than a copy of the value to the function. Any modifications made to the value within the function will be mirrored in the original value outside the function.

```php
function incrementValue(&$value) {
    $value++;
}

$x = 10;
incrementValue($x);
echo $x; // Outputs: 11
```

In this example, the `&` operator is used to send the value of `$x` as an argument to the `incrementValue()` method. The value of `$value` is increased by one throughout the function. Because `$value` is a reference to the original value of `$x`, when the original value is updated, this change is reflected in the original value of `$x`.

# What is composer, difference between composer install and composer update, and what are the other functionalities of composer

If you are a PHP developer, you must know about the functionality of composer. Let's answer the questions one by one.

## What is composer?

Composer is a PHP dependency management tool. It allows you to define the libraries on which your project depends and manages (installs/updates) them for you.

Composer is not a package manager like `YUM` , `APT` or `BREW` . The composer handles packages on a project basis. When you install dependencies through composer, it will download all the packages to a `/vendor` folder inside the project root. However, it does support global functionalities if you install it as global.

## Difference between composer install and update

Before that, have you seen there are two files in the project called `composer.json` and `composer.lock`?

Basically, the composer.json file includes the project dependencies that is needed for the project development. Unless you remove something from composer.json it will always get installed on the development project whenever you run `composer update.`

When installing your project for the first time or upgrading, composer.lock is produced. It includes references to the particular versions that were utilized. It should be committed to the version tracking repository so that this precise mix of libraries may be restored.

Okay, to the question about **composer install** and **composer** **update**.

Imagine you are doing the developments on a local or a development server. You have some project dependencies that need to be updated. Then you run `composer update` . So all the dependencies will get updated according to the composer.json file and will save the updated dependency details in the composer.lock file. Once you run `composer update` it will,

* Read `composer.json`
    
* Remove installed packages that are no more required in `composer.json`
    
* Check the availability of the latest versions of your required packages
    
* Install the latest versions of your packages
    
* Update `composer.lock` to store the installed packages version
    

Imagine you are setting up the project for the first time in your local environment or the production server. Then you need to run `composer install` . Once you run `composer install`, it will do below things.

* Check if `composer.lock` file exists (if not, it will run `composer update` and create it)
    
* Read `composer.lock` file
    
* Install the packages specified in the `composer.lock` file
    

> As an example, if you download a project such as Laravel, the first you should do is run `composer install`. Then it will automatically install the dependencies according to the lock file that was in the repository. After a while, you realize that you need another package to be installed. What you should do is, add that package to the composer.json file and run the `composer update`. It will re-read the json file and install/ upgrade your packages as defined in the json, then write-out the utilized versions on the lock file.
> 
> When you push your changes to your remote, make sure to push your lock file. Because, if not, the next person who downloads your changes will not have your updated lock file. Hence, they will miss the newly added dependency.
> 
> AND, it is a bad practice to push your `/vendor` directory on to the remote. Always use `composer install` to install your dependencies on your new environment.

## What are other functionalities of composer?

* The composer may produce an autoloader for your project, allowing you to autoload PHP classes from your project and its dependencies without having to require each class file explicitly.
    
* *Scripts*: In your composer.json file, you may create custom scripts that can be performed at various stages during the Composer workflow (e.g., before or after installing or updating dependencies). This can be handy for operations like running tests, creating assets, or doing database migrations.
    
* *Version* *restrictions*: You may establish version constraints for your dependencies in Composer, which helps guarantee that your project only downloads and utilizes compatible versions of those dependencies.
    
* *Virtual environments*: The create-project command in Composer allows you to build a new project based on a package or template and install all of its dependencies in a single command. This might be beneficial for quickly generating development environments or launching new projects.
    
* Composer includes a plugin system that allows developers to expand their capabilities by creating new plugins. There are plugins available for connecting Composer with version control systems, delivering projects to servers, and producing documentation, among other things.
    

If you know all these answers for the questions, you are a well seasoned developer. But, if you do not know for some questions, do not be afraid. That is why there is this technique is placed called **Learning**, and you will be able to learn these in no-time. No one knows everything. Only this that matters is your willing-to-learn attitude.

See you soon with another topic!

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1672045160753/9fa0c37f-82e6-4d4b-a713-121bab90d836.gif align="center")