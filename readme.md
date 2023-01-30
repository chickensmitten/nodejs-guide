# Getting Started with NodeJS
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

