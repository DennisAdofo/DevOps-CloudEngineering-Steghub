# Implementation of MERN Stack Project on AWS
---
 ![Architural Diagram](../3.%20MERN_Stack/Images/0.png)
 ---
# Overview
# The MERN Stack is a web service solution consisting of:
* MongoDB- Document based NO-SQL Database
* ExpressJS - Server side Web Application on Javascript
* ReactJS - A Frontend Framwork based on Javascript
* NodeJS - A javascript Runtime Environment

We will deploy the MERN stack on an Amazon EC2 instance running Ubuntu.
# Project Prerequisites
* An AWS account
* Basic knowledge of Linux commands
* A key pair for SSH access
* AWS security group rules allowing;
  * HTTP (Port 80) : To serve we traffic
  * SSH (PORT 22) : For secure access
* Custom port (port 3000) - For the Frontend
* Custom port (port 5000) - For the Backend
* A Local terminal (Linux/Mac/Git bash) or PuTTY (Windows).
* Github for central repsoitory, version control and documentation
#  Step by Step Implementation
# Step 1: Launch an EC2 instance
 * Log into the AWS Management console to setup the EC2 Instance

 ---
 * Search for EC2 on the search bar
 ![Search EC2 instance](../3.%20MERN_Stack/Images/1.png)
---
 * Click on Launch Instance

 * Enter the name of the web 
 server
  ---

 ![Enter server name ](../3.%20MERN_Stack/Images/2.png)
 ---
 * Choose Ubuntu server 22.04 or the latest version
  ![Search EC2 instance](../3.%20MERN_Stack/Images/2.png)
  ---

 * Select an instance type
 * Configure security group to allow HTTP, HTTPS, SSH
  ![Configure Security ](../3.%20MERN_Stack/Images/4.png)
 *  Launch and download the .pem key pair.
 *  Configure the storage to what you prefer
  ![Configure storage](../3.%20MERN_Stack/Images/4.png)
 *  Click on launch instance
 ---
 ![AWS EC2 interface](../3.%20MERN_Stack/Images/5.png)
 ---
 * Check status to confirm if the instance has been launced and running
---
![AWS EC2 status](../3.%20MERN_Stack/Images/6.png)
---
 * Copy the public IP Address of your instance
 ---
![Cpying IP address](../3.%20MERN_Stack/Images/7.png)
---


# Step 2: Connect to your instance
* Open your terminal (Git bash)
---
* ![terminal](../2.%20Lempstack/images/0i.png)
---
Enter download
```bash
 cd downloads
 ```
---
* ![terminal](../2.%20Lempstack/images/0i.png)
---
```bash
 ssh -i MERN.pem ubuntu@<EC2_Public_IP_Address>
 ```
---
* ![connecting](../3.%20MERN_Stack/Images/8.png)
---
# Step 3: Update the system
```bash
sudo apt update && sudo apt upgrade -y
```
# Step 4: Install Node.js on the server

```bash
sudo apt-get install -y nodejs

```
* NB:This will install npm and nodejs. NPM is a package manager for Node like rpm for redis, apt for Ubuntu and yum for most unix/linux devices.

---
* Verify the node installation

```bash
  node -v
```
---
# Step 5: Application Code Setup
* Create a new directory for the TODO project
```bash
   mkdir Todo
   ```
---
* Change your current directory to the newly created Todo directory
```bash
cd Todo
```
---
* Initialise your project

```bash
npm init
```
* NB:  This will create package.json which contains information about your application and the dependencies that it needs to run. Press Enter to accept default values, then type yes to accept everything.
---
 ![Package json](../3.%20MERN_Stack/Images/10.png)
 ---

---

# Step 6: Install ExpressJs
```bash
npm install express
```
---
* Create a file index.js with this command
```bash
touch index.js
```
---
Install dotenv module
```bash
npm install dotenv
``` 
---
* Open the index.js file with either vim or nano
 ```bash
  nano index.js
 ```
---
* Copy the code and save: 
```bash
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
        res.header("Access-Control-Allow-Origin", "\*");
        res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
        next();
});

app.use((req, res, next) => {
        res.send('Welcome to Express');
});

app.listen(port, () => {
        console.log(`Server running on port ${port}`)
});

```
---
* NB:The above code specifies the port 5000. This will be useful later. Use :w to save in vim and use :qa to exit vim and if you used nano, you can press ctrl + o then enter to save then ctrl+x to exit nano.
---
* Start the server to see if it works
```bash
node index.js
```
---
* You should see if it works

![Configuration](../3.%20MERN_Stack/Images/11.png)

* Open your browser and access your site
---
```bash
http://<PublicIP-or-PublicDNS>:5000
```
* Access your site

![Welcome to Express](../3.%20MERN_Stack/Images/12.png)

---

# Step 7: Routes

* Our TODO list application will perform three things:

  Create a new task

  Display list of all task

  Delete a complicated task

   Each task will used a standard HTTP requst methods like the POST, GET, DELETE respectively so we will have to create routes for each endpoints that the TO-DO app depends on.
   ---
* Create a folder called routes inside Todo

```bash
mkdir routes
```
* Inside directory routes create a file api.js and copy these codes into it.
--- 
```bash
const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {
});

router.post('/todos', (req, res, next) => {
});

router.delete('/todos/:id', (req, res, next) => {
});

module.exports = router;
```
---
* Create Models directory inside Todo to make use of the Mongodb

```bash
mkdir models
```
---
  * NB: Models are used to define database schema. This talks about how our data is constructed and also stored inside the database.

  ---
* nstall mongoose to create a Schema and a model
```bash
npm install mongoose
```
* change directory to models and create a file todo.js
```bash
touch todo.js
```
* Copy and paste the code into todo.js

```bash
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

//Create schema for todo
const TodoSchema = new Schema ({
action: {
type: String,
required: [true, 'The todo text field is required']
}
})

//Create model for todo
const Todo = mongoose.model('todo', TodoSchema);

module.exports = Todo;
```
To save and close the file completely with nano:

* Hit the ctrl + o and Enter. That will save the file
* Type ctrl + x to close the file

---
* Update the api.js in our routes directory to make use of the new model
* Open api.js with vim api.js. Delete the existing code with this command :%d and paste the code below into it.

```bash
const express = require ('express');
const router = express.Router();
const Todo = require('../models/todo');

router.get('/todos', (req, res, next) => {

//this will return all the data, exposing only the id and action field to the client
Todo.find({}, 'action')
.then(data => res.json(data))
.catch(next)
});

router.post('/todos', (req, res, next) => {
if(req.body.action){
Todo.create(req.body)
.then(data => res.json(data))
.catch(next)
}else {
res.json({
error: "The input field is empty"
})
}
});

router.delete('/todos/:id', (req, res, next) => {
Todo.findOneAndDelete ({"_id": req.params.id})
.then(data => res.json(data))
.catch(next)
});

module.exports = router;
```
---
# Step 8: Setting up MongoDB Database
* We will need a database to store our data. Here we will use MongoDB. 

* Creating a MongoDB Database and Collection
---
* Click on clusters
* Go through the options and create a cluster.
* Give your cluster a name.
* Choose aws or any as a provider.
* Choose a region closer to you.
* Create the cluster deployment
* Click browse collections
* Click on create database
* Enter the name you want to give your database then click create
---

# Step 9: Connecting the database
* In the index.js we specified process.env to access environment variables but that file is not created. Create that file.
```bash
touch .env
vi .env
```
* Add the connection string to access the database in it as below:
```bash
DB = DB = 'mongodb+srv://db_user:<db_password>@cluster0.mignz4q.mongodb.net/?retryWrites=true&w=majority&appName=Cluster0'
```
---
* NB: Ensure to update <username> in our case it is db_user, <db_password>, <network-address> in our case it is cluster0.mignz4q.mongodb.net and <database> according to your setup.
---
# Step 10: How to get your connection string
* Go to the Clusters Overview Page. You will see connect button on your right. Click on it

---
![Display](../3.%20MERN_Stack/Images/13.png)

---
* Click on the Drivers where we have connect to your application
---
![Display](../3.%20MERN_Stack/Images/18.png)
---
* Select the Driver and the version depending on your setup. Then right beneath it, you will see the connection string. Copy it.
---
![Display](../3.%20MERN_Stack/Images/19.png)

---
* Since we have the connection string. We need to update the index.js to reflect the use of the .env so that Node.js can connect to the database. NB: You can delete the existing code in index.js by using the command in vim :%d after you have press esc. Then Hit Enter.

---
* Press i to enter the insert mode in vim and paste the code below:

```bash
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const routes = require('./routes/api');
const path = require('path');
require('dotenv').config();

const app = express();
const port = process.env.PORT || 5000;

//connect to the database
mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
.then( () => console.log(`Database connected successfully`))
.catch(err => console.log(err));

//since mongoose promise is deprecated, we overide it with node's promise
mongoose.Promise = global.Promise;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use(bodyParser.json());
app.use('/api', routes);

app.use((err, req, res, next) => {
console.log(err);
next();
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```
---
* Start your server using the command
```bash
node index.js
```
---
* You should see this
![Display](../3.%20MERN_Stack/Images/20.png)

# Step 11: Testing Backend Code Without A Frontend using RESTFul API
* API stands for Application Programming Interface. Since we have only backend and configured the database, we will be working on our react frontend but since that is not yet done, we need to test our code by using RESTful API. In this case, we will use Postman to test our API. Install Postman

----
* Create a POST request to the API with the url http://<PublicIP-or-PublicDNS>:5000/api/todos. This will make a new task to our TO-DO list and will be stored in our database.

---


---
* Make sure you set header key Content-Type as application/json

---

---
* Create a GET request to the API on the url http://<PublicIP-or-PublicDNS>:5000/api/todos. This will get all existing records from our TO-DO application which is stored in our database and display it as a response to our request.

---



---
* If we want to delete any of our task, we send a DELETE request to delete the task from our TO-DO list application. NB: Anytime you want to delete a task, don't forget that you will need its ID as part of the DELETE request as I have circled in the image below:

---

![Delete](../3.%20MERN_Stack/Images/30.png)

---


---
* So we just tested the following:

Dislay all the list of tasks in our TO-DO app - HTTP GET request.

Add a new task to the list of TO-DOs - HTTP POST request

Delete an existing task from the list - HTTP DELETE request

---
* 
If you want to edit your tasks then you will have to update the api.js with this code so that you can also update your task as well.

```bash
const express = require('express');
const router = express.Router();
const Todo = require('../models/todo');

// GET all todos
router.get('/todos', (req, res, next) => {
  // return all data, exposing only id and action field
  Todo.find({}, 'action')
    .then(data => res.json(data))
    .catch(next);
});

// POST new todo
router.post('/todos', (req, res, next) => {
  if (req.body.action) {
    Todo.create(req.body)
      .then(data => res.json(data))
      .catch(next);
  } else {
    res.json({
      error: "The input field is empty"
    });
  }
});

// PUT (update) todo by ID
router.put('/todos/:id', async (req, res, next) => {
  try {
    const updatedTodo = await Todo.findByIdAndUpdate(
      req.params.id,
      { action: req.body.action },
      { new: true, runValidators: true } // return updated doc + validate
    );

    if (!updatedTodo) {
      return res.status(404).json({ error: "Todo not found" });
    }

    res.json(updatedTodo);
  } catch (err) {
    next(err);
  }
});

// DELETE todo by ID
router.delete('/todos/:id', (req, res, next) => {
  Todo.findOneAndDelete({ "_id": req.params.id })
    .then(data => res.json(data))
    .catch(next);
});

module.exports = router;
```
# Step 12:Frontend Creation
* We create our user interface to interact with the application via API. In the same root directory as your backend code, run this command: 

```bash
npx create-react-app client
```
* NB: This will create a directory called client in the TODO directory.

---
Installing Dependencies and Running our React App
* install some dependencies for testing our react app.

---
* Install concurrently. Concurrently will let you run more than one command simultaneously from the same terminal window
```bash
npm install concurrently --save-dev
```
* Install nodemon. This will run and monitor the server and update new changes if there's any change in the server code.
```bash
npm install nodemon --save-dev
```
* Open the package.json file and change the "scripts" and replace it with the code below
```bash
"scripts": {
"start": "node index.js", 
"start-watch": "nodemon index.js", 
"dev" : "concurrently \"npm run start-watch\" \"cd client && npm start\"" 
} ,
```
* Configure Proxy in package.json

Change directory to client
```bash
cd client
```
* Open the package.json file
```bash
vi package.json
```
* Add the key value pair in the package.json file
```bash
"proxy": "http://localhost:5000"
```
* NB: The reason for the proxy configuration is to access the application directly from the browser easily by typing http://localhost:5000 instead of typing http://localhost:5000/api/todos

---
* Ensure you are in the Todo directory and run this command
```bash
npm run dev
```
![Display](../3.%20MERN_Stack/Images/26.png)
---
# 13. Creating Your React Components
* React make use of components which are reusable and are independent building blocks for creating user interface. For our Todo app, we will create two stateful components and one stateless component. Stateful components hold and manage data that can change over time whereas stateless doesn't manage internal Read More.

---
* Run
```bash 
  cd client
  cd src
  ```
  * Inside your srcc folder create another folder called components

  ```bash
  mkdir components
  ```
* Move into the components directory with
```bash
cd components
```
* Inside components directory create three files: Input.js, ListTodo.js and Todo.js.
```bash
touch Input.js ListTodo.js Todo.js
```
* Open Input.js
```bash
vi Input.js
```
* copy and paste the following code into it
```bash
import React, { Component } from 'react';
import axios from 'axios';
class Input extends Component {

state  = {
action: ""
}

addTodo = ()=> {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0) {
        axios.post('/api/todos', task)
          .then(res => {
            if(res.data) {
              this.props.getTodos();
              this.setState({action: ""})
            }
         })
         .catch(err => console.log(err))
    }else {
       console.log('input field required')
    }
}

handleChange =(e) => {
this.setState({ 
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
) 
}
}

export default Input
```
* You need to install axios inorder to make use of it. It's a Promise based HTTP client for the browser and node.js

---
* Change directory into client folder

---
```bash
cd ../..
```
* Install Axios
```bash
npm install axios
```
* Go to Components directory
```bash
cd  src/components
```
* Open your ListTodo.js directory
```bash
vi ListTodo.js
```
* Copy and paste the following code into ListTodo.js
```bash
import React, { useState } from 'react';

const ListTodo = ({ todos, deleteTodo, editTodo }) => {
  const [editingId, setEditingId] = useState(null);
  const [editText, setEditText] = useState('');

  const handleEditClick = (todo) => {
    setEditingId(todo._id);
    setEditText(todo.action);
  };

  const handleSave = (id) => {
    if (editText.trim()) {
      editTodo(id, editText);
      setEditingId(null);
      setEditText('');
    }
  };

  return (
    <ul>
      {todos && todos.length > 0 ? (
        todos.map(todo => (
          <li key={todo._id}>
            {editingId === todo._id ? (
              <>
                <input
                  type="text"
                  value={editText}
                  onChange={(e) => setEditText(e.target.value)}
                />
                <button onClick={() => handleSave(todo._id)}>Save</button>
                <button onClick={() => setEditingId(null)}>Cancel</button>
              </>
            ) : (
              <>
                {todo.action}
                <button onClick={() => handleEditClick(todo)}>Edit</button>
                <button onClick={() => deleteTodo(todo._id)}>Delete</button>
              </>
            )}
          </li>
        ))
      ) : (
        <li>No todo(s) left</li>
      )}
    </ul>
  );
};

export default ListTodo;
```
* Then in the Todo.js directory
```bash
import React, { Component } from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {
  state = {
    todos: []
  }

  componentDidMount() {
    this.getTodos();
  }

  getTodos = () => {
    axios.get('/api/todos')
      .then(res => {
        if (res.data) {
          this.setState({
            todos: res.data
          });
        }
      })
      .catch(err => console.log(err));
  }

  deleteTodo = (id) => {
    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if (res.data) {
          this.getTodos();
        }
      })
      .catch(err => console.log(err));
  }

  // NEW: Edit todo
  editTodo = (id, newAction) => {
    axios.put(`/api/todos/${id}`, { action: newAction })
      .then(res => {
        if (res.data) {
          this.getTodos();
        }
      })
      .catch(err => console.log(err));
  }

  render() {
    let { todos } = this.state;
    return (
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos} />
        {/* Pass editTodo to ListTodo */}
        <ListTodo 
          todos={todos} 
          deleteTodo={this.deleteTodo} 
          editTodo={this.editTodo} 
        />
      </div>
    );
  }
}

export default Todo;
```

* We need to edit our App.js to change the default of our react application.
* Move to the src folder
```bash
cd ..
```

* Make sure you are in the src directory and run
```bash
vi App.js
```

* Copy and paste the code below into it.
```bash
import React from 'react';
import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;
```
* Exit the editor and open App.css
* Copy and paste the code into App.css
```bash
.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}


input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}


input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%;
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}


input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}
```
* In the src directory open the index.css
```bash
vim index.css
```
* Copy and paste the code below:

```bash
body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue", sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New", monospace;
}
```
* Go to the Todo directory
```bash
cd ../..
```
* When you are in the Todo directory run
```bash 
npm run dev
```
* If there's no error, you should see

![Display](../3.%20MERN_Stack/Images/27.png)

---
* You can test all the CRUD (Create, Read, Update and Delete) operation on the TODO application. You can edit by press the edit button as in the image below.

![Display](../3.%20MERN_Stack/Images/28.png)


---
* After updating the text you see the save at your right. Click on save to make your editing take effect.


![Display](../3.%20MERN_Stack/Images/29.png)

End of Details

---