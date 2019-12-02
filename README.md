# pokemon_CRUD_instructions
step by step on how to make a Pokemon CRUD app using node.js, several dependencies, PostgreSQL, and a basic front end (HTML, CSS, JavaScript)

## POKEMON APP Section 1

1. Set up the server
2. Set up the initial view
3. Set up the basic database
4. Complete MVC structure for just localhost:8000/pokemon (SECTION 1)
5. Create a view & db connection to show ONE pokemon (SECTION 1.5)
6. Begin lining things up to CREATE a pokemon, create the new view and front end javascript. Create controller method and db entry (SECTION 2, BEGINNING CRUD)
7. Begin lining things up to UPDATE a pokemon, create the new view and front end javascript. Create controller method and db entry (SECTION 3)
8. Begin lining things up to DELETE a pokemon, create the front end javascript. Create controller method and db entry (SECTION 4) (COMPLETE)


Note: BEFORE UPLOADING ANY OF THIS TO GITHUB, DO THE FOLLOWING: Create a new file called “.gitignore” in a way where you can edit the text on the file (preferrably in your text editor). The only text that should exist on the file is “/node_modules”. The reason for this is that you will locally create a lot of node modules when you install dependencies to your application. These node modules are really only important for a full, published version of the application. If you upload these files to github, you may run into errors for uploading too much data at once. Having a `.gitignore` file resolves this issue. As stated later in this readme.md, you will add `--save` to the end of installing your dependencies, so that the name of the node modules are located on the package.json file that comes with any app like this. This way, if you needed to pass the application to anybody else, they can download everything (without the node_modules folder because it shouldn't be on your online repository), and run the command `npm install` in their terminal in order to auto-install all necessary dependencies.

## Setting up Server

The following commands in terminal are for the following reasons:
* Create the root folder for the application
* Navigate into the root folder while in terminal
* Create the entry file to the server
* Initialize the node app
* Create the router file to redirect users depending on where they want to navigate through the website

```bash
Mkdir pokemon_fullstack
Cd pokemon_fullstack
touch app.js
npm init -y
Touch router.js
```

The following is the normal setup/settings for the server. The number used for PORT can be anything you want, as long as it isn't occupied by another server running on the same port. All of this won't function until you install your dependencies, and get a folder called `node_modules` when it happens, we will get to this soon

```javascript
// app.js
const express        = require('express');
const mustacheExpress = require('mustache-express');
const bodyParser     = require('body-parser');
const pgp = require('pg-promise')();

const app  = express();
const PORT = process.env.PORT || 8000;

// normal setup for express & mustache (if we want to go there)
app.engine('html', mustacheExpress());
app.set('view engine', 'html');
app.set('views', __dirname + '/views');
app.use(express.static(__dirname + '/public'));

// body-parser setup
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());

// connect router.js
app.use(require('./router'));

// When the server is live, tell me in the terminal
app.listen(PORT, () => console.log('Server is listening on port', PORT));
```

The purpose of this router is to direct users to a certain page based on the url. In this case, when users go to the base URL `'/'` the application will render index.html in the views folder.

```javascript
// router.js
const router = require('express').Router();

// '/' refers to 'localhost:8000/'
router.get('/', (req, res) => res.render('index'));

// this is so that the file can be plugged into app.js when it's required
module.exports = router;
```

## Installing dependencies

Each of these installs the 4 dependencies you need at minimum to run any CRUD application

* express allows you to connect files to each other more easily, and handles any HTTP requests from the user
* mustache-express allows you to template information on webpages, to be filled in with information it gets back from the database
* body-parser allows you to easily translate data between the front end and the back end. It also helps in formatting any data you receive from an external API
* pg-promise allows you to format SQL queries and connect your application to your SQL database

```bash
npm install express --save

Npm install mustache-express --save

Npm install body-parser --save

npm install pg-promise --save
```

Side note: You add `--save` at the end so that the package.json has a way of publicly remembering the dependencies that you installed. This helps you avoid having to upload node modules to GitHub, when you upload your project. The way to avoid uploading them is to create a .gitignore file, but by default, the file will be hidden. So if you use the terminal to create the file, you might not have a way of opening the file to edit it. I suggest you use your text editor to create a new file called “.gitignore” in a way where you can edit the text on the file. The only text that should exist on the file is “/node_modules”

## Setting up a view

These commands in terminal will
* create the views folder (The V in MVC, Models Views Controllers)
* create the webpage for the base URL of your app

```bash
Mkdir views
touch views/index.html
```

Here's index.html:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="/css/style.css">
    <script type="text/javascript" src="/js/script.js"></script>

    <title>Welcome!</title>
  </head>
  <body>
    <p> This is a pokemon database thing</p>
    <a href="/pokemon">Click here to see some of them</a>
  </body>
</html>
```

Test it, you can run your application with the following command in terminal:

```bash
Node app.js
```

Or, you can go into the package.json file and add a script that says `"start": "nodemon app.js",` if you have nodemon globally installed.

After running this command, open your browser and navigate to `http://localhost:8000/` to see the website live!!

## Setup Database

Either in the terminal for Mac, or manually in the SQL shell, let’s create a database. The following commands will:

* create the psql database, in this case called `pokemon_fullstack`

* create the db folder (short for database) which will hold files directly related to the database
* create the backup SQL file that serves as a "save point" to your database.
* create the entry file to the database

```bash
createdb pokemon_fullstack

mkdir db
touch db/pokemon_seeds.sql
touch db/index.js
```

below is `db/pokemon_seeds.sql`. The purpose of this file is to reset the database to a default setting. This is done by running the file, which will erase any current data tables and recreate it from scratch. If you're on Mac, you can simply enter the command `psql -d <database_name_here> -f db/<seed_file.sql_goes_here>` from the root folder, in order to reset your database. There's a command for Windows that you can google where you need the *absolute file path* (Hint: you can get that file path by navigating into the db folder while in bash, and use the `pwd` command to get the address, which might look like `c:/..../db/pokemon_seeds.sql`

```sql
DROP TABLE IF EXISTS pokemon;

CREATE TABLE pokemon(
  id SERIAL PRIMARY KEY,
  name VARCHAR(50) NOT NULL,
  description TEXT NOT NULL,
  image VARCHAR(250) NOT NULL,
  type VARCHAR(50) NOT NULL
);

INSERT INTO pokemon (name, description, image, type) VALUES
(
  'Pikachu',
  'Pikachu (ピカチュウ Pikachuu) is an Electric-type Pokémon, which was introduced in Generation I. Pikachu is renowned for being the most well-known and recognizable Pokémon. Over the years, Pikachu has become so popular that it serves as the Pokémon franchise mascot. It is the Version Mascot for the game Pokémon Yellow. It is also well known from the anime, where Ash Ketchum, the protagonist, owns a Pikachu. It evolves from Pichu when leveled up with high friendship and evolves into Raichu when exposed to a Thunder Stone.',
  'http://vignette2.wikia.nocookie.net/pokemon/images/0/0d/025Pikachu.png/revision/latest?cb=20140328192412',
  'Electric'
),
(
  'Charmander',
  'Charmander (ヒトカゲ Hitokage) is a Fire-type Pokémon. It evolves into a Charmeleon starting at level 16. It is one of the three Starter Pokémon that can be chosen in the Kanto region.',
  'http://vignette2.wikia.nocookie.net/pokemon/images/7/73/004Charmander.png/revision/latest?cb=20140724195345',
  'Fire'
),
(
  'Squirtle',
  'Squirtle (ゼニガメ Zenigame) is a Water-type Pokémon introduced in Generation I. It evolves into Wartortle starting at level 16. It is one of the three Starter Pokémon that can be chosen in the Kanto region.',
  'http://vignette2.wikia.nocookie.net/pokemon/images/3/39/007Squirtle.png/revision/latest?cb=20140328191525',
  'Water'
),
(
  'Bulbasaur',
  'Bulbasaur (フシギダネ Fushigidane) is a Grass/Poison-type Pokémon introduced in Generation I. It evolves into Ivysaur starting at level 16. It is one of the three Starter Pokémon that can be chosen in the Kanto region.',
  'http://vignette4.wikia.nocookie.net/pokemon/images/2/21/001Bulbasaur.png/revision/latest?cb=20140328190757',
  'Grass'
),
(
  'Hitmonlee',
  'Hitmonlee (Japanese: サワムラー Sawamuraa) is a Fighting-type Pokémon introduced in Generation I. According to the Pokédex, its nickname is "The Kicking Fiend".',
  'http://vignette1.wikia.nocookie.net/pokemon/images/3/32/106Hitmonlee.png/revision/latest?cb=20140328205305',
  'Fighting'
),
(
  'Vulpix',
  'Vulpix (Japanese: ロコン Rokon) is a Fire-type Pokémon introduced in Generation I, and an Ice-type introduced in Generation VII which is exclusive to Pokémon Sun.',
  'http://vignette4.wikia.nocookie.net/pokemon/images/6/60/037Vulpix.png/revision/latest?cb=20140328193205',
  'Fire'
),
(
  'Oddish',
  'Oddish (Japanese: ナゾノクサ Nazonokusa) is a Grass/Poison-type Pokémon introduced in Generation I.',
  'http://vignette1.wikia.nocookie.net/pokemon/images/4/43/043Oddish.png/revision/latest?cb=20140328194044',
  'Grass'
),
(
  'Jolteon',
  'Jolteon (Japanese: サンダース Sandaasu) is a Electric-type Pokémon introduced in Generation I.',
  'http://vignette1.wikia.nocookie.net/pokemon/images/b/bb/135Jolteon.png/revision/latest?cb=20140328210733',
  'Electric'
),
(
  'Lapras',
  'Lapras are a very large Water/Ice type Pokemon that resemble a plesiosaur. Its skin is mainly a light blue with a cream/white underside. It has a very long neck with a rounded head. Its ears are shaped like spirals and it has a small horn on its forehead. Having no legs, it instead has four flippers for easy mobility in the water. Lapras also have large, gray shells on their backs with "knobs" covering them.',
  'http://vignette2.wikia.nocookie.net/pokemon/images/a/ab/131Lapras.png/revision/latest?cb=20140328210730',
  'Water'
),
(
  'Machop',
  'Machop is a bluish-gray Pokémon with large arm muscles. It has three brown ridges on its head, rib-like protrusions on its sides, has a tail, and resembles a human child.',
  'http://vignette3.wikia.nocookie.net/pokemon/images/8/85/066Machop.png/revision/latest?cb=20140328203356',
  'Fighting'
),
(
  'Slowpoke',
  'Slowpoke is a mostly pink Pokémon that doesnt look much like a water-type Pokémon at all. It stands on four legs, and has a long tail that is used like a fishing pole. Its mouth is a pale brown color.',
  'http://vignette3.wikia.nocookie.net/pokemon/images/7/70/079Slowpoke.png/revision/latest?cb=20140328203941',
  'Water'
);
```

The following file is the entry point to your database. I cannot emphasize enough to read the file, and add your user name to the database URL. It might take some playing around until you get it right if you're on windows, but on Mac the name should be your computer's username

```javascript 
// db/index.js
const pgp = require('pg-promise')();

const db = pgp(
	process.env.DATABASE_URL
	// modify the following string with your user name:
  // || 'postgres://<username>@localhost:5432/<database name>');
	|| 'postgres://briancarela@localhost:5432/pokemon_fullstack');


module.exports = db;
```

**DON’T FORGET TO EDIT THE ADDRESS TO YOUR COMPUTER’S USER NAME**

## Setup pipeline to view the database as a user - Router & View

First let’s set up a route and a view

```javascript
// router.js, ABOVE the router.get ‘/‘
router.use('/pokemon', require('./controllers/pokemon'));
```

When we make a new URL extension to arrive at, you want to make an entire folder for it in the views

```bash
Mkdir views/pokemon
touch views/pokemon/index.html
```

Thanks to mustache express, we can 

```html
<!-- /views/pokemon/index.html -->
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>ALL POKEMON</title>
  </head>
  <body>
    <h1>Pokemon!</h1>
    <a href="/pokemon/new">Create new Pokemon</a>

    {{ #pokemon }}
      <a href="/pokemon/{{ id }}">
        <h3>{{ name }}</h3>
        <img src={{ image }} />
      </a>
    {{ /pokemon }}
  </body>
</html>

```

Setup pipeline to view the database as a user - Controller

```bash
Mkdir controllers
Mkdir controllers/pokemon
Touch controllers/pokemon/index.js
Touch controllers/pokemon/controller.js
```


```javascript
// controllers/pokemon/index.js
const router = require('express').Router();
const controller = require('./controller');

router.get('/', controller.index);

module.exports = router;
```

```javascript
// controllers/pokemon/controller.js

const Pokemon = require('../../models/pokemon');

const controller = {};

controller.index = (req, res) => {
  Pokemon
    .findAll()
    .then((data) => {
      res.render('pokemon/index', {pokemon: data});
    })
    .catch(err => console.log('ERROR:', err));
};

module.exports = controller;
```

Setup pipeline to view the database as a user - Model

```bash
Mkdir models
Touch models/pokemon.js
```

```javascript
// models/pokemon.js
const db = require('../db');

const Pokemon = {};

Pokemon.findAll = () => {
  return db.manyOrNone('SELECT * FROM pokemon');
};

module.exports = Pokemon;
```

OK TEST IT

```bash
node app.js
```

## Section 1.5, displaying 1 Pokemon at a time

Ok so we need a new page to view, if we want to see just 1 pokemon in the database, so we build that path out inside files that mostly already exists. Section 2 is building out a path to respond to URL requests with database queries, which also means setting up some front end javascript with FETCH in order to make URL requests upon submitting the new Pokemon form. Here’s a list of actions that need to be completed

- Create a view to show 1 Pokemon
- Create a “find by ID” method (SQL query) on /models/pokemon.js
- Create a route on /controllers/pokemon/index.js to listen to users arriving at localhost:8000/pokemon/:id (RESTful Routing notes*)
- Create a controller method on /controllers/pokemon/controller.js to handle the URL request, which means access the model method, and respond to the user with html

The View

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <script type="text/javascript" src="/js/script.js"></script>
    <title></title>
  </head>
  <body>
    <a href="/pokemon/">Back to all Pokemon</a>
    <hr />

    <h5>{{ name }} - {{ type }} Type Pokemon</h5>
    <img src={{ image }} />

    <p>{{ description }}</p>
    <hr />

    <a href="/pokemon/{{ id }}/update">Edit this Pokemon</a>
    <button class="delete-pokemon" data-id={{ id }}>DELETE THIS POKEMON? ARE YOU SURE?</button>
  </body>
</html>
```

Note the edit link and the delete, which we will come back to. 
The Model’s SQL query

```javascript
Pokemon.findById = (id) => {
  return db.one(
    'SELECT * FROM pokemon WHERE id=$1',
    [id]
  );
};
```

Talk about .one() and how $1 and the array work together

The URL route

```javascript
router.get('/:id', controller.show);
```

Put this above the router.get(‘/‘, controller.index); so you can demonstrate why RESTful routing is important.
Also talk about how the controller method name should reflect the html file name, as per naming convention

The Controller Method

```javascript
controller.show = (req, res) => {
  const id = req.params.id;
  Pokemon
    .findById(id)
    .then((data) => {
      res.render('pokemon/show',data);
    })
    .catch(err => console.log('ERROR:', err));
};
```

Pokemon.findById is the query, you use the id that’s entered in the URL, it responds with html and includes the data that comes back from the database

TEST IT

So far in terms of the controllers, there only exists a path for the app to respond to URL requests with a View, and not a /controllers/api/ path for database responses to URL requests


## SECTION 2: Create to the database


Creating to the database from this point is going to take some more setup than usual. Here’s a checklist of things needed:

- A view with a form to create new things to the database, and a full path to arrive to the page (controller method)
- Some front end JavaScript to take the values of the form, and send them to the database
- A Route on router.js at the root of the app, in order to listen to these API POST requests
- Create /controllers/api/ to keep our concerns separated 
- Create /controllers/api/index.js to handle routing on API requests
- Create /controllers/api/controller.js and a method to handle data being sent to the database
- Create a method on /models/pokemon.js which includes a SQL query that will speak to the database

View with an input form

```bash
Touch views/pokemon/new.html
```

```html
<!DOCTYPE html>
<html>
  <!-- views/pokemon/new.html -->
  <head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="/css/style.css">
    <script type="text/javascript" src="/js/script.js"></script>
    <title>New Pokemon</title>
  </head>
  <body>
    <h5> Create a new pokemon </h5>

    <form id="new-pokemon-form">
      <label>Name:</label>
      <input class="poke-name-input" />
      <br />

      <label>Description:</label>
      <textarea class="poke-description-input"></textarea>
      <br />

      <label>Image:</label>
      <input class="poke-image-input" />
      <br />

      <label>Type:</label>
      <input class="poke-type-input" />
      <br />

      <button type="submit">Submit</button>
    </form>
  </body>
</html>
```

Classify each of the buttons as plainly as possible, so it’s easy to target from the front-end javascript. We will come back to it, but let’s create a path to arrive at this page first

Since controllers/pokemon deals with handing users the Views, we don’t need to create anything on router.js just yet. Let’s add this line to controllers/pokemon/index.js

```javascript
// controllers/pokemon/index.js
router.get('/new', controller.new);
```

Place this below the ‘/‘ and above the ‘:id’ listeners, according to RESTful routing

```javascript
// controllers/pokemon/controller.js
controller.new = (req, res) => {
  res.render('pokemon/new');
};
```

Aaaaand that’s it! Let’s TEST THIS NEW PAGE

After connecting to the page, let’s build a path to post to the database via the back end, and then build out the front end bridge 

```bash
mkdir controllers/api
touch controllers/api/index.js controllers/api/controller.js
```

```javascript
// router.js
router.use('/api', require('./controllers/api'));
```

Place that line btwn ‘/‘ and ‘pokemon’

```javascript
// controllers/api/index.js
const router = require('express').Router();

const controller = require('./controller');

router.post('/pokemon', controller.create);

module.exports = router;
```

If you read the extension correctly, you’ll notice that the full URL to this route would be “ localhost:8000/api/pokemon ”. The reason for the /api extension is that this ENTIRE ROUTE is dedicated to reaching the database from the front end. The /pokemon extension is because this lane is dedicated to all things related to the pokemon table in the database. When you have other data or other functionality in your application, you should name it to what it’s related to

```javascript
// controllers/api/controller.js
const Pokemon = require('../../models/pokemon');

const controller = {};

controller.create = (req, res) => {
  const name = req.body.name,
        description = req.body.description,
        image = req.body.image,
        type = req.body.type;

  Pokemon
    .create(name, description, image, type)
    .then(data => res.json(data))
    .catch(err => console.log('ERROR:', err));
};

module.exports = controller;
```

…What is Pokemon.create()? Does the Model have a SQL query lined up to actually post to the database yet? Let’s get that done!

```javascript
Pokemon.create = (name, description, image, type) => {
  return db.one(
    'INSERT INTO pokemon (name, description, image, type) VALUES ($1, $2, $3, $4) returning id',
    [name, description, image, type]
  );
};
```
OK the full path is available, and now if we POST from the front end to localhost:8000/api/pokemon with the proper data (name, description, image, type) we can access the database. But how do we do that??

```bash
Mkdir public public/js
Touch public/js/script.js
```
____________________________________________________________________________________________________________________


Let’s first make a function that matches the pokemon form in HTML, and simply packs up the data into a neat object to be used for later

```javascript
// public/js/script.js
function packUpForm(){
  // Select the 4 sections of the form's value
  const name = document.querySelector('.poke-name-input').value,
        description = document.querySelector('.poke-description-input').value,
        image = document.querySelector('.poke-image-input').value,
        type = document.querySelector('.poke-type-input').value;

  // turn it into an object
  const pokeData = {name: name, description: description, image: image, type: type};

  return pokeData;
}
```

Now let’s try to use fetch

```javascript
window.onload = function() {
  document.getElementById("new-pokemon-form").addEventListener("submit", function(e){
    e.preventDefault();
    // call the backend URL that we set up, AND call it response so we can do something with it
    fetch('/api/pokemon', {
            method: 'post',
            body:    JSON.stringify(packUpForm()),
            headers: { 'Content-Type': 'application/json' }
    })
    .then(response => {
        // resolve the promise
        return response.json();
    })
    .then(data =>{
      console.log("data: ", data);
      window.location.replace('/pokemon/' + data.id);
    })
    .catch(err => console.error("fetch error: ", err)); // end of functional instructions
  }); // end of event listener
}; // end of window.onload
```

Proper url, method is post, the body we’re sending comes from our other function, then upon success we go to the next page (the response comes with the ID of the new entry in the database, so we use that to automate going to the newly created Pokemon’s page)

Also note that all of this is defined unload, because if we try to add an event listener to an element in the DOM before the user/browser gets it, the element comes up as null, and we can’t attach functionality to the element.

LET’S TRY MAKING A NEW POKEMON

## Section 3: Update the database

Now that we can create new Pokemon and add them to the database, what happens if we misspell something, or learn something new about them? We should be able to update them! Here are the following things we need to build out to be able to update the database:

- Views/pokemon/update.html should be created for the user to fill out the form
- Controllers/pokemon/index.js should be updated with a new route (URL)
- Controllers/pokemon/controller.js should be updated with a method to bring the edit page pre-filled with the original content from the database
- Public/js/script.js should be updated with a way to handle form submission, and send that data to the database. On success, return to the show.html
- Controllers/api/index.js should be updated with a new backend route
- Controllers/api/controller.js should be updated with a method to send the updated info to the backend
- Models/pokemon.js should be updated with a SQL query that communicated the updated to the database, and return the ID to redirect the user to show.html

```html
<!- -  views/pokemon/update.html  - - >
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <script type="text/javascript" src="/js/script.js"></script>
    <title></title>
  </head>
  <body>
    <h5> Edit {{ name }} </h5>

    <form id=“update-pokemon-form">
      <label>Name:</label>
      <input class="poke-name-input" value={{ name }} />
      <br />

      <label>Description:</label>
      <textarea class="poke-description-input">
        {{ description }}
      </textarea>
      <br />

      <label>Image:</label>
      <input class="poke-image-input" value={{ image }} />
      <br />

      <label>Type:</label>
      <input class="poke-type-input" value={{ type }} />
      <br />

      <input type="hidden" id="poke-id-input" value={{ id }} />

      <button type="submit">Submit</button>
    </form>
  </body>
</html>
```

Notice that the classes match our front-end javascript, so we already have a function to package up this form information. This is separate from the function to add to the database, so that we can simply re-use some of the script. The id for the FORM itself is different, so that we can make a new function for this form specifically. We will also include the use of the hidden input above the button, which is to help pass us the id of the pokemon in this case.

```javascript
// controllers/pokemon/index.js
router.get('/:id/update’, controller.update);
```
Note that this route should go btwn the ‘/new’ route and the ‘/:id’ route. Because if it’s below the ‘:id’, it will always redirect to show and you’ll never get to edit.

```javascript
// controllers/pokemon/controller.js
controller.edit = (req, res) => {
  const id = req.params.id;
  Pokemon
    .findById(id)
    .then(data => {
      res.render('pokemon/update’, data);
    })
};
```
We query the database to find this Pokemon’s info so that we can auto fill the form on the next page with the current information. You can have plain text to show the previous information as well, but we can worry about that when we want to make the app a better experience for the user. Right now we’re just building out an MVP (Minimal Viable Product) so as long as everything works at it’s bare minimum without error, we’re concerned about the right things!

```javascript
// public/js/script.js
window.onload = function() {
  document.getElementById(“update-pokemon-form").addEventListener("submit", function(e){
    let id = document.getElementById(“poke-id-input”).value;
    let dataPackage = packUpForm();
    dataPackage.id = id
    e.preventDefault();
    // call the backend URL that we set up, AND call it response so we can do something with it
    fetch(‘/api/pokemon/' + id, {
            method: 'put',
            body:    JSON.stringify(dataPackage),
            headers: { 'Content-Type': 'application/json' }
    })
    .then(response => {
        // resolve the promise
        return response.json();
    })
    .then(data =>{
      console.log("data: ", data);
      window.location.replace('/pokemon/' + data.id);
    })
    .catch(err => console.error("fetch error: ", err)); // end of functional instructions
  }); // end of event listener
}; // end of window.onload
```

This time, we added the line `let id = document.getElementById(“poke-id-input”).value;` so that we can pass in the id dynamically into the url. Then we stored up the object we’re going to send to the backend, so we can attach the id. You can think of this as adding someone’s name to a package before mailing it away!

```javascript
// controllers/api/index.js
router.put('/pokemon/:id', controller.update);
```

This is listening to localhost:8000/api/pokemon/:id for the update and will use the following method:

```javascript
// controllers/api/controller.js
controller.update = (req, res) => {
  const name = req.body.name,
             description = req.body.description,
             image = req.body.image,
             type = req.body.type,
             id = req.body.id;

  Pokemon
    .update(name, description, image, type, id)
    .then(data => res.json(data))
    .catch(err => console.log('ERROR:', err));
};
```

By using Pokemon.update we should be updating that method in the Model!

```javascript
// models/pokemon.js
Pokemon.update = (name, description, image, type, id) => {
  return db.one(
    'UPDATE pokemon SET name = $1, description = $2, image = $3, type = $4 WHERE id = $5 returning id',
    [name, description, image, type, id]
  );
};
```

As usual, note the ‘returning id’ right at the end of the query

ANYWAYS, IT’S TIME TO TEST THAT THIS WORKS OUT. Try editing Pikachu!! You can always re-seed the database to start over

## Section 4: Deleting from the database

Finally we’re almost done! Time to put the D in CRUD app. Here are the following actions to be taken:

- Public/js/script.js should be edited (with show.html in mind) so that the button will POP UP A CONFIRM. If true, fetch(URL, { method: ‘delete’}. If false, do nothing
- Controllers/api/index.js should have a router.delete method
- Controllers/api/controller.js should have a controller method that activates the query to the database
- Models/pokemon.js should have that database query ready to delete a pokemon based on id. Db.none()
