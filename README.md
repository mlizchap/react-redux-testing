# react-redux-testing

## Types of Tests
- **Unit**: Tests one piece (usually 1 function)
- **Integration**:  Tests how multiple units work together
- **End-to-End**: Tests how a user would interact with the app
- **Snapshot**: Freezes a component and future output is compared against it, fails if there are changes

- BDD - Behavior Driven Development - focuses on the behavior of the app

## Jest

## Enzyme
- creates a virtual DOM for testing
- allows access to props and state to test for values 
- **shallow**: renders component only 1 level deep; renders parent but uses placeholders for children 
- **mount**: 
- **.debug()** renders the DOM as a string 

## Setup
- install jest, enyzme, and the enzyme adapter 
  ```javascript
  $ npm install --save jest enzyme enzyme-adapter-react-16
  ```
 - setup the enzyme adapter in a file called `setupTest.js`
   ```javascript
   import Enzyme, { shallow } from 'enzyme';
  import EnzymeAdapter from 'enzyme-adapter-react-16';

  Enzyme.configure({ 
      adapter: new EnzymeAdapter(),
      disableLifecycleMethods: true,
  });
 ```

## Types of Tests
### Test to find a component (helper function)
```javascript 
```
