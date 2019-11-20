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


Create a new file called “.gitignore” in a way where you can edit the text on the file. The only text that should exist on the file is “/node_modules”
Begin

Setting up Server

```bash
Mkdir pokemon_fullstack
Cd pokemon_fullstack
touch app.js
npm init -y
Touch router.js
```
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

// connect router
app.use(require('./router'));

app.listen(PORT, () => console.log('Server is listening on port', PORT));
```

```javascript
// router.js
const router = require('express').Router();

router.get('/', (req, res) => res.render('index'));

module.exports = router;
```

Installing dependencies

```bash
npm install express --save

Npm install mustache-express --save

Npm install body-parser --save

npm install pg-promise --save
```
Side note: You add `--save` at the end so that the package.json has a way of publicly remembering the dependencies that you installed. This helps you avoid having to upload node modules to GitHub, when you upload your project. The way to avoid uploading them is to create a .gitignore file, but by default, the file will be hidden. So if you use the terminal to create the file, you might not have a way of opening the file to edit it. I suggest you use your text editor to create a new file called “.gitignore” in a way where you can edit the text on the file. The only text that should exist on the file is “/node_modules”

Setting up a view

```bash
Mkdir views
touch views/index.html
```

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <link rel="stylesheet" href="/css/style.css">
    <script type="text/javascript" src="/js/jquery.js"></script>
    <script type="text/javascript" src="/js/script.js"></script>

    <title>Welcome!</title>
  </head>
  <body>
    <p> This is a pokemon database thing</p>
    <a href="/pokemon">Click here to see some of them</a>
  </body>
</html>
```
Test it

```bash
Node app.js
```

Or, you can go into the package.json file and add a script that says `"start": "nodemon app.js",` if you have nodemon globally installed.

Setup Database

Either in the terminal for Mac, or manually in the SQL shell, let’s create a database 

```bash
createdb pokemon_fullstack

mkdir db
touch db/pokemon_seeds.sql
touch db/index.js
```

below is `db/pokemon_seeds.sql`

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

```javascript 
// db/index.js
const pgp = require('pg-promise')();

const db = pgp(
	process.env.DATABASE_URL
	// modify the following string with your user name:
  // || 'postgres://<username>@localhost:5432/<database name>');
	|| 'postgres://briancarela@localhost:5432/pokemon_fullstack’);


module.exports = db;
```

DON’T FORGET TO EDIT THE ADDRESS TO YOUR COMPUTER’S USER NAME

Setup pipeline to view the database as a user - Router & View

First let’s set up a route and a view

```javascript
// router.js, ABOVE the router.get ‘/‘
router.use('/pokemon', require('./controllers/pokemon'));
```

When we make a new URL extension to arrive at, you want to make an entire folder for it

```bash
Mkdir views/pokemon
touch views/pokemon/index.html
```



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

    <a href="/pokemon/{{ id }}/edit">Edit this Pokemon</a>
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
