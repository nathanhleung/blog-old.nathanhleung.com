---
layout: post
title: Async Actions in React/Redux with Redux-Thunk
author: Nathan Leung
comments: true
tags: [javascript]
---
Let's say we're writing a small React/Redux app and we need to get data from an API—for example, the current price of a bitcoin.

To start, we write up a generic action creator like so: 

```
function getBitcoinPrice() {
  return {
    type: 'GET_BITCOIN_PRICE',
  };
}
```

Our corresponding reducer looks like this:

```
function bitcoinPriceApp(state = 0, action) {
  switch (action.type) {
    case: 'GET_BITCOIN_PRICE':
      return // How can we get the new price?
    default:
	  return state;
  }
}
```

Since, per the [Redux docs](http://redux.js.org/docs/basics/Reducers.html), our reducer must be a pure function, always returning the same output for a given input and performing no side effects, we cannot call our API—that would be a side effect. So, how can we get our data?

## Thunks

Enter [Redux Thunk](https://github.com/gaearon/redux-thunk). Redux Thunk allows us to return functions in our action creators rather than just plain objects, which allows us to query our API in an action.

Here's what our action creator looks like with Redux Thunk. We'll have to create a new action, `RECEIVE_BITCOIN_PRICE`, to handle the completion of the request.

```
function receiveBitcoinPrice(price) {
  return {
    type: 'RECEIVE_BITCOIN_PRICE',
    price, // ES6 property value shorthand
  }
}
// This is our "thunk" - the function that wraps our
// code and allows it to be run later
// In our case, our dispatch of receiveBitcoinPrice()
// is being delayed
function getBitcoinPrice() {
  return async function(dispatch) {
    const response = await fetch('https://www.bitstamp.net/api/ticker/');
    // convert response to JSON, returns a Promise
    // so we can await it
    const json = await response.json();
    // Get last traded Bitcoin price
    const price = Number(json.last);
    dispatch(receiveBitcoinPrice(price));
  }
}
```

Notice the use of the ES7 async function notation—this is because we're using `fetch` to get our data, which returns a `Promise`. With async/await, the completion of that `Promise` can be `await`ed rather than handled in a `.then()` chain.

## Middleware

We've got our action now, but our reducer will be confused if we try to run our code, since it's expecting a plain object with a `type` key, not a function. In order to allow Redux to understand our thunk, we need to register Redux Thunk as middleware:

```
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import bitcoinPriceApp from './reducers/bitcoinPriceApp';

const store = createStore(
  bitcoinPriceApp,
  applyMiddleware(thunk)
);
```

Redux Thunk will intercept actions that are functions and handle them, while still allowing plain Object actions through. This prevents our reducers from calling foul.

There are just a few edits we need to make to our reducer before we're finished.

```
function bitcoinPriceApp(state = 0, action) {
  switch (action.type) {
    // Our thunk will call the
    // RECEIVE_BITCOIN_PRICE action
    // pass it the current BTC price
    // when the API request finishes
    case: 'RECEIVE_BITCOIN_PRICE':
      return action.price
    default:
	  return state;
  }
}
```

And that's all there is to it! We've now got a Redux action that'll asynchronously update the state, and we've kept our reducer function pure.
