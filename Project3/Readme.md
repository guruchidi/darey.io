# DevOps-Project3
Third project from the guided [darey.io](https://www.darey.io) DevOps Engineer Scholarship 

**Project Implementation**
Technology Stack used was **MERN**( MongoDB ExpressJS ReactJS Node.js)

**Step 1: Creating an AWS instance & Connecting to it using Windows Terminal**
- Created an Amazon account and provisioned an Ubuntu Server 22.04 LTS (HVM), SSD Volume Type EC2 Instance with a free tier option.
 ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/aws%20instance%20launch.png)
- Downloaded my pem file to access my  AWS Ubuntu server using Terminal on my windows pc.
- Made the download folder as the persistent home directory since that's where my pem file was located.
 ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/connected%20to%20instance%20via%20windows%20terminal.png)

**Step 2 : Installing Nodejs**
  - Connected to the AWS instance on windows terminal and installed Nodejs by running the following command
  - 'curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -'
  -  ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/getting%20nodejs%20folder.png)
  -  Next thing I did was to check for the version of nodejs and npm installed on my server.
  -  ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/node%20and%20npm%20version.png)
  - After installing nodejs, I created a directory called Todo, migrated to that directory and initialized npm by running the following command
  - 'mkdir Todo && cd Todo'
  - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/todo%20directory%20created.png)
  - And then after this , I initialized npm so that it created a new json file called package.json
  - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/package%20json%20file%20created.png)

**Step 3: Installing ExpressJS**
  - Installed expresjs using npm by running the command below
  - 'npm install express'
  - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/express%20installed.png)
  - Then I created an index.js file by running the command below
  - 'touch index.js'
  - After which, I installed dotenv with npm using the command below
  - 'npm install dotenv'
  - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/dotev%20and%20mongoose%20installed.png)
  - Next, I edited the index.js file with the vim command by running 'vim index.js' and pasting the following code inside
  -  'const express = require('express');
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
      });'
      
- After that, I started the server by running the code below
- 'node index.js'
- Then I edited my security group of my instance to allow port 5000
- ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/inbound%20rules.png)
- And then I checked my web browser to see if expressjs was running successfully
- ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/express%20running%20on%20web%20browser.png)
- After I confirmed my expressjs server running, I then created a new folder called routes folder and navigated into it by running the command below
- 'mkdir routes && cd routes'
- And then in the routes folder, I created a file called api.js by running the command 'touch api.js'
- Next thing I did was to edit the api.js file by running the command 'vim api.js'
- Then I pasted the code below into the api.js file
-  'const express = require ('express');
    const router = express.Router();

    router.get('/todos', (req, res, next) => {

    });

    router.post('/todos', (req, res, next) => {

    });

    router.delete('/todos/:id', (req, res, next) => {

    })

    module.exports = router;'
    
**Step 4: Installing Mongooose**
  - I installed mongoose inside the Todo directory by navigating back into the Todo directory with the command 'cd ..' and running the command below
  - 'npm install mongoose'
  - Then I created a new folder called models and navigated into the models directory and created a new file called todo.js by running the following command 
  - 'mkdir models && cd models && touch todo.js'
  - Then I opened the todo.js file with 'vim todo.js' and inserted the following code inside
  -   'const mongoose = require('mongoose');
       const Schema = mongoose.Schema;

       //create schema for todo
       const TodoSchema = new Schema({
       action: {
       type: String,
       required: [true, 'The todo text field is required']
       }
       })

       //create model for todo
       const Todo = mongoose.model('todo', TodoSchema);

       module.exports = Todo;'
   - Next thing I did was to open api.js with vim api.js, delete the code inside with :%d command and pasted the code below into it then save and exit
   - 'const express = require ('express');
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
      Todo.findOneAndDelete({"_id": req.params.id})
      .then(data => res.json(data))
      .catch(next)
      })

      module.exports = router;'

**Step 5: MongoDB Database**
  - I created a free account at https://www.mongodb.com/atlas-signup-from-mlab 
  - Then I created a mongodb database and collection inside mlab 
  - (**Unfortunately, I forgot to take a screenshot of the process of my account creation at mongodb and creating the database and collection**)
  - If you remember, in the index.js file, we specified process.env to access environment variables, but we have not yet created this file. So we need to do that now.
  - Create a file in your Todo directory and name it .env. by running the command touch .env.
  - Then open the file by running the command vi .env and pasted the code below:
  - 'DB = mongodb+srv://guruchidi:<password>@cluster0.q56guvk.mongodb.net/?retryWrites=true&w=majority'
  -Now we need to update the index.js file to reflect the use of .env so that Node.js can connect to the database. To do that, simply delete existing content in the file, and update it with the entire code below.
   - 'const express = require('express');
      const bodyParser = require('body-parser');
      const mongoose = require('mongoose');
      const routes = require('./routes/api');
      const path = require('path');
      require('dotenv').config();

      const app = express();

      const port = process.env.PORT || 5000;

      //connect to the database
      mongoose.connect(process.env.DB, { useNewUrlParser: true, useUnifiedTopology: true })
      .then(() => console.log(`Database connected successfully`))
      .catch(err => console.log(err));

      //since mongoose promise is depreciated, we overide it with node's promise
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
      });'
   -After that, I ran the following code
   - 'node index.js'
   - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/database%20connected%20successfully.png)
  
**Step 6: Testing Backend Code without Frontend using RESTful API**
 - We will make use of Postman to test our API. Download postman from https://www.getpostman.com/downloads/
 - Now open your Postman, create a POST request to the API http://<PublicIP-or-PublicDNS>:5000/api/todos. This request sends a new task to our To-Do list so the          application could store it in the database.
 - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/post%20request.png)
 - After creating a post request, next thing we want to do is to create a GET request to your API on http://<PublicIP-or-PublicDNS>:5000/api/todos. This request retrieves all existing records from out To-do application (backend requests these records from the database and sends it us back as a response to GET request).
 - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/get%20request.png)
 
**Step 7 : Frontend Creation.**
 -Now, go back to your Todo directory and run the command below to create a new foldee where we will insert all the react code
 - ' npx create-react-app client'
 - After that, install concurrently by running the command below (concurrently allows you to run more than one command at the same time)
 - 'npm install concurrently --save-dev'
 - Then we install nodemon after installing concurrently by running the code below
 - 'npm install nodemon --save-dev'
 - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/concurrently%20and%20nodemon.png)
 - In the Todo folder, open the package.json file and edit the scripts area with the following code:
 - "scripts": {
   "start": "node index.js",
   "start-watch": "nodemon index.js",
   "dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
   },
 -Next is to configure the proxy in the package.json file. To do that, we change directory to client and open the package.json file by running the code below:
 - ' cd client && vi package.json'
 -Add the key value pair in the package.json file
 - "proxy": "http://localhost:5000"
 - Now go back to the Todo directory and run the code below
 - 'npm run dev'
 - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/npm%20run%20dev.png)
 
**Step 8: Creating React Components.**
 - From the Todo directory, move to the client directory. From the client direcory, move to the src directory. From the src directory byb running the code below:
 - ' cd client && cd src'
 - Inside the src directory, create a new directory called components and move into the directory by running the code:
 - 'mkdir components && cd components'
 -Inside ‘components’ directory create three files Input.js, ListTodo.js and Todo.js by running the code below
 - 'touch Input.js ListTodo.js Todo.js'
 -Open Input.js file
 - 'vi Input.js'
 -Insert the following code into input.js
 - 'import React, { Component } from 'react';
    import axios from 'axios';

    class Input extends Component {

    state = {
    action: ""
    }

    addTodo = () => {
    const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

    }

    handleChange = (e) => {
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

    export default Input'
 - Next, we install Axios by navigating back to the clients folder by running the code below
  - 'cd ../..' and then run 'npm install axios'
 - Go to ‘components’ directory
    'cd src/components'
 - After that open your ListTodo.js
    'vi ListTodo.js'
 - In the ListTodo.js copy and paste the following code:
     import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo
Then in your Todo.js file you write the following code

import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;
 
 - We need to make little adjustment to our react code. Delete the logo and adjust our App.js to look like this.
 - Move to the src folder
    'cd ..'
 - Make sure that you are in the src folder and run
    'vi App.js'
 -Copy and paste the code below:
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
 
- After pasting, exit the editor.
- In the src directory open the App.css
   'vi App.css'
- Then paste the following code into App.css:
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
width: 100%
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
 - Exit
 - In the src directory open the index.css
   'vim index.css'
 - Copy and paste the code below:
  body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}
 - Now, go back to the Todo directory by running 
    'cd ../..'
 -In the Todo directory, run the code below:
   'npm run dev'
   ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/todo%20list%20running%20on%20react.png)
