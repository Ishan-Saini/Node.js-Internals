NodeJS

**How Node.JS works?**
* * *
NodeJS is built on few things like :
1. V8 Engine - This is a JavaScript engine which basically parses the javascript code into the machine language.
2. Libuv - A library that focuses on asynchronous I/O which implements a lot of features in NodeJS like event loop, thread pool, networking, file system etc.
3. http-parse
4. c-ares
5. OpenSSL
6. zlib

- NodeJS process runs in a **single thread** which means the execution of program is done sequentially i.e. Process initialization -> Top-level code executed(which is not inside any callback function) -> Require modules -> Register event Callbacks -> Event loop

- When really heavy tasks like cryptography, compression, file system APIs, DNS lookups have to be executed, it becomes hard to not block the code (which is running in a single thread) and that's why **Thread Pool** comes into the picture. Event loop offloads these heavy tasks to thread pool which generally have 4 threads (which can be extended upto 128 threads) to be executed. This makes it sure that the code is not blocked and this is done automatically.

- **NodeJS Event Loop** - It is the heart of NodeJS architecture. It helps in the execution of code that is inside callback functions. It receives events and call necessary callback functions. The event loop searches for functions in **call stack** and execute them in the order they are called but this is not true for setTimeout() function. Whenever timer events or user-initiated events like click or keyboard events, or fetch responses are executed, the loop transfers them in **message queue**. Functions in message queue only executes when call stack is completely empty and that's the reason why setTimeout() only executes its callback function after every other function.
**The loop gives priority to the call stack, and it first processes everything it finds in the call stack, and once there's nothing in there, it goes to pick up things in the message queue.**

```
const bar = () => console.log('bar')
const baz = () => console.log('baz')
const foo = () => {
  console.log('foo')
  setTimeout(bar, 0)
  baz()
}
foo()
```
Result ->
foo
baz
bar

There is another queue called **Job queue** which are used by Promises. These function do not wait for the end of the call stack but instead executes immediately after the current function.

To know more about Node.js event loop, please go through https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick

I spent 2 days in wrapping my head around this :P


**Node.js Events & Event driven architecture**
* * *
NodeJS has EventEmitter class which is utilised by many core modules like http,fs etc. to use events.
An object of this class is used to emit named events. These named events are received by event listeners which are also defined by developers once these events finally happen.

```
const EventEmitter = require('events');
emitter = new EventEmitter();
emitter.on('demoEvent', () => callback function)
emitter.emit('demoEvent');
```

**Node.js Streams**
* * *
Streams are used to process a data in chunks or pieces rather than processing the whole data in the memory. It works just like streaming services eg. Youtube, Netflix
For example, If we were to read a really big textfile then we could first load the whole file in memory and then store it in a variable but streams load the file in chunks which means we can start using data even before it has been fully loaded.

Streams are of 4 types - 
1. Readable Stream
2. Writable Stream
3. Duplex Stream
4. Transform Stream 

Imp - pipe() function is used to kinda link the readable and writable stream which helps in avoiding back-pressure. 

**What is back-pressure** ?

If the response writable stream is kinda slow than the readable stream i.e. readable stream is reading at a much faster rate than the response writable stream sending back responses, it is called back-pressure.

We can fix this with `<readableStream>.pipe(<writableStream>)`


**Node.js Modules**
* * * 
Every JavaScript file is a module. 
Every module has a wrapper function (we don't see it obviously) which is actually an IIFE (Immediately Invoked Function Expression).

```
(function (exports, require, module, __filename, __dirname) {  

 });
```
These arguments are very useful for us.(eg. We use require to import a module)

There are two methods of exporting a module. 
1. `module.exports` - It is used when we need to export a single entity like a class or a function.

```
module.exports = class {

}
```

2. `exports.<property>` - It is used when we need to export multiple things as an object property.

```
exports.add = (a,b) => a + b;
exports.multiply = (a,b) => a * b;
```
We can import these using JS destructuring.

`const {add, multiply} = require(./path-to-file);`