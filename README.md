Streamers
=========

A library of stream classes, pipe functions and other stream-related utilities for node.js

Synopsis
--------

    var streamers = require("streamers");

    var source = new streamers.BufferReadStream("first\nsecond\nthird\n");
    var reader = new streamers.ProactiveReadStream(source);

    function readNextLine(reader, lineNumber){
        reader.read("\n", function(line, error){
            if(line){
                console.log("Line " + lineNumber + ": " + line.toString());
                readNextLine(reader, lineNumber + 1);
            }
        });
    }

    readNextLine(reader, 1);

BufferReadStream
----------------

Use a buffer object as the source for a readable stream

* new streamers.BufferReadStream(source, [options])
* stream.setEncoding(encoding)
* stream.pause()
* stream.resume()
* stream.destroy()
* stream.readable
* Event: 'data'
* Event: 'end'

Constructor options:

    {
        chunkSize: undefined,
        readDelay: 0
    }

`chunkSize` sets the buffer length of the chunk given as the data argument in
data events, a value of undefined or null means a single data event will be
emitted with a reference to the entire buffer data. `readDelay` sets
the idle time between data events.

BufferWriteStream
-----------------

Creates a growable buffer object to use as a sink for a writable stream

* new streamers.BufferWriteStream([options])
* stream.write(string, encoding)
* stream.write(buffer)
* stream.destroy()
* stream.getBuffer()
* stream.getCommittedSlice()
* stream.consume(length)
* stream.writable
* Event: 'drain'

Constructor options:

    {
        onWrite: undefined,
        onFull: <reference to an internal function that grows the buffer>,
        onEnd: <reference to a noop function>,
        minBlockAllocSize: 0,
        maxBlockAllocSize: undefined,
        drainDelay: 0
    }

`onWrite` is an optional callback function (`function(continuationCallback,
thisCallback)`) that allows the constructor caller to be notified when data is
written to the buffer, the onWrite callback function can perform async
operations and then call the `continuationCallback` function (it must do this
in every case). `onFull` is a function that's called (`function(buffer,
extraSize, continuationCallback)`) when the buffer runs out of space, the `
continuationCallback` function is called with two buffer object arguments (the
second argument should be a buffer slice of the first argument). `onEnd` is a
function that's called when the stream user calls `.end()`. `minBlockAllocSize`
and `maxBlockAllocSize` sets the minimum and maximum value range for the extra
buffer length that is requested when the buffer becomes full.
`minBlockAllocSize` also sets the initial buffer length. `drainDelay` sets the
time duration to wait before emitting a drain event.

ProactiveReadStream
-------------------

Perform asynchronous read operations on a readable stream

* new streamers.ProactiveReadStream(source, [bufferWriteStreamOptions])
* proactor.read(completionCondition, callback)
* proactor.read(string, callback)
* proactor.read(number, callback)
* proactor.cancel()
