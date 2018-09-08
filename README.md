# react-redux-testing

## TOC
- [Overview](#overview)
- [Jest](#jest)
- [Enzyme](#enzyme)
- [Enzyme Setup](#enzyme-setup)
- [Basic Tests](#basic-tests)
  - [Renders Component](#renders-component)
  - [State](#state)
  - [Props](#props)
  - [Style](#style)
  - [Check Proptypes](#check-proptypes)
- [Routing Tests](#react-router-tests)
  - [Links](#links)
  - [URL Paths](#url-paths)
- [Redux Tests]

## Overview
- BDD - Behavior Driven Development: focuses on the behavior of the app
- TDD - Test Deiven Development: a method of writing tests before writing the app

### Types of Tests
- Unit Testing: Tests one piece (usually 1 function)
- Integration Testing:  Tests how multiple units work together
- End-to-End Testing: Tests how a user would interact with the app
- Snapshot Testing: Freezes a component and future output is compared against it, fails if there are changes

### Tools:
- Jest: tests Javascript
- Enzyme: creates a virtual DOM for testing and allows access to props and state to test for values 


## Jest
### Jest Functions
- **test()**: a singular test in Jest
 ```javacript
 test("testdescription") {}
 ```

- **describe()**: can group multiplue tests together
  ```javascript
  describe("description of group of test") {
    test("test #1 description") { }    
    test("test #2 description") { }
  }    
  ```

- **beforeEach()** and **afterEach()**
  - functions that run before and after tests are written, written inside the describe block
    ```javascript
    describe('description of group of test', () => {
      beforeEach(() => { }); // do something before each test
      afterEach(() => {  }); // do something after each test
      // tests are written here
    }
    ```
  
- to chaeck if values are equal: 
  ```javacsript
  expect(value1).toBe(value2);
  ```
- to test if two objects have the same value
  ```javascript
  expect(obj1).toequal(obj2)l
  ```

- reates a mock function
  ```javascript
  const mockFn = jest.fn();
  ```

## Enzyme
### Enzyme Functions:
- **rendering components**:
  - **Shallow Render** (1 level deep)
    ```javascript
    import Enzyme, { shallow } from 'enzyme';
    const wrapper = shallow(<ComponentName />
    ```
  - **Full DOM Render**
    ```javascript
    import { mount } from 'enzyme';
    const wrapper = mount(<ComponentName />);
    ```
  
- **debugging**
  ```javascript
  wrapper.debug()
  ```

- **Finding a component**
  ```javascript
  wrapper.find('[data-test="ATTRIB_NAME"]');
  wrapper.find('ELEM_NAME');
  wrapper.find('.CLASS_NAME');
  wrapper.find('#ID_NAME');
  ```
- **Creating a component instance and grabbing props**
  ```javsacript
  wrapper.instance().props
  ```

- **Simulate a click**
  ```javascript
  wrapper.simulate("click")
  ```
 
- **Simulate input change** 
  ```javascript
  input.simulate('change', { target: { value: userInput }});
  ```

- **Setting State**
  ```javascript
  wraper.setState({value: 'x'});
  ```

## Enzyme Setup
- install enyzme and the enzyme adapter 
  ```javascript
  $ npm install --save enzyme enzyme-adapter-react-16
  ```
- setup the enzyme adapter in a file called `setupTests.js`, Jest will run this file before the other test files
   ```javascript
   
    import Enzyme from 'enzyme';
    import EnzymeAdapter from 'enzyme-adapter-react-16';

    Enzyme.configure({ 
      adapter: new EnzymeAdapter(),
    });
    ```
## Basic Tests
- import react, the shallow/mount functions from enzyme, and the component you are testing
  ```javascript
  import React from 'react';
  import { mount } from 'enzyme';
  import ComponentName from './ComponentFile Name'
  ```
- use the mount or shallow function to create a wrapper component to perform tests on 
  ```javascript
  const wrapper = shallow(<ComponentName />);
  ```

#### Renders Component 
- 'in `componentName.test.js`
  ```javascript 
    const component = wrapper.find(`.COMPONENT_NAME`)
    expect(component.length).toBe(1);
  ```
  
#### State
- give no initial state and make sure component is not rendered 
  ```javascript
  wrapper.setState({ todos: [] });
  const todos = wrapper.find('.list__item');
  expect(todos.length).toBe(0);
  ```
- give some initiual state and make sure
  ```javascript
  wrapper.setState({ todos: [{name: 'thing 1', completed: false}, {name: 'thing 2', completed: false}]});
  const todos = wrapper.find('.list__item');
  expect(todos.length).toBe(2);
  ```
#### Props
- give initilal props to a component:
   ```javascript
   const defaultProps = { success: false};
  ```
- create a setup for creating a component copy, give the component initial props, with the optional arg to add new props
  ```javascript
   const setup = (props={}) => {
      const setupProps = {...defaultProps, ...props}; /* can add new props to give to component (overwrites default props) */
      return shallow(<Congrats { ...setupProps } />)
    }
   ```
- test the components with the props the same as default:
  ```javascript
  test('renders no text when success prop is false', () => {
      const wrapper = setup({ success: false }); 
      const component = findByTestAttr(wrapper, 'component-congrats');
      expect(component.text()).toBe('');
  });
  ```
- test the component with props given that are different from the defaultProps
  ```javascript
  test('renders non-empty congrats message when congrats message is true', () => {
      const wrapper = setup({ success: true }); // test where props given are diff from default
      const message = findByTestAttr(wrapper, 'congrats-message');
      expect(message.text().length).not.toBe(0);
  });
  ```
  
#### Style
 ```javascript
 expect(wrapper.find('.list__item').get(0).props.style.textDecoration).toBe('none');
 ```
    
#### Input Submit 
```javascript
    const userInput = 'abc';
    const input = wrapper.find('.form__input');
    input.simulate('change', { target: { value: userInput }});
    wrapper.find('.form').simulate('submit');
    const listItem = wrapper.find('.list__item');
    
    expect(wrapper.state().todos[0].name).toBe(userInput);
    expect(listItem.text()).toBe(userInput)
```
#### Checks Proptypes
- maked sure there is no `propError` when rendering with propTypes
  ```javascript
      const propError = checkPropTypes(
          Component.propTypes,
          conformingProps,
          'prop',
          Component.name
      );
      expect(propError).toBeUndefined();
  }
  ```
### React Router Tests:
- import the `MemoryRouter` component from react-router-dom 
  ```javascript
  import { MemoryRouter } from 'react-router';
  ```
#### Links
- wrapp the app component in a `MemoryRouter` tag 
  ```javascript
  const wrapper = mount(
      <MemoryRouter>
          <App/>
      </MemoryRouter>
  );
  ```
 - find the link item you will click and simulate a click event, make the default not a button
  ```javascript
  const componentLink = wrapper.find('li');
  console.log("LINK", componentLink.debug())
  componentLink.simulate('click', { button: 0 });
  ```
- find the component you expect to be rendered to the screen when the link is clicked and test it exists
  ```javascript
  const componentClicked = wrapper.find(component);
  expect(componentClicked.length).toBe(1);
  ```

#### URL Paths 
- wrap the app component in a Memory Router and give an initial route entry
  ```javascript
      const wrapper = mount(
          <MemoryRouter>
              <App/>
          </MemoryRouter>
      );
  ```
- find the component and make sure it exists
  ```javascript
  xpect(wrapper.find(component).length).toBe(1);
  ```

### Redux Tests:
#### The Store
- create a **store factory**: a function that returns a store that will provide a store for testing purposes
  ```javascript
  // testUtils.js
  import { createStore } from 'redux';
  import reducer from './reducers';

  export const storeFactory = (initialState) =>{
      return createStore(reducer, initialState);
  }
  ```
- if using middleware, such as Redux Thunk, create the store with middleware 
  ```javascript
  // testUtils.js
  import { createStore, applyMiddleware } from 'redux';
  import rootReducer from '../reducers';
  import { middlewares } from '../configStore';

  export const storeFactory = (initialState) => {
      const createStoreWithMiddleWare = applyMiddleware(...middlewares)(createStore);
      return createStoreWithMiddleWare(rootReducer, initialState);
  }
  ```
- using the store in the component
  ```javascript
  import { storeFactory } from '../testUtils';
  import App from './App';

  /* ... */
  const store = storeFactory();
  const wrapper = mount(<App store={store} />
  ```
- dispatching an action
  ```javascript
  store.dispatch({type: 'CREATE_USER', payload: newUser});
  ```
- getting the state 
  ```javascript
  const state = store.getState();
  ```
- getting the component state of a connected component
  ```javascript
  const wrapper = shallow(<AddForm store={store} />).dive();
  wrapper.state();
  ```
  
#### Actions 
##### Dispatch Action and Make Sure State Updates
```javascript
// actions/index.js
it('when createTodo is dispatched, state is updated', () => {
  const userInput = 'x'
  const store = storeFactory();
  store.dispatch(createTodo(userInput));
  const expectedState = [{
      name: userInput,
      completed: false
  }]
  expect(store.getState().todos).toEqual(expectedState);
})  
```
##### Action Creator Updates State When Dispatched 
- **moxios**: instead of looking at the HTTP response from Axios, looks at a hard coated response we get from testing 
- install moxios and import into action creator file:
  ```javascript
  // in action/index.test.js
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
##### When Component is Mount - Action is dispatched
- in `setupTest.js` disable lifeCycleMethods to prevent componentDidMount from running in the actual component
    ```javascript
    Enzyme.configure({ 
        adapter: new EnzymeAdapter(),
        disableLifecycleMethods: true,
    });
    ```
 - in `input.test.js` create a mock function and create props with the mock funciton
   ```javascript
   // 
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
  
#### When User Submits - Action is Dispatched 
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
#### Component Has Access to Redux ActionCreator Props 
- in `component.test.js`
  ```javascript
  const setup = (initialState={}) => {
      const store = storeFactory(initialState);
      const wrapper = shallow(<Input store={store}/>).dive();
      return wrapper;
  }
  test('guessword action creator is a function prop', () => {
      const wrapper = setup();
      const successProp = wrapper.instance().props.guessWord;
      expect(successProp).toBeInstanceOf(Function);
  })
  ```


#### Reducers 
#### Reducer Returns Correct State Depending on if Action is Passed
- in `reducers/reducerName.test.js`
  ```javascript
  // reducers/reducerName.js
  import { actionTypes } from '../actions';
  import successReducer from './successReducer';

  test('returns default initial state of false when no action is passed', () => {
      const newState = successReducer([initial state], {});
      expect(newState).toBe(false);
  });
  test('returns state of true upon receiving an action of type CORRECT_GUESS', () => {
      const newState = successReducer([initial state], {type: actionTypes.CORRECT_GUESS})
      expect(newState).toBe(true);
  });
  ```

#### Component Has Access to Redux State Props 
- in `component.test.js`
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



  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  





























