# DevOps-Project4
Fourth project from the guided [darey.io](https://www.darey.io) DevOps Engineer Scholarship 

**Project Implementation**
Technology Stack used was **MEAN**( MongoDB Express Angular Node.js)

**Step 1: Creating an AWS instance & Connecting to it using Windows Terminal**
- Created an Amazon account and provisioned an Ubuntu Server 22.04 LTS (HVM), SSD Volume Type EC2 Instance with a free tier option.
 ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project4/instance%20launched.png)
- Downloaded my pem file to access my  AWS Ubuntu server using Terminal on my windows pc.
- Made the download folder as the persistent home directory since that's where my pem file was located.
 ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project3/connected%20to%20instance%20via%20windows%20terminal.png)

**Step 2 : Installing Nodejs**
- After updating and upgrading my linux server, I ran the two codes below separately.
- -First, I ran 
-  'sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates'
-  And next, I ran 
-  'curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -'
-  Then I now ran the code below to install nodejs
-  ' sudo apt install -y nodejs'
-  ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project4/nodejs%20installed.png)

**Step 3 : Installing MongoDB**
-Before installing mongodb, we will run the following commands:
 'sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6'
-And then run the command below
 'echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list'
-Then run the command below to install mongodb
 'sudo apt install -y mongodb'
-![alt text](https://github.com/guruchidi/darey.io/blob/main/Project4/mongodb%20installed.png)
-After installing mongodb, we are now going to start the mongodb service by running the code below:
 'sudo service mongodb start'
-To verify that the mongodb service is now running, we run the code below:
 'sudo systemctl status mongodb'
 - ![alt text](https://github.com/guruchidi/darey.io/blob/main/Project4/mongodb%20running.png)
- Install npm – Node package manager by running the code below: 
 - 'sudo apt install -y npm'
- Install body-parser package by running the code below. We need ‘body-parser’ package to help us process JSON files passed in requests to the server.
   'sudo npm install body-parser'
- Create a folder named ‘Books’
 - 'mkdir Books && cd Books'
- In the Books directory, Initialize npm project
   'npm init'
- Add a file to it named server.js by running the code below
 - 'vi server.js'
- Copy and paste the web server code below into the server.js file.
 - 'var express = require('express'); var bodyParser = require('body-parser'); var app = express(); app.use(express.static(__dirname + '/public')); app.use(bodyParser.json()); require('./apps/routes')(app); app.set('port', 3300); app.listen(app.get('port'), function() { console.log('Server up: http://localhost:' + app.get('port')); });'


**Step 3 : Installing ExpressJS**
- We also will use Mongoose package which provides a straight-forward, schema-based solution to model your application data. We will use Mongoose to establish a schema for the database to store data of our book register. Install mongoose by running the code below:
- 'sudo npm install express mongoose'
- In ‘Books’ folder, create a folder named apps
  - 'mkdir apps && cd apps'
- Create a file named routes.js
  - 'vi routes.js'
- Copy and paste the code below into routes.js
- var Book = require('./models/book');
module.exports = function(app) {
  app.get('/book', function(req, res) {
    Book.find({}, function(err, result) {
      if ( err ) throw err;
      res.json(result);
    });
  }); 
  app.post('/book', function(req, res) {
    var book = new Book( {
      name:req.body.name,
      isbn:req.body.isbn,
      author:req.body.author,
      pages:req.body.pages
    });
    book.save(function(err, result) {
      if ( err ) throw err;
      res.json( {
        message:"Successfully added book",
        book:result
      });
    });
  });
  app.delete("/book/:isbn", function(req, res) {
    Book.findOneAndRemove(req.query, function(err, result) {
      if ( err ) throw err;
      res.json( {
        message: "Successfully deleted the book",
        book: result
      });
    });
  });
  var path = require('path');
  app.get('*', function(req, res) {
    res.sendfile(path.join(__dirname + '/public', 'index.html'));
  });
};
- In the ‘apps’ folder, create a folder named models
  - 'mkdir models && cd models'
- Create a file named book.js
  - vi book.js
- Copy and paste the code below into ‘book.js’
   - 'var mongoose = require('mongoose');
     var dbHost = 'mongodb://localhost:27017/test';
     mongoose.connect(dbHost);
     mongoose.connection;
     mongoose.set('debug', true);
     var bookSchema = mongoose.Schema( {
  name: String,
  isbn: {type: String, index: true},
  author: String,
  pages: Number
});
var Book = mongoose.model('Book', bookSchema);
module.exports = mongoose.model('Book', bookSchema);'

- Access the routes with AngularJS by navigating back to the books directory
   - 'cd ../..'
- Create a folder named public
   'mkdir public && cd public'
- Add a file named script.js
   'vi script.js'
- Copy and paste the Code below (controller configuration defined) into the script.js file.
- var app = angular.module('myApp', []);
app.controller('myCtrl', function($scope, $http) {
  $http( {
    method: 'GET',
    url: '/book'
  }).then(function successCallback(response) {
    $scope.books = response.data;
  }, function errorCallback(response) {
    console.log('Error: ' + response);
  });
  $scope.del_book = function(book) {
    $http( {
      method: 'DELETE',
      url: '/book/:isbn',
      params: {'isbn': book.isbn}
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
  $scope.add_book = function() {
    var body = '{ "name": "' + $scope.Name + 
    '", "isbn": "' + $scope.Isbn +
    '", "author": "' + $scope.Author + 
    '", "pages": "' + $scope.Pages + '" }';
    $http({
      method: 'POST',
      url: '/book',
      data: body
    }).then(function successCallback(response) {
      console.log(response);
    }, function errorCallback(response) {
      console.log('Error: ' + response);
    });
  };
});

- In public folder, create a file named index.html;
   'vi index.html'
- Copy and paste the code below into index.html file.
- <!doctype html>
<html ng-app="myApp" ng-controller="myCtrl">
  <head>
    <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
    <script src="script.js"></script>
  </head>
  <body>
    <div>
      <table>
        <tr>
          <td>Name:</td>
          <td><input type="text" ng-model="Name"></td>
        </tr>
        <tr>
          <td>Isbn:</td>
          <td><input type="text" ng-model="Isbn"></td>
        </tr>
        <tr>
          <td>Author:</td>
          <td><input type="text" ng-model="Author"></td>
        </tr>
        <tr>
          <td>Pages:</td>
          <td><input type="number" ng-model="Pages"></td>
        </tr>
      </table>
      <button ng-click="add_book()">Add</button>
    </div>
    <hr>
    <div>
      <table>
        <tr>
          <th>Name</th>
          <th>Isbn</th>
          <th>Author</th>
          <th>Pages</th>

        </tr>
        <tr ng-repeat="book in books">
          <td>{{book.name}}</td>
          <td>{{book.isbn}}</td>
          <td>{{book.author}}</td>
          <td>{{book.pages}}</td>

          <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
        </tr>
      </table>
    </div>
  </body>
</html>

- Change the directory back up to Books
   'cd ..'
- Start the server by running this command:
   'node server.js'
To see the final project, we will have to enable port 3300 in the security group of our instance.
-![alt text](https://github.com/guruchidi/darey.io/blob/main/Project4/port%203300%20enabled.png)
-This is what your final result should look like.
-![alt text](https://github.com/guruchidi/darey.io/blob/main/Project4/complete%20project.png)
