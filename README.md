# hyperblobs
![Test on Node.js](https://github.com/andrewosh/hyperblobs/workflows/Test%20on%20Node.js/badge.svg)

A simple blob store for Hypercore.

Each blob is identified by its unique bounds within the Hypercore, e.g. `{ byteOffset: 0, blockOffset: 0, blockLength: 5, byteLength: 327680 }`, which makes them easy to save and retrieve:
```js
const blobs = new Hyperblobs(core)
// ID is { byteOffset: 0, blockOffset: 0, blockLength: 1, byteLength: 11 }
const id = await blobs.put(Buffer.from('hello world', 'utf-8')) 
await blobs.get(id) // Buffer.from('hello world', 'utf-8')
```

You can also get from start/end bounds within a single blob:
```js
const blobs = new Hyperblobs(core)
// ID is { byteOffset: 0, blockOffset: 0, blockLength: 1, byteLength: 11 }
const id = await blobs.put(Buffer.from('hello world', 'utf-8')) 
await blobs.get(id, { start: 1, length: 2 }) // Buffer.from('el', 'utf-8')
```

If the blob is large, there's a Streams interface (`createReadStream` and `createWriteStream`) too.

## Installation
```
npm i hyperblobs
```

## API
`const Hyperblobs = require('hyperblobs')`

#### `const blobs = new Hyperblobs(core, opts)`
Create a new blob store wrapping a single Hypercore.

Options can include:
```js
{
  blockSize: 64KB // The block size that will be used when storing large blobs.
}
```

#### `const id = await blobs.put(blob, opts)`
Store a new blob. If the blob is large, it will be chunked according to `opts.blockSize` (default 64KB).

Options can include:
```js
{
  blockSize: 64KB, // The block size that will be used when storing large blobs.
  start: 0, // Relative offset to start within the blob
  end: blob.length - 1, // End offset within the blob (inclusive)
  length: blob.length // Number of bytes to read.
}
```

#### `const stream = blobs.createReadStream(id, opts)`
Create a Readable stream that will yield the `id` blob.

Options match the `get` options.

#### `const stream = blobs.createWriteStream(opts)`
Create a Writable stream that will save a blob.

The corresponding ID will be set on the stream at `stream.id`.

## License
MIT
