### Never manipulate the state directly

Be careful impure functions like `Array.push()`.

```javascript
class PatientList extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      patients: props.patients,
    };
  }

  addPatient({ firstName, id, dateOfBirth }) {
    this.state.patients.push({
      firstName,
      insuranceType: getInsuranceType(id),
      age: moment().year() - moment(dateOfBirth).year(),
    });
  }
}
```
This implementation of `addPatient()` won't work as expected.

```javascript
class PatientList extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      patients: props.patients,
    };
  }

  addPatient({ firstName, id, dateOfBirth }) {
    const patients = this.state.patients;
    patients.push({
      firstName,
      insuranceType: getInsuranceType(id),
      age: moment().year() - moment(dateOfBirth).year(),
    });

    this.setState({ patients });
  }
}
```

`patient` is a *reference* to `this.state.patient`, _not_ a copy! Using `push()` will directly modify the state.

```javascript
class PatientList extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      patients: props.patients,
    };
  }

  addPatient({ firstName, id, dateOfBirth }) {
    this.setState({
      patients: [
        ...this.state.patients,
        {
          firstName,
          insuranceType: getInsuranceType(id),
          age: moment().year() - moment(dateOfBirth).year(),
        }
      ]
    });
  }
}
```

The spread operator (`...`) creates a copy of `this.state.patients` and spreads the values in the array. The object will added to the end of the list.

This will also work for objects.

### Avoid spreading props on components

Instead of this
```javascript
<Character {...character} />
```

Be verbose!
```javascript
<Character
  firstName={character.firstName}
  lastName={character.lastName}
  age={character.age}
  gender={character.gender}
/>
```

Pros
 - Easier to understand how data flows
 - Better to see which props are being used and which are missing

This is especially true when the data flows several component levels deep.


### Keep props simple

Instead of using objects or arrays...
```javascript
<Character
  characterObject={{
    firstName: 'Homer',
    lastName: 'Simpson',
    age: 39,
    gender: 'male',
  }}
/>
```

try to use a single prop for a single value.
```javascript
<Character
  firstName="Homer"
  lastName="Simpson"
  age={39}
  gender="male"
/>
```

Pros
 - Easier to understand
 - Helps with component composition

### Use components instead of arrays

Instead of
```javascript
<CharacterList
  characters={
    [
      {
        firstName: 'Homer',
        lastName: 'Simpson',
        age: 39,
        gender: 'male',
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
    ]
  }
/>
```

re-use components

```javascript
<CharacterList>
  <Character
    firstName="Homer"
    lastName="Simpson"
    age={39}
    gender="male"
  />
</CharacterList>
```
