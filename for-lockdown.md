# Backend step-by-step Simple Server
* with MongoDB already installed on computer
## Make repository
* On the desktop, make a new folder, and give it the name that you will use for your app.
* Go to gitHub and create a new repository that has the exact same name as the folder that you created in your desktop.
* gitHub will return something like the following:
````
…or create a new repository on the command line
echo "# for-lock-down" >> README.md
git init
git add README.md
git commit -m "first commit"
git remote add origin git@github.com:youraccountreponame/for-lock-down.git
git push -u origin master               
…or push an existing repository from the command line
git remote add origin git@github.com:youraccountreponame/for-lock-down.git
git push -u origin master
````
* Open your desktop folder with VSCode and open the terminal. Copy and paste the first section of code "create a new repository on the command line" on  the command line starting with 'git init' and ending with 'git push -u origin master'(can do all at one time).
* Follow the prompt and enter your git password (you will not be able to see on the terminal what you are entering).
* You can now push changes to your repo up to gitHub.
## Initialize NPM, create package.json, install dependencies
* In terminal type in: npm init -y
* A package.json file will now appear in your repository.
* In terminal type: npm i express mongoose and push enter. Npm should install express and mongoose, create a package-lock.json and node_modules.
* In terminal type: npm i -D nodemon dotenv and push enter. Npm should install nodemon and dotenv as dev dependencies.
* In the package.json create two start scripts. In the "scripts" object, add a comma after the test scripts. Add "start": "node server.js" and "dev": "nodemon server.js". It should look like this:
````
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
````
* Create a file called server.js
* Create a file called .env and another file called .gitignore
* In the .gitignore file type in .env and node_modules (this will prevent them from being pushed to gitHub).
* In terminal type in: git add .  (!! the period is important !!- it means git add everything in folder) and push enter.
* Type in terminal: git commit -m "set up" (the string is obligatory, it is what you will call your commit) and push enter.
* Type in terminal: git push and press enter. You will be prompted to type in your gitHub password and will not be able to see what you are typing. Enter password and press enter.
* On gitHub you will see that in your repository you have a .gitignore, a README.md, a package-lock.json, a package.json and a server.js. Other than the README.md which should be called "first commit" the rest of the files should be labelled "set up" or whatever you put in your git commit -m string.
## Server
### Very Basic Server and the '/' Route
*In server.js create the following variables:
````
const express = require('express');
const app = express();
const PORT = 3000;
````
* A few lines below these variables type in: app.listen(PORT, () => { console.log("a message " + PORT); });
* In the terminal type in npm run dev. Your server should start with nodemon and the message, along with the port should be displayed in the terminal.
* In between your variables and the 'app.listen' type in app.get('/', (req, res) => { res.send("Hello"); }); 
* req means request and res means response
* Open your browser to http://localhost:3000 , "Hello" should be displayed (but not as a string).
* Your entire server should look something like this:
````
const express = require('express');
const app = express();
const PORT = 3000;
app.get('/', (req, res) => { res.send("Hello"); })
app.listen(PORT, () => { console.log("Tiny electronic ears are listening on port " + PORT); });
````
* close out of server (in terminal press control c and then enter Y)
* Push the server to gitHub:
1. git add . (press enter)
2. git commit -m "very basic server" (press enter)
3. git push (press enter)
4. enter password (will not be able to see -press enter)
* On gitHub if you open the server.js file you will see that the contents have been pushed up to gitHub
### Keeping Secrets!
* In order to keep port numbers, API keys, database URIs or encryption keys a secret in a development environment, they will be saved in a .env folder. This prevents them from being pushed to a public access area such as gitHub. Later, in production, these variables will be stored on the host server.
* At the very first line of your server, before the variables are declared, type in const env = require('dotenv').config();
* Change the variable PORT to const PORT = process.env.PORT; (this tells the server to look in the .env file for PORT).
* In the .env file enter PORT=3000 (just like that: no spaces, no quotes, no semi-colon).
* Restart your server. It should run just as before.
* Close server ( control c ) and push to gitHub saving the commit as "dotenv". Check to see that your port is hidden.
* Your server.js file should now be labeled "dotenv" and inside should show the following:
````
const env = require('dotenv').config();
const express = require('express');
const app = express();
const PORT = process.env.PORT;
app.get('/', (req, res) => { res.send("Hello"); });
app.listen(PORT, () => { console.log("Tiny electronic ears are listening on port " + PORT); });
````
### Know Your Environment! 
* Because you are working on your own computer and have not pushed the site to an external host, you need to control the variables such as the port and database URI, and you also need to control where you keep secret variables. This is the development or 'dev' environment. When you push your site to an external host they will assign you ports and if  you have been using a local (on your computer) database, you will need to use an external database URL. You will also be asked to store any secrets currently in your .env file on your host site (they will provide the means to do this).This is called the production environment. The next step is to let your server know to use the variables provided if in a dev environment and use the host server variables if in production.
* Wrap the env declaration in your server in an if statement. The first line of your server code should now read: 
````
if (process.env.NODE_ENV !== 'production') { require('dotenv').config(); }
````
* This code means that if the environment is not production (therefore, development) use the .env file.
* The PORT variable will not have to change because in production it will just be reassigned using process.env
* Start server and check that everything is working the same.
* Push to gitHub as "environmental variables"
## Connect to the Database
* Mongoose is the name of the middleware which comunicates between your server and the mongodb database.
* Require 'mongoose' in the variable section of server.js.
* In between the route '/' and the app.listen in server.js, type mongoose.connect()
* The fist parameter will be the mongodb URI. Because we are using a local instance of Mongodb (one that is installed on  our computers) we will give an 'or' statement so that mongoose connects to one in development and another in production.
* The first parameter will be mongoose.connect(process.env.DATABASE_URL || process.env.MONGODB_URI) This tells mongoose to either use the variable in the .env file or to use the URL provided to the host server.
* In your .env file create a variable called DATABASE_URI Your .env file should look like this:
````
PORT=3000
MONGODB_URI=mongodb://localhost/backendexplained
````
* This connects monogdb to your computer(localhost) and creates a collection called "backendexplained" to the database.
* The second parameter to the connection is an object that sets or changes the defaults to some mongoose methods. This will avoid deprecation warnings in the terminal (which don't really stop the program from working, yet still should be eliminated).
* Mongoose has several deprecation warnings (perhaps so that they can update the middleware without doing a major update that would cause problems with servers made before the update). Therefore, some of the following code is copy and paste.
* Your mongoose.connect() in server.js should now look like this:
````
mongoose.connect(process.env.DATABASE_URL || process.env.MONGODB_URI,
    {useNewUrlParser: true,
     useUnifiedTopology: true,
     useFindAndModify: false,
     useCreateIndex: true});
````
* The third parameter in mongoose.connect() is an anonymous function that allows you to display an message in the terminal.
* after the deprecation object type a comma and then () => { console.log("my database is connected") }
* Your mongoose.connect() should now look like this:
````
mongoose.connect(process.env.DATABASE_URL || process.env.MONGODB_URI, {
        useNewUrlParser: true,
        useUnifiedTopology: true,
        useFindAndModify: false,
        useCreateIndex: true},
    () => {console.log("My database is connected"); });
````
* push your work up to gitHub, save the commit as "database connected". Inside the server.js it should look like this:

````
if (process.env.NODE_ENV !== 'production') { require('dotenv').config(); }
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const PORT = process.env.PORT;
app.get('/', (req, res) => { res.send("Hello"); });
mongoose.connect(process.env.DATABASE_URL || process.env.MONGODB_URI, {
        useNewUrlParser: true,
        useUnifiedTopology: true,
        useFindAndModify: false,
        useCreateIndex: true},
    () => {console.log("My database is connected"); });
app.listen(PORT, () => {console.log("Tiny electronic ears are listening on port " + PORT); });
````
## Serve up some HTML
* Create a new folder called 'public'.
* Inside the public folder create a file called 'index.html'.
* In the html file create an h1 tag that says "hello Word" and an h2 tag that says "This is html"
* In the server.js file, change the get '/' route so that it does the following: res.sendFile(__dirname + '/public/index.js')
* Your get route should look like this:
````
app.get('/', (req, res) => { res.sendFile(__dirname + '/public/index.html'); });
````
* This code tells the server that in response to the get request to send a file called "index.html" which is in the 'public' folder.
* Start server and go to http://localhost:3000 and you should see your HTML displayed
* Stop server and save the changes to gitHub. Call the commit "serving html". In gitHub, your server should now look like this:
````
if (process.env.NODE_ENV !== 'production') { require('dotenv').config(); }
const express = require('express');
const app = express();
const mongoose = require('mongoose');
const PORT = process.env.PORT;
app.get('/', (req, res) => { res.sendFile(__dirname + '/public/index.html'); });
mongoose.connect(process.env.DATABASE_URL || process.env.MONGODB_URI, {
        useNewUrlParser: true,
        useUnifiedTopology: true,
        useFindAndModify: false,
        useCreateIndex: true},
    () => {console.log("My database is connected"); });
app.listen(PORT, () => { console.log("Tiny electronic ears are listening on port " + PORT); });
````


	


