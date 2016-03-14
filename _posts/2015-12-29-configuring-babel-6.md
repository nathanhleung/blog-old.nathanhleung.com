---
layout: post
title: Configuring Babel 6.0
author: Nathan Leung
comments: true
tags: [nodejs,javascript]
---
Today we'll be configuring [Babel](https://babeljs.io/) 6.0, a Javascript transpiler that allows you to write ES6 (and beyond) code.  Babel transpiles files into vanilla ES5 which can be used in any major browser (or Node.js).  Here's how to get started.

## Preset
First, you need to install a preset.  Previously, Babel came with a few transpilers (e.g. [React](https://babeljs.io/docs/plugins/preset-react/), [ES2015](https://babeljs.io/docs/plugins/preset-es2015/)), but with the release of [Babel 6.0](https://babeljs.io/blog/2015/10/29/6.0.0/), the API was changed such that the developer now has to specify which transpilers should be used.

Let's install the [stage-0](https://babeljs.io/docs/plugins/preset-stage-0/) preset, which has the most bleeding-edge set of 
features - decorators, async functions, exponentiation, and more.

To install, run `npm install babel-preset-stage-0 --save-dev`.  Once the package is done installing, we need to create a `.babelrc` file to tell Babel which presets we want to use.  Create that file, and put the following contents inside:

```js
// .babelrc
{
  "presets":["stage-0"]
}
```

Now, Babel is ready for use.

## CLI
To use the Babel CLI, install it as a `devDependency` via `npm install babel-cli --save-dev`.  It's recommended for the CLI to be installed locally to allow for more portability.

If we had installed Babel globally, we'd be able to run `babel myscript.js`. However, since it's installed locally we need to invoke
it via an npm script.  In your `package.json`, add the following lines:

```js
// package.json
},
"scripts": {
  "babel": "babel src --out-dir lib",
  "babel:w": "babel -w src --out-dir lib"
},
"devDependencies": {
  ...
```

Now, from the terminal you can run `npm run babel` or `npm run babel:w` (if you'd like to watch for changes) and Babel will transpile our files.  In this case, Babel will transpile files in `src` and output them to `lib`.

## Require Hook
To use the require hook, run `npm install babel-register --save-dev`.  If you put `require('babel-register')` at the top of a file, all subsequently required files will be transpiled by Babel.

```js
// app.js
require('babel-register');
require('./app.es6');
```

```js
// app.es6
import express from 'express';
let app = express();
let port = process.env.PORT || 3000;

app.get('*', (req, res) => {
  res.send('Babel is the best!');
});

app.listen(port, () => {
  console.log(`Server listening on ${port ** 0.5} squared!`);
});
```

```
$ node app
=> Server listening on port 54.772255750516614 squared!
```

## Gulp

### ES6 Gulpfile
To write your Gulpfile in ES6, name your gulpfile `gulpfile.babel.js`.  When you run `gulp`, Babel will automatically be used to
compile your gulpfile.

### Transpile Files
To use Gulp to transpile ES6, first install the plugin: `npm install gulp-babel --save-dev`.  Here's an example of an ES6 gulpfile
that uses `gulp-babel`:

```js
// gulpfile.babel.js
import gulp from 'gulp';
import babel from 'gulp-babel';

gulp.task('default', () => {
  return gulp.src('src/app.js')
    .pipe(babel())
    .pipe(gulp.dest('lib'));
});
```

## Jade (with Express)
To use the `:babel` filter in Jade with Express, run `npm install jade-babel --save`.  In your `app.es6`, add this:

```js
// app.es6
import jade from 'jade';
import babel from 'jade-babel';
...
jade.filters.babel = babel();
app.set('view engine', 'jade');
```
Now, when you call `res.render`, the code in the `:babel` filter in your Jade templates will be transpiled.

```jade
doctype html
html
  head
    title Jade Example
    meta(charset='utf-8')
    meta(name='viewport' content='width=device-width,initial-scale=1.0')
  body
    p#text
    script(src='https://ajax.googleapis.com/ajax/libs/jquery/2.1.4/jquery.min.js')
    script
      :babel
        $(document).ready(() => {
          let text = `Babel is cooler than ${10 ** 6} ice cubes!`;
          $('#text').html(text);
        });
```

## Conclusion
ES6 is fast approaching, and using Babel is a great way to future-proof your apps.  Thanks for reading!
