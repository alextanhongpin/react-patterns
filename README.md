#React Patterns

A list of best practices that I have accumulated while using React.

###States
- store states in the root component, pass it in as props to the children component
- do not store states in React getInitialState, it's an anti-pattern
- components should build based on compositions from smaller components, rather than inheritance/extending
- children components should be stateless (pure functions)
- shared states should always be placed on the parent that hosts both component - better to store all in the root component
- since all the states will be stored in the root component, group them according to the *Model*
- a *Model* can hold the following props:


const expenseModel = {
  collection: [], // you will be working with collection all the time
  selected: 0, // index or ID of the selected item in the collection
  lastRecord: false, // if you are doing pagination/load more
  links: {
    next: 'http://api/v1/expenses?offset=1', // url for the prev/next/current collection
  }
}

- when updating an object in the state, make sure to pass back the whole object
- work with immutable states
- you can copy an object easily by using the following trick

const copy = JSON.parse(JSON.stringify(objName))

If you are using Babel or EcmaScript6, you can consider the Object.assign method. Note that this will only create a shallow copy.


###Lifecycle
- show loading when mounting a component, and remove the loading state after it is mounted or Ajax call is successful
- Ajax calls are triggered in the componentWillMount lifecycle
- dispatcher will be registered/subscribed in the componentWillMount, and unregistered/unsubscribe in the componentWillUnmount


###Communication between events
- component should not interact with one another directly, there should be a central dispatcher to manage the communication or data transfer
- when using a dispatcher, always describe the transaction that is happening, not just the value
- when there are communication between different components, use the namespace source:action:target (or current_component:action_type:target_component) to track where the changes are happening - do not create ghosts events (a situation where the component that is updating another component is unknown to the user)
