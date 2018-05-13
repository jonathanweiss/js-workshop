# JavaScript Workshop

**Watch on your screen: https://tinyurl.com/dg-js2018**

## ES6 / ES2015

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

### Mapping data

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



Good to know:

 - `map()` returns a new array
 - therefore `map()` can be chained with other functions


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

### Use plain objects

It's ok create and pass around plain objects. Only use generator functions when you have some logic in them.

Instead of
```javascript
function generateUserObject(firstName, lastName, age, id, accountType) {
  return {
    firstName: firstName,
    lastName: lastName,
    age: age,
    id: id,
    accountType: accountType,
  };
}

var myUser = generateUserObject(data.firstName, data.lastName, data.age, userInfo.id, userInfo.accountType);
validateUser(myUser);
```

use a plain object

```javascript
validateUser({
  firstName: data.firstName,
  lastName: data.lastName,
  age: data.age,
  id: userInfo.id,
  accountType: userInfo.accountType,
});
```
### Be careful with `falsy` values

```javascript
const getInsuranceType = patientId => 'premium';

const generatePatientInfo = (firstName, id, dateOfBirth) => ({
  firstName,
  insuranceType: getInsuranceType(id),
  age: moment().year() - moment(dateOfBirth).year(),
});

const validatePatient = patient => (
  patient.firstName &&
  patient.insuranceType &&
  patient.age
);
const hans = generateUserObject('Hans', 1337, moment('2018-02-01'));

validatePatient(hans); // => 0
// "Hans" && "premium" && 0 => 0 => "falsy"
```

Better use `in`:

```javascript
const validatePatient = patient => (
  patient.firstName.trim() !== '' &&
  patient.insuranceType.trim() !== '' &&
  'age' in patient
);

validatePatient(hans); // => true
```

## Functional Programming

### Prefer pure functions 1

Avoid side-effects in functions
```javascript
class PatientList {
  constructor() {
    this.patients = [];
  }

  addPatient(firstName, id, dateOfBirth) {
    this.patients.push({
      firstName,
      insuranceType: getInsuranceType(id),
      age: moment().year() - moment(dateOfBirth).year(),
    });
  }
}
```

`createPatient()` is free of side-effects
```javascript
export const createPatient = ({ firstName, id, dateOfBirth }) => {
  return {
    firstName,
    insuranceType: getInsuranceType(id),
    age: moment().year() - moment(dateOfBirth).year(),
  }
};

class PatientList {
  constructor() {
    this.patients = [];
  }

  addPatient(patient) {
    this.patients.push(createPatient(patient));
  }
}
```
(Note that we've changed the parameters in `addPatient` to accept a single object.)

Pros

 - Easier to understand
 - Easier to test
 - Function is re-usable 
 - Bonus: we've removed the dependencies (`moment()` and `getInsuranceType()`) from `PatientList`.

### Prefer pure functions 2

Avoid relying on state (or other class members)

```javascript
class CharacterSelector extends React.Component {
  displayCharacters() {
    return this.props.characters.map(character => {
      return (
        <Character
          firstName={character.firstName}
          lastName={character.lastName}
          age={character.age}
          gender={character.gender}
        />
      );
    });
  }

  render() {
    return (
      <div>
        <Header />
        { this.displayCharacters() }
        <Footer />
      </div>
    );
  }
}
```

Instead, pass in the list and move the function out of the class

```javascript
export const displayCharacters = (characters) => {
  return characters.map(character => {
    return (
      <Character
        firstName={character.firstName}
        lastName={character.lastName}
        age={character.age}
        gender={character.gender}
      />
    );
  });
}

class CharacterSelector extends React.Component {
  render() {
    return (
      <div>
        <Header />
        { displayCharacters(this.props.characters) }
        <Footer />
      </div>
    );
  }
}
```

Pros
 - Easier to understand
 - Easier to test
 - Function is re-usable
