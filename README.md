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

- **.debug()** renders the DOM as a string 

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
      disableLifecycleMethods: true,
    });
    ```
    
## Jest Functions:
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

## Enzyme Functions:
- rendering components a creating a wrapper:
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


## Popular Tests: 

- testing if a component exists:
    ```javascript 
    import ComponentName from './ComponentFile Name'

    const wrapper = shallow(<ComponentName />
    const component = wrapper.find(`[data-test="${val}"]`)
    assert(component.length).toBe(1);
    ```
- checking text of a component 
  ```javascript
    import ComponentName from './ComponentFile Name'

    const wrapper = shallow(<ComponentName />
    const component = wrapper.find(`[data-test="${val}"]`)
    expect(component.text()).toBe('');
  ```

 - Testing components with props 
   ```javascript
   const defaultProps = { success: false}; /* initial props givin to component */

   const setup = (props={}) => {
      const setupProps = {...defaultProps, ...props}; /* can add new props to give to component (overwrites default props) */
      return shallow(<Congrats { ...setupProps } />)
    }
    test('renders no text when success prop is false', () => {
        const wrapper = setup({ success: false }); // test where props given are the same as default
        const component = findByTestAttr(wrapper, 'component-congrats');
        expect(component.text()).toBe('');
    });
    test('renders non-empty congrats message when congrats message is true', () => {
        const wrapper = setup({ success: true }); // test where props given are diff from default
        const message = findByTestAttr(wrapper, 'congrats-message');
        expect(message.text().length).not.toBe(0);
    });
    ```
- testing propTypes
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
  
  // test file
  import Componet from './Component.js'
  
  test('does not throw warning with expected props', () => {
    const expectedProps = { success: false };
    checkProps(Componet, expectedProps);
  });
  ```
## Redux Testing
