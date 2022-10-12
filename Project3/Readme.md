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
