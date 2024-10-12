# Errors

Writing code with errors is a natural part of programming. But rather than avoiding them at all costs, we should learn to understand them! Errors provide us valuable information about how we can improve our programs.

## What is an error? Why are they “thrown”?

- An error is any code that prevents a program from running successfully.
- In the world of programming, we say that **“an error is thrown”**.
- An error that is not handled is considered **”uncaught”** until we fix it. Uncaught errors will cause the program to crash.

```jsx
const str = 'hello world'
str.push('!!!')
// Uncaught TypeError: str.push is not a function
```

You can manually throw an error in JavaScript like this:

```jsx
throw Error('an error occurred')
```

## What causes an error? Syntax and Runtime Errors

There are many causes of errors but in general there are two categories:
- **Syntax Errors:** The code you’ve written is invalid and can’t be executed
    - e.g. you’re missing a closing `"` or you’ve used a keyword that doesn’t belong
    
    ```jsx
    console.log("foo)
    
    // console.log("foo)
    //             ^^^^^
    //
    // Uncaught SyntaxError: Invalid or unexpected token
    ```
    
- **Runtime Errors:** The code you’ve written can be executed but an error has occured as a result of faulty logic or improper use of data types
    - e.g. you’ve attempted to invoke the `.push` method on a variable holding a string which doesn’t have that method
    
    ```jsx
    "hello".push('hi')
    // Uncaught TypeError: "hello".push is not a function
    ```
        

## Types of Errors


### Syntax Errors:

#### `SyntaxError`:
    
*Very common*. Indicates that a program is not valid JavaScript. These errors may only be generated and propagated as a result of code evaluation. Code evaluation may happen as a result of `eval`, `Function`, `require`, or [vm](https://nodejs.org/api/vm.html). These errors are almost always indicative of a broken program.

```js
console.log("hello);
// Uncaught SyntaxError: Invalid or unexpected token
```

### Runtime Errors:

#### `ReferenceError` 

*Very common*. Indicates that an attempt is being made to access a variable that is not defined. Such errors commonly indicate typos in code, or an otherwise broken program.
    
```jsx
doesNotExist;
// Throws ReferenceError, doesNotExist is not a variable in this program.
```
    
#### `TypeError` 

*Very common*. Indicates that a provided argument is not an allowable type. For example, passing a function to a parameter which expects a string would cause a `TypeError`.

```jsx
"hello".push('hi')
// Uncaught TypeError: "hello".push is not a function
```

#### `SystemError` 

*Common*. Node.js generates system errors when exceptions occur within its runtime environment. These usually occur when an application violates an operating system constraint. For example, a system error will occur if an application attempts to read a file that does not exist.

<details><summary>This is a list of system errors commonly-encountered when writing a Node.js program.</summary>

For a comprehensive list, see the [`errno`(3) man page](https://man7.org/linux/man-pages/man3/errno.3.html).

- `EACCES` (Permission denied): An attempt was made to access a file in a way forbidden by its file access permissions.
- `EADDRINUSE` (Address already in use): An attempt to bind a server ([`net`](https://nodejs.org/api/net.html), [`http`](https://nodejs.org/api/http.html), or [`https`](https://nodejs.org/api/https.html)) to a local address failed due to another server on the local system already occupying that address.
- `ECONNREFUSED` (Connection refused): No connection could be made because the target machine actively refused it. This usually results from trying to connect to a service that is inactive on the foreign host.
- `ECONNRESET` (Connection reset by peer): A connection was forcibly closed by a peer. This normally results from a loss of the connection on the remote socket due to a timeout or reboot. Commonly encountered via the [`http`](https://nodejs.org/api/http.html) and [`net`](https://nodejs.org/api/net.html) modules.
- `EEXIST` (File exists): An existing file was the target of an operation that required that the target not exist.
- `EISDIR` (Is a directory): An operation expected a file, but the given pathname was a directory.
- `EMFILE` (Too many open files in system): Maximum number of [file descriptors](https://en.wikipedia.org/wiki/File_descriptor) allowable on the system has been reached, and requests for another descriptor cannot be fulfilled until at least one has been closed. This is encountered when opening many files at once in parallel, especially on systems (in particular, macOS) where there is a low file descriptor limit for processes. To remedy a low limit, run `ulimit -n 2048` in the same shell that will run the Node.js process.
- `ENOENT` (No such file or directory): Commonly raised by [`fs`](https://nodejs.org/api/fs.html) operations to indicate that a component of the specified pathname does not exist. No entity (file or directory) could be found by the given path.
- `ENOTDIR` (Not a directory): A component of the given pathname existed, but was not a directory as expected. Commonly raised by [`fs.readdir`](https://nodejs.org/api/fs.html#fsreaddirpath-options-callback).
- `ENOTEMPTY` (Directory not empty): A directory with entries was the target of an operation that requires an empty directory, usually [`fs.unlink`](https://nodejs.org/api/fs.html#fsunlinkpath-callback).
- `ENOTFOUND` (DNS lookup failed): Indicates a DNS failure of either `EAI_NODATA` or `EAI_NONAME`. This is not a standard POSIX error.
- `EPERM` (Operation not permitted): An attempt was made to perform an operation that requires elevated privileges.
- `EPIPE` (Broken pipe): A write on a pipe, socket, or FIFO for which there is no process to read the data. Commonly encountered at the [`net`](https://nodejs.org/api/net.html) and [`http`](https://nodejs.org/api/http.html) layers, indicative that the remote side of the stream being written to has been closed.
- `ETIMEDOUT` (Operation timed out): A connect or send request failed because the connected party did not properly respond after a period of time. Usually encountered by [`http`](https://nodejs.org/api/http.html) or [`net`](https://nodejs.org/api/net.html). Often a sign that a `socket.end()` was not properly called.

</details>

#### `AssertionError` 

*Less common.* Indicates the failure of an assertion. All errors thrown by the `node:assert` module will be instances of the `AssertionError` class.

#### `RangeError` 

*Less common.* Indicates that a provided argument was not within the set or range of acceptable values for a function; whether that is a numeric range, or outside the set of options for a given function parameter.

```js
require('node:net').connect(-1);
// Throws "RangeError: "port" option should be >= 0 and < 65536: -1"
```
    

## How to Read errors

Errors provide us valuable information about how we can improve our programs so let's learn how to read error messages!

Consider the code below which will throw an error. 

{% code title="index.js" overflow="wrap" lineNumbers="true" %}
```javascript
const causeTrouble = (arr) => {
	arr.push('!!!');
}

const playNice = () => {
	console.log('yipeeee');
}

const main = () => {
	causeTrouble('hello world');
	playNice();
}

main();

/* 
Order of Operations:
1. Run the main function on line 14
2. Run causeTrouble on line 10
3. Run the code inside causeTrouble which will throw the TypeError on line 2
4. When causeTrouble throws the error, the program will crash and playNice will not be executed

The following error message will be printed to the console:

/Users/benspector/Desktop/index.js:2
	str.push('!!!');
	    ^

TypeError: str.push is not a function
    at causeTrouble (/Users/benspector/Desktop/index.js:2:6)
    at main (/Users/benspector/Desktop/index.js:10:2)
    at Object.<anonymous> (/Users/benspector/Desktop/index.js:14:1)
    at Module._compile (node:internal/modules/cjs/loader:1256:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1310:10)
    at Module.load (node:internal/modules/cjs/loader:1119:32)
    at Module._load (node:internal/modules/cjs/loader:960:12)
    at Function.executeUserEntryPoint [as runMain] (node:internal/modules/run_main:81:12)
    at node:internal/main/run_main_module:23:47

Node.js v18.16.1
*/
```
{% endcode %}

To read the error, we want to look for the following:

- The **error type** (`SyntaxError`, `ReferenceError`, `TypeError`, etc…)
    - In this case, we have a `TypeError`
- The **error message** describing the problem.
    - `str.push is not a function`
- The **error call stack**
    - From most newest to oldest we see the function names, file names and line numbers tracing how we got to the error
        - `causeTrouble` in `index.js` on line `6` column `18` was invoked by…
        - `main` in `index.js` on line `14` column `2` which was invoked by…
        - `Object.<anonymous>` (which means the global scope) in `index.js` on line `17` column `1` which was executed by Node
        - Everything from `Module._compile` and below will almost always be there and will be the the same for runtime errors (for syntax errors, the error stack isn’t all that useful)
  

{% hint style="info" %}
The **call stack** is a data structure that Node uses to keep track of the functions that are called while the program is running. It saves function calls in a first-in-last-out (LIFO) order which means that the most recent function call is always at the top, followed by the function that called it, and so on.
{% endhint %}

## Handling Errors

- Uncaught errors will crash the program. For most errors, we can just edit our program and fix them.
- Some errors we can’t fix though. For example, if our program requests data from another program via the internet but the internet is down, then there is nothing we can do (other than fix the internet). What we _can_ do is prevent our program from crashing in these cases.
- We can plan ahead and add code to our program that **“catches a thrown error”** using `try` and `catch` blocks. This prevents the program from crashing and allows us to decide what to do next.

```jsx
const causeTrouble = (arr) => {
    try {
      // attempt to run the code that might throw an error
      arr.push('!!!');
    } catch (err) {
      // Handle the error here.
      console.log(`Oops! ${err.message}`);
      console.log('not to worry. carry on...');
    }
}

const playNice = () => {
    console.log('yipeeee');
}

const main = () => {
    causeTrouble('hello world');
    playNice();
}

main();

/* 
Prints to the console:

Oops! arr.push is not a function
not to worry. carry on...
yipeeee
*/
```

Explanation:

- The `try {}` code block attempts to do something that we suspect might throw an error. In this case, it attempts to use the `.push` method on the given `arr` value (which it assumes is an array). Since `causeTrouble` was invoked with a string `'hello world'` and NOT an array, a `TypeError` will be thrown.
- The `catch(err) { }` code block is executed if an error is thrown and is given the `TypeError` object which we can reference with the parameter-like variable `err`. It has properties like `.message` which lets us log the error message before continuing on.
- Since the error in `causeTrouble` was caught, the program doesn’t crash and `playNice` can be executed.