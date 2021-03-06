
Hey all,  

I know today's lecture was a challenging one. Proper handling of async conditions is difficult and the source of many errors at runtime.

This is what we covered today:

- simple scenario for error handling `try/catch`
- test with asynchronous functions
- how to handle error conditions with async (callbacks)
- better way: promises
- create your own Promise
- build error traps around promises
- async/await
- combine with simple error handling scenario


# Errors and Promises

The name of this lecture sounds like a whiny album by a shoegazer band from
the 90s, but we're actually still talking about Javascript.

## "Traditional" error handling

* `try` / `catch` and control flow with errors
* Works fine with synchronous code, breaks horribly with async. [MDN has extensive documentation on it.](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Statements/try...catch)

Look at [`simple.js`](https://github.com/jugonzal/lectures/tree/master/w4d4-promises-try-catch/code/simple.js) as an example of using `try`/`catch`.

Clearly we need something better, because dealing with errors in async code
usually means you need to deal with them inside callbacks. We've seen plenty of
examples in previous lectures:

```
db.query(query, [artistName, albumName], (err, result) => {
  if (err) {
    console.log("Something went wrong:", err);
    return;
  }
  // Do something with `result` here
}
```

You can also explore [`request.js`](https://github.com/jugonzal/lectures/tree/master/w4d4-promises-try-catch/code/request.js) to see what happens when certain error conditions happen. Not all of them will be handled nicely by the `try`/`catch` block.

## Promises to the rescue

Promises provide a way to make async code less painful. Many async-heavy
libraries support them natively, but it's important to know how to use them from
scratch using the `Promise` constructor. Again, [MDN has extensive
docs](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise),
but [this article by Dave Atchley has better
examples](http://www.datchley.name/es6-promises/).

The main idea goes like this:

* Functions **don't** return values, but an instance of `Promise` (also known as a _future_)
  * Think of it as an IOU for the function's return value
* When things go well: `.then((something) => {something()})`
  * `.then` is called with the **sucessful** return value of the previous step
* You can add more `.then`s here, as many as you want
* When errors happen: `.catch((oops) => console.log("Things went boom, yo"))`
  * The chain will be halted and `.catch` will be called immediately with the error as first argument

See [`promises.js`](https://github.com/jugonzal/lectures/tree/master/w4d4-promises-try-catch/code/promises.js) for an introductory example and [`p-request.js`](https://github.com/jugonzal/lectures/tree/master/w4d4-promises-try-catch/code/p-request.js) for a practical use of promises being used with the `request` module.

## async/await

The ultimate pattern for dealing with async issues AND having a simple error handling flow looks like this:

```
(async () => {
  try {
    let body = await promisifiedGet(url)
    console.log("1st inside doGet ",body)
    body = await promisifiedGet(process.argv[3])
    console.log("2nd inside doGet ",body)
    return body
  } catch (e) {
    console.log('bad bad bad ',e)
  }
})()
```

The `await` keyword will stop the code until the given promise is resolved.  If something goes wrong with that promises, the rejection is thrown as an error, which means you can rely on a simple `try/catch` block to do your error handling.

Notice that you can **only** use the `await` keyword inside an `async` function. This means you should not use it in the main context, but look at my code for a way to work around that.

There, the best of both worlds. You'll find this implemented in [`await.js`](https://github.com/jugonzal/lectures/tree/master/w4d4-promises-try-catch/code/await.js)




