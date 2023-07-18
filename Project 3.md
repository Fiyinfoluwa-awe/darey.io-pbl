# SIMPLE TO-DO APPLICATION ON MERN WEB STACK
### STEP 1 – BACKEND CONFIGURATION
update and upgrade ubuntu 
commands - 
```
sudo apt update 
sudo apt upgrade
```

![updating ubuntu ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/639b4fd0-3866-4217-8d61-fb232257a3d1)
![upgrading ubuntu 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e52b6224-9edb-4263-bcf5-b04f43865f12)

Getting the location of Node.js software from Ubuntu repositories

command - `curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`
![getting the location of node in ubuntu directory 3](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/a6aed9a5-b64d-4a34-9eca-ca90b38b7829)

#### Installing and verification of installation of Node.js on the server
commands -
```
sudo apt-get install -y nodejs
node -v
npm -v
```
![installing and verification of node ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/458a2c01-7623-4ed6-aaae-9e3c87606eec)

#### Application Code Setup
Creating a new directory for my To-Do project, changing directory, creating package.json

commands -
```
mkdir Todo
ls 
cd Todo
npm init
```
![creating directory for to do project 4](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/727c056d-0411-4888-9765-214788a0edcf)

## INSTALLING EXPRESSJS
Commands -
```
npm install express
touch index.js
```
![installing express js 6](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/27b9c024-409b-4404-be76-c9b8950afbe7)

Installing the dotenv module
commands -
```
npm install dotenv
vim index.js
```

![installing dotenv module and vim 7](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/66a1d873-a9d8-48a3-8e1b-55ac7b22c821)

checking to see if the server works. Open my terminal in the same directory as your index.js file and use command:
`node index.js'
![starting my server using node command ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0d10a6bb-bd04-48fd-b476-6878a0f1638a)

Editing  an inbound rule, for port 5000, 

![editing the security inbound rule 8](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7466b36d-2280-48ef-841d-9d24dd943c2d)

Opening up my browser to access your server’s Public IP or Public DNS name followed by port 5000:


![broswer check using port 5000 9](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/e4ed0ded-e9f2-4fcd-b453-5091fc654e4a)

### Routes
There are three actions that our To-Do application needs to be able to do:

1. Create a new task
2. Display list of all tasks
3. Delete a completed task

   
Each task will be associated with some particular endpoint and will use different standard HTTP request methods: POST, GET, DELETE.

For each task, I need to create routes that will define various endpoints that the To-do app will depend on. 

Creating a folder routes, changing directory to routes folder, creating a file api.js 

commands - 
```
mkdir routes
cd routes
touch api.js
vim api.js
```
![making directories and vim  10](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/00bfc7e9-4d8f-4001-854f-375a6db508bc)

### MODELS
Creating a Schema and a model, installing mongoose which is a Node.js package that makes working with mongodb easier.Creating a new folder models,
Changing directory into the newly created ‘models’ folder.
Inside the models folder, created a file and named it todo.js

commands - 
```
npm install mongoose
mkdir models
cd models
touch todo.js
vim todo.js
vim api.js
```
![installing mongoose 11 vim rewriting](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/920cf06f-9c11-43d4-b1a7-67899eb0c6fb)

## MONGODB DATABASE
Signed up for an account on MongoDB database, where i will store my data, following the stated instructions.
Created a file in your Todo directory and name it .env.

Added the connection string to access the database in it, just as seen below:


![cat  env ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/9ed83625-43ee-4206-a4a9-eddd2b3dccc0)

 Updated the index.js to reflect the use of .env so that Node.js can connect to the database.
 Started my server using the command:
 `node index.js`

 ![starting my server using node command ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/649935d8-2e60-42ef-bd96-78982a7410c9)

 #### Testing Backend Code without Frontend using RESTful API
 Used Postman to test My API
 ![postman Post test 2](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/48802e95-bde5-44c0-99c1-4fc8f4952c4f)
 
![pstman test 2 get ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/90cb8b65-d883-4863-a5f0-bf0b12d056ce)
![pstman delete command ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/f3e12780-514f-47f1-b2b1-bc64757eb0df)
![MongoDb postman check](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/d84dbd1c-6e46-463b-b596-cb32f3562b15)

Successfully created my Backend

## STEP 2 – FRONTEND CREATION

  In the root directory,which is the Todo directory, I ran this command ;
  ` npx create-react-app client`
  ![Frontend creation step 1 react folder](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/414bc463-92d9-4ecd-a0ed-4895556357c2)
 
This created a new folder in my Todo directory called client, where I will add all the react code.

#### Running a React App
Before testing the react app, there are some dependencies that are needed to be installed.
Installed concurrently.Install nodemon, which will be used to run and monitor the server.
If there is any change in the server code, nodemon will restart it automatically and load the new changes.

commands -
```
npm install concurrently --save-dev
npm install nodemon --save-dev
```

![installing dpendencies after react installation](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/47add172-b1c7-4be0-ac0d-5f70674f6748)

Configured Proxy in package.json
```
cd client
vi package.json
```

![adding proxy to the package json file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/40d99ccf-0989-494c-9d4a-0bce74e9bfb3)
Inside the Todo directory, and ran `npm run dev` command

![running npm run dev](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/73b69e11-91ef-4ff2-98d7-2de01d798e88)

The app opened and started running on localhost:3000


![browser check ](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0efbeb4f-3b3c-47df-93c0-3f36bbc59757)


#### Creating your React Components

In your Todo directory, ran these commands :

```
cd clients
cd src
mkdir components
cd components
touch Input.js ListTodo.js Todo.js
vi Input.js
cd ..
cd ..
npm install axios
```
![making component directory installing axios](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/7e8204bd-61d0-4aae-be39-db64e6c14a3d)

```
cd src/components
vi ListTodo.js
vi Todo.js
```

![cat v i listtodo js](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/a7076c77-0f7f-468e-bb86-19acca8646b1)

![vi Todo js file](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/3295adff-369e-40b8-adfc-8d5dd09ae694)

Made little adjustment to the react code by deleting the logo and adjusting the App.js 

First, moved to the src folder

```
cd ..
vi App.js
vi App.css
cd ../..
npm run dev
```

![vi App js editing the logo](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/5c1a4acd-02fb-4846-9ee2-e7fed2c4a982)

![vi App css](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/dbb95ebd-f4b5-45f9-8b03-25d2c574c897)

![completion of project 3 on terminal](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/09c7f311-d612-4df4-8bf8-19d5dbca0c4a)

![completion of Project 3 app final look](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/0675f682-ebc1-4a86-a0d4-0eb106293039)

![doneeeeee](https://github.com/Fiyinfoluwa-awe/darey.io-pbl/assets/131634975/1cbca793-6599-4105-800c-4e1faab31a24)
