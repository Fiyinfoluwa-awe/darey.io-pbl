# MEAN Stack Deployment to Ubuntu in AWS

updated and upgraded ubuntu

`sudo apt update`

![step 1 sudo apt update](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/052a8e08-2a2f-466b-9482-a4de3fd95858)

`sudo apt upgrade`

![step 2 sudo apt upgrade](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0a6d2e18-71b2-4c4c-9be1-d1dd803cd489)

Added certificates

```
sudo apt -y install curl dirmngr apt-transport-https lsb-release ca-certificates

curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```
![step 4 adding certificates](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/4b52de76-7597-4ffe-955e-6619a46ac897)

installed node.js

`sudo apt install -y nodejs`

![step 3 sudo apt install -y nodejs](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/bc1016fe-6041-4e84-b2f3-88481194dfe6)

## Step 2: Install MongoDB

MongoDB stores data in flexible, JSON-like documents. 

Importing the Public Key used by Package Management System


`echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list`


![step 5 sudo apt-key and repository](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/23bdad9d-b76a-482d-b5e0-7758ad577957)

Reloaded Local Packages

`sudo apt-get update`

![step 6 reload](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/41429701-16ae-4c58-921d-b4740483ebbf)

### installing , starting and enabling Mongodb

`sudo apt install -y mongodb`

![step 7 sudo apt install mongodb](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/80be4e0e-d6a2-445e-998b-86c1268c94f4)
```
sudo systemctl start mongodb

sudo systemctl enable mongodb

sudo systemctl status mongodb
```


![step 8 sudo start, enable , status mongodb](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/fd91abb3-131e-4bf0-b9f0-88d29cf4705e)


### Installing 'body-parser package
We need 'body-parser' package to help us process JSON files passed in requests to the server.

`sudo npm install body-parser`

![step 9 sudo npm install bosy parser](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/ee5ee296-c470-48de-8748-79a4e35e1eb6)

creating a folder called Books

```
mkdir Books && cd Books
npm init
```

![step 10 mkdir books and npm init](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/77935157-3f61-4421-b5de-1039a43ee74f)

Add a file to it named server.js
Copy and paste the web server code below into the server.js file.

```
var express = require('express');
var bodyParser = require('body-parser');
var app = express();
app.use(express.static(__dirname + '/public'));
app.use(bodyParser.json());
require('./apps/routes')(app);
app.set('port', 3300);
app.listen(app.get('port'), function() {
    console.log('Server up: http://localhost:' + app.get('port'));
});
```

![step 11 vi server js](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/1979af3b-1df2-4764-9dad-30d7aad5536f)

### Installing Express and set up routes to the server
Express is a minimal and flexible Node.js web application framework that provides features for web and mobile applications. Express will be used to pass book information to and from our MongoDB database.
Mongoose package which provides a straight-forward, schema-based solution to model your application data, will be used and to establish a schema for the database to store data of our book register.

`sudo npm express mongoose`

In 'Books' folder, created a folder named apps

`mkdir apps && cat apps`

Created a file named routes.js

` vi routes.js`

![step 12 mkdir routes](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/cf732f0d-5dac-4d39-a8ed-a040a81e6189)
 
Copied and pasted a code into routes.js

In the 'apps' folder, create a folder named models

`mkdir models && cd models`

Created a file named book.js. Copied and pasted a code into this file

`vi book.js`

![step 14 mkdir routes](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/48705638-509a-4310-865a-692e04622b0f)


### Accessing the routes with AngularJS

AngularJS provides a web framework for creating dynamic views in your web applications. Used AngularJS to connect my web page with Express and perform actions on the book register.

changed the directory back to Books

`cd ../..`

created a folder called public 

`mkdir public && cd public`

Added a file named script.js.Copied and pasted a Code (controller configuration defined) into the script.js file.

`vi script.js`

In 'public' folder, create a file named index.html. copied and pasted a code into it 

`vi index.html`

![step 15 mkdir public script js index html](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/30876309-9756-431c-9f46-554260728d3c)

Change the directory back up to 'Books'

`cd ..`

Started the server by running this command:

`node server.js`

The server is now up and running, connected it via port 3300

![step 16 node server js](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/addbc946-18f9-48d4-ac2a-92d473903b39)

![step 17 curl 3300](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/6d8ec598-a57b-4ee2-bb55-4412e35ac82f)

![finallly done](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/824d633f-3e7a-48c5-bd93-b6f30700eefc)









