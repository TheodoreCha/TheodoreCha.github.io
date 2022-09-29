---
layout: post
title: "Why type assertion is bad in Typescript?"
subtitle: "Typescript"
date: 2022-09-29
author: "Theo Cha"
tags:
  - Typescript
---

## Overview

> One of TypeScript’s primary [design principles](https://github.com/microsoft/TypeScript/wiki/TypeScript-Design-Goals) is that “Statically identify constructs that are likely to be errors.”

While TypeScript provides type safety, it also gives us the ability to override the compiler’s type checker by using a language feature called type assertion.

## [Type assertions](https://www.typescriptlang.org/docs/handbook/basic-types.html#type-assertions)

Type assertion in TypeScript is the syntax and angle-bracket syntax made available by TypeScript to ‘assert’ any TypeScript identifier to a type of the implementer’s choosing.

### Examples

It weakens Type Safety
Example of type assertion using either of the two syntaxes :

```ts
export interface Human {
  name: string;
  age: number;
  occupation: string;
}
//here we are casting the object literal to Human using the `as` syntax for type assertion
const theo = {
  name: "Theo",
  age: 33,
} as Human;
//here we are casting the object literal to Human using the `ang-bracket` syntax of type assertion
const chelsea = <Human>{
  name: "Chelsea",
  age: 30,
};
```

### Exceptional use cases

Here is an example of a valid use case of the type assertion

```ts
const deserialize = <T>(data: string): T => JSON.parse(data) as T;
const jake = deserialize<Person>(
  '{"name":"Jake", "age":24, "occupation": "artist"}'
);
```

This utility method lets you deserialize a JSON string to a known Type. This can be useful when deserializing WebSocket payloads for instance.

Here we are asserting that event is of type MouseEvent which seems to be a valid use case:

```ts
element.addEventListener("click", (event) => {
  let mouseEvent = event as MouseEvent;
});
```

### Use Type Annotations Instead

Here is the best practice. we should use Type Annotations over Type Assertions. This empowers type safety and will cause compile-time errors if the declared types do not have any properties or are wrongly typed.

```ts
// bad :(
  const theo = {
    name: 'Theo',
    age: 33,
} as Human
// bad :(
const chelsea = <Human>{
    name: 'Chelsea',
    age: 30,
};
 
 
we should use the below version Instead,
// Good :)
const theo: Human = {
    name: 'Theo',
    age: 33,
    occupation: 'programmer'
};
```

This way if the object literal is missing any properties it will throw a runtime error because the typing is wrong. Sometimes we can’t avoid using type assertions. In these cases, it is considered better to use the as syntax over the<> angle-brackets syntax because the as syntax is recommended for JSX.

## References

- <https://www.typescriptlang.org/docs/handbook/basic-types.html#type-assertions>
- <https://www.bytelimes.com/why-you-should-avoid-type-assertions-in-typescript/#:~:text=It%20weakens%20Type%20Safety&text=Because%20of%20this%2C%20type%20assertions,party%20library%20is%20not%20accurate.>
