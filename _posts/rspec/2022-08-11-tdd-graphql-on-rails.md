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

### Query for Returning product
----------------------------------------

We have a `product.rb` file in `app/models` that will looks as following:

```cpp
class Product < ApplicationRecord
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
- Testable: Since the logic in a controller or resolver is thinner and simpler, it becomes really easy to test. also, testing business processes becomes more isolated. it is easier to stub and check whether specific processes are invoked within our service.


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

References
----------

- <https://www.toptal.com/ruby-on-rails/rails-service-objects-tutorial>
- <https://www.honeybadger.io/blog/refactor-ruby-rails-service-object/>









