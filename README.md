# react-redux

Installation 

$ npm install react-redux
or
$ yarn add react-redux


Provider

 React Redux provides <Provider />, which makes the Redux store available to the rest of your app:

import React from 'react'
import ReactDOM from 'react-dom'

import { Provider } from 'react-redux'
import store from './store'

import App from './App'

const rootElement = document.getElementById('root')
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  rootElement
)


connect()

React Redux provides a connect function for you to connect your component to the store.

Normally, you'll call connect in this way:

import { connect } from 'react-redux'
import { increment, decrement, reset } from './actionCreators'

// const Counter = ... 

const mapStateToProps = (state /*, ownProps*/ => {
  return {
    counter: state.counter,
  }
}

const mapDispatchToProps = { increment, decrement, reset }

export default connect(mapStateToProps, mapDispatchToProps)(Counter)


Basic Tutorial

To see how to use React Redux in practice, we'll show a step-by-step example by creating a todo list app

A Todo List Example

The React UI Components:

- TodoApp is the entry component for our app. It renders the header, the AddTodo, TodoList and VisibilityFilters components.
- AddTodo is the component that allows a user to input a todo item and add to the list upon clicking its "Add todo" button:
	- It uses a controller input that sets state upon onChange
	- When the user clicks on the "Add Todo" button, it dispatches the action (that we will provide using React Redux) to add the todo to the store.
- TodoList is the component that renders the list of todos:
	- It renders the filterd list of todos when one of the VisibilityFilters is selected
- Todo is the component that renders the list of todos:
	- It renders the todo conte,t and shows that a todo is completd by crossing it out
	- It dispatches the action to toggle the todo's complete status upon onClick
- VisibilityFilters renders a simple set of filters: all, completed, and incomplete. Clicking on each one of them filters the tdoso:
	- It accepts an activeFilter prop from the parent that indicates which filter is currently selected by the user. An active filter is rendered with an underscore.
	- It dispatches the setFilter action to update the selected filter
- constants hold the constants data for our app
- index renders our app to the DOM


The Redux Store

The Redux portion of the application has been set up using the patterns recommended in the Redux docs:

- Store
	- todos: A normalized reducer of todos. It contains a byIds map of all todos and a allIds that contains the list of all ids.
	- visibilityFilters: A simple string all, completed, or incomplete
- Action Creators
	- addTodo creates the action to add todos. It takes a single string variable content and return an ADD_TODO action with payload containing a self-incremented id and content
	- toggleTodo creates the action to toggle todos. It takes a single number variable id and returns a TOGGLE_TODO action with payload containing id only
	- setFilters creates the action to set the app's active filter. It takes a single variable filter and returns a SET_FILTER action with payload containing the filter itself
- Reducers
	- The todos reduccer
		- Appends the id to its allIds field and sets the todo within it byIds field upon receiving the ADD_TODO action
		- Toggles the completed field for the todo upon receiving the TOGGLE_TODO action
	- The visibilityFilters reducer sets its slice of store to the new filter it receives from the SET_FILTER action payloads
- Action Types
	- We can use a file actionTypes.js to hold the constants of action ypes to be reused 
- Selectors
	- getTodoList returns the allIds list from the todos store
	- getTodoById finds the todo in the store given by id
	- getTodos is slightly more complex. It takes all the ids from allIds, finds each todo in byIds, and returns the final array of todos
	- getTodosByVisibilityFilter filters the todos according to the visibility filter


Providing the Store

First we need to make the store available to our app. To do this, we wrap our app with the <Provider /> API provided by React Redux

// index.js
import React from 'react'
import ReactDOM from 'react-dom'
import TodoApp from './TooApp'

import { Provider ] from 'react-redux'
import store from './redux/store'

const rootElement = document.getElementById('root')
ReactDOM.render(
  <Provider store={store}>
    <TodoApp />
  </Provider>,
  rootElement
)

Notice how our <TodoApp /> is now wrapped with the <Provider /> with store passed in as a prop.


Connecting the Components

React Redux provides a connect function for you to read values from the Redux store (and re-read the values when the store updates)

The connect function takes two arguments, both optional:

- mapStateToProps: called every time the store state changes. It receives the entire store state, and should return an object of data this component needs.
- mapDispatchToProps: this parameter can either be a function, or an object.
	- If it's a function, it will be called once on component creation. It will receive dispatch as an argument, and should return an object full of functions that use dispatch to dispatch actions.
	- If it's an object full of action creators, each action reator will be turned into a prop function that automatically dispatches its action when called. Note: WWe recommend using this "object shorthand" form.

Normally, you'll call connect in this way:

const mapStateToProps = (state, ownProps) => ({
  // ... computed data from state and optionally ownProps
}) 

const mapDispatchToProps = {
  // ... normally is an object full of actions creators
}

// `connect` returns a new function that accepts the component to wrap:
const connectToStore = connect(
  mapStateToProps,
  mapDispatchToProps
)

// and that function returns the conneceted, wrapper component:
const ConnectedComponent = connectToStore(Component)

// We normally do both in one step, like this:
connect(
  mapStateToProps,
  mapDispatchToProps
)(Component)

Let's work on <AddTodo /> first. It needs to trigger changes to the store to add new todos. Therefore, it needs to be able to dispatch actions to the store. Here's how we do it.

Our addTodo action creator looks like this:

// redux/actions.js
import { ADD_TODO } from './actionTypes'

let nextTodoId = 0
export const addTodo = content => ({
  type: ADD_TODO,
  payload: {
    id: ++nextTodoId,
    contente
  }
})

// ... other actions

By passing it to connect, our component receives it as a prop, and it will automatically dispatch the action when it's called.

// components/AddTodo.js

// ... other imports
import { connect } from 'react-redux'
import { addTodo } from '../redux/actions'

class AddTodo extends React.Component {
  // ... component implementation
}

export default connect(
  null,
  { addTodo }
)(AddTodo)

Notice now that <AddTodo /> is wrapper with a parent component called <Connect(AddTodo) />
Meanwhile, <AddTodo /> now gains one prop: the addTodo action

WWe also need to implement the handleAddTodo function to let it dispatch the addTodo action and reset the input

// components/AddTodo.js

import React from 'react'
import { connect } from 'react-redux'
import { addTodo } from '../redux/actions'

class AddTodo extends React.Component {
  // ...

  handleAddTodo = () => {
    // dispatches actions to add todo
    this.props.addTodo(this.state.input)

    // sets state back to empty string
    this.setState({ input: '' })
  }

  render() {
    return (
      <div>
        <input
          onChange={e => this.updateInput(e.target.value)}
          value={this.state.input}
        />
        <button className="add-todo" onClick={this.handleAddTodo}>
          Add Todo
        </button>
      </div>
    )
  }
}

export default connect(
  null,
  { addTodo }
)(AddTodo)

Now our <AddTodo /> is connected to the store. When we add a todo it would dispatch an action to change the store. We are not seeing in the app because the other components are not connected yet. If you have the Redux DevTools Extension hooked up, you should see the action being dispatched:

You should also see that the store has changed accordingly

The <TodoList /> component is responsible for rendering the list of todos. Therefore, it needs to read data from the store. We enable it by calling connect with the mapStateToProps parameter, a function describing which part of the data we need from the store.

Our <Todo /> component takes the todo item as props. We have this information from the byIds field of the todos. However, we also need the information from the allIds field of the store indicating which todos and in what order they should be rendere. Our mapStateToProps function may look like this:


// components/TodoList.js

// ... other imports
import { connect } from 'react-redux';

const TodoList = // ... Ui component implementation

const mapStateToProps = state => {
  const { byIds, allIds } = state.todos || {};
  const todos = 
    allIds && allIds.lenght
      ? allIds.map(id => (byIds ? { ...byIds[id], id } : null))
      : null;
  return { todos };
};

export default connect(mapStateToProps)(TodoList);


Luckily we have a selctor that does exactly this. We may simply import the selector and use it here.


// redux/selectors.js

export const getTodosState = store => store.todos

export const getTodoList = store => 
  getTodosState(store) ? getTodosState(store).allIds : []

export const getTodoById = (store, id) => 
  getTodosState(store) ? {...getTodosStore(store).byIds[id], id } : {}

export const getTodos = store =>
  getTodoList(store).map(id => getTodoById(store, id))


// componens/TodoList.js

// ...other imports
import { connect } from 'react-redux';
import { getTodos } from '../redux/selectors';

const TodoList = // ... UI Component implementaton

export default connect(state => ({ todos: getTodos(state) }))(TodoList);

We recommend encapsulating any complex lookups or computations of data in selector functions. In addition, you can further optimize the performance by using Reselect to write “memoized” selectors that can skip unnecessary work. (See the Redux docs page on Computing Derived Data and the blog post Idiomatic Redux: Using Reselect Selectors for Encapsulation and Performance for more information on why and how to use selector functions.)

Now that our <TodoList /> is connected to the store. It should receive the list of todos, map over them, and pass each todo to the <Todo /> component. <Todo /> will in turn render them to the screen. Now try adding a todo. It should come up on our todo list!
