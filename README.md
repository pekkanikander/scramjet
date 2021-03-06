![Scramjet Logo](https://signicode.com/scramjet-logo-light.svg)

**Version 3**

[![Master Build Status](https://travis-ci.org/signicode/scramjet.svg?branch=master)](https://travis-ci.org/signicode/scramjet)
[![Develop Build Status](https://travis-ci.org/signicode/scramjet.svg?branch=develop)](https://travis-ci.org/signicode/scramjet)
[![Dependencies](https://david-dm.org/signicode/scramjet/status.svg)](https://david-dm.org/signicode/scramjet)
[![Dev Dependencies](https://david-dm.org/signicode/scramjet/dev-status.svg)](https://david-dm.org/signicode/scramjet?type=dev)
[![Known Vulnerabilities](https://snyk.io/test/github/signicode/scramjet/badge.svg)](https://snyk.io/test/github/signicode/scramjet)

## What is scramjet

Scramjet is a powerful, yet simple functional stream programming framework written on top of node.js object streams that
exposes a standards inspired javascript API and written fully in native ES6. Thanks to it some built in optimizations
scramjet is much faster than similar frameworks in asynchronous operations (like for instance calling an API).

It is built upon the logic behind three well known javascript array operations - namingly map, filter and reduce. This
means that if you've ever performed operations on an Array in JavaScript - you already know Scramjet like the back of
your hand.

The main advantage of scramjet is running asynchronous operations on your data streams. First of all it allows you to
perform the transformations both synchronously and asynchronously by using the same API - so now you can "map" your
stream from whatever source and call any number of API's consecutively.

The benchmarks are published in the [scramjet-benchmark repo](https://github.com/signicode/scramjet-benchmark).

## Example

How about a CSV parser of all the parkings in the city of Wrocław from http://www.wroclaw.pl/open-data/...

```javascript
const request = require("request");
const StringStream = require("scramjet").StringStream;

let columns = null;
request.get("http://www.wroclaw.pl/open-data/opendata/its/parkingi/parkingi.csv")
    .pipe(new StringStream())
    .split("\n")
    .parse((line) => line.split(";"))
    .pop(1, (data) => columns = data)
    .map((data) => columns.reduce((acc, id, i) => (acc[id] = data[i], acc), {}))
    .on("data", console.log.bind(console))
```

## API Docs

Here's the list of the exposed classes and methods, please review the specific documentation for details:

* [```scramjet.DataStream```](docs/data-stream.md) - the base class for all scramjet classes.
* [```scramjet.BufferStream```](docs/buffer-stream.md) - a DataStream of Buffers.
* [```scramjet.StringStream```](docs/string-stream.md) - a DataStream of Strings.
* [```scramjet.MultiStream```](docs/multi-stream.md) - a DataStream of Strings.
* [```scramjet.plugin```](docs/index.md) - method for adding plugins, please see the docs
* [more on plugins](docs/plugins.md) - a description and link.

Note that:

* Most of the methods take a callback argument that operates on the stream items.
* The callback, unless it's stated otherwise, will receive an argument with the next chunk.
* If you want to perform your operations asynchronously, return a Promise, otherwise just return the right value.

The quick reference of the exposed classes:


<a name="DataStream"></a>
### ~DataStream ⇐ stream.PassThrough



[Detailed DataStream docs here](docs/data-stream.md)

| Method | Description | Example
|--------|-------------|---------
| new DataStream(opts) | Create the DataStream. | [DataStream example](../samples/data-stream-constructor.js) |
| dataStream.use(func) ⇒ <code>\*</code> | Calls the passed in place with the stream as first argument, returns result. | [use example](../samples/data-stream-use.js) |
| dataStream.tee(func) ⇒ <code>DataStream</code> | Duplicate the stream | [tee example](../samples/data-stream-tee.js) |
| dataStream.reduce(func, into) ⇒ <code>Promise</code> | Reduces the stream into a given accumulator | [reduce example](../samples/data-stream-reduce.js) |
| dataStream.each(func) ↩︎ | Performs an operation on every chunk, without changing the stream |  |
| dataStream.map(func, Clazz) ⇒ <code>DataStream</code> | Transforms stream objects into new ones, just like Array.prototype.map | [map example](../samples/data-stream-map.js) |
| dataStream.filter(func) ⇒ <code>DataStream</code> | Filters object based on the function outcome, just like | [filter example](../samples/data-stream-filter.js) |
| dataStream.while(func) ⇒ <code>DataStream</code> | Reads the stream while the function outcome is truthy. |  |
| dataStream.until(func) ⇒ <code>DataStream</code> | Reads the stream until the function outcome is truthy. |  |
| dataStream.pipe(to, options) ⇒ <code>Writable</code> | Override of node.js Readable pipe. |  |
| dataStream.toBufferStream(serializer) ⇒ <code>BufferStream</code> | Creates a BufferStream | [toBufferStream example](../samples/data-stream-tobufferstream.js) |
| dataStream.stringify(serializer) ⇒ <code>StringStream</code> | Creates a StringStream | [stringify example](../samples/data-stream-tostringstream.js) |
| dataStream.toArray(initial) ⇒ <code>Promise</code> | Aggregates the stream into a single Array |  |
| dataStream.debug(func) ⇒ <code>DataStream</code> | Injects a ```debugger``` statement when called. | [debug example](../samples/data-stream-debug.js) |
| dataStream.cluster(hashFunc, count, stringify, parse) ⇒ <code>ClusteredDataStream</code> | [NYI] Distributes processing to multiple forked subprocesses. |  |
| dataStream.separate(func, createOptions) ⇒ <code>DataStream</code> | Separates execution to multiple streams using the hashes returned by the passed callback. | [separate example](../samples/data-stream-separate.js) |
| dataStream.slice(start, end, func) ⇒ <code>DataStream</code> | Gets a slice of the stream to the callback function. | [slice example](../samples/data-stream-slice.js) |
| dataStream.accumulate(func, into) ⇒ <code>Promise</code> | Accumulates data into the object. | [accumulate example](../samples/data-stream-accumulate.js) |
| dataStream.reduceNow(func, into) ⇒ <code>\*</code> | Reduces the stream into the given object, returning it immediately. | [reduceNow example](../samples/data-stream-reduceNow.js) |
| dataStream.remap(func, Clazz) ⇒ <code>DataStream</code> | Remaps the stream into a new stream. | [remap example](../samples/data-stream-remap.js) |
| dataStream.flatMap(func, Clazz) ⇒ <code>DataStream</code> | Takes any method that returns any iterable and flattens the result. | [flatMap example](../samples/data-stream-flatmap.js) |
| dataStream.unshift(item) ↩︎ | Pushes any data at call time |  |
| dataStream.flatten() ⇒ <code>DataStream</code> | A shorthand for streams of Arrays to flatten them. |  |
| dataStream.batch(count) ⇒ <code>DataStream</code> | Aggregates chunks in arrays given number of number of items long. | [batch example](../samples/data-stream-batch.js) |
| dataStream.timeBatch(ms, count) ⇒ <code>DataStream</code> | Aggregates chunks to arrays not delaying output by more than the given number of ms. | [timeBatch example](../samples/data-stream-timebatch.js) |
| dataStream.assign(func) ⇒ <code>DataStream</code> | Transforms stream objects by assigning the properties from the returned | [assign example](../samples/data-stream-assign.js) |
| dataStream.shift(count, func) ⇒ <code>DataStream</code> | Shifts the first n items from the stream and pipes the other | [shift example](../samples/data-stream-shift.js) |
| DataStream.fromArray(arr) ⇒ <code>DataStream</code> | Create a DataStream from an Array | [fromArray example](../samples/data-stream-fromarray.js) |
| DataStream.fromIterator(iter) ⇒ <code>DataStream</code> | Create a DataStream from an Iterator | [fromIterator example](../samples/data-stream-fromiterator.js) |


<a name="StringStream"></a>
### ~StringStream ⇐ DataStream

A stream of string objects for further transformation on top of DataStream.

[Detailed StringStream docs here](docs/string-stream.md)

| Method | Description | Example
|--------|-------------|---------
| new StringStream(encoding) | Constructs the stream with the given encoding | [StringStream example](../samples/string-stream-constructor.js) |
| stringStream.shift(bytes, func) ⇒ <code>StringStream</code> | Shifts given length of chars from the original stream | [shift example](../samples/string-stream-shift.js) |
| stringStream.split(splitter) ⇒ <code>StringStream</code> | Splits the string stream by the specified regexp or string | [split example](../samples/string-stream-split.js) |
| stringStream.match(splitter) ⇒ <code>StringStream</code> | Finds matches in the string stream and streams the match results | [match example](../samples/string-stream-match.js) |
| stringStream.toBufferStream() ⇒ <code>StringStream</code> | Transforms the StringStream to BufferStream | [toBufferStream example](../samples/string-stream-tobufferstream.js) |
| stringStream.parse(parser) ⇒ <code>DataStream</code> | Parses every string to object | [parse example](../samples/string-stream-parse.js) |
| StringStream.SPLIT_LINE | A handly split by line regex to quickly get a line-by-line stream |  |
| StringStream.fromString(str, encoding) ⇒ <code>StringStream</code> | Creates a StringStream and writes a specific string. |  |


<a name="BufferStream"></a>
### ~BufferStream ⇐ DataStream

A factilitation stream created for easy splitting or parsing buffers

[Detailed BufferStream docs here](docs/buffer-stream.md)

| Method | Description | Example
|--------|-------------|---------
| new BufferStream(opts) | Creates the BufferStream | [BufferStream example](../samples/buffer-stream-constructor.js) |
| bufferStream.shift(chars, func) ⇒ <code>BufferStream</code> | Shift given number of bytes from the original stream | [shift example](../samples/string-stream-shift.js) |
| bufferStream.split(splitter) ⇒ <code>BufferStream</code> | Splits the buffer stream into buffer objects | [split example](../samples/buffer-stream-split.js) |
| bufferStream.breakup(number) ⇒ <code>BufferStream</code> | Breaks up a stream apart into chunks of the specified length | [breakup example](../samples/buffer-stream-breakup.js) |
| bufferStream.stringify(encoding) ⇒ <code>StringStream</code> | Creates a string stream from the given buffer stream | [stringify example](../samples/buffer-stream-tostringstream.js) |
| bufferStream.parse(parser) ⇒ <code>DataStream</code> | Parses every buffer to object | [parse example](../samples/buffer-stream-parse.js) |


<a name="MultiStream"></a>
### ~MultiStream

An object consisting of multiple streams than can be refined or muxed.

[Detailed MultiStream docs here](docs/multi-stream.md)

| Method | Description | Example
|--------|-------------|---------
| new MultiStream(streams, options) | Crates an instance of MultiStream with the specified stream list | [MultiStream example](../samples/multi-stream-constructor.js) |
| multiStream.streams : <code>Array</code> | Array of all streams |  |
| multiStream.map(aFunc) ⇒ <code>MultiStream</code> | Returns new MultiStream with the streams returned by the tranform. | [map example](../samples/multi-stream-map.js) |
| multiStream.filter(func) ⇒ <code>MultiStream</code> | Filters the stream list and returns a new MultiStream with only the | [filter example](../samples/multi-stream-filter.js) |
| multiStream.mux(cmp) ⇒ <code>DataStream</code> | Muxes the streams into a single one | [mux example](../samples/multi-stream-mux.js) |
| multiStream.add(stream) | Adds a stream to the MultiStream | [add example](../samples/multi-stream-add.js) |
| multiStream.remove(stream) | Removes a stream from the MultiStream | [remove example](../samples/multi-stream-remove.js) |


## Browserifying

Scramjet works in the browser too, there's a nice, self-contained sample in here, just run it:

```bash
    git clone https://github.com/signicode/scramjet.git
    cd scramjet
    npm install .
    cd samples/browser
    npm start # point your browser to http://localhost:30035 and open console
```

If you need your scramjet version for the browser, grab browserify and just run:

```bash
    browserify lib/index -standalone scramjet -o /path/to/your/browserified-scramjet.js
```

With this you can run your transformations in the browser, use websockets to send them back and forth. If you do and fail for some reason, please remember to be issuing those issues - as no one person can test all the use cases and I am but one person.

## Usage

Scramjet uses functional programming to run transformations on your data streams in a fashion very similar to the well known event-stream node module. Most transformations are done by passing a transform function. You can write your function in two ways:

1. Synchronous

 Example: a simple stream transform that outputs a stream of objects of the same id property and the length of the value string.

 ```javascript
    datastream.map(
        (item) => ({id: item.id, length: item.value.length})
    )
 ```

2. Asynchronous (using Promises)

 Example: A simple stream that fetches an url mentioned in the incoming object

 ```javascript
    datastream.map(
        (item) => new Promise((resolve, reject) => {
            request(item.url, (err, res, data) => {
                if (err)
                    reject(err); // will emit an "error" event on the stream
                else
                    resolve(data);
            });
        })
    )
 ```

The actual logic of this transform function is as if you passed your function
to the ```then``` method of a Promise resolved with the data from the input
stream.

## License and contributions

As of version 2.0 Scramjet is MIT Licensed.

## Help wanted

The project need's your help! There's lots of work to do - transforming and muxing, joining and splitting, browserifying, modularizing, documenting and issuing those issues.

If you want to help and be part of the Scramjet team, please reach out to me, signicode on Github or email me: scramjet@signicode.com.
