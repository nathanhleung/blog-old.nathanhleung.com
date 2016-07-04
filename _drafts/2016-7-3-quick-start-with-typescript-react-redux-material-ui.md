---
layout: post
title: Extending the Redux Todo with Material-UI
author: Nathan Leung
comments: true
tags: [js]
---

Today we'll be using Typescript, React, Redux, and Material UI to make a todo app! It'll look like this when we're done:

![todo app screenshot](https://i.imgur.com/QF7Y3Cl.png?1)

## Redux
This guide is based off of the official [Redux](http://redux.js.org/docs/basics/index.html) todo app tutorial  

## The Stack
- [Typescript](http://www.typescriptlang.org/): Microsoft-backed superset of Javascript, adds compile-time type checking
- [React](https://facebook.github.io/react/): Facebook-backed library centered around building modular components which all get data from one single state object
- [Redux](http://redux.js.org/): A library that serves as our state object, loosely based on Facebook's [Flux](http://facebook.github.io/flux/) architecture
- [Material UI](http://www.material-ui.com/): A set of [Material Design](https://material.google.com/) elements handily written as React components

## Setup
First, create a new directory (for example, `redux-todo`) to hold all your source files. Here's what the tree should look like initially (these are all directories, by the way).

```
----redux-todo
|   ----src
|   |   ----actions
|   |   ----components
|   |   ----constants
|   |   ----containers
|   |   ----definitions
|   |   ----reducers
|   ----views
```

### Directories
We'll go into more depth later, but for now here's a quick summary of what each directory will hold.
- `actions` will hold Redux action creator functions - when one of these functions is called by a component, it'll return an action object which specifies which action took place and data pertaining to the action (e.g. "Todo was added" and the text of the todo)
- `components` will hold our React components - in our case, the buttons, the todo list, etc.
- `constants` will hold constants for action types - more on why later
- `containers` will hold our React components after we pass them state information (e.g. the todos) and their corresponding action creator functions (e.g. we pass the "Add Todo" button the `addTodo` function and bind it to `onClick`) - we separate the state and actions from the component files so we can better separate application logic and presentation
- `definitions` will hold our Typescript definitions, so we can take advantage of its type-checking features
- `reducers`, last but not least, will hold the functions which take an action object and apply that action to the state
