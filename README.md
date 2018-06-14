# Promises async/await presentation

## Promises

A Promise is a proxy for a value not necessarily known when the promise is created. It allows you to associate handlers with an asynchronous action's eventual success value or failure reason.

This lets asynchronous methods return values like synchronous methods: instead of immediately returning the final value, the asynchronous method returns a promise to supply the value at some point in the future.

## History

Promises were proposed as a spec back in 2012.
And became part of ECMAScript standard in es6 ( es2015 )
Promises have been used in form or another in some libraries like:
* Q
* when
* WinJS
* RSVP.js
* Bluebird

## The four states of a promise:

* pending
* fulfilled ( resolved )
* rejected
* settled

## Syntax

```javascript
const promise = new Promise( ( resolve, reject ) => {
    const condition = true; // you should add a better condition
    if ( condition ) {
        resolve( "Success" );    
    } else {
        reject( "error" );
    }
} );
```

## Geting the promise values
### .then
.then receives two functions as params one for resolved and one for reject
```javascript
const promise = new Promise( ( resolve, reject ) => {
    const condition = false; // you should add a better condition
    if ( condition ) {
        resolve( "Success" );    
    } else {
        reject( "Error" );
    }
} );

promise.then( 
    data => console.log( "Resolved: ", data ),
    data => console.log( "Rejected: ", data )
);

// this will write in the console: Rejected: Error
```

### .then and .catch 
In this case the .then will receive only the resolve fuction as param.
The error case is treated in the .catch

```javascript
const promise = new Promise( ( resolve, reject ) => {
    const condition = true; // you should add a better condition
    if ( condition ) {
        resolve( "Success" );    
    } else {
        reject( "error" );
    }
} );

promise.then( data => console.log( "Resolved: ", data ) )
       .catch( data => console.log( "Rejected", data ) );
// this will write in the console: Resolved: Success
```
### .then vs .then and .catch

**Case 1**

In this case there is no difference between the two implementation
```javascript
const promise = new Promise( 
   ( resolve, reject ) => setTimeout( ( ) => resolve(3), 2000 )
);

promise
    .then( () => console.log( "then" ) )
    .catch( () => console.log( "catch" ) )
promise
    .then( 
        () => console.log( "then" ), 
        () => console.log( "catch" )
    );
```
**Case 2**

The problem here is that if you use only the .then the error that is throw inside it is not catched.
The reject function sent to .then will not handle the error thrown in the resolve function.

```javascript
const promise = new Promise( 
    ( resolve, reject ) => setTimeout( ( ) => resolve(3), 2000 )
);

promise
    .then( 
        () =>  {
        console.log( "then " );
        throw "Some error";
        } 
    )
    .catch( ( err ) => console.log( "catch", err ) )

promise
    .then( 
        () =>  {
        console.log( "then " );
        throw "Some error";
        },
        () => console.log( "inside Catch" )
    );
```

***.then and .catch always return a Promise***


### Resources:

* https://github.com/tc39/proposal-top-level-await


Error Handling Async / Await:
* https://blog.grossman.io/how-to-write-async-await-without-try-catch-blocks-in-javascript/
* https://dzone.com/articles/easier-error-handling-using-asyncawait
