# React BucketJS!

[![npm version](https://badge.fury.io/js/react-bucketjs.svg)](https://badge.fury.io/js/react-bucketjs)

bucket.js is an ultra lightweight state-management for your react projects.

-   Easily find your bucket references across your codebase
-   Supports primitive, arrays and object types
-   Hooks to track changes by bucket or bucket selector
-   No dependencies, only react 18 as peerDependency

#### Install

```
npm install react-bucketjs
```

---

### Basic Example

Simple counter and re-rendering on change.

```js
import { bucket, useBucket } from "react-bucketjs";

//suggestion: export your bucket in a globals file to access anywhere
let btCount = bucket(0);

function Counter() {
    let count = useBucket(btCount);

    return (
        <div>
            <h1>{count}</h1>
            <button onClick={() => btCount.set(count + 1)}>Increment</button>
        </div>
    );
}
```

### Advanced Example

Multiple counters in a single bucket, showing how to increment in different ways.

```js
import { bucket, useBucketSelector } from "react-bucketjs";

let btCounters = bucket({ Larry: 0, Curly: 0, Moe: 0 });

function Counter() {
    let larry = useBucketSelector(btCounters, (bucket) => bucket.Larry);
    let curly = useBucketSelector(btCounters, (bucket) => bucket.Curly);
    let moe = useBucketSelector(btCounters, (bucket) => bucket.Moe);

    return (
        <div>
            <h1>Curly: {curly}</h1>
            <button
                onClick={() => {
                    let counters = btCounters.get();
                    counters.Curly++;
                    btCounters.set(counters);
                }}
            >
                Slap
            </button>

            <h1>Larry: {larry}</h1>
            <button
                onClick={() => {
                    btCounters.assign({ Larry: larry + 1 });
                }}
            >
                Bonk
            </button>

            <h1>Moe: {moe}</h1>
            <button
                onClick={() => {
                    let moeCount = btCounters.get((bucket) => bucket.Moe);
                    btCounters.assign({ Moe: moeCount + 1 });
                }}
            >
                Pinch
            </button>
        </div>
    );
}
```

### Comparator

You can control if `useBucket` and `useBucketSelector` should trigger a re-render using a comparator. This is helpful if you have an object in a bucket and only want specific keys in that bucket to be triggered instead of any key/value.

```js
import { useBucket, useBucketSelector } from "react-bucketjs";

// A JSON stringify comparator is available to use as well for complex objects/arrays
import { compareStringified } from "react-bucketjs";

// Example 1
// Render only when specific keys do not match
const comparatorA = (prev, next) => prev.key !== next.key;
let value = useBucket(myBucket, comparatorA);

// Example 2
// Render selector values always, even if they are equal
const comparatorB = (prev, next) => true;
let selectedValue = useBucketSelector(
    myBucket,
    (bucket) => bucket.key,
    comparatorB
);
```

### Subscribe to bucket

Track bucket changes outside of a react function component using any function.

```js
import { bucket } from "react-bucketjs";

let btCounter = bucket(0);

/**
 * Note: internally we always use an object to store the value in a "state".
 * This is required for useSyncExternalStore,
 * so for regular functions you'll need to access the bucket.state.
 */
const onIncrement = (bucket) => console.log("Count: ", bucket.state);

// Subscribe to the bucket
btCounter.subscribe(onIncrement);

// Trigger an increment
function increment() {
    let count = btCounter.get();
    btCounter.set(count + 1);
}

//...

increment();
//output: Count: 1

increment();
//output: Count: 2
```
