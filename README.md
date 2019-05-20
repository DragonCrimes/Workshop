# How to connect React (front-end) to MongoDB (back-end)
### This is a simple fullstack MERN (Mongodb, Express, React, Node)app

- Clone this repository.
- Make a project folder , for example fullstack_app.
- In this folder we make 2 new folders named frontend and backend.

Now, we need to make sure we have nodejs installed in our system, if not here is a link to do it :
https://www.guru99.com/download-install-node-js.html

What is NodeJS

The Node environment includes everything you need to execute a program written in JavaScript :
https://medium.freecodecamp.org/what-exactly-is-node-js-ae36e97449f5


REACT

- What is react? 
	React is a JavaScript library created by Facebook,
	it is a User Interface (UI) library and
	it is a tool for building UI components.


- What can we do with it? 
 
https://reactjs.org/
https://reactjs.org/tutorial/tutorial.html

- Go into the main directory of the project then type in the terminal the following command 
`npm i -g create-react-app`

- We can now create our react app with the following command :
`create-react-app`

- We also need another package called Axios for making Get and Post requests :
`npm i -S axios`


MongoDB

- What is MongoDB and what can we do with it :
https://www.mongodb.com/what-is-mongodb

- Make a free account on MongoDB Atlas :
https://www.mongodb.com/

- Direct to the backend folder and type in the terminal 
`npm init`

- create a new file called server.js
- copy the following code into it :

```javascript
const mongoose = require(“mongoose”);
const express = require(“express”);
var cors = require(‘cors’);
const bodyParser = require(“body-parser”);
const logger = require(“morgan”);
const Data = require(“./data”);

const API_PORT = 3001;
const app = express();
app.use(cors());
const router = express.Router();

// this is our MongoDB database
const dbRoute = "mongodb://jelo:a9bc839993@ds151382.mlab.com:51382/jelotest";

// connects our back end code with the database
mongoose.connect(
  dbRoute,
  { useNewUrlParser: true }
);

let db = mongoose.connection;

db.once("open", () => console.log("connected to the database"));

// checks if connection with the database is successful
db.on("error", console.error.bind(console, "MongoDB connection error:"));

// (optional) only made for logging and
// bodyParser, parses the request body to be a readable json format
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());
app.use(logger("dev"));

// this is our get method
// this method fetches all available data in our database
router.get("/getData", (req, res) => {
  Data.find((err, data) => {
    if (err) return res.json({ success: false, error: err });
    return res.json({ success: true, data: data });
  });
});

// this is our update method
// this method overwrites existing data in our database
router.post("/updateData", (req, res) => {
  const { id, update } = req.body;
  Data.findOneAndUpdate(id, update, err => {
    if (err) return res.json({ success: false, error: err });
    return res.json({ success: true });
  });
});

// this is our delete method
// this method removes existing data in our database
router.delete("/deleteData", (req, res) => {
  const { id } = req.body;
  Data.findOneAndDelete(id, err => {
    if (err) return res.send(err);
    return res.json({ success: true });
  });
});

// this is our create methid
// this method adds new data in our database
router.post("/putData", (req, res) => {
  let data = new Data();

  const { id, message } = req.body;

  if ((!id && id !== 0) || !message) {
    return res.json({
      success: false,
      error: "INVALID INPUTS"
    });
  }
  data.message = message;
  data.id = id;
  data.save(err => {
    if (err) return res.json({ success: false, error: err });
    return res.json({ success: true });
  });
});

// append /api for our http requests
app.use("/api", router);

// launch our backend into a port
app.listen(API_PORT, () => console.log(`LISTENING ON PORT ${API_PORT}`));

```