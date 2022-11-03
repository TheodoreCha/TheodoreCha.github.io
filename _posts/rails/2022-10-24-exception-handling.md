---
layout: post
title: "Exception handling"
subtitle: "Ruby on Rails"
date: 2022-10-24
author: "Theo Cha"
tags:
  - Ruby on Rails
---

## Why exception handling?

> Even if software engineers write codes perfectly, there are usually unexpected cases that they can not fully cover in many edge cases. we often say never trust users. we always assume that user will run software in a very unexpected way. but we can prepare for the exception in advance

## What?

### Program error

If the program cannot cope with the situation for some reason while the program is running, it may end abnormally or an error pop-up window may appear. This situation is called a program error.

### Error classification according to the time of occurrence

![Errors](/img/post/errors.png "Errors")

#### Compile Error:

This is an error that occurs at the time of compilation. At the time the compiler compiles the source code, it checks the source code for typos, incorrect syntax, and data type. Errors that occur here are called compilation errors, and problems occurring at this point are corrected. After the compilation is completed successfully, a class file (\*.class) file is created.

#### Runtime Error:

An error that occurs at the time of program execution. The compiler can catch predictable errors at compile time, such as syntax errors and typos, at compile time, but it cannot catch potential errors that may occur during execution. Therefore, the compilation is completed without any problem and the program is executed, and an error may occur because it cannot cope with an unintended operation during execution.

#### Exception handling:

Exception handling is when software engineers write codes in advance to prevent an exceptional situation so that we prevent software for unexpected shutting down and keep the software in a healthy condition.

## How?

If you're familiar with JavaScript in a C-inspired language, you're probably familiar with the try/catch concept. Rail has something similar to handling exceptions with begin-end blocks as well. It can contain one or more rescue clauses

```ruby
begin
  # Your code
  cause_an_error()
  rescue
    puts "An error has occurred"
end
```

This is one way to write a basic ruby exception handler. it catches common errors, as rescue without any arguments will catch any StandardError and its subclasses.

### Avoid rescuing StandardError. Don’t rescue Exception.

Ruby’s Exception is the parent class to all errors. we should avoid using StandardError because it even secures errors that we suppose not to do and it leads to unexpected behavior. for example, if we secure such as memory errors, or SignalException::Interrupt, these do not belong to the application scope but rather belong to external factors.

The safest approach is to rescue the errors you are expecting and deal with the consequences of that error inside the rescue block. In the event of an unexpected error in your application, you want to know that a new error has occurred and deal with the consequences of that new error inside its own rescue block. Being specific with rescue means your code doesn’t accidentally swallow new errors. You avoid subtle hidden errors that lead to unexpected behavior for your users and bug hunting for you.

### Creating custom exceptions

This allows consumers of your code to rescue based on the error type, just as we've done in the examples so far. To create your own exceptions, it is common to make them subclasses of StandardError.

```ruby
class MyCustomError < StandardError; end
```

```ruby
begin
  raise CircuitError.new("The circuit breaker is active", "OPEN")
  rescue CircuitError => error
    puts "#{error.class}: #{error.message}. The STATE is: #{error.state}."

    # => CircuitError: The circuit breaker is active. The STATE is: OPEN.
end
```

## References

- <https://andycroll.com/ruby/rescue-specific-errors-avoid-standarderror-do-not-rescue-exception/>
- <https://dev.to/bearer/custom-exception-handling-in-ruby-4kkf>
- <https://catsbi.oopy.io/92cfa202-b357-4d47-8de2-b9b3968dfb2e>
