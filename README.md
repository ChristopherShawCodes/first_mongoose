# first_mongoose


(require --> connect --> Schema --> Model --> route)
----------------------------------
----------------------------------
----------------------------------




server - This is your backend server / project folder and will hold all server related files

config - will handle the database configuration and connection

controllers - will hold all logic for each model (i.e creating, updating, etc...)

models - will hold all the schemas

routes - will handle all of our routes for each model

server.js - will handle all the server logic with express

----------------------------------
----------------------------------
----------------------------------


Navigate To Server Folder
Run Commands:

`npm init -y`

`npm install mongoose express`

----------------------------------

Import/Require Mongoose 

needed in any file that needs access to the Mongoose library

`const mongoose = require('mongoose');`

----------------------------------

--Connecting MongoDB to Mongoose--

Navigate to Config Folder

Create `mongoose.config.js` file


Add Code Snippet To mongoose.config.js File 

`const mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/name_of_your_DB', {
    useNewUrlParser: true,
    useUnifiedTopology: true
})
    .then(() => console.log('Established a connection to the database'))
    .catch(err => console.log('Something went wrong when connecting to the database ', err));`
    
    
----------------------------------
----------------------------------

----Create Mongoose Schema and Model-----

Navigate to Models Folder

Create `user.model.js` file

Example: User Collection Code Snippet 

 `const mongoose = require('mongoose');
    const UserSchema = new mongoose.Schema({
    name: {
        type: String
    },
    age: {
        type: Number
    }
});
 const User = mongoose.model('User', UserSchema);
   module.exports = User;`


mongoose.model() method's job is to take a blueprint object and create the necessary database collection out of the model.

We get this blueprint by making a new schema instance from the mongoose.Schema() object constructor

The structure of the object within the schema will determine how each new document put into the collection will be formatted

--------------------------------------------------
--------------------------------------------------

-----Use Mongoose Models to Create/Retrieve/Update/Destroy-----

Navigate to Controllers folder

Create `user.controller.js` file 

This will house all of our logic for Creating, Retrieving, Updating and Deleting users 

*Note the require statement imports the User variable that was exported from user.model.js

`const User = require('../models/user.model');
 module.exports.findAllUsers = (req, res) => {
    User.find()
        .then((allDaUsers) => {
            res.json({ users: allDaUsers })
        })
        .catch((err) => {
            res.json({ message: 'Something went wrong', error: err })
        });
}
 module.exports.findOneSingleUser = (req, res) => {
    User.findOne({ _id: req.params.id })
        .then(oneSingleUser => {
            res.json({ user: oneSingleUser })
        })
        .catch((err) => {
            res.json({ message: 'Something went wrong', error: err })
        });}
 module.exports.createNewUser = (req, res) => {
    User.create(req.body)
        .then(newlyCreatedUser => {
            res.json({ user: newlyCreatedUser })
        })
        .catch((err) => {
            res.json({ message: 'Something went wrong', error: err })
        });}
 module.exports.updateExistingUser = (req, res) => {
    User.findOneAndUpdate(
        { _id: req.params.id },
        req.body,
        { new: true, runValidators: true }
    )
        .then(updatedUser => {
            res.json({ user: updatedUser })
        })
        .catch((err) => {
            res.json({ message: 'Something went wrong', error: err })
        });}
 module.exports.deleteAnExistingUser = (req, res) => {
    User.deleteOne({ _id: req.params.id })
        .then(result => {
            res.json({ result: result })
        })
        .catch((err) => {
            res.json({ message: 'Something went wrong', error: err })
        });}`


*Key Notes*

User is a constructor function which creates new user objects and talks to the database

.then() executes upon successfully inserting data into the database

.catch() executes only if there is an error 


--------------------------------------------------
--------------------------------------------------


-----Routing-----

Navigate to Routes folder

Create `user.routes.js` file

This file will be responsible for all of the routes dealing with the user model 

*Note*

The REQUIRE statement is importing everything from the Controller file

`const UserController = require('../controllers/user.controller');
 module.exports = app => {
    app.get('/api/users', UserController.findAllUsers);
    app.get('/api/users/:id', UserController.findOneSingleUser);
    app.put('/api/users/:id', UserController.updateExistingUser);
    app.post('/api/users', UserController.createNewUser);
    app.delete('/api/users/:id', UserController.deleteAnExistingUser);
}`

--------------------------------------------------
--------------------------------------------------

-----Server-----

Navigate to the Server.JS file 

Add Code Snippet

`const express = require("express");
const app = express();
    require("./config/mongoose.config");
    app.use(express.json(), express.urlencoded({ extended: true }));
    const AllMyUserRoutes = require("./routes/user.routes");AllMyUserRoutes(app);
    app.listen(8000, () => console.log("The server is all fired up on port 8000"));`


--------------------------------------------------
--------------------------------------------------

Run Server

`nodemon server.js`

You may see this error, if so it's not an issue:

  DeprecationWarning: Listening to events on the Db class has been deprecated and will be removed in the next major version

--------------------------------------------------


