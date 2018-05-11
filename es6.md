# JavaScript Workshop

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

getFullNames(simpsonsCharacters);
```

Use `map()`:

```javascript
const getFullNames = (characters) => {
  return characters.map(character => `${character.firstName} ${character.lastName}`);
};

getFullNames(simpsonsCharacters);
```

This can be even shortened to

```javascript
const getFullNames = (characters) =>
  characters.map(character => `${character.firstName} ${character.lastName}`);
```

Why should we do this?

 - Less code
 - Easier to read
 - `map()` returns a new array
 - `map()` can be chained with other functions

 
