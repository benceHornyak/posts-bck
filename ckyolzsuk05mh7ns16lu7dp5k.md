---
title: "Javascript - pure functions"
seoTitle: "Javascript - pure functions"
seoDescription: "Let's learn how and why write pure functions in javascript"
datePublished: Fri Jan 21 2022 16:20:11 GMT+0000 (Coordinated Universal Time)
cuid: ckyolzsuk05mh7ns16lu7dp5k
slug: javascript-pure-functions
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1642781909925/Wh4piNzio.png
tags: newbie, javascript, web-development, beginners, functional-programming

---

## What is a pure function

A pure function is a function with the following rulset:

- the function return values are identical for identical parameters
- the function has zero side effects

If you think back to algebra, you've probably already saw good examples for pure functions.



```math
f(x) = 2x

```

In this case f `function` takes an argument called x and it multiplies by two. 

To "use" this we simply call our function with a provided value for `x`:

```math
f(2)
```

In algebra and in programming: this function always return identical values for identical parameters and has zero side effects. So checks all the boxes. 


## Why should you start write pure functions

 - extreme independence: easy to move around, refactor and reorganize if needed. Meaning your software can be more flexible to changes and it will easier to adapt changes in the future
- used heavily in functional programming
- easy to test - remember it will always produce the same results with the same parameters
- generally easier to maintain the codebase

## Examples

Let's first write our `f` function in javascript: 

```javascript

const f = (x) => 2*x;

```

And call it as you'd call a regular function:

```javascript

f(2); // 4

```

We have some built-in pure function as well:

```javascript

Math.max(1,3,5,8,13); // 13

```

```javascript

Math.floor( 45.95); //  45
Math.floor( 45.05); //  45
Math.floor(  4   ); //   4
Math.floor(-45.05); // -46
Math.floor(-45.95); // -46

```

Let's see an impure function and refactor into a pure function:

```javascript

const addATodo = (todos, todo) => {
  todos.push(todo);
  return todos;
}

const todosInitial = ['hello'];

const todos = addATodo(todosInitial, 'foo');

```

You might first assume, that it's okay. But js arguments are references, which means that this has a side effect: changing the outer world. Not okay. Let's fix it.


```javascript

const addATodo = (todos, todo) => {
  const clonedTodos = [...todos];
  clonedTodos.push(todo);
  return clonedTodos;
}

const todosInitial = ['hello'];

const todos = addATodo(todosInitial, 'foo');

```

Now our function doesn't interfere with the outer world, copies the original data and pushes the new todo into our copied array. 


## Closing thoughts

Hopefully I demonstrated good the benefits of using pure functions. Of course you mix and max pure and impure functions. Let me know if I missed anything important about pure functions 😊