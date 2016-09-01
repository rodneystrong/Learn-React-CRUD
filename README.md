# Learn-React-CRUD
Learn CRUD endpoint calls with React

# Setting up webpack
1. I highly recommend you copy the package.json and webpack.config.js files and put them in a "resource" folder on your desktop. They are setup to be put into any new project
-- We are going to modify the package.json and webpack.config.js files so they are connected to this project's current file structure
2. In your package.json we have the option to edit the name, description, and author keys (It isn't necessary) They are located on lines 2, 4, and 9
-- However the webpack.config.js file changes ARE necessary. This file tells webpack where to output bundle.js and the entry point for webpack.
3. In your webpack.config.js change line 3 to match the location of your reactApp.js file. The file location is based off of webpack.config.js' location. ( './public/app/reactApp.js' )
4. In your webpack.config.js change line 7 to match the location of your scripts folder. The file location is based off of webpack.config.js location. ( './public/script' )


# Setting up your react app
1 - Every react app, at the bare minimum needs, React and ReactDOM imported. On the very first two lines of code import them. Also for this projet we'll need axios to make backend calls, import that as well.
~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import axios from 'axios';
~~~~
2 - We're now ready to create our App using var.
~~~~
var App = React.createClass({

})
~~~~
3 - React apps need a render method that returns the html that gets put on the page. Create a render method for our app
~~~~
var App = React.createClass({
  render() {
    return (
      <div>
        <p>Hello World</p>
      </div>
    )
  }
})
~~~~
4 - To initialize our React application you have to use ReactDOM with the render method.  
~~~~
ReactDOM.render(<App />, document.getElementById('app'));
~~~~
5 - Now the basics of our react app are ready to go. Setup a basic html file and include at least one div in the body with the id of 'app'. We have to use 'app' because our initialize render method is looking for a document with element id of 'app' You can also change this to whatever you like. In addition include a script tag with a src attribute equal to the directory of where webpack will send bundle.js
~~~~
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>React CRUD</title>
  </head>
  <body>
    <div id='app'></div>
    <script src="./script/bundle.js"></script>
  </body>
</html>
~~~~
6 - Remember to run webpack to see any react changes or run `webpack -w` so webpack will re-run when you edit the reactApp.js file
7 - Live-server or open your index.html and you should see Hello World on the page

# Setting up your server and database
1 - Using node create a basic backend. Make sure to install express, body-parser, massive and cors at the bare minimum
`npm install --save express body-parser massive cors`

2 - Your server.js file should look similar to this after Setup
~~~~
var express = require('express');
var bodyParser = require('body-parser');
var cors = require('cors');
var massive = require('massive');
var connectionString = 'postgress://jameslemire@localhost/sandbox'
var massiveInstance = massive.connectSync({connectionString : connectionString});
var app = express();
app.set('db', massiveInstance);

app.use(cors({origin: 'http://localhost:3000/'}));
app.use(bodyParser.json());
app.use(express.static('../public'));

app.listen(3000, function() { console.log('Server started on port 3000'); });
~~~~
3 - Create a table named people `CREATE TABLE people (id SERIAL PRIMARY KEY, first_name VARCHAR(50), last_name VARCHAR(50) );`

4 - Don't forget to make a db folder in the same directory as your server.js file

#Setting up your endpoints
-- Now we're ready to make some CRUD endpoints in our server. (Create, Read, Update, Delete)

1 - Create four basic end points that follow CRUD with four sql commands. Your code should look similar to this

#### server.js
~~~~
app.post('/api/add/person', function(req, res) {
  db.addPerson([req.body.first_name, req.body.last_name], function(err, r) {
    res.status(200).send('Person created');
  });
})

app.get('/api/people', function(req, res) {
  db.getPeople(function(err, r) {
    res.json(r);
  });
});

app.post('/api/update/person', function(req, res) {
  db.updatePerson([req.body.first_name, req.body.last_name], function(err, r) {
    res.status(200).send('Person updated');
  })
})

app.delete('/api/person/:personId', function(req, res) {
  db.deletePerson([req.params.personId], function(err, r) {
    res.status(200).send('Person deleted');
  })
})
~~~~

#### SQL commands
addPerson.sql
~~~~
INSERT INTO people (first_name, last_name) VALUES ($1, $2);
~~~~
getPeople.sql
~~~~
SELECT * FROM people;
~~~~
updatePerson.sql
~~~~
UPDATE people SET
first_name = $2,
last_name $3
WHERE id = $1;
~~~~
deletePerson.sql
~~~~
DELETE FROM people WHERE id = $1;
~~~~

#Friendly Reminder
-- Don't forget to pack your webz (shoutout to Heather). Meaning don't forget to run webpack when you're trying to see changes you make on your react app. Also remember we need our server running to make the api calls. Which means we also need postgres running. (What a handful..)
#Setting up the front-end
-- In order to get our endpoints to work with our front end we are going to need some inputs and a button

1 - Add two inputs (first name, last name) and a button (add person)
~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import axios from 'axios';

var App = React.createClass({
  render() {
    return (
      <div>
        <h1>Add Person</h1>
        <input type="text" />
        <input type="text" />
        <button>Add Person</button>
      </div>
    )
  }
})

ReactDOM.render(<App />, document.getElementById('app'));
~~~~
2 - Using getInitialState() add variables to store the array of people, first_name input, and last_name input.
~~~~
getInitialState() {
  return {
    first_name: '',
    last_name: '',
    people: []
  }
},
~~~~
3 - Using `onChange={}` update these variables while the user types in either input.
~~~~
<input onChange={this.first_nameCatcher} type="text" />
<input onChange={this.last_nameCatcher} type="text" />

first_nameCatcher(event) {
  this.setState({
    first_name: event.target.value
  })
},
last_nameCatcher(event) {
  this.setState({
    last_name: event.target.value
  })
},
~~~~
-- Your code will now look something like this
~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import axios from 'axios';

var App = React.createClass({
  getInitialState() {
    return {
      first_name: '',
      last_name: '',
      people: []
    }
  },
  render() {
    return (
      <div>
        <h1>Create Person</h1>
        <input onChange={this.first_nameCatcher} type="text" />
        <input onChange={this.last_nameCatcher} type="text" />
        <button>Add Person</button>
      </div>
    )
  },
  first_nameCatcher(event) {
    this.setState({
      first_name: event.target.value
    })
  },
  last_nameCatcher(event) {
    this.setState({
      last_name: event.target.value
    })
  }
})

ReactDOM.render(<App />, document.getElementById('app'));
~~~~

#CRUD - Create
1 - Using `onClick={}` make an api call to add a person. Make sure to pass the data in the call, axios works just like $http (how freaking cool!)
~~~~
<button onClick={this.createPerson}>Create Person</button>

createPerson() {
  var person = {first_name: this.state.first_name, last_name: this.state.last_name}
  axios({
    method: 'POST',
    url: 'http://localhost:3000/api/add/person',
    data: person
  })
}
~~~~

#CRUD - Read
1 - To read our database we can use another `onClick={}` to make an api call to our backend. Then using a map function we can display the users it finds
~~~~
<button onClick={this.getPeople}>Get People</button>

getPeople() {
  axios({
    method: 'GET',
    url: 'http://localhost:3000/api/people'
  }).then(r => {
    this.setState({
      people: r.data
    })
  });
}

{this.state.people.map( (value, index) => {
  return (
    <p key={index}>{value.first_name} {value.last_name}</p>
  )
})}
~~~~
-- Your code you should now look something like this
~~~~
import React from 'react';
import ReactDOM from 'react-dom';
import axios from 'axios';

var App = React.createClass({
  getInitialState() {
    return {
      first_name: '',
      last_name: '',
      people: []
    }
  },
  render() {
    return (
      <div>
        <h1>Create Person</h1>
        <input onChange={this.first_nameCatcher} type="text" />
        <input onChange={this.last_nameCatcher} type="text" />
        <button onClick={this.createPerson}>Create Person</button>
        <br></br>
        <br></br>
        <h1>Read People</h1>
        <button onClick={this.readPeople}>Read People</button>
        {this.state.people.map( (value, index) => {
          return (
            <p key={index}>{value.first_name} {value.last_name}</p>
          )
        })}
      </div>
    )
  },
  first_nameCatcher(event) {
    this.setState({
      first_name: event.target.value
    })
  },
  last_nameCatcher(event) {
    this.setState({
      last_name: event.target.value
    })
  },
  createPerson() {
    var person = {first_name: this.state.first_name, last_name: this.state.last_name}
    axios({
      method: 'POST',
      url: 'http://localhost:3000/api/add/person',
      data: person
    })
  },
  readPeople() {
    axios({
      method: 'GET',
      url: 'http://localhost:3000/api/people'
    }).then(r => {
      this.setState({
        people: r.data
      })
    });
  }
})

ReactDOM.render(<App />, document.getElementById('app'));
~~~~
