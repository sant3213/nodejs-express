"# nodejs-express" 
### **Node.js**
**1.** npm init.

**2.** npm install express.

**3.** For debugging :

  ```js script 
 npm i chalk@4.1.2
  ```

 &nbsp; &nbsp; Allow us to set some colors on our messages so that we can group them together.

**4.** 
```js script
const debug = require('debug')('app'); ==> execute this line in cmd if failed in visual studio
```
```js script
npm install debug
```
 ```js script
 set DEBUG=app
 ```
**5.** Install morgan middleware. This is a HTTP request logger middleware for node.js. It can be changed to combined if you want to get more information.
 ```js script
npm i morgan
 ```
  ```js script
 app.use(morgan('tiny'));
  ```

**6.** NPM Scripts
&nbsp;&nbsp;&nbsp; Modify the package.json "scripts" and add
 ```js script

    "start":"set DEBUG=app & node app.js",
    "debug":"set DEBUG=* & node app.js",
 ```
 &nbsp;&nbsp;&nbsp; To run other script different from start you have to do it with run:
 ```js script
npm run debug
 ```
 &nbsp;&nbsp;&nbsp; or 
  ```js script
npm run test
 ```
 &nbsp;&nbsp;&nbsp; For running start: 
```js script
npm start
 ```

 **7.** Nodemon is a tool that helps develop Node.js based applications by automatically restarting the node application when file changes in the directory are detected.

```js script
npm install nodemon
 ```
 The use to configure it is adding:
 ```js script
"nodemonConfig": {
    "restartable":"rs",
    "delay": 2500
}
 ```
 &nbsp;&nbsp;&nbsp; **rs**: When typing rs in the console it will restart.
 &nbsp;&nbsp;&nbsp; **delay**: In some situations, you may want to wait until a number of files have changed.

 Finally change the script to be executed with nodemon:
  ```js script

    "start":"set DEBUG=app & nodemon app.js",
    "debug":"set DEBUG=* & nodemon app.js",
 ```

  **8.** Environmental Variables 
  &nbsp;&nbsp;&nbsp; Add the following script to the nodemonConfig in the package.json: 
```js script
  "env": {
      "PORT": 4000
    }
```

**9.** **Templating Engines**
It´s going to allow us to render an HTML page, but at the same time, embedding data into that page to make it **dynamic**

```js script
  npm i ejs
```
add the following code to app.js:
```js script
  app.set('views', './src/views');/* allow us to set variables inside the context of our app*/
    app.set('view engine', 'ejs');
```
Change 
```js script
app.get('/',(req, res)=>{
    res.send('Hello from my app')
});

By

app.get('/',(req, res)=>{
    res.render('index', {title: 'Surymantics'})
})

```
And delete the file index.html from the public folder.

to get the data in the HTML use the notation 
```js script
<%=title%>
```

To get an array of data
```js script
 1. <% data.map((i) => {}) %>
 
 2. <% data.map((i) => {
       <li></li>
	   }) 
    %>
 
 3. <% data.map((i) => {
      %> <li></li><%
	   }) 
    %>
 ```

 **10.** **Routing**

 To handles all the routing neccesary for everything that falls under sessions you have to build a sessionRouter with:
```js script
 const sessionRouter = express().Router();
  ```
To use it
```js script
sessionsRouter.route('/').get((req, res)=> {
  res.send()
  // or res.render() to render the page
})
  ```
To pass in parameter values in the URL use /:<*parameterName*>
```js script
sessionsRouter.route('/:id').get((req, res)=> {
  console.log(id)
})
 ```

 **10.** **Connecting to MongoDB Locally**

```js script
 const { MongoClient } = require('mongodb'); //Object Destructuring

 const url = 'mongodb://localhost:27017';
 const dbName = 'expressDb';

 sessionsRouter.route('/').get((req, res)=> {
    (async function mongo() {
        let client;
        try {
    
            client = await MongoClient.connect(url);
            debug('Connected to the mongo DB');

            const db = client.db(dbName);
            const response = await db.collection('sessions').insertMany(sessions);
            res.json(response);
        } catch (error) {
            debug(error.stack);
        } 
        client.close();
    })();
})

  ```
**Queries**

  ```js script

db.collection('persons')
.find({name:"Sant"})

  ```



   *Comparison Operators*

  ```js script
$eq: Equal            $gt: greater than                  $lt: Less than
$ne: not equal        $gte: Greater than or equal        $lte: Less than or equal

/*$and: Logically combines multiple conditions. Resulting documents must match ALL conditions. */
{ $and: [{<condition1>}, [{<condition2>}...}]} 
{ $and: [{"gender": "male"}, [{"age": 25}]}

/*When the query has the same field we use Explicit $and. If we don´t use $and and the query has the same fields the first condition will be overwritten by the second one.*/
    - With <strong>Explicit</strong> $and ===> { $and: [{"age": {"$ne": 25}}, {"age": {"$gte": 20}} ]}
    - With **Implicit** $and ===> {"age": {"$ne": 25}}, {"age": {"$gte": 20}}: The first condition will be overwritten.

/*In the following query I can use implicit AND because fields are different or I can even use explicit as well*/
    db.collection('persons')
    .find({gender: "female", favoriteFruit: "banana"})

/*$or: Logically combines multiple conditions. Resulting documents must match ANY of the conditions*/
{ $or: [{<condition1>}, [{<condition2>}...}]} 
{ $or: [{"gender": "male"}, [{"age": 25}]} => Same as : {"age": {"$in": [20,27]}}

db.getCollection('persons')
.find({$or: [{eyeColor: "green"}, {eyeColor: "blue"}]}) /*==> a better approach for the queries with the same field would be the following:*/

db.getCollection('persons')
.find({eyeColor: {$in: ["green","blue"]) 


db.collection('persons')
.find({"eyeColor": {"$ne": "green"}}) //persons with color not equal to green

db.collection('persons')
.find({"age": {"$gt": 25}}) // persons with age greater than 25

db.collection('persons')
.find({"age": {"$gte": 25, "$lte": 30}}) // persons with age greater or equal than 25 AND less or equal to 35. The comma is AND

db.collection('persons')
.find({"name": {"$gt": "N"}})
.sort({"name": 1}) // persons with name with at least N in alphabetical order


  ```

   *$in and $nin Operators*

  ```js script
/*$in operator is used to include more than one condition in an array. "$in": [21,21] meaning that the result should have 21 or 22*/

db.collection('persons')
.find({"age": {"$in": [21, 22]}}) //persons with age 21 or 22

db.collection('persons')
.find({"age": {"$nin": [21, 22]}}) //persons who aren´t 21 or 22


  ```

  *Query for Embedded Documents*

  ```js script
"company": {
    "title":"SHEPARD",
    "email":"sdasd@gmail.com",
    "location":{
      "country":"USA",
      "address": "379 Tabor Court"
    }
}
// Use dot notation. it has to be used with quotes.
{"company.title":"SHEPARD"}
{"company.location.address": "379 Tabor Court"}
  
  db.getCollection('persons')
  .find({"company.location.country":"USA"})
  ```

 *Query Arrays by Specific Value*

  ```js script
  "tags": [
    "enim",
    "id",
    "velit",
    "ad",
    "consequat"
  ]

  db.getCollection('persons')
  .find({"tags.0":"ad"}) // Finds the tags with the "ad" in position 0 of tags

  ```


    *Helpers*

  ```js script
count()
sort({"name": 1}) // Ascending order

  ```

  **11.** **Security**
```js script
  npm i passport cookie-parser express-session passport-local
  ```