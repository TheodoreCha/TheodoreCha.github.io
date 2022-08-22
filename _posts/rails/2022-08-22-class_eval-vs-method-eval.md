---
layout: post
title: "Class eval vs Instance eval in Ruby Meta programming"
subtitle: "Ruby on Rails"
date: 2022-08-22
author: "Theo Cha"
tags:
  - Ruby on Rails
---

### Overview

Class eval and Instance eval sometimes confuse me so I want to make an article to understand them better. The confusing part was their naming. they create exactly opposite things to what they mean by the class_eval and instance_eval. here are more clear examples:
I

#### `ClassName.class_eval` = `define an instance method`

> It’s used to define an instance method on a class object which applies on all the instances of the class.

```cpp
class MyClass
end

MyClass.class_eval do
  def say_hello
   "Hello!"
  end
end

class = MyClass.new
class.say_hello # "Hello!"
```

#### `ClassName.instance_eval` = ` define a class method`

> It’s used to define a class method that is associated with a class object but it’s not visible to instance objects of that class.

```cpp
class MyClass
end

MyClass.instance_eval do
  def human?
    true
  end
end

MyClass.human? # true
```

### Why it is so confusing?

This example of a instance_eval is similar, but evaluates code in the context of an instance instead of a class. This is confusing at first because class_eval creates instance methods and instance_eval creates class methods in this example. But there is a reason behind the insaneness.

class_eval is a method of the Module class. That is, the receiver is either a module or a class. Blocks you pass to class_eval are evaluated in the context of that class. When you define a method using the standard def keyword within a class, you define an instance method, and that's where it happens.

On the other hand, instance_eval is a method of class Object, meaning that the receiver will be an object. Blocks you pass to instance_eval are evaluated in the context of that object. This means that Person.instance_eval is evaluated in the context of the Person object. Remember that a class name is simply a constant pointing to an instance of a class class. Because of this, defining a method in the context of a Class instance referenced by Person creates a class method for the Person class.

source: <https://www.jimmycuadra.com/posts/metaprogramming-ruby-class-eval-and-instance-eval/>

## References

- <https://www.jimmycuadra.com/posts/metaprogramming-ruby-class-eval-and-instance-eval/>
