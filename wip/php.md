---
title: PHP
category: PHP
layout: 2017/sheet
prism_languages: [php]
---

### Hello world

#### hello.php

```php
<?php
<<<<<<< HEAD
function greetMe($name) {
  return "Hello, " . $name . "!";
=======
function greetMe($name): string
{
    return "Hello, " . $name . "!";
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024
}

$message = greetMe($name);
echo $message;
```

All PHP files start with `<?php`.

<<<<<<< HEAD
See: [PHP tags](http://php.net/manual/en/language.basic-syntax.phptags.php)
=======
See: [PHP tags](https://php.net/manual/en/language.basic-syntax.phptags.php)
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024

### Objects

```php
<?php

$fruitsArray = array(
<<<<<<< HEAD
  "apple" => 20,
  "banana" => 30
=======
    "apple" => 20,
    "banana" => 30
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024
);
echo $fruitsArray['banana'];
```

Or cast as object

```php
<?php

$fruitsObject = (object) $fruits;
echo $fruitsObject->banana;
``` 

### Inspecting objects

```php
<?php
var_dump($object)
```

Prints the contents of a variable for inspection.

<<<<<<< HEAD
See: [var_dump](http://php.net/var_dump)
=======
See: [var_dump](https://php.net/var_dump)
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024

### Classes

```php
<<<<<<< HEAD
class Person {
=======
class Person
{
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024
    public $name = '';
}

$person = new Person();
$person->name = 'bob';

echo $person->name;
```

### Getters and setters

```php
class Person 
{
<<<<<<< HEAD
    public $name = '';

    public function getName()
=======
    private $name = '';

    public function getName(): string
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024
    {
        return $this->name;
    }

<<<<<<< HEAD
    public function setName($name)
=======
    public function setName(string $name)
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024
    {
        $this->name = $name;
        return $this;
    }
}

$person = new Person();
$person->setName('bob');

echo $person->getName();
```

### isset vs empty
```php

$options = [
<<<<<<< HEAD
  'key' => 'value',
  'blank' => '',
  'nothing' => null,
=======
    'key' => 'value',
    'blank' => '',
    'nothing' => null,
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024
];

var_dump(isset($options['key']), empty($options['key'])); // true, false
var_dump(isset($options['blank']), empty($options['blank'])); // true, true
var_dump(isset($options['nothing']), empty($options['nothing'])); // false, true
<<<<<<< HEAD

=======
>>>>>>> b27d7d7f553a5d67a51f2917f4fc3c9998aac024
```
