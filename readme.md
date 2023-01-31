# Complete NodeJS, ExpressJS
- Proper MVC file structure in ExpressJS
- Middleware lessons
- Introduction to MongoDB
- Mongoose lessons
- API for filtering data, sorting, limiting, pagination and aliasing
- Proper error handling
- Authentication, authorization and security
- Payments, email, file uploads

# Quick Intros
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

## Section 6, building express API
**Using Postman**
- Paste the URL
- Select the right method
- Input header (if any)
- Input body (if any)

**MVC**
- File path: server.js -> app.js -> routes -> controllers

**Middleware**
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

**ESLint + Prettier**
- ESLint find code for errors and bad coding practices.
- To install, run `npm i eslint prettier eslint-config-prettier eslint-plugin-prettier eslint-config-airbnb eslint-plugin-node eslint-plugin-import eslint-plugin-jsx-ally eslint-plugin-react --save-dev`
- the dependencies should be installed in dev in package.json
- ".prettierrc" and ".eslintrc.json" is where the rules can be found.
- 