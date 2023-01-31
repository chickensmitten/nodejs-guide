# Complete NodeJS, ExpressJS guide with Best Practices
- Proper MVC file structure in ExpressJS
- Middleware lessons
- Introduction to MongoDB
- Mongoose lessons
- API for filtering data, sorting, limiting, pagination and aliasing
- Proper superior error handling
- Authentication, authorization and security
- Payments, email, file uploads

## Quick Intros
- How to use node in the console. Run `node` in the terminal.
- underscore gives you previous results in node terminal.
```
> 3*8
24
> _+6
30
> _-30
0
```
- Synchronous is blocking. i.e. `fs.readFileSync`. Asynchrounous is non-blocking. i.e. `fs.readFile`
- `fs.readFile` has file name and path, file format and callback
```
// read file
fs.readFile(`./txt/${data1}.txt`, 'utf-8', (err, data2) => {})

// write file
fs.writeFile('./txt/final.txt', `${data2}\n${data3}`, 'utf-8', err => {})
```
- To prevent blocking in NodeJS, Don't do the following:
  - Don't use sync versions of functions in fs, crypto and zlib modules in your callback functions
  - Don't perform complex calculations (e.g. loops inside loops)
  - Be careful with JSON in large objects (e.g. parsing or stringify JSON)
  - Don't use too complex regular expressions (e.g. nested quantifiers)
- `CMD + D` to click on multiple same text at the same time
- `__dirname` is a variable that translate to the directory that the current executing script is located.
```
const tempCard = fs.readFileSync(
  `${__dirname}/templates/template-card.html`,
  'utf-8'
);
```
- to install NPM `npm init` created "package.json" file
- Streams are useful for video streaming. It allows to process (read and write) data piece by piece (chunks), without completing the whole read or write operation and therefore without keeping all the data in memory. Four types of streams:
  - readable streams: http requests, fs read streams, `pipe()`, `read()`
  - writable streams: http responses, fs write streams, `write()`, `end()`
  - duplex streams: net web socket
  - transform streams: zlib Gzip creation compression data.
- streaming code example
```
const fs = require("fs");
const server = require("http").createServer();

server.on("request", (req, res) => {
  // Solution 1
  // fs.readFile("test-file.txt", (err, data) => {
  //   if (err) console.log(err);
  //   res.end(data);
  // });

  // Solution 2: Streams
  // const readable = fs.createReadStream("test-file.txt");
  // readable.on("data", chunk => {
  //   res.write(chunk);
  // });
  // readable.on("end", () => {
  //   res.end();
  // });
  // readable.on("error", err => {
  //   console.log(err);
  //   res.statusCode = 500;
  //   res.end("File not found!");
  // });

  // Solution 3
  const readable = fs.createReadStream("test-file.txt");
  readable.pipe(res);
  // readableSource.pipe(writeableDest)
});

server.listen(8000, "127.0.0.1", () => {
  console.log("Listening...");
});

```

## Using Postman
- Paste the URL
- Select the right method
- Input header (if any)
- Input body (if any)
- PUT will complete replace the object. PATCH only updates the fields that are different.
```
// PATCH updates the price field only
> { name: "Sam", price: 23 }
> { price: 43 }
> { name: "Sam", price: 43 }


// PUT replaces the entire object
> { name: "Sam", price: 23 }
> { price: 43 }
> { price: 43 }
```
- When an item is deleted, the response is 204 because there shouldn't be any content.

## MVC
- MVC
  - View: Presentation logic
  - Model: Business logic
    - Code that actually solves the business problem we set out to solve;
    - Directly related to business rules, how the business works,and business needs;
    - Examples:
    - Creating new tours in the database;
    - Checking if user’s password is correct;
    - Validating user input data;
    - Ensuring only users who bought a tour can review it.
  - Controller: Application logic
    - Code that is only concerned about the application’s implementation, not the underlying business problem we’re trying to solve (e.g. showing and selling tours);
    - Concerned about managing requests and responses;
  - About the app’s more technical aspects;
  - Bridge between model and view layers.
- Fat models/thin controllers: offload as much logic as possible into the models, and keep the controllers as simple and lean as possible.
- Code path: server.js -> app.js -> routes -> controllers (with models)

## Middleware
- middleware is a pipeline. request-response cycle: incoming request -> middleware -> response
- middleware is found in "app.js". It has `next` as shown in the following code. The code below shows `app.use` then do something, then run `next` to go to the next `app.use` code. Then finally, run the routes
```
app.use((req, res, next) => {
  // do something 
  next();
});
```
- morgan is a popular logging middleware
- when serving static files, there is no need to put `/public` path because if the process can't find the url, it will go to `/public` directly

## ESLint + Prettier
- ESLint find code for errors and bad coding practices.
- To install, run `npm i eslint prettier eslint-config-prettier eslint-plugin-prettier eslint-config-airbnb eslint-plugin-node eslint-plugin-import eslint-plugin-jsx-ally eslint-plugin-react --save-dev`
- the dependencies should be installed in dev in package.json
- ".prettierrc" and ".eslintrc.json" is where the rules can be found.

## MongoDB
- To access MongoDB Atlas shell, 
  - go to clusters
  - click connect, click Mongo Shell
  - copy connecting string (should look like this `mongo "mongodb+srv://srv:cluster0-pwikv.mongodb.net/test" --username jonas MongoDB shell version v4.0.9`)then paste the string in terminal, enter password
  - it should work. then can use mongodb commands like `show dbs` to see all the database, `use <db name>` to switch, `db.tours.find` to get find the collection etc

## Mongoose
- Mongoose through `mongoose.model` can automatically create a database collection in MongoDB Atlas.
- Mongoose. Find all: `find()`, Find one: `findById()`, Create: `create()`, Update: `findByIdAndUpdate()`, Delete: `findByIdAndDelete()`.
- `req.query` gets all params. This is not mongoose method, but expressJS method.
- refer to APIFeatures.js for filtering, sorting, limiting fields, pagination using javascript and MongoDB code.
- advanced filter with greater than equals (gte), greater than (gt) etc etc, refer to "apiFeatures.js". It basically replaces `gte` with `$gte`, `$gte` is a method that MongoDB recognises
- creating aliases for search queries. i.e. "/top-5-cheap" will rank search results from lowest price to highest limit to 5 only.
- To get statistics, use aggregation pipeline (matching and grouping), refer to `router.route('/tour-stats').get(tourController.getTourStats);` and `router.route('/monthly-plan/:year').get(tourController.getMonthlyPlan);`
- Virtual properties are fields that can be found in schema but is not persistent. It is useful for temporary fields like converting miles to KM. Refer to:
```
// models/tourModel.js
...
    toJSON: { virtuals: true },
    toObject: { virtuals: true }
  }
);

tourSchema.virtual('durationWeeks').get(function() {
  return this.duration / 7;
});
```

```
// when request is api/v1/tours/top-5-cheap
// routes
.route('/top-5-cheap')
  .get(tourController.aliasTopTours, tourController.getAllTours);

// controller
exports.aliasTopTours = (req, res, next) => {
  req.query.limit = '5';
  req.query.sort = '-ratingsAverage,price';
  req.query.fields = 'name,price,ratingsAverage,summary,difficulty';
  next();
};
```
- Mongoose has document middleware helps with issueing a command between when the save command is issued and the actual saving of the document. It can also be done after a command is issued. There are four types of middleware: Document Middleware, Query Middleware, Aggregate Middleware and Model Middleware. 
  - Document middleware is useful for creating slugs with sluggify.
  ```
  // DOCUMENT MIDDLEWARE: runs before .save() and .create()
  tourSchema.pre('save', function(next) {
  ```
  - Query middleware is useful for search
  ```
  // QUERY MIDDLEWARE
  tourSchema.pre(/^find/, function(next) { // regex is used here so that all the string that starts with find will use this middleware including FindOne
  ```
  - Aggregation middleware is useful for excluding outlier data like secret tours
  // AGGREGATION MIDDLEWARE
  ```
  tourSchema.pre('aggregate', function(next) {
  ```
- `next();` is needed in Model pre and post middleware functions to avoid the API call being stuck
- Data Validation before saving in Models
  - Built-In Validator
  ```
  minlength: [10, 'A tour name must have more or equal then 10 characters']
  ...
  enum: {
    values: ['easy', 'medium', 'difficult'],
    message: 'Difficulty is either: easy, medium, difficult'
  }
  ```
  - Custom Validator
  ```
    ...
      priceDiscount: {
      type: Number,
      validate: {
        validator: function(val) {
          // this only points to current doc on NEW document creation
          return val < this.price;
        },
        message: 'Discount price ({VALUE}) should be below regular price'
      }
    },
  ```
- Example script for data migration. With the code below run `node dev-data/data/import-dev-data.js`
```
// /dev-data/data/import-dev-data.js
const fs = require('fs');
const mongoose = require('mongoose');
const dotenv = require('dotenv');
const Tour = require('./../../models/tourModel');

dotenv.config({ path: './config.env' });

const DB = process.env.DATABASE.replace(
  '<PASSWORD>',
  process.env.DATABASE_PASSWORD
);

mongoose
  .connect(DB, {
    useNewUrlParser: true,
    useCreateIndex: true,
    useFindAndModify: false
  })
  .then(() => console.log('DB connection successful!'));

// READ JSON FILE
const tours = JSON.parse(
  fs.readFileSync(`${__dirname}/tours-simple.json`, 'utf-8')
);

// IMPORT DATA INTO DB
const importData = async () => {
  try {
    await Tour.create(tours);
    console.log('Data successfully loaded!');
  } catch (err) {
    console.log(err);
  }
  process.exit();
};

// DELETE ALL DATA FROM DB
const deleteData = async () => {
  try {
    await Tour.deleteMany();
    console.log('Data successfully deleted!');
  } catch (err) {
    console.log(err);
  }
  process.exit();
};

if (process.argv[2] === '--import') {
  importData();
} else if (process.argv[2] === '--delete') {
  deleteData();
}
```
## Debugging NodeJS
- `npm install ndb --global` this may require `sudo`. Or install it locally with `npm install ndb --save-dev`
- add `"debug": "ndb server.js"` to package.json
- To get started, add breakpoint in a code line, click "next", click "step in", review code then click "step out".

## Error Handling
- Write a global error handling middleware after all the routers because this means none of the routers were able to catch the requests).
- `app.all` is used to catch all the http methods.
```
app.all('*', (req, res, next) => {
  next(new AppError(`Can't find ${req.originalUrl} on this server!`, 404)); // this is used to catch 404 errors
});
```
- Create an error global class
```
// /utils/apperror.js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);

    this.statusCode = statusCode;
    this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
    this.isOperational = true; // check if this is an operational error and not programming error i.e. bug

    Error.captureStackTrace(this, this.constructor);
  }
}

module.exports = AppError;
```
- Any other operational error that is not 404 use this code `app.use(globalErrorHandler);` in app.js. It references `app((err, req, res, next) => {})` which express automatically knows this is an error handling middleware
- `catchAsync()` replaces the `try catch` method, shown in code below. `.catch(next);` is as good as `.catch(err => next(err));` because `.catch` catches what comes after an operations when there is an error. Then the code continues down `app.js` until it hits `app.use(globalErrorHandler);` where the error is being handled.
```
module.exports = fn => {
  return (req, res, next) => {
    fn(req, res, next).catch(next);
  };
};
```
- `return (req, res, next) => {...}` is needed so that the functions within `fn(req, res, next).catch(next);` will be assigned to the method calling it i.e. `exports.createTour`
- `if (error.name === 'CastError') error = handleCastErrorDB(error);` CastError is a mongoose specific error when ID not found
- `if (error.code === 11000) error = handleDuplicateFieldsDB(error);` It is when trying to create new object in database when it already exists. It isn't a Mongoose error, but a MongoDB specific error.
- `if (error.name === 'ValidationError')` is for invalid data type error from Mongoose.
- to handle uncaught and unhandled errors that are no inside `app.js`
```
process.on('uncaughtException', err => {
  console.log('UNCAUGHT EXCEPTION! 💥 Shutting down...');
  console.log(err.name, err.message);
  process.exit(1);
});

...

process.on('unhandledRejection', err => {
  console.log('UNHANDLED REJECTION! 💥 Shutting down...');
  console.log(err.name, err.message);
  server.close(() => {
    process.exit(1);
  });
});
```
- `server.close` is used in unhandled rejection to allow the server to close gracefully