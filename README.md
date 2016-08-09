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
- components lifecycle and what you can do there
#### componentWillMount
- is called before a component is mounted (rendered)
- trigger service call here and set the state here
- bind dispatcher events here
- trigger a loading state here (if necessary)
- subscribe to pub/sub events
#### render
- parse the model to be rendered here
- handle the props that is passed down from parent components here
- if / else checking to target the view to be rendered
#### componentDidMount
- refs can be called here
- remove loading state
#### componentWillUnmount
- unsubscribe to pub/sub events
#### componentWillReceiveProps
#### componentDidUpdate
#### ajaxWillCall
- set loading indicator
- disable other actionable trigger that might trigger the same service call twice
#### ajaxDidCall
- remove loading indicator

#### ajaxFail
- allow users to retry 
- render retry view
#### ajaxSuccess
- update the collection
- enable back disabled components


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


### Higher-order-components (HOCs)
There are several use cases for HOCs. To highlight a few:-

- feature toggle (deciding if the components should be rendered or not)
- placeholder (check the states to see if there are any collection, return a placeholder if there are none)
- adding services in between
- throttling events (useful for preventing multiple requests being submitted)
- refresh service mechanism (?). If a service call fail, render a view to refresh the particular component.
- adding tooltips 



### Handling conditionals
- in determining the styles
- hide or show components
- default values
- for width/height, setting dimensions in px is better than using 'large', 'small', 'medium'
- for button, it's okay to use classes like 'btn--small', 'btn--large'




### Props
There are several useful props that can be passed down to components:-
- placeholder: a placeholder text or component to be rendered for empty states
- selected: selected state of components (highlights etc)
- active: active state of component
- disabled: disabled state of component (greyed out or not clickable)
- value: default value
- model: components should best hold only one model
- events: onClick, or better a global event handler, etc
- id: if necessary
- ns: short for namespace, useful when using a dispatcher
- dispatcher: for communication between components, or triggering service call
- store: holds the service call, but will usually be placed in the root component


###Design Patterns
- dispatcher (observer pattern implementation, used to dispatch Actions in this case)
- higher order components (HOC) a.k.a. decorators (used to add functionality/additional properties to a component)
- stores
- asynchronnous ajax call
- state machine (when deciding which template to render - such as empty placeholders when there are no data or list when there's a collection present)
- factory (when there are multiple instances of a component - eg. Button, ButtonLarge, ButtonDark should be created from a ButtonFactory)
- template
- facade
