---
layout: post
title: "Ruby - Blocks"
subtitle: "Ruby on Rails"
date: 2023-06-13
author: "Theo Cha"
tags:
  - Ruby on Rails
---

> “Blocks”

### What are blocks?

---

Blocks are enclosed in a do / end statement or between brackets {}, and they can have multiple arguments. The argument names are defined between two pipe characters.

```ruby
block_name {
   statement1
   statement2
   ..........
}

block_name do
   statement1
   statement2
   ..........
end
```

##### `yield` keyword

---

```ruby
def test
   puts "You are in the method"
   yield
   puts "You are again back to the method"
   yield
end

test {puts "You are in the block"}
# Output
# You are in the method
# You are in the block
# You are again back to the method
# You are in the block
```

You also can pass parameters with the yield statement. Here is an example −

```ruby
def test
   yield 5
   puts "You are in the method test"
   yield 100
end
test {|i| puts "You are in the block #{i}"}

# Output:
# You are in the block 5
# You are in the method test
# You are in the block 100
```

##### Implicit vs Explicit Blocks

---

Blocks can be “explicit” or “implicit”.Explicit means that you give it a name in your parameter list. You can pass an explicit block to another method or save it into a variable to use later.

```ruby
def explicit_block(&block)
  block.call # same as yield
end
explicit_block { puts "Explicit block called" }
```

### Why we use the block?

---

Using blocks give us closure-like structures. Used properly, they can reduce repetition and even make coding less error-prone.
Let's have a look an example. A client has requested feature is the ability to take all the prices on an order and total them. and we have an array of prices from a database.

```ruby
item_prices = [3, 24, 9]
```

Now, We need a method that takes such an array, and totals the prices. As in most other languages,
we could just loop through the array items, and add them to a variable’s value as we go.

```ruby
def total(prices)
  amount = 0
  index = 0
  while index < prices.length
    amount += prices[index]
    index += 1
  end
  return amount
end

puts total(item_prices)
# Output: 36
```

After first implement, the client request a second feature that can process a refund for lost orders. It needs to loop through the invoice prices, and subtract each amount from the customer’s account balance.

```ruby
def refund(prices)
  amount = 0
  index = 0
  while index < prices.length
    amount -= prices[index]
    index += 1
  end
  amount
end

puts refund(item_prices)
# Output: -36
```

As you can see that the refund method looks highly similar to total; we’re just working with negative values instead of positive ones. also, the client keep requesting the next feature to show discounted prices. If you look at the above 2 methods, there’s some duplicated code that looping through the array of prices. It’s definitely a violation of the DRY (Don’t Repeat Yourself) principle.

How we could extract the repeated code out into another method, and have total, refund, and call it? something like the below code example.

```ruby
def do_something_with_every_item(array)

  # Set up total or refund variables here,
  # IF we need them.

  index = 0
  while index < array.length

    # Add to the total OR
    # Add to the refund

    index += 1
  end
end

```

The next question will be how will you set up the variables you need prior to running the loop? And how will you execute the code you need within the loop?

##### Use-case for the block

---

If only we could pass a chunk of code into a method, to be executed while that method runs, our problem would be solved. We could rely on `do_something_with_every_item` to handle the looping for us, and pass it chunks of code that add all the prices, or subtract them, or calculate a discount. A block is basically a chunk of code that you associate with a method call. While the method runs, it can invoke the block one or more times.

```ruby
def total(prices)
  amount = 0
  do_something_with_every_item(prices) do |price|
    amount += price
  end
  return amount
end

def refund(prices)
  amount = 0
  do_something_with_every_item(prices) do |price|
    amount -= price
  end
  return amount
end

def do_something_with_every_item(array)
  index = 0
  while index < array.length
    yield array[index]
    index += 1
  end
end
```

By using the block, we don't write the looping logic twice and reuse the `do_something_with_every_item` method.

---

- <http://radar.oreilly.com/2014/02/why-ruby-blocks-exist.html>
- <https://www.rubyguides.com/2016/02/ruby-procs-and-lambdas/>
