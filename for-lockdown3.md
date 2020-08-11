# Backend step-by-step The Database

## The Mongoose Model

### What is a model?

* The model is the information that you want to store in the database for a particular collection of data. It might be a site user/membership, it might be shopping preferences, recipes, inventory, whatever information it is that you want to store so that your app has access to it. The model is a structure that you set up to store information in the way that you need it to be stored. 
* Mongoose is the middleware that communicates between the server and the database. The model is the tool that mongoose uses to communicate. For  this example we are going to make a model that takes in the name and the age of a user (from form.ejs). We will call this collection "CatFancier".
* Everytime we call on "CatFancier" what we are doing is saying is: "mongoose (the middleware) take this information structure and ask the server what we want done with it, then go to the database and compare it to what is there. Then, report back." All that is rather wordy, so instead we say things like "CatFancier.save()" or "CatFancier.findOneByIdAndUpdate()". 
* The model is essentially a constuctor, a function that makes an object. Since, by convention a constructor is always capitalized, and since this convention seems built into the middleware (mongoose), the model is always capitalized.

### Making the model

* In the root directory make a folder called models (with a small 'm'), and in that folder make a file called CatFancier.js (with a capital 'C').
* In CatFancier.js require mongoose.
* Copy and paste or type in the following two lines of code.
	1. const CatFancierSchema = new mongoose.Schema({  }); This is the constructor that you will use to make the 'CatFancier' object. Fields of information that you want stored go between the curly braces.
	2. module.exports = mongoose.model('CatFancier', CatFancierSchema); This exports the mongoose model with the name 'CatFancier' (the first parameter) and as a constructor (the second parameter).
* Fill out the appropriate fields in the CatFancierSchema. In this case, we want to save the name and the age of the CatFancier ( because that is what we put in our form in form.ejs ). It should look like this:

````
const mongoose = require('mongoose');


const CatFancierSchema = new mongoose.Schema({
    name: {
        type: String,
        required: true,
        trim: true
    },
    age: {
        type: Number,
        required: true,
        trim: true
    }
});

module.exports = mongoose.model('CatFancier', CatFancierSchema);

````

* notice that while name is a string, age is a number. Asking the database to store the wrong type can lead to unexpected behaviors in an application.
* When the data is stored to mongodb, each document (group of data) will be assigned an '_id' number. This _id is the most secure way to retrieve or to pass database information.

### In server.js

* In server.js the database is already connected. All that is left to do is to bring in the model so that we can start asking the database to save, update, remove or serve up information.
* At the end of the variables in server.js require CatFancier. It should look like this: const CatFancier = require('./models/CatFancier');
* Clean out the code in the post '/catimage' route, leaving just let name = req.body.name and let age = req.body.age. It should look like this:

````
app.post('/catimage', (req, res) => {
    let name = req.body.name;
    let age = req.body.age;
});

````

* Next we want to store the CatFancier in the database.
* Type in let catFancier = new CatFancier({ name, age }); This code creates a new CatFancier object that has the name and the age that are being passed in. This code could also be written let catFancier = new CatFancier({ name: req.body.name, age: req.body.age });
* Notice the catFancier, the new instance of CatFancier, has a lower case 'c'.
* Next save the information to the database with catFancier.save(); (which is really anticlimatic).
* Now the post route should look like this:

````
app.post('/catimage', (req, res) => {
    let name = req.body.name;
    let age = req.body.age;
    let catFancier = new CatFancier({ name, age });
    catFancier.save();
});

````
* Next, we want to take the information that we just saved and render it onto the 'other-page.ejs' view. This could be done by just passing in the name and the age from req.body, but what we really need to pass to 'other-page.ejs' is the _id that mongodb gives each document. If we have this _id we can use it later to call up this same document and add things to it, change it, or remove it. To make things easier to  see (maybe) we will use a callback function in catFancier.save() to do this.
* We can make a callback that takes in an error or err, and the data that we just saved. This data can be called anything; unicorn, for example. We will just call it data.
* If there is an error we want to console.log it.
* If there is no error we want name = data.name, age = data.age and id = data._id and we want to render those values in the 'other-page.ejs' view.
* The route should look like this:

````
app.post('/catimage', (req, res) => {
    let name = req.body.name;
    let age = req.body.age;
    let catFancier = new CatFancier({ name, age });
    catFancier.save((err, data) => {
        if (err) {
            console.log(err);
        } else {
            let name = data.name;
            let age = data.age;
            let id = data._id;
            res.render('layouts/other-page', {
                name,
                age,
                id 
            });
        }
    });
});

````

* Since we have no place to render the id in other-page.ejs (and that would be weird), let's find a place to store it, so that we can use it or pass it on.
* First, however, lets push this up to gitHub as "mongoose model".

### in views/layouts

#### Break for Validation

* Before storing the catFancier id, let's make sure that all the fields form get filled out. We can do this in the html. Since name and age are required fields itn the database, they will throw an error if not filled out on the form.
* In form.ejs add required to both the name and the age inputs.
* form.ejs shoud now look like this:

````
<div class="form-container">
    <h1>What is your name and age?</h1>
    <form action="/catimage" method="POST">
    <h3>Please enter your name and age</h3>
    <input id="name" type="text" name="name" placeholder="Your Name" required/>
    <input id="age" type="number" name="age" placeholder="Your Age" required/>
    <input type="submit" value="Submit">
    </form>
</div>

````

#### Back to Passing the All Important Id

* In other-page.ejs wrap the cat image in an anchor tag that leads to /third-page/<%= id %> . When the user clicks on the image it will go to a third page (that we don't need to really make) where in our case just the catFancier info will be displayed in case we did want to use it to make the page.
* The id in the parameter of the url is accessible through req.params (more on that at the route)
* In other-page.ejs make an h3 tag above the image that says "click me":
* other-page.ejs should look similar to this:

````
<div class="cat-container">
<h1 id="catTitle" data-name="<%= name %>">Hello, <%= name %>, here are your cat pics!</h1>
<h2 id="catAge" data-age="<%= age %>" >This cat is three. Our records show that you are <%= age %> years old, <%= name %>!</h2>
<br/>
<br/>
<h3>Click me!</h3>
<a href="/third-page/<%= id %>"><img src="assets/images/justaintright.gif" /></a>
</div>


````

* Because we are not building a third page, just sending json, there is no need to build a new view, however, we will need another 'get' route.

### in server.js

* Make a get '/third-page/:id' route after the post '/catimage' route. The colon ':' indicates that anything behind it is a varabiable that we can pass to our server. It will be passed as whatever name we give it after the colon. This is the request parameter and we can access it with req.params.id .
* This route should start out app.get('/third-page/:id', (req, res) => { let id = req.params.id });
* Then we call "CatFancier" to find a catFancier whose _id matches the id:
* CatFancier.findById({ _id: id });
* After comparing the ids there is the option of a callback function.
* First we will check for errors: CatFancier.findById({ _id: id }, (err, data) => { if (err) { console.log(err) })
* if there is no error, we want to pull the data from the database: CatFancier.findById({ _id: id }, (err, data) => { if (err) { console.log(err) } else { let name = data.name; let age = data.age; let id = data._id });
* Finally we want to display that information: res.json({name, age, id});
* The completed route should look like this:

````

app.get('/third-page/:id', (req, res) => {
    let id = req.params.id;
    CatFancier.findById({ _id : id }, (err, data) => {
        if (err){
            console.log(err);
        }else{
            let name = data.name;
            let age = data.age;
            let id = data._id;
            res.json({ name, age, id });
        }
    });
});

````
* This information is now available to build the third view, or can be passed on to a fourth view, etc.
* Push to gitHub as "manage data".

## Cleaning Up the Server

* The current server.js looks like this:

````

if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const expressLayouts = require('express-ejs-layouts');
const PORT = process.env.PORT;
const CatFancier = require('./models/CatFancier');

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
    let catFancier = new CatFancier({ name, age });
    catFancier.save((err, data) => {
        if (err) {
            console.log(err);
        } else {
            let name = data.name;
            let age = data.age;
            let id = data._id;
            res.render('layouts/other-page', {
                name,
                age,
                id 
            });
        }
    });
});

app.get('/third-page/:id', (req, res) => {
    let id = req.params.id;
    CatFancier.findById({ _id : id }, (err, data) => {
        if (err){
            console.log(err);
        }else{
            let name = data.name;
            let age = data.age;
            let id = data._id;
            res.json({ name, age, id });
        }
    });
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

* If there are any more routes added, the server will soon become unmanagable. Moving the routes also creates another layer of security by making them less visable.

### routes/index.js

* In the root of the application make another folder called 'routes' and in that folder make a file called 'index.js'. (You do not need to call this file index, but if later you have multiple route pages, the first page will need to be called 'index.js' -- so you are looking to the future).
* In index.js require express.Router() as router, like so : const router = ('express').Router();
* Notice express.Router() has a capital 'R' and we are saving and exporting it with a small 'r'.
* Take the database(CatFancier) from the server and require it underneath the router variable. The path might have to be adjusted, as you are now another level away from CatFancier.js
* Next export the router: module.exports = router;
* Cut the routes out of server.js and paste them between the variables and the export statement.
* Where the route reads 'app' change this to router.
* index.js should look like this:

````
const router = require('express').Router();
const CatFancier = require('../models/CatFancier');


router.get('/', (req, res) => {
    res.render('layouts/form');
});

router.post('/catimage', (req, res) => {
    let name = req.body.name;
    let age = req.body.age;
    let catFancier = new CatFancier({ name, age });
    catFancier.save((err, data) => {
        if (err) {
            console.log(err);
        } else {
            let name = data.name;
            let age = data.age;
            let id = data._id;
            res.render('layouts/other-page', {
                name,
                age,
                id 
            });
        }
    });
});

router.get('/third-page/:id', (req, res) => {
    let id = req.params.id;
    CatFancier.findById({ _id : id }, (err, data) => {
        if (err){
            console.log(err);
        }else{
            let name = data.name;
            let age = data.age;
            let id = data._id;
            res.json({ name, age, id });
        }
    });
});


module.exports = router;

````

### server.js

* In server.js, in the slot formally occupied by  the database (CatFancier), require routes as routes: const routes = require('./routes/index');
*  At the end of the middleware (the 'app.use' block) in the place formerly occupied by the routes, tell express to use the routes: app.use(routes);
* The server now looks like this:

````
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const expressLayouts = require('express-ejs-layouts');
const PORT = process.env.PORT;
const routes = require('./routes/index');

app.set('view engine', 'ejs');
app.set('views', __dirname + '/views');
app.set('layout', 'layouts/page');

app.use(express.urlencoded({
    extended: false
  }));
app.use(express.json());
app.use(express.static('public'));
app.use(expressLayouts);
app.use(routes);

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

* Push to gitHub as "Backend Step by Step"

## Almost the End

* In these three documents we have gone over the basics of creating a web page / app from the backend. Our program still lacks CRUD status (Create, Read, Update, Delete) but is finished enough for the scope of discussing the backend. To be complete, a fourth document should be elaborated later.