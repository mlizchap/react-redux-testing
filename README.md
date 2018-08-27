# react-redux-testing


## TOC
- [Overview](#overview)
- Jest
- Enzyme
- Setup
- Popular Tests:
  - Renders Component
  - Renders Component Props
  - Checks Proptypes
  - Renders Component Conditionally Based on State
  - Renders Text Conditionally Based on State
  - Redux Tests: 
    - Updates State When Action is Dispatched
    - Action is Dispatched When User Submits
    - Action is Dispatched When Component is Mount
    - Component Has Access to Redux Props - State
    - Component Has Access to Redux Props - ActionCreator
    - Reducer Returns Correct State Depending on if Action is Passed
    - Action Creator Updates State When Dispatched 

## Overview
- **Unit**: Tests one piece (usually 1 function)
- **Integration**:  Tests how multiple units work together
- **End-to-End**: Tests how a user would interact with the app
- **Snapshot**: Freezes a component and future output is compared against it, fails if there are changes

- **BDD** - Behavior Driven Development - focuses on the behavior of the app

- **Jest**:
- **Enzyme**: creates a virtual DOM for testing and allows access to props and state to test for values 


## Jest
### Jest Functions
- **test()**

- **describe()**: can group multiplue tests together
  ```javascript
  describe("description of group of test") {
    test("test #1 description") {
      /*...*/
    }
    test("test #2 description") {
      /*...*/
    }
  }
  ```

- beforeEach()

- afterEach()

- jest.fn(): creates a mock functions
## Enzyme
### Enzyme Functions:
- **rendering components** by creating a wrapper:
  - **shallow**: renders component only 1 level deep; renders parent but uses placeholders for children 
    ```
    import Enzyme, { shallow } from 'enzyme';

    const wrapper = shallow(<ComponentName />
    ```
  - **mount**: 
  
- **.debug()**: logs the html of the component

- **.find()** : finds a component.  Can be by className, id, element or attribute.
  ```javascript
  wrapper.find('[data-test="ATTRIB_NAME"]');
  wrapper.find('ELEM_NAME');
  wrapper.find('.CLASS_NAME');
  wrapper.find('#ID_NAME');
  ```
- **.toBe()**:

- **.toEqaul()**

- **.isnstance()**: renders an instance of a component where you can pull of props 
  ```javsacript
  wrapper.instance().props
  ```


## Setup
- install jest, enyzme, and the enzyme adapter 
  ```javascript
  $ npm install --save jest enzyme enzyme-adapter-react-16
  ```
- setup the enzyme adapter in a file called `setupTest.js`, Jest will run this file before the other test files
   ```javascript
    import Enzyme, { shallow } from 'enzyme';
    import EnzymeAdapter from 'enzyme-adapter-react-16';

    Enzyme.configure({ 
      adapter: new EnzymeAdapter(),
    });
    ```
## Popular Tests

### Renders Component 
```javascript 
import ComponentName from './ComponentFile Name'

const wrapper = shallow(<ComponentName />
const component = wrapper.find(`[data-test="VALUE"]`)
assert(component.length).toBe(1);
```

### Renders Component Props
- give initilal props to a component:
   ```javascript
   const defaultProps = { success: false}; /* initial props givin to component */
  ```
- create a setup for creating a component copy, give the component initial props, with the optional arg to add new props
```javascript
 const setup = (props={}) => {
    const setupProps = {...defaultProps, ...props}; /* can add new props to give to component (overwrites default props) */
    return shallow(<Congrats { ...setupProps } />)
  }
 ```
- test the component with props given that are different from the defaultProps
  ```javascript
  test('renders non-empty congrats message when congrats message is true', () => {
      const wrapper = setup({ success: true }); // test where props given are diff from default
      const message = findByTestAttr(wrapper, 'congrats-message');
      expect(message.text().length).not.toBe(0);
  });
  ```
- test the components with the props the same as default:
  ```javascript
  test('renders no text when success prop is false', () => {
      const wrapper = setup({ success: false }); 
      const component = findByTestAttr(wrapper, 'component-congrats');
      expect(component.text()).toBe('');
  });
  ```

### Checks Proptypes
- maked sure there is no `propError` when rendering with propTypes
- if checking propTypes on multiple components, may want to put in a `testUtils.js` file
  ```javascript
  // testUtils.js
  export const checkProps = (component, conformingProps) => {
      const propError = checkPropTypes(
          component.propTypes,
          conformingProps,
          'prop',
          component.name
      );
      expect(propError).toBeUndefined();
  }
  ```
- testing the component:
  ```javascript
  import Componet from './Component.js'

  test('does not throw warning with expected props', () => {
    const expectedProps = { success: false };
    checkProps(Componet, expectedProps);
  });
  ```

### Renders Component Conditionally Based on State

### Renders Text Conditionally Based on State
```javascript
test('renders no text when success prop is false', () => {
    const wrapper = setup({ success: false });
    const component = findByTestAttr(wrapper, 'component-congrats');
    expect(component.text()).toBe('');
});
test('renders non-empty congrats message when congrats message is true', () => {
    const wrapper = setup({ success: true });
    const message = findByTestAttr(wrapper, 'congrats-message');
    expect(message.text().length).not.toBe(0);
});
```
### Redux Tests:

#### Updates State When Action is Dispatched (Redux Thunk)
- set up storeFactory with middleware
  ```javascript
  import { createStore, applyMiddleware } from 'redux';
  import rootReducer from './reducers';
  import ReduxThunk from 'redux-thunk';

  export const middlewares = [ReduxThunk];
  const createStoreWithMiddleware = applyMiddleware(...middlewares)(createStore)

  export default createStoreWithMiddleware(rootReducer);
  ```
- in the test file, import the store and before each test create a store with the initial state;
  ```javascript
  describe('no guessed words', () => {
      let store;
      const initialState = { secretWord };
      beforeEach(() => {
          store = storeFactory(initialState);
      });
      test('update state correctly for unsuccessful guess', () => { 
        /* ... */
      }
  }
  ```
- dispatch the action creator with `store.dispatch()` and get the state with `store.getState()`
  ```javascript
  store.dispatch(guessWord(unsuccessfulGuess));
  const newState = store.getState();
  ```
- assert that the new state is what we expected
```javascript
const expectedState = { x }
expect(newState).toEqual(expectedState);
```


#### Action is Dispatched When User Submits
- in component where user submits: `componentName.test.js`
- export the unconccected component (still export the connected component by default)
    ```javascript
    export class UnconnectedInput extends Component { }
    ```
- import and test the unconnected component
  ```javascript
  import { UnconnectedInput } './Input';
  
  wrapper = shallow(<UnconnectedInput {...props} />);
  ```
- before the test runs:
  - set up a mock function for the action creator
    ```javascript
    describe('guessword action creator call', () => {
      let guessWordMock;
      let wrapper;
      const guessedWord = 'train';
      beforeEach(() => {
          // set up mock for guess word 
          guessWordMock = jest.fn();
          const props = {
              guessWord: guessWordMock
          };
    ```
  - set up the unconnected component with the mock fn just created 
    ```javascript
     wrapper = shallow(<UnconnectedInput {...props} />);
    ```
  - add value to the input 
    ```javascript
    wrapper.instance().inputBox.current = { value: guessedWord };
    ```
  - simulate a click
    ```javascript
    const submitButton = findByTestAttr(wrapper, 'submit-button');
    submitButton.simulate('click', { preventDefault() {} });
    ```
- test that the action is called
  ```javascript
  test('calls guessword when button is clicked', () => {
      // check to see if mock ran once 
      const guessWordCallCount = guessWordMock.mock.calls.length;
      expect(guessWordCallCount).toBe(1);
  })
  ```
- testing that the action is called on the right arg
  ```javascript
  test('calls guessWord with input value as an arguement', () => {
      const guessedWordArg = guessWordMock.mock.calls[0][0];
      expect(guessedWordArg).toBe(guessedWord);
  })
```
- testing that the input box clears on submit
  ```javascript
  test('input box clears on submit', () => {
      expect(wrapper.instance().inputBox.current.value).toBe('');
  })
  ```
### Action is Dispatched When Component is Mount
- in `setupTest.js` disable lifeCycleMethods to prevent componentDidMount from running in the actual component
    ```javascript
    Enzyme.configure({ 
        adapter: new EnzymeAdapter(),
        disableLifecycleMethods: true,
    });
    ```
 - create a mock function and create props with the mock funciton
   ```javascript
    const getSecretWordMock = jest.fn();

    const props = {
      getSecretWord: getSecretWordMock,
      success: false,
      guessedWords: []
    }
    ```
- set uo component with the mock function as the prop
  ```javascript
  const wrapper = shallow(<UnconnectedApp {...props} />)
  ```
- run the lifecycle method
  ```javascript
  wrapper.instance().componentDidMount();
  ```
- assert if the mock function ran
  ```javascript
  const getSecretWordCallCount = getSecretWordMock.mock.calls.length;

  expect(getSecretWordCallCount).toBe(1);
  ```

#### Component Has Access to Redux Props - State
- since a connected component is a higher order component, we need to render the child component to test it using`dive()`
  ```javascript
  const setup = (initialState={}) => {
      const store = storeFactory(initialState);
      const wrapper = shallow(<Input store={store}/>).dive();
      return wrapper;
  }
  ```
- use `instance().props` to grab redux props off of components
  ```javascript
  test('has success piece of state as prop', () => {
      const success = true;
      const wrapper = setup({ success });
      const successProp = wrapper.instance().props.success;
      expect(successProp).toBe(success)
  });
  ```
#### Component Has Access to Redux Props - ActionCreator
```
const setup = (initialState={}) => {
    const store = storeFactory(initialState);
    const wrapper = shallow(<Input store={store}/>).dive();
    return wrapper;
}

setup();

test('guessword action creator is a function prop', () => {
    const wrapper = setup();
    const successProp = wrapper.instance().props.guessWord;
    expect(successProp).toBeInstanceOf(Function);
})
```

#### Reducer Returns Correct State Depending on if Action is Passed
```javascript
  ```javascript
  import { actionTypes } from '../actions';
  import successReducer from './successReducer';

  test('returns default initial state of false when no action is passed', () => {
      const newState = successReducer(undefined, {});
      expect(newState).toBe(false);
  });
  test('returns state of true upon receiving an action of type CORRECT_GUESS', () => {
      const newState = successReducer(undefined, {type: actionTypes.CORRECT_GUESS})
      expect(newState).toBe(true);
  });
  ```
#### Action Creator Updates State When Dispatched 
- tests the action - `action/index.test.js`
- **moxios**: instead of looking at the HTTP response from Axios, looks at a hard coated response we get from testing 
- install moxios and import into action creator file:
  ```javascript
  import moxios from 'moxios';
  ```
- also import the storeFactory acnd action creator
  ```javascript
  import { storeFactory } from '../test/testUtils';
  import { getSecretWord } from './';
  ```
- install moxios before the test and uninstall after
  ```javascript
    beforeEach(() => {
        moxios.install();
    });
    afterEach(() => {
        moxios.uninstall();
    });
  ```
- use `moxios.wait()` to wait for the response and give a response that has the same shape as the expected response:
  ```javascript
    moxios.wait(() => {
        const request = moxios.requests.mostRecent();
        request.respondWith({
            status: 200,
            response: secretWord 
        });
    });
  ```
- dispatch the action creator and see if store is appropriately updated:
  ```javscript
  return store.dispatch(getSecretWord())
      .then(() => {
          const newState = store.getState();
          expect(newState.secretWord).toBe(secretWord);
      });
  ```


















### checking text of a component 
  ```javascript
    import ComponentName from './ComponentFile Name'

    const wrapper = shallow(<ComponentName />
    const component = wrapper.find(`[data-test="${val}"]`)
    expect(component.text()).toBe('');
  ```





## Redux Testing

### Creating a Store Factory
```javascript
  import { createStore } from 'redux';

  export const storeFactory = (initialState) => {
    return createSotre (rootReducer, initialState);
  }
```










