---
duration: 2h
---

# Lab: getting started with Node.js & Git

## Objectives

- Getting up and ready with Node.js
- Project and repository initialization
- Web server in Node.js

## Tasks

1. Start a project
2. Create a simple Node.js script 
3. Create a simple HTTP server
4. Integrate Nodemon
5. Create a basic application with multiple routes
6. Reading from a JSON file
7. Publish your project to GitHub

## Notes

The lab starts a new Node.js project which serves as the basis for the following courses. Every week, incremental features are completed toward the creation of a working application.

In the end, it becomes the basis for your final project. The final grade reflects the delivered project, as well as its Git history.

## Part 2. Create a simple Node.js script 

### 1. Start working with a text editor (easy level)

Now, we start using a text editor or IDE (Atom, VS Code, WebStorm, or up to your choice). 

Open a project folder in your editor. You also can use bash commands for opening it. Being under the root of the project directory, run one of the commands:

```bash
# For VS Code
code .
# For Sublime Text
subl .
# For power users
vim .
```

### 2. Create a script (easy level)

Create a file `index.js` with the following content:

```js
console.log("Hello Node.js!")
```

Run the Node.js script in the terminal:

```bash
node index.js
```

It will print the message `Hello Node.js!`.

### 3. Define an NPM script (medium level)

Add the script `start` to the `package.json` file like this:

```json
{
  ...
  "scripts": {
    "start": "node index.js"
  },
  ...
}
```

Run the NPM script with the command:

```bash
npm run start
# or
npm start
```

It will do the same as in step 2.

## Part 3. Create an HTTP server

### 3.1. Create an HTTP server (medium level)

Modify the `index.js` file with the following content

```javascript
// Import a module
const http = require('http')

// Declare an http server
http.createServer(function (req, res) {

  // Write a response header
  res.writeHead(200, {'Content-Type': 'text/plain'})

  // Write a response content
  res.end('Hello World\n')

// Start the server
}).listen(8080)
```

Read and understand each line in this code using the [`http` Node.js official module documentation](https://nodejs.org/api/http.html). 

### 3.2. Run HTTP server (medium level)

Run the command:

```bash
npm run start
```

It will start a web server accessible on http://localhost:8080:
- open it in a browser
- or `curl localhost:8080` in a terminal to get the home page content

### 3.3. Define callback function (hard level)

Rewrite the code to define a callback function

```javascript
const serverHandle = function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'})
  res.end('Hello World\n')
}

http
.createServer(serverHandle)
.listen(8080)
```

Don't forget to restart the server. To terminate a blocking process in the terminal use the combination of keys `Ctrl + C`. Start it again with `npm start`.

### 3.4. Sending back HTML (hard level)

Change the content type & response content:

```javascript
// Define a string constant concatenating strings
const content = '<!DOCTYPE html>' +
'<html>' +
'    <head>' +
'        <meta charset="utf-8" />' +
'        <title>ECE AST</title>' +
'    </head>' + 
'    <body>' +
'       <p>Hello World!</p>' +
'    </body>' +
'</html>'

const serverHandle = function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/html'})
  res.write(content)
  res.end()
}

http
.createServer(serverHandle)
.listen(8080)
```

### 3.5. Get the current path (medium level)

Enrich the previous code with the following: 

```javascript 
...
// Import Node url module
const url = require('url')

const serverHandle = function (req, res) {
  // Retrieve and print the current path
  const path = url.parse(req.url).pathname
  console.log(path)

  res.writeHead(200, {'Content-Type': 'text/html'})
  res.write(path)
  res.end()
}

http
.createServer(serverHandle)
.listen(8080)
```

Access to your local website by different URLs like `localhost:8080` and `localhost:8080/my/path` and see what is printed in the terminal.

### 3.6. Get query parameters (medium level)

Enrich the previous code with the following:

```javascript 
const url = require('url')
const qs = require('querystring')

const serverHandle = function (req, res) {
  // Retrieve and print the queryParams
  const queryParams = qs.parse(url.parse(req.url).query)
  console.log(queryParams)

  res.writeHead(200, {'Content-Type': 'text/html'})
  res.write(content)
  res.end()
}

http
.createServer(serverHandle)
.listen(8080)
```

Access your local website by different URLs like `localhost:8080/?name=John&email=john@email.com`, and see what is printed in the terminal.

### 3.7. Basic routing example (hard level)

Enrich the previous code with the following:

```javascript 
const url = require('url')
const qs = require('querystring')

const serverHandle = function (req, res) {
  const route = url.parse(req.url)
  const path = route.pathname 
  const params = qs.parse(route.query)

  res.writeHead(200, {'Content-Type': 'text/plain'})

  if (path === '/hello' && 'name' in params) {
    res.write('Hello ' + params['name'])
  } else {
    res.write('Hello anonymous')
  }
  
  res.end()
}
```

Access to your local website by different URLs like `localhost:8080/hello?name=John`.

### 3.8. Organize the source code in a module (hard level)

Create `handles.js` and `index.js` files and reorganize the previous code like:

```javascript
// ./handles.js
// Necessary imports
module.exports = {
  serverHandle: function (req, res) {
    // ...
  } 
}
```

```javascript 
// ./index.js
const http = require('http')
const handles = require('./handles')

http
.createServer(handles.serverHandle)
.listen(8080)
```

## Part 4. Integrate Nodemon

Tired of restarting your webserver after every modification of the source code? Let's fix it!

### 4.1. Install Nodemon (easy level)

Run: 

```bash
npm install nodemon
# or
yarn add nodemon
# then
npx nodemon index.js
# npx avoids running `./node_modules/.bin/nodemon index.js`
```

Now, it will restart the web server when the file is updated. There is no need to restart it manually to apply modifications of code. Just refresh the page in a browser.

> Note! Don't forget to define a `.gitignore` to prevent committing the `node_modules` folder.

### 4.2. Define an NPM script in `package.json` (easy level)

Enrich your `scripts` in `package.json` with:

```json
"scripts": {
  ...
  "dev": "nodemon index.js"
  ...
}
```

And then always run when developing:

```bash
npm run dev
```

## Part 5. Create a basic application with multiple routes (hard level)

Create an application with 3 routes:

1. `/` explains how `/hello` works (containing the links)
2. `/hello` takes a `name` query parameter and:
  - random names reply `hello [name]`
  - your own name replies with a short intro of yourself
3. Any other path replies a 404 code with a not found message

## Part 6. Reading from a JSON file

1. Create a subfolder with the name `content` and create a JSON file `about.json` inside it with the example content like this:

```json
{
  "title": "About",
  "content": "Example content here.",
  "author": "Your Name",
  "date": "27/09/2022"
}
```

2. Create the route `/about` displaying the content of this JSON file:

- use `require()` method to access a file
- chose a proper `Content-Type` for displaying JSON

3. Create dynamic routing.

Parse the path and verify if a JSON file exists in the `content` folder. If yes, print its content. If no, redirect to the 404 error page.

## Part 7. Publish your project to GitHub

### 7.1. Document your project (medium level)

Add a `README.md` file with a title, an introduction, running/usage instructions, and your name.

### 7.2. Push it to GitHub (medium level)

Commit your changes, use your **PRIVATE** group repository on GitHub, and push it.
Create a tag `lab2` for the last commit and also push it.

## Part 8. Cultivate yourself

- [Learn Markdown](https://www.markdownguide.org/)
- [How to write README](https://dev.to/scottydocs/how-to-write-a-kickass-readme-5af9)
