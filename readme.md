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
- On the top right side, Postman can support environments. Then using double curly brackets "{{ <NameField> }}" to use the environment variables.
- in tests, you can also set the environment variable with javascript code snippets `pm.environment.set("jwt", pm.response.json().token);`
- Postman can do auto create documentation.

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
- Virtual Populate with Mongoose. Keeping a reference of all the child documents on the parents document without persisting that information on the database. When reviews are called, the tour information will be pulled along as well.
```
// Virtual populate
tourSchema.virtual('reviews', {
  ref: 'Review',
  foreignField: 'tour',
  localField: '_id'
});
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

## Signing Up Users
### Creating User
- When creating a sign up user, be specific on the fields. 
```
// use this method to sign up users
exports.signup = catchAsync(async (req, res, next) => {
  const newUser = await User.create({
    name: req.body.name,
    email: req.body.email,
    password: req.body.password,
    passwordConfirm: req.body.passwordConfirm
  });

  createSendToken(newUser, 201, res);
});

// don't use this method
const newUser = await User.create(req.body);

```

### Managing Password
1. make sure password and password confirm are the same using validation
```
  passwordConfirm: {
    type: String,
    required: [true, 'Please confirm your password'],
    validate: {
      // This only works on CREATE and SAVE!!!
      validator: function(el) {
        return el === this.password;
      },
      message: 'Passwords are not the same!'
    }
  },
```
2. using bcrypt hash in pre save to hash the password before saving. Passwords can also be hashed in the front end with bcrypt
```
userSchema.pre('save', async function(next) {
  // Only run this function if password was actually modified
  if (!this.isModified('password')) return next();

  // Hash the password with cost of 12
  this.password = await bcrypt.hash(this.password, 12);

  // Delete passwordConfirm field
  this.passwordConfirm = undefined;
  next();
});
```

### How JWT tokens work
- to install jwt `npm i jsonwebtoken`
- JWT based Login process
  1. User make a post request to server with email and password
  2. Server check if user exists and that the password is correct. Then a unique JWT token is create using a secret that is stored in the server
  3. Server will send the JWT back to the client to store as a cookie or in localstorage
  4. To access protected route, the user sends the JWT along in the request
  5. Once it hits the server, the server will check if JWT is valid by comparing the received JWT token with the regenerated  one to allow access
  6. If it is right, the protected data will be sent to the client. If not, an error will be sent.
- All JWT token transactions must happen over https
- Code for JWT
```
const signToken = id => {
  return jwt.sign({ id }, process.env.JWT_SECRET, {
    expiresIn: process.env.JWT_EXPIRES_IN
  });
};

const createSendToken = (user, statusCode, res) => {
  const token = signToken(user._id);
  const cookieOptions = {
    expires: new Date(
      Date.now() + process.env.JWT_COOKIE_EXPIRES_IN * 24 * 60 * 60 * 1000
    ),
    httpOnly: true
  };
  if (process.env.NODE_ENV === 'production') cookieOptions.secure = true;

  res.cookie('jwt', token, cookieOptions);

  // Remove password from output
  user.password = undefined;

  res.status(statusCode).json({
    status: 'success',
    token,
    data: {
      user
    }
  });
};
```
- Can use "jwt.io" as a debugger
- this code `if (process.env.NODE_ENV === 'production') cookieOptions.secure = true;` only allow the cookie to be sent through https

### Login
- when logging in, the code must add `.select('+password');` in `const user = await User.findOne({ email }).select('+password');` because in userModel, the user's password field is `select: false` to avoid getAllUsers from returning with password, even though it is encrypted.
```
// in user controller
if (!user || !(await user.correctPassword(password, user.password))) {
  return next(new AppError('Incorrect email or password', 401));
}

// in user model
userSchema.methods.correctPassword = async function(
  candidatePassword,
  userPassword
) {
  return await bcrypt.compare(candidatePassword, userPassword);
};
```

## Instance and Class/Static Methods
- `userSchema.methods` aka instance method
- `userSchema.static` aka static method or class method
- example implementation with static
```
reviewSchema.statics.calcAverageRatings = async function(tourId) {
  const stats = await this.aggregate([
    {
      $match: { tour: tourId }
    },
    {
      $group: {
        _id: '$tour',
        nRating: { $sum: 1 },
        avgRating: { $avg: '$rating' }
      }
    }
  ]);
  // console.log(stats);

  if (stats.length > 0) {
    await Tour.findByIdAndUpdate(tourId, {
      ratingsQuantity: stats[0].nRating,
      ratingsAverage: stats[0].avgRating
    });
  } else {
    await Tour.findByIdAndUpdate(tourId, {
      ratingsQuantity: 0,
      ratingsAverage: 4.5
    });
  }
};
```

## Gotcha for lifecycles (reviewSchema.pre and reviewSchema.post) with updating and deleting
- Reengaging `calcAverageRatings` when updating or deleting is more difficult than creating, because `.save()` is not engaged. Use the code below to reengage it. Note that the review data is save to `this.r`
```
// findByIdAndUpdate
// findByIdAndDelete
reviewSchema.pre(/^findOneAnd/, async function(next) {
  this.r = await this.findOne();
  // console.log(this.r);
  next();
});

reviewSchema.post(/^findOneAnd/, async function() {
  // await this.findOne(); does NOT work here, query has already executed
  await this.r.constructor.calcAverageRatings(this.r.tour);
});
```

## Access with protected routes
- use `.protect` in `.get(authController.protect, tourController.getAllTours)`
- add protects exports `exports.protect = catchAsync(async (req, res, next) => {}`
- Example code implementation to access protected routes
```
exports.protect = catchAsync(async (req, res, next) => {
  // 1) Getting token and check of it's there
  let token;
  if (
    req.headers.authorization &&
    req.headers.authorization.startsWith('Bearer')
  ) {
    token = req.headers.authorization.split(' ')[1];
  }

  if (!token) {
    return next(
      new AppError('You are not logged in! Please log in to get access.', 401)
    );
  }

  // 2) Verification token
  const decoded = await promisify(jwt.verify)(token, process.env.JWT_SECRET);

  // 3) Check if user still exists
  const currentUser = await User.findById(decoded.id);
  if (!currentUser) {
    return next(
      new AppError(
        'The user belonging to this token does no longer exist.',
        401
      )
    );
  }

  // 4) Check if user changed password after the token was issued
  if (currentUser.changedPasswordAfter(decoded.iat)) {
    return next(
      new AppError('User recently changed password! Please log in again.', 401)
    );
  }

  // GRANT ACCESS TO PROTECTED ROUTE
  req.user = currentUser;
  next();
});
```

## Authorization and Permissions
- can use the code below in routes to allow specific user roles to perform specific functions.
```
  .delete(
    authController.protect,
    authController.restrictTo('admin', 'lead-guide'),
    tourController.deleteTour
  );
```
- Use model add roles
```
role: {
    type: String,
    enum: ['user', 'guide', 'lead-guide', 'admin'],
    default: 'user'
  },
```
- in controller add the following code. 
```
exports.restrictTo = (...roles) => {
  return (req, res, next) => {
    // roles ['admin', 'lead-guide']. role='user'
    if (!roles.includes(req.user.role)) {
      return next(
        new AppError('You do not have permission to perform this action', 403)
      );
    }

    next();
  };
};

```

## Password reset function
- refer to `exports.forgotPassword` and `exports.resetPassword` in controller and `userSchema.methods.createPasswordResetToken` in model
- When creating password reset function, use `.save()` and not `.FindByIdAndUpdate`, because validation only runs with save
- refer to `exports.updatePassword` for update password.

## Sending email
- to send email, use `npm i nodemailer`
- then use "mailtrap" to create SMTP for dev to collect email
```
// /utils/email.js
const nodemailer = require('nodemailer');

const sendEmail = async options => {
  // 1) Create a transporter
  const transporter = nodemailer.createTransport({
    host: process.env.EMAIL_HOST,
    port: process.env.EMAIL_PORT,
    auth: {
      user: process.env.EMAIL_USERNAME,
      pass: process.env.EMAIL_PASSWORD
    }
  });

  // 2) Define the email options
  const mailOptions = {
    from: 'Jonas Schmedtmann <hello@jonas.io>',
    to: options.email,
    subject: options.subject,
    text: options.message
    // html:
  };

  // 3) Actually send the email
  await transporter.sendMail(mailOptions);
};

module.exports = sendEmail;

```

## Security Best Practices
- COMPROMISED DATABASE
  - Strongly encrypt passwords with salt and hash (bcrypt)
  - Strongly encrypt password reset tokens (SHA 256)
- NOSQL QUERY INJECTION
  - Use mongoose for MongoDB (because of SchemaTypes) 
  - Sanitize user input data
- BRUTE FORCE ATTACKS
  - Use bcrypt (to make login requests slow)
  - Implement rate limiting (express-rate-limit)
  - Implement maximum login attempts
- CROSS-SITE SCRIPTING (XSS) ATTACKS
  - Store JWT in HTTPOnly cookies
  - Sanitize user input data
  - Set special HTTP headers (helmet package)
- DENIAL-OF-SERVICE (DOS) ATTACK
  - Implement rate limiting (express-rate-limit) 
  - Limit body payload (in body-parser)
  - Avoid evil regular expressions
- OTHER BEST PRACTICES AND SUGGESTIONS
  - Always use HTTPS
  - Create random password reset tokens with expiry dates 
  - Deny access to JWT after password change
  - Don’t commit sensitive config data to Git
  - Don’t send error details to clients
  - Prevent Cross-Site Request Forgery (csurf package) 
  - Require re-authentication before a high-value action 
  - Implement a blacklist of untrusted JWT
  - Confirm user email address after first creating account 
  - Keep user logged in with refresh tokens
  - Implement two-factor authentication
  - Prevent parameter pollution causing Uncaught Exceptions
- install `npm i express-rate-limit` to rate limit requests
```
const limiter = rateLimit({
  max: 100,
  windowMs: 60 * 60 * 1000,
  message: 'Too many requests from this IP, please try again in an hour!'
});
app.use('/api', limiter);
```
- to limit `req.body` size `app.use(express.json({ limit: '10kb' }));`
- use `npm i helmet` for `app.use(helmet());` to set security headers
- use `npm i express-mongo-sanitize` to sanitize data against SQL injections. `app.use(mongoSanitize());`
- use `npm i xss-clean` to clean malicious html code `app.use(xss());`
- to prevent parameter pollution, `npm i hpp`
```
// the whitelist allows for duplicate methods.
app.use(
  hpp({
    whitelist: [
      'duration',
      'ratingsQuantity',
      'ratingsAverage',
      'maxGroupSize',
      'difficulty',
      'price'
    ]
  })
);
```

## Data Modelling
- Data can be referenced or embedded in MongoDB. Deciding on which one to use will depend on 
  - relationship types: 1 to few is better for embedding. others are for referencing
  - data accessing patterns: High read/write ratio use embedding. Else use referencing.
  - data closeness: We use embedding, datasets belong to each other, like it is a subset of a set i.e. user + email. When we need to frequently query data on their own, we use referencing i.e. movies + images.
- Referencing has the following:
  - child referencing: A parent references a child. useful when there are few.
  - parent referencing: Children references the parent. useful when there are many to tons of children.
  - two-way referencing: child and parents referencing each other. for many to many relationships.
- referencing in mongoose
```
  guides: [
    {
      type: mongoose.Schema.ObjectId,
      ref: 'User'
    }
  ]
```
- embedding in mongoose
```
// tourModel
guides: Array

// then find the users by ID before saving and add it to the guides
// tourSchema.pre('save', async function(next) {
//   const guidesPromises = this.guides.map(async id => await User.findById(id));
//   this.guides = await Promise.all(guidesPromises);
//   next();
// });
```
- use `Tour.findById(id).populate(path: "<name>". select: "<-__V -passwordChangedAt>")` to get the tour with the guides in JSON.
- Or predefine it at model to execute it pre find
```
tourSchema.pre(/^find/, function(next) {
  this.populate({
    path: 'guides',
    select: '-__v -passwordChangedAt'
  });

  next();
});
```

## Nested Routes
- When creating a nested route where the a review is being created, with this example URL "POST /tour/234fad4/reviews" . You will need to follow the this path: through the tour route, go to review route, merge the params, then create the review. Example implementation below
  - create `router.use('/:tourId/reviews', reviewRouter);` in tourRoutes.js
  - then in "reviewRoutes.js" add `const router = express.Router({ mergeParams: true });`
  - then the params will be merged to this where the review is created.
  ```
  router
  .route('/')
  .get(reviewController.getAllReviews)
  .post(
    authController.restrictTo('user'),
    reviewController.setTourUserIds,
    reviewController.createReview
  );
  ```
  - in controller allow:
  ```
  exports.setTourUserIds = (req, res, next) => {
    // Allow nested routes
    if (!req.body.tour) req.body.tour = req.params.tourId;
    if (!req.body.user) req.body.user = req.user.id;
    next();
  };
  ```

## Indexes with MongoDB to improve read performance
- index is best used by studying the access patterns then index according to the access patterns to improve the read performance.
- `explain` in `const doc = await features.query.explain();` will provide a lot information about the query.
- use `index` for indexing `tourSchema.index({ price: 1, ratingsAverage: -1 });`. this means that when searching for price or ratingsAverage, it will scan less documents with "totalDocsExamined" to return the targeted document "nReturned"

## Geospatial queries
- The code below help you find a tour near you
  - first create routes `.route('/tours-within/:distance/center/:latlng/unit/:unit')` or the following methods
  ```
  // /tours-within?distance=233&center=-40,45&unit=mi
  // /tours-within/233/center/-40,45/unit/mi
  ```
  - Then in controller, use the following codes. Note the `$geoWithin`
  ```

  ...
  const tours = await Tour.find({
    startLocation: { $geoWithin: { $centerSphere: [[lng, lat], radius] } }
  });

  // also can use $near instead of $geoWithin
  ...

  ```
- Calculating distances
  - using `$geoNear`. To use it, you will need `2dsphere` indexing.
  - `$project` is used to only get `distance` and `name` from the tour object
  ```
  tourSchema.index({ startLocation: '2dsphere' });
  ...

    const distances = await Tour.aggregate([
      {
        $geoNear: {
          near: {
            type: 'Point',
            coordinates: [lng * 1, lat * 1]
          },
          distanceField: 'distance',
          distanceMultiplier: multiplier
        }
      },
      {
        $project: {
          distance: 1,
          name: 1
        }
      }
    ]);
  ...

  ```

## Server side rendering Pug Template
- Previously with ruby on rails, I have been using server side renderring, where the HTML, CSS and Javascript templates are built on the server side before being sent to the browser. The new modular approach for some time now has been to do client side renderring that consumes API from the server.
- Setup Pug Template
  - `npm i pug`
  - To setup the pug template engine, do
  ```
  app.set('view engine', 'pug');
  app.set('views', path.join(__dirname, 'views')); // path.join will auto correct for any slashes, cause sometimes, the paths does or doesn't have it
  ```
  - Create views folder
  - then add "base.pug" into the views folder where we can add html templates
  - then set routes in "app.js" with the following code:
  ```
  app.use('/', viewRouter);
  router.get('/', authController.isLoggedIn, viewsController.getOverview);
  ```
- To pass data into pug template
  - in controller create a code like this
  ```
  exports.getOverview = catchAsync(async (req, res, next) => {
    // 1) Get tour data from collection
    const tours = await Tour.find();

    // 2) Build template
    // 3) Render that template using tour data from 1)
    res.status(200).render('overview', {
      title: 'All Tours',
      tours
    });
  });
  ```
  - then in pub template, call `each tour in tours` anywhere, then in the relevant place `span= tour.name`. In a string, use `#{title}` and `#{tours}` as normal
- To add partial templates, use `include` in `include _header`
- To extend a base template
  - in base.png add `block content`, then in all the relevant pages like tour.pug or overview.pug, add the following code:
  ```
  // /views/overview.pug
  extends base
  block content
  ...
  ```
  - then in routes and controller, do the following code to render overview.
  ```
  // routes
  router.get('/', authController.isLoggedIn, viewsController.getOverview);

  // overview controller
  res.status(200).render('overview', {
    title: 'All Tours',
    tours
  });  
  ```
- Use Mixin like a function in pug 
  - Declare the mixin above the block
  ```
  mixin overviewBox(label, text, icon)
  .overview-box__detail
    svg.overview-box__icon
      use(xlink:href=`/img/icons.svg#icon-${icon}`)
    span.overview-box__label= label
    span.overview-box__text= text
  ```
  - then use the mixin within the `block content` with `+overviewBox('Next date', date, 'calendar')`
  - if else statement `- if (guide.role === 'lead-guide')`
  - using javascript `- const parapraphs = tour.description.split('\n');`
- adding more code to `head` block with the code below:
```
block append head
  script(src='https://api.mapbox.com/mapbox-gl-js/v0.54.0/mapbox-gl.js')
  link(href='https://api.mapbox.com/mapbox-gl-js/v0.54.0/mapbox-gl.css' rel='stylesheet')
```

## Adding map with MapBox
  - first add the tour locations data to map class
  ```
  /views.tour.pug
  section.section-map
    #map(data-locations=`${JSON.stringify(tour.locations)}`)

  / then add CDN
  block append head
    script(src='https://api.mapbox.com/mapbox-gl-js/v0.54.0/mapbox-gl.js')
    link(href='https://api.mapbox.com/mapbox-gl-js/v0.54.0/mapbox-gl.css' rel='stylesheet')
  ```
  - then use Mapbox from mapbox.com by adding code to "/public/js/mapbox.js". Mapbo also has the npm method if using front end framework clients like NextJS

## Login
- use Axios to submit form
- after user logs in, have to request cookie
  - `npm i cookie-parser` 
  - `app use(cookieParser())`
  - then run `req.cookies` in authController, so that now we can verify the user based on authorization cookies and not just the header
  ```
  // 1) Getting token and check of it's there
  let token;
  if (
    req.headers.authorization &&
    req.headers.authorization.startsWith('Bearer')
  ) {
    token = req.headers.authorization.split(' ')[1];
  } else if (req.cookies.jwt) {
    token = req.cookies.jwt;
  }
  ```
- by putting `user` into `res.locals.user = currentUser;` pug template will get access to them