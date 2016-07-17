#React Patterns

A list of best practices that I have accumulated while using React.

###States
- store states in the root component, pass it in as props to the children component
- do not store states in React getInitialState, it's an anti-pattern
- components should build based on compositions from smaller components, rather than inheritance/extending
- children components should be stateless (pure functions)
- shared states should always be placed on the parent that hosts both component - better to store all in the root component
- states are equivalent to the *Model* in *MVC*
- since all the states will be stored in the root component, group them according to the *Model*
- a *Model* can hold the following props:

```javascript
// Example model
const expenseModel = {
  collection: [], // you will be working with collection all the time
  selected: 0, // index or ID of the selected item in the collection
  lastRecord: false, // if you are doing pagination/load more
  links: {
    next: 'http://api/v1/expenses?offset=2', // url for the prev/next/current collection
    prev: 'http://api/v1/expenses?offset=0'
  }
}
```

- states can be further abstracted using _higher-order-components_
- it is recommended to work with normalized states
- avoid more than two levels of object nesting in the state
- when updating an object in the state, make sure to pass back the whole object
- work with immutable states
- you can copy an object easily by using the following trick
```javascript
const copy = JSON.parse(JSON.stringify(objName))
```
- if you are using Babel or EcmaScript6, you can consider the Object.assign method. Note that this will only create a shallow copy.
```javascript
// create a shallow copy of the state
const copy = Object.assign({}, this.state);
```


###Lifecycle
- there are several lifecyles in React
- componentWillMount, componentDidMount, componentWillReceiveProps, componentDidUpdate, etc
- show loading when mounting a component, and remove the loading state after it is mounted or Ajax call is successful
- Ajax calls are triggered in the componentWillMount lifecycle
- dispatcher will be registered/subscribed in the componentWillMount, and unregistered/unsubscribe in the componentWillUnmount
- when using refs in React, you can only access it once it is mounted, call componentDidMount

###Component structure
- split the component into small parts
- use atomic design pattern, e.g. split the components to atom, molecules, organisms, pages etc
- atoms, molecules, organisms should not hold any states
- single responsibility principle for components design
- hide/show components

There are a few ways of toggling components
```javascript

// Approach A: If/else statement
{ ifConditionsIsMet ? <div>Show Component</div> : null }

// Approach B: ClassName to hide/show
<div className={ifConditionsIsMet ? 'someclassname is-visible' : 'someclassname'}>Show Component</div>

// Approach C: New Component
class Component extends React.Component {
  render() {
    const { visible:isVisible } = this.props;
    return isVisible ? <div>Show Component</div> : null;
  }
}

<Component visible={true}/>

// Approach D: Props

const Component = ({ visible }) => {
  return visible ? <div>Show Component</div> : null;
}
```
- abstract similar functionality using higher-order-components


###Communication between events
- component should not interact with one another directly, there should be a central dispatcher to manage the communication or data transfer
- when using a dispatcher, always describe the transaction that is happening, not just the value
- when there are communication between different components, use the namespace source:action:target (or current_component:action_type:target_component) to track where the changes are happening - do not create ghosts events (a situation where the component that is updating another component is unknown to the user)


### ClassName
- you will be passing className often to children component, so use a utility helper to help concatenate className
```javascript

// Method A
function ClassName(separator) {
  var args = Array.prototype.slice.call(arguments, 1);
  return args.join(separator);
}

// Method B
function ClassName() {
  return [...arguments].join(' ')
}
```
- long className? separate that component as a wrapper and wrap other children elements with it
- another alternative to deal with long className is to define the className in a Style object
```javascript

const Style = {
  list: 'list list--small',
  listSelected: 'list list--small is-selected',
  // you can also create a function to return className based on bool
  // you can avoid writing the conditional statement in the render method this way
  listClassName(selected) {
    return selected ? this.listSelected : this.list;
  }
}
```


###Design Patterns
- dispatcher
- higher order components (HOC)
- stores
- asynchronnous ajax call
