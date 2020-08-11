# Backend step-by-step Including CSS and javascript

## Create and connect an assets folder

* In the 'public' folder of your repo make a file called 'assets'
* Using this assets folder for your css, javascript, fonts and images etc. can help to prevent a path to file error.
* Inside the 'assets' folder, create two more folders; one called 'css' and the other called 'javascript'.
* In the css folder create a file called style.css and in that file put body { background-color: red; } .
* In the javascript folder create a file called script.js and in that file put console.log("I'm here"); .
* In the html file that you made earlier, include these scripts in the usual manner. It should look something like this:

````

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="assets/css/style.css" />
    <script defer src="assets/javascript/script.js"></script>
    <title>Backendt Step-by-Step</title>
</head>
<body>
    <h1>Hello World</h1>
    <h2>This is html</h2>
</body>
</html>


````

* Open the server and run it. When you go to http://localhost:3000 you will notice that none of the styling has been applied.
* In order for the server to 'see' the css and javascript, you must 'point' it toward the public folder. To do this, in server.js add the line: app.use(express.static('public')); between the variables and the get '/' route.
* This code tells the server to use the resources in the public folder. If you refresh your page on localhost:3000 you will notice that the background color is red, and if you inspect the page and open up the console you will see "I'm here".
* Finally, as the index.html is also in the public folder (but not in the assets folder) you no longer need the __dirname + '/public/index.html' in the route. In the route change the response to res.sendFile('index'); . The server will recognize any file called index at the root of a folder, and by default the view engine in an express server is set to html, so you no longer have to include the .html either.
* Close the server and push your code up to gitHub and call the commit "assets folder".
* When you open the server.js in gitHub it should now look like this:

````

if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const PORT = process.env.PORT;

app.use(express.static('public'));

app.get('/', (req, res) => {
    res.sendFile('index');
});

mongoose.connect(process.env.DATABASE_URL || process.env.MONGODB_URI, {
        useNewUrlParser: true,
        useUnifiedTopology: true,
        useFindAndModify: false,
        useCreateIndex: true
    },
    () => {
        console.log("My database is connected");
    }
);

app.listen(PORT, () => {
    console.log("Tiny electronic ears are listening on port " + PORT);
});


````

## Create a Second Get Route and HTML

### public folder

* In the public (but not the assets) folder create a second html file called 'other-page.html'.
* In assets/css create a new file called 'other-style.css' and set the background to cyan. 
* In assets/javascript create a new file called 'otherScript.js' and console.log("I'm here too"); .
* In other-page.html link both the script and the css files.
* In assets create a folder called images and include an image (preferably a cat gif).
* In other-page html make an image tag and link to the image (cat gif).

### server.js

* In server.js make a new get '/catimage' route underneath the get '/' route. The response should send __dirname + 'public/other-page.html' . Because other-page.html is not called index.html, the server must be pointed to it. The route should look like this:

````
app.get('/catimage', (req, res) => {
    res.sendFile(__dirname + '/public/other-page.html');
});

````

* In the browser go to http://localhost:3000/catimage and you should see your image with a cyan background and when you inspect the page it should say: "I'm here too".
* Save to gitHub with the commit "second html".
* server.js should look like this:

````

if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const PORT = process.env.PORT;

app.use(express.static('public'));

app.get('/', (req, res) => {
    res.sendFile('index');
});

app.get('/catimage', (req, res) => {
    res.sendFile(__dirname + '/public/other-page.html');
});

mongoose.connect(process.env.DATABASE_URL || process.env.MONGODB_URI, {
        useNewUrlParser: true,
        useUnifiedTopology: true,
        useFindAndModify: false,
        useCreateIndex: true
    },
    () => {
        console.log("My database is connected");
    }
);

app.listen(PORT, () => {
    console.log("Tiny electronic ears are listening on port " + PORT);
});


````

## Set up form in index.html

* If you would like, change the background-color in style.css
* In index.html remove the <h1> and <h2> tags.
* Still in index.html create a form with a name and age input. The action will be '/catimage' and the method will be POST.
* index.html should look like this:

````

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="/assets/css/style.css" />
    <script defer src="/assets/javascript/script.js"></script>
    <title>Backendt Step-by-Step</title>
</head>
<body>
    <div>
        <h1>What is your name and age?</h1>
        <form action="/catimage" method="POST">
          <h3>Please enter your name and age</h3>
          <input id="name" type="text" name="name" placeholder="Your Name">
          <input id="age" type="number" name="age" placeholder="Your Age">
          <input type="submit" value="Submit">
        </form>
    </div>
</body>
</html>

````

* If you open the server and go to http://localhost:3000 in the browser you should see the form.
* If you enter your name and age in the inputs and press submit you should get the error: Cannot POST /catimage (because there is no post route yet).
* In other-page.html, above the img tag add the following h1 : <h1>Hello, <%= name %>, here are your cat pics!</h1>
* In other-page.html, below the h1, make the following h2 : <h2>This cat is three. Our records show that you are <%= age %> years old, <%= name %>!</h2>
* other-page.html should now look like this:

````

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="assets/css/other-style.css" />
    <script src="assets/javascript/otherScript.js"></script>
    <title>Other Page</title>
</head>
<body>
    <h1>Hello, <%= name %>, here are your cat pics!</h1>
    <h2>This cat is three. Our records show that you are <%= age %> years old, <%= name %>!</h2>
    <img src="assets/images/justaintright.gif" />
</body>
</html>


````

* If you display other-page.html it will look exactly how it is written, weird percent signs and all.

## Create a PUT route and Grab Variables

### server.js

* In order to post a request object the server will need two pieces of middleware. These can go below the variables but above the routes. The first is: app.use(express.urlencoded({extended: false})); This is a method built into express that allows the server to see the express object and strings or arrays. The second is: app.use(express.json);. It allows express to 'read' the incomming request object. Together these lines of code create a 'body parser'.
* Below the get '/catimages' route make an app.post('/catimages', (req, res) => { }) .
* Within the response, request function create three variables: let name = req.body.name; let age = req.body.age; let data = []; The req.body will come from the input in index.html. It must be called the same thing as in the "name" field in the input (i.e. id="id" type="type" name="name" placeholder="placeholder"). So, if the name was unicorn, it would be req.body.unicorn. Console.log name and age and then send a message some json so that the browser can return something. (Otherwise the browser cannot complete the route -- it will execute the console.log and just keep spinning until it times out or is given something else to do.)
* The server should look like this:

````

if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const PORT = process.env.PORT;


app.use(express.urlencoded({
    extended: false
  }));
app.use(express.json());
app.use(express.static('public'));

app.get('/', (req, res) => {
    res.sendFile('index');
});

app.get('/catimage', (req, res) => {
    res.sendFile(__dirname + '/public/other-page.html');
});

app.post('/catimage', (req, res) => {
    let name = req.body.name;
    let age = req.body.age;
    let data = [];
    console.log(name, age);
    res.json({"msg": "hello"});
});

mongoose.connect(process.env.DATABASE_URL || process.env.MONGODB_URI, {
        useNewUrlParser: true,
        useUnifiedTopology: true,
        useFindAndModify: false,
        useCreateIndex: true
    },
    () => {
        console.log("My database is connected");
    }
);

app.listen(PORT, () => {
    console.log("Tiny electronic ears are listening on port " + PORT);
});

````

* Go to localhost:3000 and enter a name and an age in the inputs and press submit. '/catimage' will display {"msg":"hello"} and in the terminal you will see the name and age that you entered.
* below the variable data type in data.push(name , age); . Instead of console.log(name, age); console.log(data);. Instead of res.json({"msg": "hello"}); , res.json(data)

````

let name = req.body.name;
    let age = req.body.age;
    let data = [];
    data.push(name, age);
    console.log(data);
    res.json(data);

````

* at localhost:3000/catimage you should get the data returned in json format. In the terminal you should get data returned as an array.
* Unfortunately, there is no way to pass this data to other-page.html. When we use sendFile the server returns the file directly, without any chance to modify it. In order to modify the html we are going to have to use a template system. First, however, let's save what we have so far.
* close the server, push to gitHub as "as far as can go html"
* The server should look like this:

````

if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const PORT = process.env.PORT;


app.use(express.urlencoded({
    extended: false
  }));
app.use(express.json());
app.use(express.static('public'));

app.get('/', (req, res) => {
    res.sendFile('index');
});

app.get('/catimage', (req, res) => {
    res.sendFile(__dirname + '/public/other-page.html');
});

app.post('/catimage', (req, res) => {
    let name = req.body.name;
    let age = req.body.age;
    let data = [];
    data.push(name, age);
    console.log(data);
    res.json(data);
});

mongoose.connect(process.env.DATABASE_URL || process.env.MONGODB_URI, {
        useNewUrlParser: true,
        useUnifiedTopology: true,
        useFindAndModify: false,
        useCreateIndex: true
    },
    () => {
        console.log("My database is connected");
    }
);

app.listen(PORT, () => {
    console.log("Tiny electronic ears are listening on port " + PORT);
});

````

## Convert to EJS Templates

* First in the command line type npm i ejs express-ejs-layouts and let npm install these programs for you. When they are done, check in the package.json under dependencies to see if they are there.
* Create a folder called views at the root of the app (not in public, but along side it).

### In views/layouts

* In views create another folder called 'layouts'
* Move index.html and other-page.html into views/layouts
* Change the name of index.html to 'page.ejs' and the name of other-page.html to 'other-page.ejs'
* Make a file called form.ejs in layouts, and copy and past the form from page.ejs to form.ejs. Page.ejs should be left with the HTML headers and the empty body tag.
* In other-page ejs take out the body tag and everythin OUTSIDE the body tag, leaving just the html.

````

// page.ejs:

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="/assets/css/style.css" />
    <script defer src="/assets/javascript/script.js"></script>
    <title>Backendt Step-by-Step</title>
</head>
<body>
    
</body>
</html>

// form.ejs:

<div>
    <h1>What is your name and age?</h1>
    <form action="/catimage" method="POST">
    <h3>Please enter your name and age</h3>
    <input id="name" type="text" name="name" placeholder="Your Name">
    <input id="age" type="number" name="age" placeholder="Your Age">
    <input type="submit" value="Submit">
    </form>
</div>

// other-page.ejs

<h1>Hello, <%= name %>, here are your cat pics!</h1>
<h2>This cat is three. Our records show that you are <%= age %> years old, <%= name %>!</h2>
<img src="assets/images/justaintright.gif" />

````

* in page.ejs, in between the body tags type <%- body %>  . This will allow you to render any ejs template in this space.
* page.ejs should now look like this:

````

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="/assets/css/style.css" />
    <script defer src="/assets/javascript/script.js"></script>
    <title>Backendt Step-by-Step</title>
</head>
<body>
    <%- body %>
</body>
</html>

````

### in server.js

* As mentioned earlier, by default the view engine in express is html. If we want to use ejs we will need to set the view engine to ejs, but first we need a variable.
* In the variables section of server.js declare const expressLayouts = require('express-ejs-layouts');
* In between the variables and the 'app.use' (middleware) section set the view engine this is three lines of code.
* first type in app.set('view engine', 'ejs'); this sets the view engine to ejs, which is a string, and therefore does not need to be declared as a variable.
* Second type in app.set('views', __dirname + '/views'); this allows the server to 'see' inside the views folder
* Third type in app.set('layouts', 'layouts/page'); This will set 'page.ejs' as the main page.
* In the middleware section (app.use();), after app.use(express.static()); , type in app.use(expressLayouts); 
* In the get '/' route res.render('layouts/form'); because now there is nothing to render in layouts/page.
* Remove the get '/catimage' route (NOT the post '/catimage' route!) .
* In the post'./catimage' route, instead of res.json, res.render('layouts/other-page') pass in the variables from data: res.render('layouts/other-page', {name: data[0], age: data[1]} ); .
* The server should now look like this:

````

if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const expressLayouts = require('express-ejs-layouts');
const PORT = process.env.PORT;

app.set('view engine', 'ejs');
app.set('views', __dirname + '/views');
app.set('layout', 'layouts/page');

app.use(express.urlencoded({
    extended: false
  }));
app.use(express.json());
app.use(express.static('public'));
app.use(expressLayouts);

app.get('/', (req, res) => {
    res.render('layouts/form');
});

app.post('/catimage', (req, res) => {
    let name = req.body.name;
    let age = req.body.age;
    let data = [];
    data.push(name, age);
    console.log(data);
    res.render('layouts/other-page', {name : data[0], age: data[1]});
});

mongoose.connect(process.env.DATABASE_URL || process.env.MONGODB_URI, {
        useNewUrlParser: true,
        useUnifiedTopology: true,
        useFindAndModify: false,
        useCreateIndex: true
    },
    () => {
        console.log("My database is connected");
    }
);

app.listen(PORT, () => {
    console.log("Tiny electronic ears are listening on port " + PORT);
});

````

* go to localhost:3000 and the form should now render. If you input a name and an age and hit submit, those variables will be passed to 'other-page'. Among two things worth noting:
	1. The cyan background is gone
	2. The name and age could just as easily been passed in as {name: name, age: age} , but in this case we are using an array to mimic (poorly) a database, so we are passing those variables through as part of the array.

### Fixing CSS

* With ejs layouts it is better to only have on css file. Delete 'other-style.css'.
* In style.css remove the body tag and background color. Type in * { box-sizing: border-box; margin: 0; padding: 0; }
* In form.ejs and other-page.ejs make sure that there are 'divs' surrounding the html. Give the divs a class, one for form.ejs and one for other-page.ejs.
* In style.css give each class a background color, a height of 100vh, and a width of 100vw.
* style.css should now look like this:

````
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

.form-container {
    background-color: red;
    height: 100vh;
    width: 100vw;
}

.cat-container {
    background-color: cyan;
    height: 100vh;
    width: 100vw;
}

````

* form.ejs and other-page.ejs should look as follows:

````

// form ejs

<div class="form-container">
    <h1>What is your name and age?</h1>
    <form action="/catimage" method="POST">
    <h3>Please enter your name and age</h3>
    <input id="name" type="text" name="name" placeholder="Your Name">
    <input id="age" type="number" name="age" placeholder="Your Age">
    <input type="submit" value="Submit">
    </form>
</div>

// other-page.ejs

<div class="cat-container">
<h1>Hello, <%= name %>, here are your cat pics!</h1>
<h2>This cat is three. Our records show that you are <%= age %> years old, <%= name %>!</h2>
<img src="assets/images/justaintright.gif" />
</div>

````

* The render should be as before, except now we can pass variables from on view to another. Push to gitHub as "ejs". 

## What about javascript?

* with ejs simple javascript functions can be performed right on the html page.
* ejs values can be passed to javascript files as well.
* In other-page.ejs give the h1 an id of "catTitle" and a data-name of "<%= name %>" .
* Still in other-page.ejs give the h2 an id of "catAge" and a data-name of "<%= age %> .

````
<div class="cat-container">
<h1 id="catTitle" data-name="<%= name %>">Hello, <%= name %>, here are your cat pics!</h1>
<h2 id="catAge" data-age="<%= age %>" >This cat is three. Our records show that you are <%= age %> years old, <%= name %>!</h2>
<img src="assets/images/justaintright.gif" />
</div>

````
* This is somewhat underhanded, because the user is unknowingly passing this info into an external javascript file which  can be manipulated for ill means. (In this case it is name and age, but think visa number and 3 digit security id.)
* In assets/javascript delete otherScript.js
* In scripts.js delete the console.log, replacing with the following code:

````

document.addEventListener("DOMContentLoaded", function () {
    const catTitle = document.getElementById('catTitle');
    const catAge = document.getElementById('catAge');
    let name = catTitle.getAttribute('data-name');
    let age = catAge.getAttribute('data-age');
    console.log(name, age);
});

````

* You now have access to the name and age variables that were passed into the html from the server. 
* Push to github as "ejs variables to js"


