# Promises and Async / Await presentation

# Promises

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

## Chaining

As I said a few rows above .then and .catch return a promise. This way you can chain as many promises as you want.
You can pass the information from one promise to the other, until you have the desired result.

```javascript
new Promise( ( resolve, reject ) => {
   setTimeout(() => resolve(1), 2000);
})
.then( ( data ) => {
  console.log("First Then ", data );
  return data + 1;
} )
.then(  ( data ) => {
  console.log("Second Then ", data );
  return data + 1;
} );
```

### Example that contains also .catch
```javascript
const prom = new Promise( ( resolve, reject ) => { 
    setTimeout( ( ) => resolve(3), 3000 );
} )

prom
    .then( ( data ) => console.log( "First Then ", data) )
    .then( ( data ) => {
        console.log( "Second Then ", data );
        return Promise.reject( "reject" );
    } )
    .then( ( success ) => console.log ("Third Then ", success ) )
    .catch( ( error ) => console.log( "Catch ", error ) )
//Result
//First Then 3
//Second Then undefined
//Catch reject
```
You might as yourself why we get Second Then with `undefined`, this is because in the Frist Then promise we don`t return any data.

## Promise All
There are times when we want to execute multiple promises and the results are not tied.
For this case you can use promise `.all`.
```javascript
const promiseArray = [
  new Promise((resolve, reject) => setTimeout(() => resolve(1), 3000)), // 1
  new Promise((resolve, reject) => setTimeout(() => resolve(2), 2000)), // 2
  new Promise((resolve, reject) => setTimeout(() => resolve(3), 1000))  // 3
];
Promise.all(promiseArray).then( data => console.log( data ) ); // [ 1, 2, 3 ] 
```
In case one of the promises fails( gets rejected ) the rest of the promises will be ignored and promise `.all` gets rejected aswell. Have a look at the example bellow:

```javascript
const promiseArray = [
  new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)), // 1
  new Promise((resolve, reject) => setTimeout(() => reject(2), 3000)), // 2
  new Promise((resolve, reject) => setTimeout(() => reject(3), 1000))  // 3
];

Promise.all(promiseArray).then( 
( data ) => console.log( data ),
( err ) => console.log( err ) );
//the result in showen in the console is: 3
```
## Promise Race
There are times when you a have a list of promises to execute and you only care the result returned by the first fulfilled ( resolved ) promise. But keep in mind that if the first promise that gets settled is rejected, the `.race` will also be rejected and the value returned by the rejected promised is passed on.
```javascript
Promise.race([
  new Promise((resolve, reject) => setTimeout(() => resolve(1), 3000)), // 1
  new Promise((resolve, reject) => setTimeout(() => resolve(2), 2000)), // 2
  new Promise((resolve, reject) => setTimeout(() => resolve(3), 1000))  // 3
])
.then( ( data ) => console.log( data ) ); //logs out 3
```
## Finally
There will be times when you want to do some clean up after a promise or a promise chain is finshed ( fulfilled or rejected ).
For example you want to show a spinner on the website until the api calls are finished( using promises ). You can set a flag `showSpinner = true` and after the api calls are done in the finally method you set the flag to `false` and hide the spinner.

```javascript
new Promise( ( resolve, reject ) => {
   setTimeout(() => resolve(1), 2000);
})
     .then( ( data ) => {
      console.log("First Then ", data ); // First Then 1
      return data + 1;
    } )
    .then(  ( data ) => {
      console.log("Second Then ", data ); // Second Then 2
      return data + 1;
    } )
    .finally( ( ) => console.log( "I`m finally done" ) )
```


# Async / Await
Async functions  allow you to write promise-based code as if it were synchronous, but without blocking the main thread. They make your asynchronous code less "clever" and more readable.

## History
Async / Wait was proposed as a spec back in 2014.
Async / Await was introduced in ES2017 ( es8 ).
It became fully supported in node 8.

## Syntax
```javascript
const resolveAfter2Seconds = () => new Promise( resolve => {
    setTimeout(() => {
      console.log("resolving");
      resolve("resolved");
    }, 2000);
});

async function asyncCall() {
  console.log("calling");
  const result = await resolveAfter2Seconds();
  console.log(result); // "resolved"
}

asyncCall();
```
## Multiple Awaits
```javascript
const resolveAfter2Seconds = () => new Promise( resolve => {
    setTimeout(() => {
      console.log("resolving");
      resolve("resolved");
    }, 2000);
});

async function asyncCall() {
  console.log("calling");
  const result = await resolveAfter2Seconds();
  const secondResult = await resolveAfter2Seconds();
  console.log(result); // "resolved"
  console.log(secondResult); // "resolved"
}

asyncCall();
```

## Error Handling
To handle the errors when using async await you have to use `try` and `catch`. If there are any error in the `try` block they will be catched in the `catch` block and any information from the try block is lost.

```javascript
const rejectAfter2Seconds = () => new Promise( ( resolve, reject ) => {
    setTimeout(() => {
      reject("rejected");
    }, 2000);
 });

async function asyncCall() {
  console.log("calling");
  try {
      const result = await rejectAfter2Seconds();
      console.log( "result ", result );
  } catch ( e ) {
      console.log( "There was an error ", e ); //
  }
}

asyncCall();
```
### Resources:

* https://github.com/tc39/proposal-top-level-await


Error Handling Async / Await:
* https://blog.grossman.io/how-to-write-async-await-without-try-catch-blocks-in-javascript/
* https://dzone.com/articles/easier-error-handling-using-asyncawait
