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
