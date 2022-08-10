---
layout: post
title: "How does class methods on Rails Action Mailers work"
subtitle: "Rails Magic"
date: 2022-08-04
author: "Theo Cha"
tags:
    - Ruby on Rails
---

> “Yeah It's magic.”

When I worked with an Action Mailer, I realized that I called a class method of the Action mailer without defining any class method at the Action mailer.
It made me so confused and curious about:

- **How can I call the class method without defining them at the mailer?**

Action Mailer example
----------------------------------------

Here is a normal way to create an action mailer.

```cpp
class UserNotifierMailer < ApplicationMailer
  default :from => "any_from_address@example.com"

  def send_signup_email(user)
    @user = user
    mail( :to => @user.email,
    :subject => "Thanks for signing up for our amazing app" )
  end
end
```

Now in the controller, add a call to UserNotifierMailer.send_signup_email when a user is saved.

```cpp
class UsersController < ApplicationController
  def create
    # Create the user from params
    @user = User.new(user_params)
    if @user.save
      # Deliver the signup email
      UserNotifierMailer.send_signup_email(@user).deliver
      redirect_to(@user, :notice => 'User created')
    else
      render :action => "new"
    end
  end

  private

  def user_params
    params.require(:user).permit(:name, :email, :login)
  end
end
```

Now, Look at line 45 - `UserNotifierMailer.send_signup_email(@user).deliver`. it is a way to call a class method.
but we created an instance method `def send_signup_email`  rather than a class method with  `self` prefix.

### [Ruby: class methods vs. instance methods](https://dev.to/adamlombard/ruby-class-methods-vs-instance-methods-4aje)

This is how normal class and instance methods would work.

```cpp
class SayHello
  def self.from_the_class
    "Hello, from a class method"
  end

  def from_an_instance
    "Hello, from an instance method"
  end
end
```

```cpp
>> SayHello.from_the_class
=> "Hello, from a class method"

>> SayHello.from_an_instance
=> undefined method `from_an_instance' for SayHello:Class


>> hello = SayHello.new
>> hello.from_the_class
=> undefined method `from_the_class' for #<SayHello:0x0000557920dac930>

>> hello.from_an_instance
=> "Hello, from an instance method"
```

Then, how action methods in the `ActionMailer` work?

### [Decoding Rails Magic: How Does Calling Class Methods On Mailers Work](https://karolgalanciak.com/blog/2016/07/31/decoding-rails-magic-how-does-calling-class-methods-on-mailers-work/)

let’s dive into Rails source code. In `ActionMailer::Base` class we indeed have `method_missing` defined for class methods:

```cpp
def method_missing(method_name, *args) # :nodoc:
  if action_methods.include?(method_name.to_s)
    MessageDelivery.new(self, method_name, *args)
  else
    super
  end
end
```
Basically, any action method defined in mailer class will be intercepted by method_missing and will return an instance of MessageDelivery, otherwise it runs the default implementation.



References
----------

- <https://karolgalanciak.com/blog/2016/07/31/decoding-rails-magic-how-does-calling-class-methods-on-mailers-work/>
- <https://dev.to/adamlombard/ruby-class-methods-vs-instance-methods-4aje>













