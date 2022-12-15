# Building Natuors gift Tour API With Node.js and Express.js

Express.js

Express is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications.
Its written using Node.js and its the most popular.
Express allows for rapid development of Node.js applications.
It also makes it easy to impliment the MVC architecture.
API

Piece of s/w which can be used by another s/w for purposes of communication.
The REST Architecture

RESTFul APIs- APIs following the REST architecture.
Resources should contain resources BUT not actions that can be performed on them.

Middleware

Middleware is software that lies between an operating system and the applications running on it. Essentially functioning as hidden translation layer, middleware enables communication and data management for distributed applications.
Stands between request and response.
Middleware and the Request-Response Cycle

In express everything is middleware.

Example of middleware

![image]

Middleware that appears first in code, is executed before the one that appears later.

Order of code is important in express.

At the end of each middleware function, next() function is called, allowing for execution of the next middleware.

Request and Response objects created in the beggining go through each middleware.

The last middleware function, its ussually a router handler, thus we dont call the next() function to move to next middleware instead we send response data back to the client, thus finishing Request-Response cycle.

![image]

For us to use middleware in node.js, we rely on use() function.

e.g app.use(express.json());

Creating our own middleware

This middleware applies to every request since we havent specified any route.
Eaxmple 1
app.use((req, res, next) => {
  console.log('Hello from the middleware!!');
  // We need to call next() to avoid Request-Response cycle being stuck
  next();
});
Example 2
app.use((req, res, next) => {
  req.requestTime = new Date().toISOString();
  next(); //call next middleware in the callstack
});
Order of code is very important in express.
Creating and Mounting Multiple Routers

const tourRouter = express.Router();
const userRouter = express.Router();

app.use('/api/v1/tours', tourRouter);
app.use('/api/v1/users', userRouter);
tourRouter.route('/').get(getAllTours).post(createTour);

tourRouter
  .route('/:id')
  .get(getTour)
  .post(createTour)
  .patch(updateTour)
  .delete(deleteTour);

app.route('/').get(getAllUsers).post(createUser);

app.route('/:id').get(getUser).patch(updateUser).delete(deleteUser);
Param Middleware is one that only runs for certain parameters. i.e it runs when we have a certain parameter in our url.
middleware1

middleware2

Environment Variables

Global Variables used to define the environment in which node app is running.
app.get('env') - gives us the environment variables
MongoDB (No-SQL DB)

Starting mongo shell mongosh
Create new DB use natours-test or switch to new DB.
Creating a collection (tours) and inserting data in BSON format
db.tours.insertOne({name:"The Forest Hiker",price: 297,rating:4.7})

viewing contents of a collection
db.tours.find()
Output
[
  {
    _id: ObjectId("631aea8a871f5067afc4c3ef"),
    name: 'The Forest Hiker',
    price: 297,
    rating: 4.7
  }
]

ObjectId("631aea8a871f5067afc4c3ef") unique identifier of the above object.
show dbs - shows all the databases
admin          40.00 KiB
config        108.00 KiB
local          72.00 KiB
natours-test   40.00 KiB

Show collections(similar to tables) - show collections
natours-test> show collections
tours
Quit mongo shell - quit()
Creating New Documents

Creating multiple documents.
db.tours.insertMany([{name:"The Sea Explorer",price:497,rating:4.8},{name:"The Snow Adventurer", price: 997, rating: 4.9,difficulty:"easy"}])

Searching for specific document

db.tours.find({name: "The Forest Hiker"})
db.tours.find({difficulty:"easy"})

{name: "The Forest Hiker"} and {difficulty:"easy"} are the search criteria.
Finding values less than and ``less than or equal to`

db.tours.find({price: {$lte: 500}})

$ is preserved for mongo operators
lte stands for less than or equal to
Doing multiple searches/queries

db.tours.find({price: {$lte: 500},rating: {$gte: 4.8}})
The above query works if both conditions are true, (AND query)
Querying with OR Operator

db.tours.find({$or: [ {price: {$lt: 500}},{rating:{$gte: 4.8}} ]})
lt - less than
gte greater than or equal to
db.tours.find({$or: [ {price: {$gt: 500}},{rating:{$gte: 4.8}} ]},{name: 1})
The command above only displays the name.
[
  {
    _id: ObjectId("631aeda0d5dbcff2c6a727af"),
    name: 'The Sea Explorer'
  },
  {
    _id: ObjectId("631aeda0d5dbcff2c6a727b0"),
    name: 'The Snow Adventurer'
  }
]
Update Documents

Updating single document
db.tours.updateOne({name: "The Snow Adventurer"}, {$set: {price: 597}})

Updating many/multiple documents
db.tours.updateMany({price: {$gt: 500},rating: {$gte: 4.8}},{$set: {premium: true}})
To replace contents of a document, we use replaceOne()
deleting documents

Deleting multiple documents.
db.tours.deleteMany({rating: {$lt: 4.8}})
Delete one document
db.tours.deleteOne({rating: {$lt: 4.8}})
Delete all documents
db.tours.deleteMany({}) - parsing empty object.

Connecting express app to mongoDB

Lets use the most popular mongoDB Driver, mongoose
const mongoose = require('mongoose');

dotenv.config({ path: './config.env' });
// console.log(app.get('env'));
// console.log(process.env);

const DB = process.env.DATABASE.replace(
  '<PASSWORD>',
  process.env.DATABASE_PASSWORD
);

// .connect(process.env.DATABASE_LOCAL, {
mongoose
  .connect(DB, {
    useNewUrlParser: true,
    useCreateIndex: true,
    useFindAndModify: false,
  })
  .then(() => {
    // console.log(con);
    console.log('DB Connection Successful');
  });
We create a model in mongoose in order to create documents using it and also query, update and delete thse documents, i.e perform CRUD(CREATE,READ,UPDATE,DELETE) operations.

To create a model we need a schema. We create models out of mongoose schema.

mongoose

MVC(Model,View,Controller) Architecture

Model layer is concerned with the application layer and the business logic.
Controller layer - Function of the controller is to handler the applications' requests, interact with models and send response to clients. This is basically known as the application logic.
The View Layer - is necessary if we have GUI in our app. (Presentation Logic)
mvc1

mvc2

Filtering

sample url for filtering
http://127.0.0.1:3000/api/v1/tours?difficulty=easy&duration[gte]=5&price[lt]=1500
Sorting

Sorting in Ascending Order
http://127.0.0.1:3000/api/v1/tours?sort=price
http://127.0.0.1:3000/api/v1/tours?sort=duration
Sorting in descending Order
http://127.0.0.1:3000/api/v1/tours?sort=-price
http://127.0.0.1:3000/api/v1/tours?sort=-duration
Limiting Fields

http://127.0.0.1:3000/api/v1/tours?fields=name,duration,difficulty,price

select in the case below makes createdAt unavailable to the client on fetching the api.
  createdAt: {
    type: Date,
    default: Date.now(),
    select: false,
  },
Aliasing

Create the route
router.route('/top-5-cheap').get(tourController.aliasTopTours,tourController.getAllTours)
Create the handler (using middleware)
exports.aliasTopTours = (req,res,next) => {
req.query.limit = '5';
req.query.sort = '-ratingsAverage,price';
req.query.fields = 'name,price,ratingAverage,summary,difficulty';
next();
}
Aggregation pipeline

Its a powerful and useful mongodb framework for data aggregation.
An aggregation pipeline consists of one or more stages that process documents: Each stage performs an operation on the input documents. For example, a stage can filter documents, group documents, and calculate values. The documents that are output from a stage are passed to the next stage.
we can use it to calculate averages, min and max values, distances and many more.
Controller(Handler)

//aggregation pipeline use case
exports.getTourStats = async (req, res) => {
  try {
    const stats = await Tour.aggregate([
      //match stage
      {
        $match: { ratingsAverage: { $gte: 4.5 } },
      },
      //group stage
      {
        $group: {
          // _id: '$ratingsAverage',
          // _id: '$difficulty',
          _id: { $toUpper: '$difficulty' },
          numTours: { $sum: 1 },
          numRatings: { $sum: '$ratingsQuantity' },
          avgRating: { $avg: '$ratingsAverage' },
          avgPrice: { $avg: '$price' },
          minPrice: { $min: '$price' },
          maxPrice: { $max: '$price' },
        },
      },
      //sorting stage (we use variable names that are used up in the GROUP stage)
      {
        //1 here represents ascending
        $sort: { avgPrice: 1 },
      },
      //Example below is to demonstrate we can actualy repeat stages
      // {
      //   $match: { _id: { $ne: 'EASY' } },
      // },
    ]);

    res.status(200).json({
      status: 'success',
      data: {
        stats,
      },
    });
  } catch (err) {
    res.status(404).json({
      status: 'fail',
      message: err,
    });
  }
};
Route for the pipeline above

router.route('/tour-stats').get(tourController.getTourStats);
$unwind

Deconstructs an array field from the input documents to output a document for each element. Each output document replaces the array with an element value.
For each input document, outputs n documents where n is the number of array elements and can be zero for an empty array.
exports.getMonthlyPlan = async (req, res) => {
  try {
    const year = req.params.year * 1; //2021
    const plan = await Tour.aggregate([
      {
        $unwind: '$startDates',
      },
      {
        $match: {
          startDates: {
            $gte: new Date(`${year}-01-01`),
            $lte: new Date(`${year}-12-31`),
          },
        },
      },
      {
        $group: {
          _id: { $month: '$startDates' }, //group by month
          numTourStats: { $sum: 1 },
          tours: { $push: '$name' },
        },
      },
    ]);

    res.status(200).json({
      status: 'success',
      data: {
        plan,
      },
    });
  } catch (err) {
    res.status(404).json({
      status: 'fail',
      message: err,
    });
  }
};

Route for the above

http://127.0.0.1:3000/api/v1/tours/monthly-plan/:year
$project

Passes along the documents with the requested fields to the next stage in the pipeline. The specified fields can be existing fields from the input documents or newly computed fields.
exports.getMonthlyPlan = async (req, res) => {
  try {
    const year = req.params.year * 1; //2021
    const plan = await Tour.aggregate([
      //unwind
      {
        $unwind: '$startDates',
      },
      //match
      {
        $match: {
          startDates: {
            $gte: new Date(`${year}-01-01`),
            $lte: new Date(`${year}-12-31`),
          },
        },
      },
      //group
      {
        $group: {
          _id: { $month: '$startDates' }, //group by month
          numTourStats: { $sum: 1 },
          tours: { $push: '$name' },
        },
      },
      {
        $addFields: { month: '$_id' },
      },

      //project
      {
        $project: {
          _id: 0, //works with 1 and 0, for 0 it means _id wont showup
        },
      },
      //sorting
      {
        $sort: { numTourStats: -1 },
      },
      //limit
      {
        $limit: 12, //limit to 12 outputs
      },
    ]);

    res.status(200).json({
      status: 'success',
      data: {
        plan,
      },
    });
  } catch (err) {
    res.status(404).json({
      status: 'fail',
      message: err,
    });
  }
};
Virtual Properties

In Mongoose, a virtual is a property that is NOT stored in MongoDB. Virtuals are typically used for computed properties on documents.
These are fields that we can define in our schema though we cant save them on DB.
we must explicitly define in our schema that we want virtual properties in our output.
Defining a virtual property
tourSchema.virtual('durationWeeks').get(function () {
  return this.duration / 7;
});

We also need to explicitly define in our schema that we need virtuals displayed in our output.
You add the options below as the second argument for const tourSchema = new mongoose.Schema().
 {
    toJSON: { virtuals: true },
    toObject: { virtuals: true },
  }
Mongoose Middleware

Mongoose has 4 types of middleware: document middleware, model middleware, aggregate middleware, and query middleware.
We define middleware on schema.
Document Middleware

Middleware that can act on the currently processed documents.
we can have middleware run before and after certain event.
runs only for .save() and .create()
we can have multiple pre and post document middleware.
pre middleware: will run before an actual event

The callback will be called before an actual document is saved on the database.
tourSchema.pre('save', function (next) {
  // console.log(this);
  //this at this point is the currently processed document
  this.slug = slugify(this.name, { lower: true });
  next();
});
post middleware: will run after an actual event

Executed after all pre middleware are executed
 tourSchema.post('save', function (doc, next) {
 console.log(doc);
 next();
 });
Query Middleware

Middleware (also called pre and post hooks) are functions which are passed control during execution of asynchronous functions. Middleware is specified on the schema level and is useful for writing plugins.
Allows us to run functions before or after certain query is executed.
this keyword will point on the current query
pre-find Middleware

// tourSchema.pre('find', function (next) {
//   this.find({ secretTour: { $ne: true } });
//   next();
// });

/^find/ - all strings that start with find
tourSchema.pre(/^find/, function (next) {
  this.find({ secretTour: { $ne: true } });
  this.start = Date.now();
  next();
});
post-find middleware

tourSchema.post(/^find/, function (docs, next) {
  console.log(`Query took ${Date.now() - this.start} milliseconds`);
  console.log(docs);

  next();
});
Aggregation Middleware

Aggregate middleware executes when you call exec() on an aggregate object.
Aggregation middleware is a natural complement to query middleware, it lets you apply a lot of the use cases for hooks like pre('find') and post('updateOne') to aggregation.
It allows us to add hooks before or after an aggregation happens.
this keyword is going to point at the aggregation object
tourSchema.pre('aggregate', function (next) {
  this.pipeline().unshift({ $match: { secretTour: { $ne: true } } });
  console.log(this.pipeline());
  next();
});
Error Handling using Global Error Middleware

error handling

If we parse an argument into next(), express automatically knows that the argument is an error
Global Error Handling Middleware

To define error handling middleware,all we need it to give it 4 arguments (error, request, response, next)
app.use((err, req, res, next) => {
err.statusCode = err.statusCode || 500;
err.status = err.status || 'error';

res.status(err.statusCode).json({
status: err.status,
message: err.message,
});
});
then for catching the error in any route , create the error like below
  const err = new Error(`Can't find ${req.originalUrl} on this server`);
  err.status = 'fail';
  err.statusCode = 404;

  next(err);
console.log(err.stack); - shows where the error happened
Password Salting and Hashing

Password salting adds a random string to a password in way that even if a 2 different users enters similar passwords, they won't look similar

Password Hashing is basically encryption.

How JSON WEB TOKEN (JWT) Authentication Works

JWT, or JSON Web Token, is an open standard used to share security information between two parties — a client and a server. Each JWT contains encoded JSON objects, including a set of claims. JWTs are signed using a cryptographic algorithm to ensure that the claims cannot be altered after the token is issued.

REST APIS should be stateless. No state should be saved in a server while a request is being sent, processed and responded to.

jwt

auth1

jwt2

Passport

This module lets you authenticate endpoints using a JSON web token. It is intended to be used to secure RESTful endpoints without sessions.
Security Best Practices

security

Cookies

A cookie is a small piece of text that a server can send to a client and then the client stores it and send it along with future requests to the same server
Rate Limiting

prevent same IP from making many requests into API thus preventing attacks such as DOS and Brute Force attacks
const rateLimit = require('express-rate-limit'); //rate limiting
Data Sanitization

cleaning all data that comes to app from malicious code. Code that is trying to attack our application

Data Sanitization against NoSQL query injection

app.use(mongoSanitize());
Data sanitization against XSS
cleans any user input from malicious html code with some js attached to it
app.use(xss());
Preventing Parameter Pollution

const hpp = require('hpp'); //http parameter pollution
prevent parameter pollution
app.use(
  hpp({
    whitelist: [
      'duration',
      'ratingsQuantity',
      'ratingsAverage',
      'maxGroupSize',
      'difficulty',
      'price',
    ],
  })
);
It clears up query string
Data Modelling and Advanced Mongoose

MongoDB Data Modelling

Data Modelling is the process of taking unstructured data generated from a real world scenario and the structure it into a logical data model in a database. We do that according to a set of criteria.

The Natours Data Model

/me endpoint

Endpoint where user can retrieve their own information.
API Documentation

Below is the published API for Natours
https://documenter.getpostman.com/view/16390985/2s8YCXMxQe
This api documentation feature exists in postman to help devs document their APIs
PUG

Its a template engine which is commonly used with express.
Its white space sensitive.
Types of comments in pug
// h1 The Park Camper - The comment is seen in dev tools
//- h1 The Park Camper - Comment isn't seen in dev tools
Hosted Versions

https://web-production-c886.up.railway.app/ -Hosted in railway.app
https://natours-api-qdk9.onrender.com/ - Hosted in render.com