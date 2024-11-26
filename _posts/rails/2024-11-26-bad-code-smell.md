---
layout: post
title: "Refactoring Rigid Code Structures in Ruby"
subtitle: "Improving Maintainability and Extensibility"
date: 2024-11-26
author: "Theo Cha"
tags:
  - Ruby
---

> "Writing maintainable code is a journey, not a destination."

When working with Ruby applications, you may encounter rigid code structures that make your application harder to maintain and extend. In this post, I’ll walk you through common scenarios where refactoring can improve your code's readability, testability, and extensibility. We’ll discuss:

- **Why rigid code structures are problematic**
- **How to refactor them using object-oriented principles**
- **Specific examples with detailed explanations**

---

## **1. Repeated Conditionals or Hardcoded Values**

### **Problem Code:**

```ruby
def price(product)
  if product == "apple"
    1.0
  elsif product == "banana"
    0.5
  else
    2.0
  end
end
```

### **Why it’s a problem:**

- **Hardcoded values** ("apple", "banana") make the method brittle.
- Adding a new product requires modifying the method, violating the **Open/Closed Principle (OCP)**.
- Any typo in hardcoded strings could break the functionality.

---

### **Solution Using Polymorphism:**

```ruby
class Product
  def price
    raise NotImplementedError
  end
end

class Apple < Product
  def price
    1.0
  end
end

class Banana < Product
  def price
    0.5
  end
end

class DefaultProduct < Product
  def price
    2.0
  end
end

# Usage
def product_price(product)
  product.price
end

apple = Apple.new
puts product_price(apple) # Output: 1.0
```

### **Why this is a good solution:**

- **Extensibility:** Adding a new product requires creating a new class, leaving existing code untouched.
- **Encapsulation:** Each product's pricing logic is isolated, making it easier to maintain and test.
- **OCP Adherence:** Existing code is closed for modification but open for extension.

---

### **Solution Using a Lookup Table:**

```ruby
PRODUCT_PRICES = {
  "apple" => 1.0,
  "banana" => 0.5
}

def price(product)
  PRODUCT_PRICES.fetch(product, 2.0)
end

puts price("apple") # Output: 1.0
```

### **Why this is a good solution:**

- **Simplicity:** Easy to read and update the mapping.
- **Flexibility:** Adding new products is as simple as updating the hash.
- **Consistency:** Centralized product information reduces typos and errors.

---

## **2. Nested Conditionals**

### **Problem Code:**

```ruby
def shipping_cost(location, weight)
  if location == "domestic"
    if weight < 5
      5.0
    else
      10.0
    end
  elsif location == "international"
    if weight < 5
      20.0
    else
      50.0
    end
  else
    "Unknown location"
  end
end
```

### **Why it’s a problem:**

- **Deep nesting:** Makes the code harder to read and maintain.
- **High cognitive load:** Developers must mentally parse multiple layers of conditionals to understand the logic.
- **Duplication:** Similar logic (weight comparisons) is repeated across locations.

---

### **Solution Using Strategy Pattern:**

```ruby
class DomesticShipping
  def cost(weight)
    weight < 5 ? 5.0 : 10.0
  end
end

class InternationalShipping
  def cost(weight)
    weight < 5 ? 20.0 : 50.0
  end
end

class UnknownShipping
  def cost(_weight)
    "Unknown location"
  end
end

def shipping_cost(strategy, weight)
  strategy.cost(weight)
end

domestic = DomesticShipping.new
puts shipping_cost(domestic, 3) # Output: 5.0
```

### **Why this is a good solution:**

- **Readability:** Eliminates deep nesting, making the logic straightforward.
- **Reusability:** Individual shipping strategies can be reused across different parts of the application.
- **Extensibility:** Adding new strategies like `FreeShipping` requires no changes to existing code.

---

## **3. Violation of the Single Responsibility Principle (SRP)**

### **Problem Code:**

```ruby
def process_order(order)
  if order.status == "new"
    "Processing order..."
    log_order(order)
  elsif order.status == "shipped"
    "Order already shipped."
  else
    "Invalid order status."
  end
end
```

### **Why it’s a problem:**

- **Mixed responsibilities:** The method handles both decision-making (`order.status`) and side effects (`log_order`).
- **Hard to test:** Testing this method requires mocking both conditions and the side effect.
- **Violates SRP:** Makes the method less modular.

---

### **Solution Using Separate Responsibility Classes:**

```ruby
class NewOrderProcessor
  def process(order)
    log_order(order)
    "Processing order..."
  end

  private

  def log_order(order)
    puts "Order logged: #{order.id}"
  end
end

class ShippedOrderProcessor
  def process(_order)
    "Order already shipped."
  end
end

class InvalidOrderProcessor
  def process(_order)
    "Invalid order status."
  end
end

def process_order(order)
  processor = case order.status
              when "new" then NewOrderProcessor.new
              when "shipped" then ShippedOrderProcessor.new
              else InvalidOrderProcessor.new
              end
  processor.process(order)
end
```

### **Why this is a good solution:**

- **Separation of concerns:** Each class handles one specific responsibility.
- **Testability:** Each processor can be independently tested without side effects.
- **Flexibility:** Adding new statuses, like "cancelled," only requires creating a new processor.

---

### **References**

- [Object-Oriented Programming Principles](https://www.codingdojo.com/blog/oop-principles)
- [Refactoring Patterns](https://refactoring.guru/refactoring)
