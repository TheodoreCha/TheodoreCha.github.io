---
layout: post
title: "Why You Need Service Objects"
subtitle: "Ruby on Rails service objects"
date: 2022-08-11
author: "Theo Cha"
tags:
    - Ruby on Rails
---


> Why You Need Service Objects?

### The problems
----------------------------------------
As your application grows you will see domain/business logic scattered across models, controllers and GraphQL resolvers. many logics don't belong to controllers and resolvers or models, making it difficult to reuse and maintain code.

```cpp
class ProductController < ApplicationController
  def create
    Product.new(*args)
  end
end
```
This seems fine but as your application grows, things can easily become a mess. For example,

```cpp
class ProductController < ApplicationController
 def create
    default_args = { genre: find_category(), location: find_store_location() }
    Product.new(attrs.merge(default_args))
 end

 private

 def find_category
   // ...
 end

  def find_store_location
   // ...
 end
end
```

Service objects allow you to extract this logic into a separate class. This simplifies the code in a better way.

```cpp
class ProductController < ApplicationController
  def
    ProductCreator.create_product
  end
end

```

### Benefits of encapsulating business logic:
----------------------------------------

- Thin Rails controller and GraphQL resolver: they are mainly responsible for understanding requests and responses.
- Re-usable services
- Testable: Since the logic in a controller or resolver is thinner and simpler, it becomes really easy to test. also,  testing business processes becomes more isolated.


### Implement
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








