---
layout: post
title: "Duck typing"
subtitle: "Design pattern"
date: 2022-11-03
author: "Theo Cha"
tags:
  - Design pattern
  - Ruby on Rails
---

## Duck typing

> "If it walks like a duck and it quacks like a duck, then it must be a duck"

### What?

Duck typing is a type of dynamic typing, and a set of variables and methods of an object determines the type of an object. Instead of classifying types by class inheritance or interface implementation, duck typing considers an object to belong to that type if it has variables and methods that match that type. It is also a concept for implementing polymorphism.

### Why?

#### Advantages of Duck Typing

Duck typing has plenty of positives. After all, there is a reason that so many newer languages allow this functionality. The first
advantage is that it leads to fewer lines of code. This makes it look cleaner; thus making your code easier to read and faster to write.

It can be hard to see the advantages of duck typing until you have used languages with and without it. Afterward, you will realize it makes your job a lot easier when you’re not dealing with interfaces (such as in Java or C#) or keeping track of what type your object is. Assuming you don’t fall into bad practices, it really is useful functionality!

### How?

This is an example which explain how the duck typing works.

```ruby
class Idol

  def initialize(skill)
    @skill = skill
  end

  def performance
    @skill.dance
  end

end
class BlackPink

  def dance
    pp ‘Sexy dance’
  end

end
class Bts

  def dance
   pp ’Smooth dance’
  end

end
```

If we try those classes:

```console
Idol.new(BlackPink.new).performance #=> "Sexy dance"
Idol.new(Bts.new).performance  #=> "Smooth dance"
```

## References

- <https://negabaro.github.io/archive/duck-typing>
- <https://thecodebytes.com/what-is-duck-typing/>
