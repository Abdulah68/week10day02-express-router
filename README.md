# Express Router

## Request and Response

### The 'req' Argument

The [req](https://expressjs.com/en/api.html#req) argument is an object that contains information about the incoming HTTP **request**:

- req.route
- req.query
- req.params
- req.body (see bodyParser below)

> You can also use the full name- `request`, but we use `req` for brevity.

### The 'res' Argument

The [res](https://expressjs.com/en/api.html#res) argument is another object that contains information about the **response** we want to send to the server.

We initially started using `res.send` to send text to the browser, when using handlebars.  However, we can use `res.render` to render an html or hbs file.

We can also use `res.redirect` to trigger another route before sending a response back to the browser.

> You can also use the full name- `response`, but we use `res` for brevity.

<br />

You can check out the Request and Reponse headers in the Network tab in your Chrome dev tools. They contain a lot of key/value pairs that we will discuss and use throughout the course.

![](https://i.imgur.com/DIA7MR4.png)

<br />

## URL Params

**Params** (short for "parameters") is an object attached to each `request` to the server. We can send params via the URL. Let's update the `server.js` to include:

```javascript
app.get("/:name", function(req, res){
  console.log(req.params);
  res.send(`Hello, ${req.params.name}!`);
});
```
Try this URL: `http://localhost:3000/sei9`. What is returned?

**CFU:** How many routes do we currently have? What are they?

<br />

### Why are params important?

Eventually, we will use "wildcard" params to grab specific information from our app. For example, if we were building a Facebook replica, and we wanted to grab a specific friend of a specific user, we might build a route that looks like this:

`http://localhost:3000/users/:user_id/friends/:friend_id`

Then, we can send a request like this:

`http://localhost:3000/users/1/friends/2`

<br />

&#x1F535; **YOU DO: 5 minutes** 

1. Create a route that uses 'food' as a parameter
2. The route should return a string that includes the food (e.g.- "I really love kahwa").

Did anyone run into any issues?

<br />

## Query Parameters

Our base route is a fixed path to a specific resource (like an html page, a piece of data in our database, an image, etc.) and we can augment or support that path by providing parameters.

The query parameter pattern should be familiar, it is essentially a key and a value:

```javascript
?q=foo&a=bar
```

The `?` tells the server that all of the text that follows, should be interpreted and parsed as query parameters, with `q` as the key and `foo` as the value. Unlike arguments in functions, or key/value pairs in JS objects, query parameters are not comma separated but separated by an `&`, so our second query parameter key is `a` and it's value is `bar`. 

A `console.log()` of our query parameters would look something like this:

```javascript
{
    q: "foo",
    a: "bar"
}
```

Let's make our `/:name` route more dynamic by adding a 'first_name' query parameter!

```javascript
app.get("/:name", function(req, res){
  console.log(req.params);
  console.log(req.route);
  console.log(req.query);
  // parameter is name, query parameter is first_name
  res.send(`Hello, ${req.params.name}. My name is ${req.query.first_name}.`);
});
```

**Try this example:** `http://localhost:3000/sei9?first_name=ahmed`

If we wanted to be formal, we could add a 2nd query parameter of 'last_name':

```javascript
app.get("/:name", function(req, res){
  console.log(req.params);
  console.log(req.route);
  console.log(req.query);
  res.send(`Hello, ${req.params.name}. My name is ${req.query.first_name} ${req.query.last_name}.`);
});
```

**Try this example:** `http://localhost:3000/sei9?first_name=ahmed&last_name=tahir`

<br />

#### Again we ask, why are these important?

You actually use query parameters all the time on Amazon, Ebay, Airbnb, etc. - anytime you search or 'query' an app. For example, the query to search for Drake tickets on Atlanta's Craigslist looks like this:

`http://atlanta.craigslist.org/search/tia?query=drake`

<br />

&#x1F535; **YOU DO: 5 minutes**

1. Write a route at `/sightings` that takes a query parameter of `state` and `sights` and responds with an object that looks like this:

```javascript
{
        state:`<the state passed in>`, 
        sights: `<how many ufo sightings you think there are in that state>`
}
```
Also, send a response that asks 'How many ufo sightings do you think there are in `the state`?   `the answer`.'

2. Write a `/bigfoot` route that takes a query parameter of `blurry` and...
   - If blurry is true, send the response: `"It's not the photographer's fault. Bigfoot is blurry, and that's extra scary to me. There's a large, out-of-focus monster roaming the countryside. Run! He's fuzzy! Get out of there!"` 
   - If blurry is false, respond with:  `"A group of researchers have amassed evidence that the legendary Bigfoot is real, ABC News reported, with the scientists presenting reams of evidence."`


<details>
    <summary>SOLUTION</summary>

```javascript
app.get('/sightings', function(req, res){
  console.log(req.route); //just to checkout the server logs
  console.log(req.query); //just to checkout the server logs
  res.send(`How many ufo sightings you think there are in that ${req.query.state}? ${req.query.sights}.`);
});

app.get('/bigfoot', function(req, res){
  console.log(req.route); //just to checkout the server logs
  console.log(req.query); //just to checkout the server logs
  if (req.query.blurry == "true") {
    res.send("It's not the photographer's fault. Bigfoot is blurry, and that's extra scary to me. There's a large, out-of-focus monster roaming the countryside! Run! He's fuzzy! Get out of there!");
  } 
  else {
    res.send("A group of researchers have amassed evidence that the legendary Bigfoot is real, ABC News reported, with the scientists presenting reams of evidence.");
  }
});
```
</details>

<br />

## Dynamic Segments

We do have another way that isn't one that we have previously discussed.  We often use dynamic segments and query parameters. For example:

    /hello/:name?human=true
    
What would this route look like?    

```javascript
app.get('/hello/:name', function(req, res) {
  res.send({params: req.params, queries: req.query});
});
```

Try this route: `http://localhost:3000/hello/sei9?human=true`

<br />

&#x1F535; **YOU DO: 5 minutes**

1. Build a route at `/favorite/:noun` where `:noun` can be any favorite 'thing' (e.g. - color, food, song, movie, jeans)

2. The route will return the parameter `:noun` in the string `I have a favorite <insert :noun here>.`

3. Add the expectation of query parameters, so that hitting the following route: `/favorite/color?color=red` will return to the browser the string `I have a favorite color, it is red.`

<details>
    <summary>SOLUTION</summary>

```javascript
app.get('/favorite/:noun', function(req, res) {
  if (req.query[req.params.noun]) {
    res.send(`I have a favorite ${req.params.noun}, it is ${req.query[req.params.noun]}.`);
  } else {
    res.send(`I have a favorite ${req.params.noun}.`);
  }
  console.log({params: req.params, queries: req.query});
});
```
</details>

<br />

## Order Matters

Keep in mind that when Express receives a request, it checks each route in order until it finds a pattern match. 

For example, if you order your routes like this:

```javascript
app.get('/:name', function(req, res){
    ...
});

app.get('/greeting', function(req, res){
    ...
});
```

and you send a request to the URL `http://localhost:3000/greeting` which route will Express think you want? In this example, you want to make sure your "wildcard" `/:name` route comes **AFTER** `/greeting` so that Express will pattern match these correctly.

<br />

## Lab Time

1. Make routes for  `add, subtract, multiply, divide` that will take two numbers as query parameters `num1` and `num2` and perform the operation specified in the rout,e and send those answers to the browser.

For example, this will send the number `15` to the browser:

```javascript
/add?num1=5&num2=10
```

2. Add a fifth route `/math/:operator`

3. Create a route that can do all four math operations using control flow. For example:

```javascript
if req.params.operator === 'add' 
    then add num1 and num2 
else if 
    etc etc...
```

---
<details>
    <summary>Additional Read to understand The Router in Express (Refactoring logic in routes</summary>

## Lesson Objectives
1. Get rid of Code Smells by utilizing the DRY principle and other best practices
2. Use the Single Responsibility Principle to explain the concept of MVC
4. Use the express Router to:
  - Bring the C of MVC
  - Dry up our routes
  - Apply Single Responsibility to our application, specifically to our main server file (server.js)
5. Describe and apply the principles of Refactoring

**Optional reading for later:**

- [3 Key Software Principles](https://code.tutsplus.com/tutorials/3-key-software-principles-you-must-understand--net-25161)
- [Express routing docs](https://expressjs.com/en/guide/routing.html)
- [Express Router](https://scotch.io/tutorials/keeping-api-routing-clean-using-express-routers)
- [Module.exports- open my mind](http://openmymind.net/2012/2/3/Node-Require-and-Exports/)
- [Module.exports- sitepoint](https://www.sitepoint.com/understanding-module-exports-exports-node-js/)

<br />

## Intro

Let's take a look at all of the things we have done so far in our single server.js file.

- Import express, handlebars, and other dependencies.
- Declared which view engine to use as our templating language.
- Create multiple routes that listens for a request and provides a response.
- Define what port our server lives on.

Just these four things are starting to make our server.js file pretty large and unmaintainable.  Imagine what this 
code would look like if it were also connected to a database and had dozens and dozens of routes! Today we will 
examine some best practices regarding breaking up our code into smaller, more readable pieces.


## DRY and Code Smells
### Code Smells
<img style="float:left; margin: 0 20px 20px 0;" width="300" src="https://cdn-images-1.medium.com/max/1600/1*_xk8HBrdtVr3snK9rZ6Jlg.jpeg" />

For many of us, a large `server.js` file may start to not feel like a best practice.  It doesn't fit in with many of the rules that we've talked about in the past like DRY and Separation of Concerns.  These feelings can help push you to noticing symptoms that can possibly indicate a deeper problem in your code.  This is commonly referred to as a `Code Smell`.

Leaving Code Smells unaddressed can easily lead to difficult to maintain or unmaintainable code down the line.  We want to do our best to avoid writing smelly code.  One easy way to make sure our code is maintainable and scalable over the long term is to utilize established design patterns, keep our code separated into many files instead of one giant file, and constantly looking for opportunities to make our code better through **merciless** refactoring.

### Keeping the Smell Out

### Don't Repeat Yourself

The **DRY** principle is aimed at reducing repetition within your code.  This makes your code easier to maintain as it grows and prevents unnecessary bloat.

> "Every piece of knowledge must have a single, unambiguous, authoritative representation within a system."

<br />

### Single Responsibility

> "Every module or class should have responsibility over a single part of the functionality provided by the software, and that responsibility should be entirely encapsulated by the class." - https://en.wikipedia.org/wiki/Single_responsibility_principle

<br />

### Design And Architectural Patterns

#### RESTful Routing
RESTful Routing is a naming convention for all of your routes which mixes HTTP Verbs with path names to create individual routes for each CRUD interaction.  RESTful routes always match 1 of 7 different actions.

![](https://i.stack.imgur.com/RyM1b.png)

We'll go more in depth with RESTful routing tomorrow.

#### Model View Controller

**MVC** is an application architectural pattern that allows us to efficiently utilize the single responsibility principle. So far we have been writing everything in the server file, the views directory, and our data file.

As our application gets large, it will become necessary to break up our code into smaller files.  To combat this ever increasing bloat, the MVC pattern emerged as a way to manage the flow of data throughout a request/response life cycle.

**Turn & Talk: MVC Life Cycle (5 min)**

1. There are roughly 7 steps from initial request to the server response when using MVC.
2. You and a partner work together to research all of the steps of the MVC cycle.

<img height="400" src="https://upload.wikimedia.org/wikipedia/commons/thumb/a/a0/MVC-Process.svg/1200px-MVC-Process.svg.png" />

<br />

## Refactoring your app to use MVC

The first step for triggering the request/response lifecycle is the router.  So far, all of our routes have lived in the `server.js` file.  Let's utilize the `Router` that is built into Express to break up our code into a more organized directory structure.

### We Do: Use the Express Router to Simplify `server.js`

You may find that your server.js file is growing into a giant file with tons of routes.  Related routes can be grouped together in separate files

```javascript
app.get('/shows', function(req, res){
    //do stuff
});

app.get('/shows/:id', function(req, res){
    //do stuff
});

app.get('/shows/:id/edit', function(req, res){
    //do stuff
});
```

These routes are all related to shows.  Let's put them in a separate "controller" file and directory.  To do this, let's create a "controllers" directory and create a file called shows_controller.js.  This will be our shows controller file.

Now move the routes pertaining to shows into that shows_controller.js file.

```javascript
app.get('/shows', function(req, res){
    //do stuff
});

app.get('/shows/:id', function(req, res){
    //do stuff
});

app.get('/shows/:id/edit', function(req, res){
    //do stuff
});
```

Require express at the top of that shows_controller.js file

```javascript
var express = require('express');
```

Invoke the router

```javascript
var router = express.Router();
```

Change app to router

```javascript
router.get('/shows', function(req, res){
    //do stuff
});

router.get('/shows/:id', function(req, res){
    //do stuff
});

router.get('/shows/:id/edit', function(req, res){
    //do stuff
});
```

At the bottom of the file, export the router

```javascript
module.exports = router;
```

> This syntax allows us to export parts of the code to be used in other files.  We've already seen this in the libraries we've used so far.  Watch this video to see a few different methods of exporting: https://www.youtube.com/watch?v=P51O_PT7NUg

Now in your `server.js`, require the controller file we created.  **Be sure to include ./ at the beginning of the path to the controller, so node knows that this is not an NPM package.**

```javascript
var showsController = require('./controllers/shows_controller.js');
```

Use the controller for all routes that start with `/shows`

```javascript
app.use('/shows', showsController);
```

> `app.use` is an example of using something called `middleware`.   This allows us to use the `express.Router()` method.  We will use middleware to add many libraries in the future. [List of Common Express Middleware Libraries](https://expressjs.com/en/resources/middleware.html)

Since we are specifying that the controller will be used for all routes starting with `/shows`, we don't need to show `/shows` in our actual routes within our controller file.

```javascript
router.get('/', function(req, res){
    //do stuff
});

router.get('/:id', function(req, res){
    //do stuff
});

router.get('/:id/edit', function(req, res){
    //do stuff
});
```
<details> 
  <summary>Where did the `shows` part of the route go?</summary>

  We declare the beginning of the path in the `server.js` when we say `app.use('/shows', showsController)`.  The first argument here determines the starting point for all routes that exist in the controller.
</details>

---

## LAB

Routes on routes on routes! Time to get more reps in building an Express app, adding Handlebars, and using Express Router to keep your code separated.

<details>
<summary>[Express Router Lab]</summary>

### Pick the driver and navigator

We will switch every 30 minutes

You will create a new router for each route defined in _Step 2_

#### Step 1

1. Initialize a new Node app

2. Touch server.js

3. Create a views directory

4. Add Express
    - npm install <module> --save

5. Add the app.listen function to your server.js

6. Add express to server.js

7. Add handlebars to server.js


#### Step 2

*A math router*
8. A route `/math/:operator` that expects `num1` and `num2` as query parameters. The
route will add, subtract, multiply or divide the two numbers. The result is rendered
to `math.hbs` (you've done this already!)

*A greeting router*
9. A route `/greeting/:name` that expects `greeting` as a query parameter. The
route will construct a custom greeting based on the parameters and render the
resulting string to `greeting.hbs`

*A reverse router*
10. A route `/reverse` that expects `word` as a query parameter. The route will
reverse the worse and render the resulting string to `reversed.hbs`

*This route will go in the math router made in #6*
11. A route that shows all the numbers in a given range

*10 and 11 will be in the same router, you can use whatever naming convention
you think makes sense.*

12. A route that renders todays date (not hardcoded)

13. A route that renders the date a specific number of days before or after
todays date. (What's the date 5 days from now, 4 days ago)
*hint: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date*
</details>



</details>
