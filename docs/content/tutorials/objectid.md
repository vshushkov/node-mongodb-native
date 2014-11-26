---
aliases:
- /doc/installing/
date: 2013-07-01
menu:
  main:
    parent: tutorials
prev: ../../tutorials/logging
next: ../../tutorials/connecting
title: ObjectId
weight: 8
---
# ObjectId

The ObjectId class is the default primary key for a MongoDB document and is usually found in the `_id` field in an inserted document. Let's look at an example.

```json
{
    "_id": ObjectId("54759eb3c090d83494e2d804")
}
```

An ObjectId is a 12 byte binary BSON type that contain any 12 bytes you want. To be helpful in generating ObjectIds MongoDB drivers and the server will generate them using a default Algorithm. A ObjectId's 12 bytes will be then contain the following.

| Size          | Description                                                |
| ------------- |:-----------------------------------------------------------|
| 4 bytes       | a 4-byte value representing the seconds since the Unix epoch |
| 3 bytes       | a 3-byte machine identifier|
| 2 bytes       | 2-byte process id|
| 3 bytes       | 3-byte counter, starting with a random value|

In the driver you can tap into this by simply creating a new ObjectId. Let's look at an example that will create an ObjectId that contains a 12 byte description that matches this specification.

```javascript
var ObjectId = require('mongodb').ObjectID
var id = new ObjectId();
console.log(id)
```

This will print a hexadecimal representation of the 12 byte ObjectId the driver generated.

It's equally valid to just pass it a 12 byte string (buffer is not currently supported but will be sometime soon). Let's create our own definition of a 12 byte ObjectId.

```javascript
var ObjectId = require('mongodb').ObjectID
var id = new ObjectId("aaaaaaaaaaaa");
console.log(id)
```

This will print the hexadecimal value `616161616161616161616161` which corresponds to the string `aaaaaaaaaaaa`

## Properties of a Generated ObjectId

One of the main reasons ObjectId's are generated in the fashion mentioned above by the drivers is that is contains a useful behavior due to the way sorting works. Given that it contains a 4 byte timestamp (resolution of seconds) and an incrementing counter as well as some more unique identifiers such as the machine id once can use the `_id` field to sort documents in the order of creation just by simply sorting on the `_id` field. This can be useful to save the space needed by an additional timestamp if you wish to track the time of creation of a document.

## Driver ObjectId Constructor methods

The driver allows you to create ObjectId's in a couple of ways as well as allowing you to introspects aspects of the ObjectId.

Let's look at the actual constructor options. The constructor lets you pass in a 12 byte string or a 24 byte hexadecimal representation as well as no arguments.

```javascript
var ObjectId = require('mongodb').ObjectID
var id = new ObjectId();
```

Creates a new generated ObjectId using the algorithm outlined above.

```javascript
var ObjectId = require('mongodb').ObjectID
var id = new ObjectId("aaaaaaaaaaaa");
```

Creates a new specific ObjectId using the 12 byte string.

```javascript
var ObjectId = require('mongodb').ObjectID
var id = new ObjectId("616161616161616161616161");
```

Creates a new specific ObjectId using the 24 byte hexadecimal 12 byte string representation. This is useful for when you are passing back Id's from a web application.

## Driver ObjectId Static methods

The ObjectId static methods are meant to be helpful in making it more explicit what your intention is when creating a new ObjectId.

```javascript
var ObjectId = require('mongodb').ObjectID
var id = ObjectId.createPk();
```

Creates a new ObjectId instance with a generated key using the algorithm outlined above.

```javascript
var ObjectId = require('mongodb').ObjectID
var id = ObjectId.createFromTime(5000);
```

Creates an ObjectId from a seconds timestamp with the rest of the bytes in the ObjectId zeroed out.

```javascript
var ObjectId = require('mongodb').ObjectID
var id = ObjectId.createFromHexString("616161616161616161616161");
```

Creates and ObjectId from the 24 byte hexadecimal representation of a 12 byte string.

## Driver ObjectId Instance methods

The more interesting methods on an ObjectId instance are the following.

### getTimestamp()

```javascript
var ObjectId = require('mongodb').ObjectID
var id = new ObjectId();
console.log(id.getTimestamp())
```

This method returns a Date object representing the timestamp part of the 12 byte ObjectId as defined by the algorithm above. If the ObjectId is generated by the algorithm you will get the creation time Date object back. However if it's just a random 12 byte sequence obviously the date coming back might be nonsensical.

### toHexString()

```javascript
var ObjectId = require('mongodb').ObjectID
var id = new ObjectId("aaaaaaaaaaaa");
console.log(id.toHexString())
```

This will print out the hexadecimal representation of the ObjectId.

## On Buffers

Until the ObjectId natively accepts a buffer the best way to transform a Buffer into a valid ObjectId is to do the following.

```javascript
var ObjectId = require('mongodb').ObjectID
var id = new ObjectId(new Buffer("aaaaaaaaaaaa").toString());
console.log(id.toHexString())
```

By converting the buffer into it's string representation we can correctly create a new ObjectId.