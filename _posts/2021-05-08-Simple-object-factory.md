---
layout: single
title: "Simple test object factory"
published: true
---


Today I want to present you very simple idea to creating entities objects for your tests. 
In a lot of PHP projects we have entities in basic style. I mean these entities has many properties and getters, 
constuctor with many requirements parameters. To creating these entities for tests purposes you can create an
instance using MockObject (from testing libraries like PHPunit), Object Mother or use simple static factory. 

For example, we have an 'classic' entity. 

```php
class User
{
    private string $name;
    private string $email;
    private bool $isActive;

    // ... and more others

    public function __construct(string $name, string $email, bool $isActive)
    {
        $this->name = $name;
        $this->email = $email;
        $this->isActive = $isActive;
    }

    public function getName(): string
    {
        return $this->name;
    }

    public function setName(string $name): self
    {
        $this->name = $name;

        return $this;
    }

    public function getEmail(): string
    {
        return $this->email;
    }

    public function setEmail(string $email): self
    {
        $this->email = $email;

        return $this;
    }

    public function isActive(): bool
    {
        return $this->isActive;
    }

    public function setIsActive(bool $isActive): self
    {
        $this->isActive = $isActive;

        return $this;
    }
}
```

Instead of creating and instance for testing in this way:
```php 
$user = new User('customName', 'customEmail', true)
// ... over and over

```

We can use and Builder pattern. The example is based on idea which I found in
**Domain-Driven Design in PHP Book by Carlos Buenosvinos, Christian Soronellas, and Keyvan Akbary**

```php
class UserBuilder
{
    private string $name;
    private string $email;
    private bool $isActive;
    // ...
    
    public function __construct()
    {
        $this->name = 'CustomName';
        $this->email  = 'CustomEmail';
        $this->isActive = true;
        // ... 
    }

    public function withAnName(string $name): self
    {
        $this->name = $name;

        return $this;
    }

    public function withAnEmail(string $email): self
    {
        $this->email = $email;

        return $this;
    }

    public function withAnIsActive(bool $isActive): self
    {
        $this->isActive = $isActive;

        return $this;
    }

    public static function anUser(): self
    {
        return new self();
    }

    public function build(): User
    {
        return new User($this->name ,$this->email, $this->isActive);
    }
}
```
__Example of usage__

```php
UserBuilder::anUser()
    ->withAnEmail('NewCustomEmail')
    ->withAnName('NewCustomName')
    ->withAnIsActive(false)
    ->create();
```

It's ok, but for me the biggest downsize is a duplication all properties from object with we want to prepare to tests. In this case, I should prefer using factories. I'm wondering about how to create
it at the simplest and easiest way to achieve it. I set the `consturctor` as `private` to prevent
try to initialize it directly by `new` keyword because it's not necessary.

Look at this one idea:

```php 
class SimpleUserFactory
{
    private User $user;

    private function __construct()
    {
        $this->user = new User(
          'CustomName',
            'CustomEmail',
            true
        );
        // and other properties
    }

    public static function create(): User
    {
        return (new self())->user();
    }

    private function user(): User
    {
        return $this->user;
    }
}
```
__Example of usage__
```php

$user = SimpleUserFactory::create();

$secondUser = SimpleUserFactory::create()
    ->setName('Marcin')
    ->setEmail('custom.marcin@email.test')
    ->setIsActive(false);
```

Maybe, we want to generate other data in each case ? Not problem. The simple example with usage
of `Faker` 

```php
use Faker\Factory;

class SimpleFakerUserFactory
{
    private User $user;

    private function __construct()
    {
        $faker = Factory::create();

        $this->user = new User(
            $faker->name,
            $faker->email,
            true
        );
    }

    public static function create(): User
    {
        return (new self())->user();
    }

    private function user(): User
    {
        return $this->user;
    }
}

SimpleFakerUserFactory::create();
```

And other issue. What with the relations between objects ? I think we can easily inject 
objects with some relations together. In example below I added some relation with other object
`Address`.

```php
// ...
private function __construct()
    {
        $faker = Factory::create();

        $this->user = new User(
            $faker->name,
            $faker->email,
            true,
            AddressSimpleFactory::create()
        );
    }
// ...    
```

Of course, we can create this in many others ways. In this post I show how we can play with testing
objects. The other thinks is creating more advanced factory to cover some object creation behind some 
interface to prevent some problems when the rules of initialization of tests object has change.

_Thanks you for reading this_ :)  
_Marcin_