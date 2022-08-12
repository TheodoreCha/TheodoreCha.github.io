---
layout: post
title: "TDD GraphQL on Rails"
subtitle: "Ruby on Rails with Rspec"
date: 2022-08-11
author: "Theo Cha"
tags:
    - Ruby on Rails
    - Rspec
    - GraphQL
---


> Let's do TDD with GraphQL + Rails + Rspec

![GraphQL is cool](/img/post/graphql-is-cool.jpeg "GraphQL is cool")

### Introduction
----------------------------------------

It has been a long time working with Rails + GraphQL technologies. so, I want to recap the Rails + GraphQL and summarize a few points by writing this public tutorial.

In this article, I will use the following technologies:

- Ruby on Rails
- GraphQL gem
- Rspec

### Getting started
----------------------------------------

**Installation**:
- Install Rails and set up GraphQL gem by following [this tutorial](https://www.howtographql.com/graphql-ruby/1-getting-started/). we assume you create a Link model, controller, view, and records.

**Focus points**
- TDD approach to create GraphQL.
- Create queries feature.
- Create mutations feature.

### Implementation
----------------------------------------

First let’s create a `new ProductCreator` And then just dump all our logic inside a new Ruby class:

```cpp
class ProductCreator
  def initialize(name:)
    @name = name
  end

  def create_product
    Product.create!(
      title: @name
    )
    rescue ActiveRecord::RecordNotUnique => e
     # handle duplicate entry
    end
  end
end

```

and then, we can call the service object within the application:

```cpp
class ProductController < ApplicationController
  def create
    ProductCreator.new(name: params[:name]).create_product
  end
end
```

### Service Object Syntactic Sugar
----------------------------------------

We can make `the ProductCreator.new(arguments).create` chain simpler by adding a class method that instantiates the ProductCreator and calls the create method for us:

```cpp
class ProductCreator
  def self.call(...)
    new(...).call
  end

  def initialize(name:)
    @name = name
  end

  def create_product
    Product.create!(
      name: @name
    )
    rescue ActiveRecord::RecordNotUnique => e
     # handle duplicate entry
    end
  end
end
```

and then, we can call the service object within the application in a simpler way:

```cpp
class ProductController < ApplicationController
  def create
    ProductCreator.call(
      name: params[:name]
    ).create_product
  end
end
```

References
----------

- <https://www.toptal.com/ruby-on-rails/rails-service-objects-tutorial>
- <https://www.honeybadger.io/blog/refactor-ruby-rails-service-object/>









