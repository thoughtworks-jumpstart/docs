# Callbacks as Event Handlers

In the previous section on JavaScript Event Loop, you see how the event handlers can be used to process events when they are ready (e.g. when a user clicks a button on browser page, or the operating system notifies JavaScript engine that a file is ready to be read).

One typical way to implement event handlers is through Callbacks.
In the example below, the codes call `readFile()` API (this is offered by Node.JS platform) which accept an event handler. The handler would be called when the data is ready.

```js
const fs = require("fs");

fs.readFile("/foo.txt", function (err, data) {
  console.log(data.toString("utf-8"));
});
```

If we write code in this style, then the event loop thread would not be blocked until the data is ready. This is the secret why a single thread can handle so many different tasks concurrently.

## Handle errors with callbacks

You probably noticed the event handler also takes in another argument called `err`, what is this thing?

It's a convention for callbacks to take an argument which represent errors when the asynchronous task does not finish normally.

For example, if the file you are trying to read does not exist at all, the callback would be called with an error which indicates the given file does not exist.

By convention, the error is usually the first argument in the callback. This is called **error-first callback**.

In case the asynchronous task finishes successfully, by convention the callback function should be called with first argument set to null, and subsequent arguments set to the result of running the asynchronous task.

With this kind of 'error-first' callback, typically you need to write your code this way:

```js
const fs = require("fs");

fs.readFile("/foo.txt", function (err, data) {
  // If an error occurred, handle it (throw, propagate, etc)
  if (err) {
    throw err;
  }
  // Otherwise, use the data
  console.log(data.toString("utf-8"));
});
```

```js
const fs = require("fs");
const gm = require("gm");

const widths = [100, 200, 400, 800];
const source = "/path/to/your/photos/";

fs.readdir(source, function (err, files) {
  if (err) {
    console.log("Error finding files: " + err);
  } else {
    files.forEach(function (filename, fileIndex) {
      console.log(filename);
      gm(source + filename).size(function (err, values) {
        if (err) {
          console.log("Error identifying file size: " + err);
        } else {
          console.log(filename + " : " + values);
          aspect = values.width / values.height;
          widths.forEach(
            function (width, widthIndex) {
              height = Math.round(width / aspect);
              console.log(
                "resizing " + filename + "to " + height + "x" + height
              );
              this.resize(width, height).write(
                dest + "w" + width + "_" + filename,
                function (err) {
                  if (err) console.log("Error writing file: " + err);
                }
              );
            }.bind(this)
          );
        }
      });
    });
  }
});
```

## Solutions to callback hell

Needless to say, this kind of codes are hard to read and maintain. What can we do about it?

### Refactoring codes to avoid nested structure

One of the choices is to refactor your code to extract the callbacks into separate functions so that you can avoid defining functions within other functions. That helps to keep your code indentation levels low.

Some examples can be found on [this website](http://callbackhell.com/).

### Use other JavaScript features (Promises, Async/Await)

Because of the problems introduced by callbacks, JavaScript has introduced other syntax to help you write asynchronous codes.

We will cover two of them in the next sections:

- promise
- async/await
