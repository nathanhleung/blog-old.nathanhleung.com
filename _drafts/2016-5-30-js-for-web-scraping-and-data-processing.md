---
layout: post
title: Using Javascript for Web Scraping and Data Processing
author: Nathan Leung
comments: true
tags: [javascript]
---
While many regard Python as the best language for web scraping and data processing, it's very easy to use Javascript for the same purposes.

We'll use [request](https://github.com/request/request) to get data from sites and [cheerio](https://github.com/cheeriojs/cheerio) to parse the HTML structure of the site. We can then use the native Javascript array methods (like mapping, reducing, and filtering) to change this data into the form we need.

## Native Array Methods

```js
let arr = [1, 2, 3, 4, 5];
// forEach
// This example prints every element of the array to the console
arr.forEach((el) => {
  console.log(el);
});
// map
// This example maps every element of the array to its square
let squares = arr.map((el) => {
  return el * el;
});
// reduce
// This example finds the sum of the elements of the array
// (the second argument is our starting value - we're adding from 0, so it's 0)
let sum = arr.reduce((prev, curr) => {
  return prev + curr;
}, 0);
// filter
// This example filters out all elements less than 3
let filtered = arr.filter((el) => {
  if (el < 3) {
    return false;
  }
  return true;
});
```

These methods are especially useful when combined.

```js
// data from website
request('http://example.com/shoes-list').then((body) => {
  let json = JSON.parse(body);
  console.log(json);
  // => { "shoes": ["Nike Jordan 3 Black Cement: $460", "Adidas Yeezy Boost 350: $1250", "Nike Kobe 11 Black Mamba, $800"] }
  let shoeArr = json.map((shoe) => {
    // get position of first space, i.e. position where brand name ends
    let firstSpace = shoe.indexOf(' ');
    // last space is right after colon
    let lastSpace = shoe.indexOf(':') + 1;
    return {
      brand: shoe.substr(0, firstSpace),
      name: shoe.substr(firstSpace + 1, lastSpace - 1),
      price: parseInt(shoe.substr(lastSpace + 1)),
    }
  });
});
```

Now, our `shoeArr` looks a little bit like this.

```js
// console.log(shoeArr);
[
  {
    name: "Jordan 3 Black Cement",
    brand: "Nike",
    price: 460
  },
  {
    name: "Yeezy Boost 350",
    brand: "Adidas",
    price: 1250
  },
  {
    name: "Kobe 11 Black Mamba",
    brand: "Nike",
    price: 800
  },
];
```

If this a user was searching for something, we can now easily filter by brand or price (or both!).

```
let nikes = shoeArr.filter((shoe) => {
  if (shoe.brand === 'Nike') {
    return true;
  }
  return false;
});

let nikesUnder500 = nikes.filter((shoe) => {
  if (shoe.price < 500) {
    return true;
  }
  return false;
});
```

If this was the user's shopping cart, we can now easily get the user's total and calculate tax.

```
let subtotal = shoeArr.reduce((currTotal, currShoe) => {
  return currTotal + currShoe.price;
}, 0); // this zero sets currTotal equal to zero

let tax = subtotal * 0; // good thing we are based in Delaware
```
