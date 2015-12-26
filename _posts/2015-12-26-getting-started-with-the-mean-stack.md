---
layout: post
title: Getting Started with the MEAN Stack
author: Nathan Leung
comments: true
tags: [nodejs]
---
Welcome to this MEAN stack quick start guide.  The MEAN Stack is made of [MongoDB](https://www.mongodb.org/), [Express.js](http://expressjs.com/), [AngularJS](https://angularjs.org/) and [Node.js](https://nodejs.org/).

A quick rundown of what every part does: MongoDB is a database, Express.js provides routing support (i.e. mapping URLs to the correct destination/action), AngularJS allows for a model-view-controller architecture on the client side (in this case, it allows the client to communicate with the Express.js-based API), and Node.js is the engine that allows us to run Javascript code on the server in the first place.

This getting started guide applies to the current latest stable versions of Angular (1.4.x), Express (4.x), and Node (4.x). As usual, we'll be making a very simple todo app.

## File Structure
Create a new directory called `todoApp` with the following structure:

```
todoApp
- models // Schemas for database objects
--- Todo.js // Todo schema
- views // Our Jade files (template engine that compiles to HTML)
- controllers // Routes and other application logic (APIs, etc).
--- main.js // Main controller file
- public // Files that the client can access, (like public_html in Apache)
--- js
----- app.js // Our Angular code
- app.js // Our main Node.js and Express code
```

Run `npm init` to begin - feel free to fill in whatever information it prompts you for (simply pressing "Enter" works to, for the non-required fields).  This will create a `package.json` file for you, which holds the dependencies for the app ([NPM](https://www.npmjs.com/) is the package manager for Node).

## Dependencies
### Node.js
Let's install the Node dependencies first.  Run `npm install express mongoose morgan body-parser method-override jade --save`. Once the dependencies have finished installing, your `package.json` should look something like this:

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
    "jade": "^1.11.0",
    "method-override": "^2.3.5",
    "mongoose": "^4.3.4",
    "morgan": "^1.6.1"
  }
}
```
### Frontend
Now, let's install the frontend dependencies.  We'll use [Bower](http://bower.io/), which is sort of like NPM, but for the frontend.  To install it, run `sudo npm install bower -g`.  This will allow you to use the `bower` command in the terminal.

To install the frontend dependencies, run `bower install angular bootstrap`. Bower will create a directory called `bower_components` and put everything in there. After the deps have finished installing, run `bower init`. This will create a file called `bower.json`.  Bower will run you through pretty much the same process as `npm init` (i.e. just press Enter and you'll be fine), but just make sure to mark the package as private and "set currently installed packages as dependencies" when those options are presented.  Your `bower.json` should look like this afterwards:

```json
{
  "name": "todoapp",
  "description": "",
  "main": "index.js",
  "authors": [
    ""
  ],
  "license": "ISC",
  "homepage": "",
  "moduleType": [],
  "private": true,
  "ignore": [
    "**/.*",
    "node_modules",
    "bower_components",
    "test",
    "tests"
  ],
  "dependencies": {
    "angular": "~1.4.8",
    "bootstrap": "~3.3.6"
  }
}
```

## Backend
### Configuring Express
Now that the dependencies have been installed, let's create our app.  To begin, open the `app.js` file that's in the app root (not in the `public/js` directory, that's where our Angular code will go).

```js
// app.js
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
mongoose.connect('mongodb://localhost:27017/todoDB'); // Connects to your MongoDB.  Make sure mongod is running!
mongoose.connection.on('error', function() {
  console.log('MongoDB Connection Error. Please make sure that MongoDB is running.');
  process.exit(1);
});

/**
 * Configure app
 */
var app = express(); // Creates an Express app
app.set('port', process.env.PORT || 3000); // Set port to 3000 or the provided PORT variable
app.set('views', path.join(__dirname, 'views')); // Set our views directory to be `/views`
app.set('view engine', 'jade'); // Set our view engine to be Jade (so when we render these views, they are compiled with the Jade compiler)
app.use(express.static(path.join(__dirname, 'public'))); // Set the static files directory - /public will be / on the frontend
app.use('/vendor', express.static(path.join(__dirname, 'bower_components'))); // Map the /bower_components directory to /vendor - /bower_components/bootstrap will be /vendor/bootstrap on the frontend
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

#### Jade
If we look above, we'll see the line `app.set('view engine', 'jade');`.  What this does is tell Express that the files in the `views` directory should be compiled using the Jade. [Jade](http://jade-lang.com/) is a very popular templating language for Node.js, and it's pretty much a terser version of HTML:

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

We'll go into more depth later, when we write the frontend code.

### Creating a Model
Now that we've got the basics of our app setup, we need to create a model for the Todos. This model will define the shape of our documents (analagous to rows in SQL), and Mongoose will use this model to create a collection in MongoDB (analagous to a table in SQL) for the Todos.  To create our Todo model, in the `models` directory create a new file called `Todo.js` with this code:

```js
// models/Todo.js
var mongoose = require('mongoose');
// Create a schema for the Todo object
var todoSchema = new mongoose.Schema({
  text: String
});
// Expose the model so that it can be imported and used in the controller
module.exports = mongoose.model('Todo', todoSchema);
```

### Creating a Controller
Now, we need to create a controller. This controller will handle the routes of our Todos REST API (e.g. GET /todos, POST /todos). Create a new file, `main.js`, in the `controllers` directory with this code:

```js
// controllers/main.js
var Todo = require('../models/Todo'); // Import the Todo model so we can query the DB

var mainController = {
  // This gets all Todos in the collection and sends it back in JSON format
  getAllTodos: function(req, res) {
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
    // This creates a new todo using POSTed data (in req.body)
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
      _id: req.params.id
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

// Allow the controller to be imported into the main app.js file
module.exports = mainController;
```
### Creating the Routes
Back in the main app file, `app.js`, we need to define our API routes. First, we need to import our route handlers ("controllers"). Right under the dependencies, add the main controller:

```js
// app.js
...
var path = require('path'); // File path utilities to make sure we're using the right type of slash (/ vs \)

/**
 * Import controllers
 */
var mainController = require('./controllers/main');

/**
 * Configure database
 */
...
```

Now, we need to configure the routes. Below, under the "Configure App" section, add this:

```js
...
app.use(methodOverride()); // Allow PUT/DELETE

/**
 * Configure routes
 */
app.get('/todos', mainController.getAllTodos);
app.post('/todos', mainController.postNewTodo);
app.delete('/todos/:id', mainController.deleteTodo);

/**
 * Start app
 */
...
```

How does this work? Here's an example: when you `GET` `/todos` in your browser, Express will execute the middleware above (logging, body parsing, method overriding) and then call the `mainController.getAllTodos` function with `(req, res)` as arguments.  The `getAllTodos` function will query the database for all the todos and send them to the client in JSON format.

If you run `node app` and visit http://localhost:3000/todos, you should see an empty array (since we have no Todos).

## Frontend
Our API has been built.  Now, we need a way to interact with it on the frontend.  To do so, we can use Angular!

Let's define our index page route handler in the main controller (`/controllers/main.js`):

```js
// controllers/main.js
...
var mainController = {
  getIndex: function(req, res) {
    res.render('index'); // Compiles the file named "index" in the views directory (`/views`) using the view engine (Jade).
  },
  // This gets all Todos in the collection and sends it back in JSON format
  getAllTodos: function(req, res) {
...
```

Now, we can use this new route handler in our app.  In the root `app.js`, add this:

```js
// app.js
...
/**
 * Configure routes
 */
app.get('/', mainController.getIndex);
...
```

If you run `node app` now and visit http://localhost:3000, you should now see a blank page.  Let's begin with our Angular setup.  Open up `/public/js/app.js` and add this code:

### Angular Setup

```js
// public/js/app.js
var app = angular.module('todoApp', []); // Creates an angular app called 'todoApp' with no dependencies ([])
// Creates an angular controller called MainCtrl, which uses the $http service (for AJAX)
app.controller('MainCtrl', function($http) {
  // We use `this` because so we can use the controllers as instances
  // We'll set vm to the controller this value because the HTTP callback this value is different from the controller this value
  var vm = this;
  vm.formData = {};

  // Get all todos on page landing
  $http.get('/todos')
    .success(function(data) {
      vm.todos = data;
    })
    .error(function(data) {
      console.log("Error: " + data);
    });

  // Notice the similarity in method names on the front and back-end.
  vm.postNewTodo = function() {
    $http.post('/todos', vm.formData)
      .success(function(data) {
        vm.formData = {}; // Clear the form
        vm.todos = data; // Remember, in our postNewTodo route handler we return all todos in JSON format
      })
      .error(function(data) {
        console.log("Error: " + data);
      });
  };

  vm.deleteTodo = function(id) {
    $http.delete('/todos/' + id)
      .success(function(data) {
        vm.todos = data;
      })
      .error(function(data) {
        console.log("Error: " + data);
      });
  };

});

```

### Jade View
Open up `views/index.jade` and put the following:

```jade
// views/index.jade
doctype html
// This tells Angular that this area of the HTML is the todoApp defined in public/app.js
html(ng-app='todoApp')
  head
    title Todo App
    meta(charset='utf-8')
    meta(name='viewport' content='width=device-width,initial-scale=1.0')
    link(rel='stylesheet' href='/vendor/bootstrap/dist/css/bootstrap.min.css')
  // This tells Angular that MainCtrl should control the user actions done in this area
  // MainCtrl as vm creates an instace of MainCtrl called "vm" - the view model (although you can call it anything)
  body(ng-controller='MainCtrl as vm')
    // .container is Jade shorthand for <div class='container'></div>
    .container
      .jumbotron.text-center
        h1 Todos&nbsp;
          span.label.label-info {{vm.todos.length}}
      .row
        .col-sm-6.col-sm-offset-3
          table.table.table-bordered
            thead
              tr
                th.text-center Todos
            tbody
              tr(ng-repeat='todo in vm.todos')
                td
                  input(type='checkbox' ng-click='vm.deleteTodo(todo._id)')
                  | &nbsp;&nbsp;
                  | {{todo.text}}
      .row
        .col-sm-6.col-sm-offset-3
          form(ng-submit='vm.postNewTodo()')
            .form-group
              // Bind this input to formData.text in the controller
              input.form-control.input-lg.text-center(type='text' placeholder='What to do next?' ng-model='vm.formData.text')
            .form-group.text-center
              br
              button.btn.btn-success.btn-lg(type='submit') Do Something


    script(src='/vendor/jquery/dist/jquery.min.js')
    script(src='/vendor/angular/angular.min.js')
    script(src='/js/app.js')
```
## Conclusion
We now have a rudimentary Todo app with a Node.js/Express server and Angular frontend.  Let me know what you make of it in the comments!

Also, the full source is viewable here: https://github.com/nathanhleung/MEAN-todo

## Acknowledgments
This was based on [Scotch.io](https://scotch.io/tutorials/creating-a-single-page-todo-app-with-node-and-angular)'s great blog post, and the architecture is based on [sahat](https://github.com/sahat)'s [Hackathon Starter](https://github.com/sahat/hackathon-starter)
