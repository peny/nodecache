node-cache
===========

[![Build Status](https://secure.travis-ci.org/tcs-de/nodecache.png?branch=master)](http://travis-ci.org/tcs-de/nodecache)
[![Build Status](https://david-dm.org/tcs-de/nodecache.png)](https://david-dm.org/tcs-de/nodecache)
[![NPM version](https://badge.fury.io/js/node-cache.png)](http://badge.fury.io/js/node-cache)

[![NPM](https://nodei.co/npm/node-cache.png?downloads=true&downloadRank=true&stars=true)](https://nodei.co/npm/node-cache/)

# Simple and fast NodeJS internal caching.

A simple caching module that has `set`, `get` and `delete` methods and works a little bit like memcached.
Keys can have a timeout after which they expire and are cleaned from the cache.  
All keys are stored in a single object so the practical limit is at around 1m keys.

*Written in coffee-script*

# Install

```bash
  npm install node-cache
```

Or just require the `node_cache.js` file to get the superclass

# Examples:

## Initialize (INIT):

```js
var NodeCache = require( "node-cache" );
var myCache = new NodeCache();
```

### Options

- `stdTTL`: the standard ttl as number in seconds for every generated cache element. Default = 0 = unlimited
- `checkperiod`: The period in seconds, as a number, used for the automatic delete check interval. 0 = no periodic check 

```js
var NodeCache = require( "node-cache" );
var myCache = new NodeCache( { stdTTL: 100, checkperiod: 120 } );
```

## Store a key (SET):

`myCache.set( key, val, [ ttl ], [callback] )`

Sets a `key` `value` pair. It is possible to define a `ttl` (in seconds).  
Returns `true` on success.

```js
obj = { my: "Special", variable: 42 };
myCache.set( "myKey", obj, function( err, success ){
  if( !err && success ){
    console.log( success );
    // true
    // ... do something ...
  }
});
```

**Since `1.0.0`**:  
Callback is now optional. You can also use synchronous syntax.

```js
obj = { my: "Special", variable: 42 };
success = myCache.set( "myKey", obj, 10000 );
// true
```


## Retrieve a key (GET):

`myCache.get( key, [callback] )`

Gets a saved value from the cache.
Returns an empty object `{}` if not found or expired.
If the value was found it returns an object with the `key` `value` pair.

```js
myCache.get( "myKey", function( err, value ){
  if( !err ){
    console.log( value );
    // { "myKey": { my: "Special", variable: 42 } }
    // ... do something ...
  }
});
```

**Since `1.0.0`**:  
Callback is now optional. You can also use synchronous syntax.

```js
value = myCache.get( "myKey" );
// { "myKey": { my: "Special", variable: 42 } }
```

## Get multiple keys (MGET):

`myCache.get( [ key1, key2, ... ,keyn ], [callback] )`

Gets multiple saved values from the cache.
Returns an empty object `{}` if not found or expired.
If the value was found it returns an object with the `key` `value` pair.

```js
myCache.get( [ "myKeyA", "myKeyB" ], function( err, value ){
  if( !err ){
    console.log( value );
    /*
      {
        "myKeyA": { my: "Special", variable: 123 },
        "myKeyB": { the: "Glory", answer: 42 }
      }
    */
    // ... do something ...
  }
});
```

**Since `1.0.0`**:  
Callback is now optional. You can also use synchronous syntax.

```js
value = myCache.get( [ "myKeyA", "myKeyB" ] );
/*
  {
    "myKeyA": { my: "Special", variable: 123 },
    "myKeyB": { the: "Glory", answer: 42 }
  }
*/
```

## Delete a key (DEL):

`myCache.del( key, [callback] )`

Delete a key. Returns the number of deleted entries. A delete will never fail.

```
myCache.del( "myKey", function( err, count ){
  if( !err ){
    console.log( count ); // 1
    // ... do something ...
  }
});
```

**Since `1.0.0`**:  
Callback is now optional. You can also use synchronous syntax.

```js
value = myCache.del( myKeyA" );
// 1
```

## Delete multiple keys (MDEL):

`myCache.del( [ key1, key2, ... ,keyn ], [callback] )`

Delete multiple keys. Returns the number of deleted entries. A delete will never fail.

```js
myCache.del( [ "myKeyA", "myKeyB" ], function( err, count ){
  if( !err ){
    console.log( count ); // 2
    // ... do something ...
  }
});
```

**Since `1.0.0`**:  
Callback is now optional. You can also use synchronous syntax.

```js
value = myCache.del( [ "myKeyA", "myKeyB", "notExistendKey" ] );
// 2
```

## Change TTL (TTL):

`myCache.ttl( key, ttl, [callback] )`

Redefine the ttl of a key. Returns true if the key has been found and changed. Otherwise returns false.  
If the ttl-argument isn't passed the default-TTL will be used.

```js
myCache = new NodeCache( { stdTTL: 100 } )
myCache.ttl( "existendKey", 100, function( err, changed ){
  if( !err ){
    console.log( changed ); // true
    // ... do something ...
  }
});

myCache.ttl( "missingKey", 100, function( err, changed ){
  if( !err ){
    console.log( changed ); // false
    // ... do something ...
  }
});

myCache.ttl( "existendKey", function( err, changed ){
  if( !err ){
    console.log( changed ); // true
    // ... do something ...
  }
});
```

**Since `1.0.0`**:  
Callback is now optional. You can also use synchronous syntax.

```js
value = myCache.ttl( "existendKey", 100 );
// true
```

## Statistics (STATS):

`myCache.getStats()`

Returns the statistics.  

```js
myCache.getStats();
  /*
    {
      keys: 0,    // global key count
      hits: 0,    // global hit count
      misses: 0,  // global miss count
      ksize: 0,   // global key size count
      vsize: 0    // global value size count
    }
  */
```

## Flush all data (FLUSH):

`myCache.flushAll()`

Flush all data.  

```js
myCache.flushAll();
myCache.getStats();
  /*
    {
      keys: 0,    // global key count
      hits: 0,    // global hit count
      misses: 0,  // global miss count
      ksize: 0,   // global key size count
      vsize: 0    // global value size count
    }
  */
```

# Events

## set

Fired when a key has been added or changed.
You will get the `key` and the `value` as callback argument.

```js
myCache.on( "set", function( key, value ){
	// ... do something ...  
});
```

## del

Fired when a key has been removed manually or due to expiry.
You will get the `key` as callback argument.

```js
myCache.on( "del", function( key ){
	// ... do something ...  
});
```

## expired

Fired when a key expires.
You will get the `key` and `value` as callback argument.

```js
myCache.on( "expired", function( key, value ){
	// ... do something ...  
});
```

## flush

Fired when the cache has been flushed.

```js
myCache.on( "flush", function(){
	// ... do something ...  
});
```

## Release History
|Version|Date|Description|
|:--:|:--:|:--|
|v1.0.3|2014-11-07|fix for setting numeric values. Thanks to [kaspars](https://github.com/kaspars) + optimized key ckeck.|
|v1.0.2|2014-09-17|Small change for better ttl handling|
|v1.0.1|2014-05-22|Readme typos. Thanks to [mjschranz](https://github.com/mjschranz)|
|v1.0.0|2014-04-09|Made `callback`s optional. So it's now possible to use a syncron syntax. The old syntax should also work well. Push : Bugfix for the value `0`|
|v0.4.1|2013-10-02|Added the value to `expired` event|
|v0.4.0|2013-10-02|Added nodecache events|
|v0.3.2|2012-05-31|Added Travis tests|

[![NPM](https://nodei.co/npm-dl/node-cache.png?months=6)](https://nodei.co/npm/node-cache/)

# License 

(The MIT License)

Copyright (c) 2010 TCS &lt;dev (at) tcs.de&gt;

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
