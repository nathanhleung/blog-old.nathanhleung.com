---
layout: post
title: Getting Started with the MEAN Stack
author: Nathan Leung
comments: true
tags: [nodejs]
---
The MEAN Stack is made of MongoDB, Express.js, AngularJS and Node.js.  MongoDB is a database, Express.js provides routing support (i.e. mapping URLs to the correct destination, a simple use case would be to make a REST API), AngularJS allows for the client to communicate with the Express.js-based API, and Node.js is the engine that allows us to run Javascript code on the server (in this case, it's the backbone for Express).

This getting started guide applies to the current latest stable versions of Angular (1.4.x), Express (4.x), and Node (4.x). As usual, we'll be making a very simple todos app

## Other Technologies
We'll also be using Jade, a very popular templating language for Node.js. It's pretty much a terser version of HTML:

```jade
doctype html
html
  head
    title My First Jade
    meta(charset='utf-8')
    meta(name='viewport' content='width=device-width,initial-scale=0')
  body
    h1 My First Jade
    p This is my first Jade template!
```

## File Structure
Create a new directory called `todoApp` with the following structure:

```
todoApp
- models // Schemas for database objects
--- Todo.js // Todo schema
- views // Our Jade files
- controllers // Routes and other application logic (APIs, etc).
--- main.js // Main controller file
- public // Files that the client can access, kind of like public_html in Apache
--- js
----- app.js // Our Angular code
- app.js // Our main Node.js and Express code
```

Run `npm init` to begin - feel free to fill in whatever information it prompts you for (simply pressing "Enter" works to, for the non-required fields).  This will create a `package.json` file for you, which holds the dependencies for the app.

Next, we need to install the dependencies.  Run `npm install express mongoose morgan body-parser method-override --save`. Once the dependencies have finished installing, your `package.json` should look something like this:

```json
{
  "name": "todoapp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "body-parser": "^1.14.2",
    "express": "^4.13.3",
    "method-override": "^2.3.5",
    "mongoose": "^4.3.4",
    "morgan": "^1.6.1"
  }
}
```

## Configuring Express
Now that the dependencies have been installed, let's create our app.  To begin, open the `app.js` file that's in the app root (not in the `public/js` directory).

```js
/**
 * Import dependencies
 */
var express = require('express');
var logger = require('morgan'); // Logs each server request to the console
var bodyParser = require('body-parser'); // Takes information from POST requests and puts it into an object
var methodOverride = require('method-override'); // Allows for PUT and DELETE methods to be used in browsers where they are not supported
var mongoose = require('mongoose'); // Wrapper for interacting with MongoDB
var path = require('path'); // File path utilities to make sure we're using the right type of slash (/ vs \)

/**
 * Configure database
 */
mongoose.connect('mongodb://localhost:27017/test'); // Connects to your MongoDB.  Make sure mongod is running!

/**
 * Configure app
 */
var app = express(); // Creates an Express app
app.set('port', process.env.PORT || 3000); // Set port to 3000 or the provided PORT variable
app.use(express.static(path.join(__dirname, 'public'))); // Set the static files directory - /public will be / on the frontend
app.use(logger('dev')); // Log requests to the console
app.use(bodyParser.json()); // Parse JSON data and put it into an object which we can access
app.use(methodOverride()); // Allow PUT/DELETE

/**
 * Start app
 */
app.listen(app.get('port'), function() {
  console.log('App listening on port ' + app.get('port') + '!');
});
```

If you run `node app` and visit http://localhost:3000, you'll see that we now have a nice little HTTP server set up.

## Creating a Model
In the `models` directory, create a new file called `Todo.js`.  In the file, put this:

```js
var mongoose = require('mongoose');
// Create a schema for the Todo object
var todoSchema = new mongoose.Schema({
  text: String,
  done: Boolean
});
// Expose the model so that it can be imported and used in the controller
module.exports = mongoose.model('Todo', todoSchema);
```

## Creating a Controller
We need to create a controller to define the actions of our Todos REST API.  Create a new file, `main.js`, in the `controllers` directory.

```js
require('../models/Todo'); // Import the Todo model so we can query the database
var mainController = {
  getTodos: function(req, res) {
    Todo.find({}, function(err, todos) {
      if (err) {
        // Send the error to the client if there is one
        return res.send(err);
      }
      // Send todos in JSON format
      res.json(todos);
    });
  },
  postNewTodo: function(req, res) {
    Todo.create({
      text: req.body.text,
      done: false
    }, function(err, todo) {
      if (err) {
        return res.send(err);
      }
      Todo.find({}, function(err, todos) {
        if (err) {
          return res.send(err);
        }
        // Send list of all todos after new one has been created and saved
        res.json(todos);
      });
    });
  },
  deleteTodo: function(req, res) {
    Todo.remove({
      _id: req.params.todo_id
    }, function(err, todo) {
      if (err) {
        return res.send(err);
      }
      Todo.find({}, function(err, todos) {
        if (err) {
          return res.send(err);
        }
        res.json(todos);
      });
    });
  }
}

module.exports = mainController;
```
## Creating the Routes
Back in the main app file, `app.js`, we need to define our API routes. First, we need to import our route actions. Right under the dependencies, add the main controller:

```js
...
var path = require('path'); // File path utilities to make sure we're using the right type of slash (/ vs \)

var mainController = require('./controllers/main');

/**
 * Configure database
 */
...
```

## Acknowledgments
This was based on [Scotch.io](https://scotch.io/tutorials/creating-a-single-page-todo-app-with-node-and-angular)'s great blog post, and the architecture is based on [sahat](https://github.com/sahat)'s [Hackathon Starter](https://github.com/sahat/hackathon-starter)
