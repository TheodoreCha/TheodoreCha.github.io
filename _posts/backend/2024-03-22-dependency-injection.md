---
layout: post
title: "Dependency Injection"
subtitle: "Nestjs"
date: 2024-03-22
author: "Theo Cha"
tags:
  - Backend
  - NestJS
---

> “Dependency injection
> In software engineering, dependency injection is a programming technique in which an object or function receives other objects or functions that it requires, as opposed to creating them internally. - Wikipedia-

## What is dependency injection?

Dependency injection is a common software design pattern that allows create dependent objects outside of a class, in nutshell, instead of creating objects of dependencies, these objects are created elsewhere and given to its as needed.

### Example dependency in a class

In the below example, we will instantiate the `PostsService` inside the `PostsController`. it introduces a tight dependency between `PostsController` and `PostsService`.

- Problems:
  - Changing Dependencies: If you decide to use a different service or a mock for testing, you need to modify the controller's code to instantiate the new service instead.
  - Testing: Isolating the controller for unit tests is challenging because it creates its own service instance. You would need to intercept the instantiation logic or refactor the controller to allow injecting mock services.

As the below example, what if we need to introduce post for another language? Should we keep adding conditions?

```ts
export class PostsController {
  private postsService = new PostsService();
  private country: string;

  getPost() {
    if (country === "sweden") {
      postsService.getSwedishPost();
    } else {
      postsService.getNonSwedishPost();
    }
  }
  // ...use postsService methods
}
```

---

### How to work with dependency injection?

Let's have a look how the DI works in the NestJS

- Step 1: Create a Service

```ts
import { Injectable } from "@nestjs/common";

@Injectable()
export class PostsService {
  getNonSwedishPost() {
    return "Non Swedish posts";
  }

  getSwedishPost(title: string) {
    return "Swedish posts";
  }
}
```

- Step 2: Create the Posts Controller

```ts
import { Controller, Get, Post, Body } from "@nestjs/common";
import { PostsService } from "./posts.service";

@Controller("posts")
export class PostsController {
  constructor(private postsService: PostsService) {}

  @Get("/non-swedish")
  getNonSwedishPost() {
    return this.postsService.getNonSwedishPost();
  }

  @Get("/swedish")
  getSwedishPost(@Body() body: { title: string }) {
    return this.postsService.getSwedishPost(body.title);
  }
}
```

- Step 3: Register Service and Controller in a Module

```ts
import { Module } from "@nestjs/common";
import { PostsService } from "./posts.service";
import { PostsController } from "./posts.controller";

@Module({
  controllers: [PostsController],
  providers: [PostsService],
})
export class PostsModule {}
```

Step 4: Use the Module in the Application

```ts
import { Module } from "@nestjs/common";
import { PostsModule } from "./posts/posts.module";

@Module({
  imports: [PostsModule],
})
export class AppModule {}
```

In this example, MyService is injected into MyController through the controller's constructor. NestJS automatically resolves MyService and provides an instance to MyController when it's instantiated.

- decoupling: PostsController doesn't know about the instantiation of PostsService. It only knows that it needs a PostsService to function. The NestJS framework takes care of creating an instance of PostsService and injecting it into PostsController.
- Testing: When writing tests for PostsController, you can easily provide a mock version of PostsService to test the controller's behavior independently of the actual service logic.

## Dependency Injection in Ruby on Rails

### Implementing DI in Ruby

Consider an Author class that requires a RevenueCalculator to compute its monthly revenue. Initially, the dependency is hard-coded, making the class tightly coupled and less flexible.

Before DI:

```ruby
class Author
  def monthly_revenue
    RevenueCalculator.calculate_for(self, month_to_date)
  end
end
```

To implement DI, we refactor Author to accept a RevenueCalculator instance upon initialization, allowing any compatible calculator to be used.

After DI:

```ruby
class Author
  def initialize(revenue_calculator: RevenueCalculator)
    @revenue_calculator = revenue_calculator
  end

  def monthly_revenue
    @revenue_calculator.calculate_for(self, month_to_date)
  end
end
```

#### Practical Example

```ruby
RSpec.describe Author do
  let(:mock_calculator) { double("RevenueCalculator") }
  let(:author) { Author.new(revenue_calculator: mock_calculator) }

  it 'calculates monthly revenue' do
    allow(mock_calculator).to receive(:calculate_for).and_return(1000)
    expect(author.monthly_revenue).to eq(1000)
  end
end
```

If you want to introduce another calculator, you could easily inject:

```ruby
Author.create(revenue_calculator: GermanRevenueCalculator)
```

---

## References

- <https://remimercier.com/dependency-injection-in-ruby/>
