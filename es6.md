# JavaScript Workshop

**Watch on your screen: https://tinyurl.com/dg-js2018**

## ES6 / ES2015

### Mapping data

Demo data used for all examples:
```
var simpsonsCharacters = [
  {
    firstName: 'Homer',
    lastName: 'Simpson',
    gender: 'male',
    age: 39,
  },
  {
    firstName: 'Selma',
    lastName: 'Bouvier',
    gender: 'female',
    age: 41,
  },
  {
    firstName: 'Barney',
    lastName: 'Gumble',
    gender: 'male',
    age: 40,
  },
];
```

Instead of this

```javascript
function getFullNames(characters) {
  var names = [];
  var i;

  for (i = 0; i < characters.length; i++) {
    names.push(characters[i].firstName + ' ' + characters[i].lastName);
  }

  return names;
}
```

Use `map()`:

```javascript
const getFullNames = (characters) => {
  return characters.map(character => `${character.firstName} ${character.lastName}`);
};
```

This can be even shortened to

```javascript
const getFullNames = characters =>
  characters.map(character => `${character.firstName} ${character.lastName}`);
```

Why should we do this?

 - Less code
 - Easier to read
 - `map()` returns a new array
 - `map()` can be chained with other functions


### Finding data
Instead of This

```javascript
function containsHomer(characters) {
  var homerFound = false;
  var i;

  for (i = 0; i < characters.length; i++) {
    if (characters[i].firstName === 'Homer') {
      homerFound = true;
    }
  }


  return homerFound;
}
```

Use `find()` or `findIndex()`:

```javascript
const containsHomer = (characters) => {
  return characters.find(character => character.firstName === 'Homer') !== undefined;
};
```

`find()` returns the element, `findIndex()` the index (or `-1`, if the element couldn't be found).

```javascript
const containsHomer = (characters) => {
  return characters.findIndex(character => character.firstName === 'Homer') !== -1;
};
```


### Cloning data with spread
```javascript
const simpsonsDuplicate = [...simpsonsCharacters];
const homerClone = {...simpsonsCharacters[0]};
```

### Cloning data and updating parts of it while doing so
```javascript
const homer2018 = {
  ...simpsonsCharacters[0],
  age: 40,
};

const fewNumbers = [0, 1, 2, 3];
const moreNumbers = [...fewNumbers, 4, 5, 6];
```

We'll need this for React later.

### Optional parameters

Instead of
```javascript
function sayHello(name, greeting) {
  var greetingStr = greeting || 'Hello';
  return greeting + ' ' + name + ', my friend!';
}
```

Use
```javascript
const sayHello = (name, greeting = 'Hello') => {
  return `${greeting} ${name}, my friend!`;
}
```

### Accessing deep nested keys

```javascript
require get from 'lodash';
const mockData = {
  foo: {
    bar: {
      baz: 42,
    },
  },
};

console.log(mockData.foo.bar.baz); // => 42
console.log(get(mockData, 'foo.bar.baz')); // => 42

console.log(mockData.foo.bar.zork); // => undefined
console.log(get(mockData, 'foo.bar.zork', 0)); // => 0


console.log(mockData.foo.zork.bar);
// => Uncaught TypeError: Cannot read property 'bar' of undefined

console.log(get(mockData, 'foo.zork.bar'));
// => undefined
```


### Promise.all()

Instead of this

```javascript
const userInfo = await getUserInfo();
const listings = await getListings();
const stats = await getStats();
const coffee = await makeCoffee();
```

```
getUserInfo() 🚀
_____getUserInfo() 🏁
_____getListings() 🚀
___________________getListings() 🏁
___________________getStats() 🚀
________________________________________________getStats() 🏁
________________________________________________makeCoffee() 🚀
____________________________________________________________makeCoffee() 🏁
```

Call them all _in parallel_.

```javascript
const [
  userInfo,
  listings,
  stats,
  coffee,
] = await Promise.all(
  getUserInfo(),
  getListings(),
  getStats(),
  makeCoffee(),
);
```

```
getUserInfo() 🚀
getListings() 🚀
getStats() 🚀
makeCoffee() 🚀
_____getUserInfo() 🏁
____________makeCoffee() 🏁
______________getListings() 🏁
_____________________________getStats() 🏁
```

What about dependencies?

```javascript
const userId = await getUserId();

const [
  userInfo,
  listings,
  stats,
  coffee,
] = await Promise.all(
  getUserInfo(userId),
  getListings(),
  getStats(userId),
  makeCoffee(),
);
```


If one call fails, all fail. Use `useClientWithFallback()` for optional calls.

```javascript
const userId = await getUserId();
const [
  userInfo,
  listings,
  stats,
  coffee,
] = await Promise.all(
  getUserInfo(),
  getListings(),
  useClientWithFallback(getStats, [userId], {}),
  makeCoffee(),
);
```
