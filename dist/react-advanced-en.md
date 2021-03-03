# React Advanced

## Topics

- React Router
- context
- effect hook in detail
- external and custom hooks
- reducer hook and state management with reducers
- styling libraries
- form libraries
- app development
- refs
- HOCs
- manual setup

# React Router

## React Router

**client-side routing**: navigating between views without leaving the React app

## Versions and Installation

React router 6 beta is available since June 2020, but development has been slow since then

packages for react router 6 (include TypeScript support):

- _react-router-dom@next_
- _history_

packages for react router 5:

- _react-router-dom_
- _@types/react-router-dom_

## Setup

the entire application is enclosed in a `BrowserRouter` element:

```js
// index.js

import { BrowserRouter } from 'react-router-dom';

ReactDOM.render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>,
  rootElement
);
```

## Basic router components (v6)

- `<Route>` - a component that renders its content when active
- `<Routes>` - a container for `<Route>` elements
- `<Link>` / `<NavLink>` - are used in place of `<a>` elements

## Basic example (v6)

```js
const App = () => {
  return (
    <div>
      <NavLink to="/slideshow">slideshow</NavLink>{' '}
      <NavLink to="/counter">counter</NavLink>
      <Routes>
        <Route path="/slideshow" element={<Slideshow />} />
        <Route path="/counter" element={<Counter />} />
      </Routes>
    </div>
  );
};
```

## Advanced routing (v6)

```jsx
const App = () => {
  return (
    <Routes>
      <Route path="/posts" element={<PostPage />}>
        <Route path="/:postId" element={<Post />} />
      </Route>
      <Route path="/shop" element={<ShopPage />}>
        <Route path="/" element={<ShopIndex />} />
        <Route path="/product/:id" element={<Product />} />
        <Route path="/cart" element={<Cart />} />
      </Route>
    </Routes>
  );
};
```

## Basic example (v5)

In v5 we use the `<Switch>` component instead of `<Routes>`

```js
const App = () => {
  return (
    <div>
      <NavLink to="/slideshow">slideshow</NavLink>{' '}
      <NavLink to="/counter">counter</NavLink>
      <Switch>
        <Route path="/slideshow">
          <Slideshow />
        </Route>
        <Route path="/counter">
          <Counter />
        </Route>
      </Switch>
    </div>
  );
};
```

## Route parameters

```jsx
<Route path="/todos/:todoId">
  <TodoDetailView />
</Route>
```

```jsx
import { useParams } from 'react-router-dom';

const TodoDetailView = () => {
  const routeParams = useParams();
  return (
    <div>
      Details of todo: {routeParams.todoId}
      <div>...</div>
    </div>
  );
};
```

## Styling links

supplying a class name that will be applied to any active link:

```xml
<NavLink to="/" activeClassName="active-link">Home</NavLink>
<NavLink to="/add" activeClassName="active-link">Add</NavLink>
```

## Navigation from React

in v6:

```jsx
const navigate = useNavigate();
// ...
navigate('/');
```

in v5:

```js
const history = useHistory();
// ...
history.push('/');
```

## Navigation from React

example:

```jsx
const AddTodoView = () => {
  const navigate = useNavigate();
  const handleSubmit = (event) => {
    event.preventDefault();
    // ...
    navigate('/');
  };
  // ...
};
```

# Context

## Context

Context is a means to provide values from a component to all components that are contained within it - without explicitly passing it through all intermediate levels.

The interface of context can pass both data and event handlers

## Context - example

```js
// TodosContext.js
import { createContext } from 'react';

const TodosContext = createContext();

export default TodosContext;
```

## Context - example

with TypeScript:

```ts
// TodosContext.ts
import { createContext } from 'react';

type TodosContextType = {
  todos: Array<Todo>;
  onToggle: (id: number) => void;
};

const TodosContext = createContext({} as TodosContextType);

export default TodosContext;
```

## Context - example

The _provider_ makes values available to all its subcomponents

```jsx
<TodosContext.Provider
  value={{
    todos: todos,
    onToggle: () => {
      // ...
    },
  }}
>
  <TodoList />
  <AddTodo />
  <TodoStats />
</TodosContext.Provider>
```

## Context - example

using the _context hook_ to query values of a specific context:

```jsx
const TodoStats = () => {
  const context = useContext(TodosContext);
  return <div>There are {context.todos.length} todos</div>;
};
```

# Effect hook in detail

## Uses of the effect hook

- triggering API queries
- saving to / loading from _localStorage_ / _indexeddb_
- explicitly manipulating the DOM (along with _refs_)
- starting timers
- ...

## Example: DocumentTitle component

We will create a component that can set the document title dynamically:

```xml
<DocumentTitle value="my custom title" />
```

This component may appear anywhere in the React application.

## Example: DocumentTitle component

```jsx
const DocumentTitle = (props) => {
  const updateTitle = () => {
    document.title = props.value;
  };
  useEffect(updateTitle, [props.value]);
  return null;
};
```

## Cleanup functions

An effect may return a "cleanup function"

This function will be executed before the next run of the effect or before the component is unmounted

Example use case: cancel an old API search request if the search term has changed

## Cleanup functions

example: hook that queries a hackernews API

```jsx
const useHackernewsQuery = (query) => {
  const [articles, setArticles] = useState([]);
  useEffect(() => {
    let isLatestRequest = true;
    fetch(
      'https://hn.algolia.com/api/v1/search?query=' + query
    )
      .then((res) => res.json())
      .then((data) => {
        if (isLatestRequest) {
          setData(data);
        }
      });
    return () => {
      isLatestRequest = false;
    };
  }, [query]);
  return articles;
};
```

## Cleanup functions

example: user will be logged out after 10 seconds of inactivity

```jsx
const App = () => {
  const [loggedIn, setLoggedIn] = useState(true);
  const [lastInteraction, setLastInteraction] = useState(
    new Date()
  );
  // restart the logout timer on user interaction
  useEffect(() => {
    const logout = () => setLoggedIn(false);
    const timeoutId = setTimeout(logout, 10000);
    return () => clearTimeout(timeoutId);
  }, [lastInteraction]);
  return (
    <button onClick={() => setLastInteraction(new Date())}>
      {loggedIn
        ? 'click to stay logged in'
        : 'logged out automatically'}
    </button>
  );
};
```

## Cleanup functions

If an effect function returns anything other than `undefined`, it is treated as a cleanup function

This is why async functions cannot be used as effect functions directly (they return promises)

## Effect after every rendering

If no second parameter is passed the effect will run after every rendering.

```jsx
const RenderLogger = () => {
  useEffect(() => {
    console.log('logger was rendered');
  });
  return <div>Render logger</div>;
};
```

# Hooks

## Hooks

Hooks = extension of function components

A _basic_ function component can render contents based on props and it can trigger events.

Hooks allow for advanced functionality, e.g. having internal component state or listening for lifecycle events.

## Hooks

Hooks are special functions that can be called at the start of a component definition.

Examples:

- `useState(...)`
- `useEffect(...)`
- `useContext(...)`
- `useReducer(...)`

## Hooks

> "In the longer term, we expect Hooks to be the primary way people write React components."

\- [React FAQ](https://reactjs.org/docs/hooks-faq.html#should-i-use-hooks-classes-or-a-mix-of-both)

## Rules of Hooks

Hooks inside a component definition must be called in the same order every time the component renders - React uses the call order to distinguish between e.g. multiple calls to `useState`

The same rules as in a component apply to hook calls inside a cutom hook

[Rules of Hooks on reactjs.org](https://reactjs.org/docs/hooks-rules.html)

# External hooks

## External hooks

Many additional hooks are provided by the React community

example use cases:

- querying APIs
- using global state
- using _localStorage_ for persistent state
- media queries
- querying the scroll position
- ... (see [awesome-react-hooks](https://github.com/rehooks/awesome-react-hooks))

## Example: react-query

<https://github.com/tannerlinsley/react-query>

Popular hook that can help with retrieving APIs

## Example: react-query

simple use:

```js
const TodoDisplay = () => {
  const [id, setId] = useState(0);
  const { status, data } = useQuery(`todo_${id}`, () =>
    fetchTodo(id)
  );
  return (
    <div>
      {status === 'success' ? data.title : status}
      <button onClick={() => setId(id + 1)}>next</button>
    </div>
  );
};

const fetchTodo = (id) =>
  fetch(
    `https://jsonplaceholder.typicode.com/${id}`
  ).then((response) => response.json());
```

# Custom hooks

## Custom hooks

Custom hooks can be used to extract logic from function components

They are functions which in turn use existing hooks like `useState` or `useEffect`

## Custom hooks - useTodos

Example: `useTodos` - can be used to extract data handling from the component definition (separating the _model_ from the _view_)

```jsx
function TodoApp() {
  const {
    todos,
    toggleTodo,
    deleteTodo,
    addTodo,
  } = useTodos();
  return (
    <div>
      <h1>Todos</h1>
      <TodoList
        todos={todos}
        onToggle={toggleTodo}
        onDelete={deleteTodo}
      />
      <AddTodo onAdd={addTodo} />
    </div>
  );
}
```

## Custom hooks - useTodos

definition of `useTodos`:

```jsx
function useTodos() {
  const [todos, setTodos] = useState([]);
  function addTodo(title) {
    const id = Math.max(0, ...todos.map((t) => t.id + 1));
    setTodos([
      ...todos,
      { id: id, title: title, completed: false },
    ]);
  }
  function deleteTodo(id) {
    setTodos(todos.filter((t) => t.id !== id));
  }
  function toggleTodo(id) {
    setTodos(
      todos.map((t) =>
        t.id === id ? { ...t, completed: !t.completed } : t
      )
    );
  }
  return { todos, addTodo, deleteTodo, toggleTodo };
}
```

## Custom hooks - useTodos

`useTodos` with API access:

```js
function useTodos() {
  const [todos, setTodos] = useState([]);
  const [isLoading, setIsLoading] = useState(false);
  function reload() {
    setIsLoading(true);
    fetchTodos().then((todos) => {
      setTodos(todos);
      setIsLoading(false);
    });
  }
  useEffect(reload, []);
  // ... (addTodo, deleteTodo, toggleTodo)
  return {
    todos,
    isLoading,
    reload,
    // ... (addTodo, deleteTodo, toggleTodo)
  };
}
```

## Custom hooks - useDate

Example: `useDate` - provides the current time and updates the component every 1000 milliseconds

```js
const Clock = () => {
  const time = useDate(1000);
  return (
    <div>The time is: {time.toLocaleTimeString()}</div>
  );
};
```

## Custom hooks - useDate

basic implementation of `useDate`:

```js
const useDate = (interval) => {
  const [date, setDate] = useState(new Date());
  useEffect(() => {
    setInterval(() => {
      setDate(new Date());
    }, interval);
  }, []);
  return date;
};
```

## Custom hooks - useExchangeRate

hook that provides the exchange rate for selected currencies

```js
function useExchangeRate(from, to) {
  const [rate, setRate] = useState(null);
  const [status, setStatus] = useState('loading');
  async function loadExchangeRateAsync() {
    try {
      const newRate = await fetchExchangeRate();
      setRate(newRate);
      setStatus('success');
    } catch {
      setRate(null);
      setStatus('error');
    }
  }
  function loadExchangeRate() {
    loadExchangeRateAsync();
  }
  useEffect(loadExchangeRate, [from, to]);
  return { status, rate };
}
```

## Custom hooks - useExchangeRate

```js
async function fetchExchangeRate(from, to) {
  const res = await fetch(
    'https://api.exchangeratesapi.io/latest?base=' +
      from.toUpperCase() +
      '&symbols=' +
      to.toUpperCase()
  );
  const data = await res.json();
  return data.rates[to.toUpperCase()];
}
```

## Custom hooks - exercise

Create a hook named `useWeather` that can be used to query Weather data - together with an associated context that enables application-wide caching of the data

```js
const { weather, status, reload } = useWeather('vienna', {
  autoReloadInterval: 60,
});
```

For the data source (API) see the next slide

## Custom hooks - exercise

OpenWeatherMap-API

example URL: <http://api.openweathermap.org/data/2.5/weather?appid=66445a4269dd911a5bbe214fadb768d6&units=metric&q=vienna>

(please only use this appid / API key for simple exercises)

entries in the API data:

- `.weather[0].main` (e.g. _Rain_)
- `.main.temp` (e.g. 24.5)
- `.wind.speed` (e.g. 2.5)
- `.name` (e.g. _Vienna_)

# State management with actions and reducers

<!-- NOTE: other sections link to this section - take care when reordering -->

## State management

In more complex applications or components it makes sense to manage the state (model) separately from the view.

Often the entire application state is represented by a data model and every change to the state will be done by triggering a change to the data model.

## State management tools

- reducer hook (included in React, conceptually similar to Redux)
- Redux (based on reducers, commonly used with React)
- recoil (based on React hooks, released by facebook in 2020)
- MobX (commonly used with React)
- ngrx (used with Angular)
- vuex (used with vue)

## State management tools

<figure>
  <img src="assets/redux-devtools-airbnb.png" style="width: 100%" alt="Redux devtools showing the state of the airbnb website">
  <figcaption>example: Redux devtools showing the complex state tree of the airbnb website</figcaption>
</figure>

## State management with actions

In the reducer hook, Redux, ngrx and vuex, each state change is triggered by an _action_, which is represented as a JavaScript object

<small>note: vuex uses the term _mutation_</small>

## State management with actions

In Redux / reducer hook:

- actions are represented by JavaScript objects
- actions always have a _type_ property
- actions commonly also have a _payload_ property

## State management with actions

example actions:

```json
{
  "type": "addTodo",
  "payload": "learn React"
}
```

```json
{
  "type": "deleteTodo",
  "payload": 1
}
```

```json
{
  "type": "deleteCompletedTodos"
}
```

## State management with reducers

Technique that is used in _Redux_ and in React's _reducer hook_:

- a state transition happens through a reducer function
- the reducer function receives the current state and an action
- the reducer function returns the new state (without mutating the old state)

## Reducer diagram

<img src="assets/redux-flow.svg" />

## Example: todos state management

Manual usage of a reducer:

```js
const state1 = [
  { id: 1, title: 'groceries', completed: false },
  { id: 2, title: 'taxes', completed: true },
];
const actionA = { type: 'addTodo', payload: 'gardening' };
const state2 = todosReducer(state1, actionA);
const actionB = { type: 'deleteTodo', payload: 1 };
const state3 = todosReducer(state2, actionB);
console.log(state3);
/* [{ id: 2, title: 'taxes', completed: true },
    { id: 3, title: 'gardening', completed: false },] */
```

## Example: todos state management

reducer implementation:

```js
const todosReducer = (oldState, action) => {
  switch (action.type) {
    case 'addTodo':
      return [
        ...oldState,
        {
          title: action.payload,
          completed: false,
          id: Math.max(0, ...oldState.map((t) => t.id)) + 1,
        },
      ];
    case 'deleteTodo':
      return oldState.filter(
        (todo) => todo.id !== action.payload
      );
    default:
      throw new Error('unknown action type');
  }
};
```

## Example: todos state management

usage with TypeScript:

```ts
type TodosState = Array<Todo>;

type TodosAction =
  | { type: 'addTodo'; payload: string }
  | { type: 'deleteTodo'; payload: number };

const todosReducer = (
  state: TodosState,
  action: TodosAction
): TodosState => {
  // ...
};
```

## Combining reducers

reducers can be easily combined / split to manage complex / nested state

state example:

```json
{
  "todoData": {
    "status": "loading",
    "todos": []
  },
  "uiData": {
    "newTitle": "re",
    "filterText": ""
  }
}
```

## Combining reducers

reducer implementation:

```js
const rootReducer = (rootState, action) => ({
  todoData: todoDataReducer(rootState.todoData, action),
  uiData: uiDataReducer(rootState.uiData, action),
});

const uiDataReducer = (uiData, action) => ({
  newTitle: newTitleReducer(uiData.newTitle, action),
  filterText: filterTextReducer(uiData.filterText, action),
});

const newTitleReducer = (newTitle, action) => {
  if (action.type === 'setNewTitle') {
    return action.payload;
  } else if (action.type === 'addTodo') {
    return '';
  } else {
    return newTitle;
  }
};
```

## Combining reducers

When combining reducers, a single reducer only manages part of the state; but every reducer receives any action and may react to it

# Reducer Hook

## Reducer Hook

For managing state we can now also utilize `useReducer` in addition to `useState`:

```js
const [state, dispatch] = useReducer(reducer, initialState);
```

specific example:

```js
const [count, countDispatch] = useReducer(countReducer, 0);
```

## Reducer Hook

Calling `useReducer` returns an array with two entries:

- current state
- a dispatch function that can be used to trigger actions

## Reducer Hook

```js
const TodoApp = () => {
  const [todos, dispatch] = useReducer(
    todosReducer,
    initialTodos
  );

  return (
    <div>
      ...
      <button
        onClick={() => dispatch({ type: 'deleteAll' })}
      >
        delete all todos
      </button>
    </div>
  );
};
```

## Reducer hook

The powerful _Redux devtools_ may be used with the reducer hook: <https://github.com/troch/reinspect> (requires some configuration work, manually dispatching actions does not work)

# Immutability helper libraries

## Immutability helper libraries

working directly with immutable state can be complicated / tedious

helper libraries:

- _immer.js_ (commonly used with _Redux_)
- _immutable.js_

## immer.js

```js
import produce from 'immer';

const todos = [
  // ...
];
const newTodos = produce(todos, (todosDraft) => {
  todosDraft[0].completed = true;
  todosDraft.push({ title: 'study', completed: false });
});
```

# Styling tools

## Styling tools

tools for external stylesheets:

- _classnames_ package
- CSS modules
- SCSS

CSS-in-JS libraries:

- emotion
- styled-components
- ...

## classnames

utility: npm package _classnames_:

```jsx
import classNames from 'classnames';

<div
  className={classNames({
    todoitem: true,
    completed: isCompleted,
  })}
>
  [...]
</div>;
```

## CSS modules

CSS modules are preconfigured in create-react-app; they enable using CSS class names that are guaranteed to be unique across CSS files

```js
import styles from './TodoItem.module.css';

<div className={styles.todoItem}>...</div>;

<div className={`${styles.todoItem} ${styles.completed}`}>
  ...
</div>;
```

## SCSS

enabling SCSS in a create-react-app project:

```bash
npm install node-sass
```

then we can use:

```js
import './TodoItem.scss';
```

## CSS-in-JS

**CSS-in-JS**: JavaScript is used to generate and attach stylesheets

Nowadays it's considered _ok_ to put styling in the same file as JavaScript / HTML

## CSS-in-JS

approaches:

- extend HTML element props (e.g. `css=...` in _emotion_)
- create React components that are only used for styling (e.g `PrimaryButton`)

## CSS-in-JS

libraries:

- styled-components
- emotion
- ...

## styled-components

library that enables creating styled versions of existing HTML elements

npm packages: `styled-components`, `@types/styled-components`

## styled-components

```jsx
import styled from 'styled-components';

const BlockImg = styled.img`
  display: block;
`;

const Container = styled.div`
  display: flex;
  justify-content: center;
`;

const Slideshow = (props) => (
  <Container>
    <button>prev</button>
    <BlockImg src="..." alt="..." />
    <button>next</button>
  </Container>
);
```

## styled-components

dynamic styles via props (TypeScript):

```tsx
import styled from 'styled-components';

const Button = styled.button<{ primary: boolean }>`
  color: ${(props) => (props.primary ? 'black' : 'white')};
  background-color: ${(props) =>
    props.primary ? 'white' : 'navy'};
`;

const Slideshow = (props) => (
  <Container>
    <Button primary={true}>prev</Button>
    <BlockImg src="..." alt="..." />
    <Button primary={true}>next</Button>
  </Container>
);
```

## Emotion

Emotion extends jsx notation with an additional _css_ prop

Installation:

```bash
npm install @emotion/core
```

## Emotion

```jsx
/** @jsx jsx */
import { jsx, css } from '@emotion/core';

const Slideshow = (props) => (
  <div
    css={css`
      display: flex;
      justify-content: center;
    `}
  >
    <button>prev</button>
    <img
      css={css`
        display: block;
      `}
      src="..."
      alt="..."
    />
    <button>next</button>
  </div>
);
```

# Form libraries

## Form libraries

examples:

- react-hook-form (based on a custom hook)
- formik (based on custom components)

functionality:

- **validation**
- managing form data
- simplifying submit handler

## react-hook-form

_react-hook-form_ does not keep input contents in React state

advantages: faster, simpler

disadvantages: deviates from standard React concepts (uses _refs_ instead of _state_)

## react-hook-form

```js
import { useForm } from 'react-hook-form';

const NewsletterSignup = () => {
  const { register, errors, handleSubmit } = useForm();
  return (
    <form onSubmit={handleSubmit(console.log)}>
      <input
        name="email"
        ref={register({
          required: true,
          pattern: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
        })}
      />
      <button>sign up for newsletter</button>
      {errors.email ? <div>invalid email</div> : null}
    </form>
  );
};
```

Note: `register()` uses a [callback ref](https://reactjs.org/docs/refs-and-the-dom.html#callback-refs) to access the input

## react-hook-form: register

The `register` function can take some parameters that specify field validation:

- `required`
- `min`, `max`
- `minLength`, `maxLength`
- `pattern`
- `validate`

## react-hook-form: errors

The `errors` object indicates errors for any registered input that has a name

```jsx
<input name="email" ref={register(/*...*/)}>
```

```jsx
errors.email ? <div>invalid email</div> : null;
```

## react-hook-form: handleSubmit

`handleSubmit` will validate form data and pass it to a function if it is valid

```jsx
<form
  onSubmit={handleSubmit((data) => {
    console.log(data.email);
  })}
>
  ...
</form>
```

## react-hook-form: mode

```js
useForm({ mode: 'onSubmit' });
```

modes:

- `onSubmit` (default)
- `onBlur` - validation happens when the input loses focus
- `onTouched` - validation happens when the input loses focus for the first time; after that, validation happens on every change
- `onChange`
- `all` - validation happens when the input changes or when it loses focus without being changed

## react-hook-form: reset

```js
const { register, errors, handleSubmit, reset } = useForm();
// ...
reset();
```

## react-hook-form: testing

component tests related to _react-hook-form_ require a setup:

```bash
npm install mutationobserver-shim
```

```js
// setupTests.js
import 'mutationobserver-shim';
```

## formik

```js
import { Formik, Form, Field, ErrorMessage } from 'formik';

const NewsletterRegistration = () => (
  <Formik
    initialValues={{ email: '' }}
    onSubmit={(values) => console.log(values)}
    validate={(values) => {
      const errors = {};
      if (!isEmail(values.email)) {
        errors.email = 'invalid email';
      }
      return errors;
    }}
  >
    {(props) => (
      <Form>
        <Field type="email" name="email" />
        <button disabled={!props.isValid}>subscribe</button>
        <ErrorMessage name="email" component="div" />
      </Form>
    )}
  </Formik>
);

const isEmail = (email) =>
  email.match(/^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i);
```

<!--
related sections in:
- react-advanced
- node-and-express-intermediate
-->

# User authentication with an identity provider

## Identity provider

an _identity provider_ can verify the identity of users (can authenticate users)

examples:

> the current user is logged in as user "foo" on this domain

> the current user is authenticated as user "x" by _Google_ / as user "y" by _facebook_

## Identity provider

mechanism for the user:

user clicks on _login_, is redirected to a login page, and then sent back to the original site once logged in

in the background the user will receive an _identity token_, a piece of data that can prove their identity with the identity provider

## Identity provider

standards:

- authorization via _OAuth2_
- authentication via _OpenID Connect_

## Auth0

**Auth0** (_auth-zero_) is a widely-used identity provider

supports authentication via "internal" accounts or external identity providers (e.g. _Google_, _Apple_, _Facebook_, ...)

## Auth0: Registration and setup

- register for an Auth0 account on <https://auth0.com>
- in the sidebar, select "Applications"
- select the default application or create a new "Single Page Web Application"; the selected name will be shown to users when they authenticate

<!--
registration details:
select region: EU / US / AU
select account type: personal / company
-->

## Auth0: Registration and setup

_Application Settings_:

- allowed redirect destinations after login: _Allowed Callback URLs_
- allowed redirect destinations after logout: _Allowed Logout URLs_
- for refreshing authentication tokens: _Allowed Web Origins_

for local development, set all three to _http&#x3A;//localhost:3000_

## Domain and client ID

under _Settings_:

each Auth0 registrant has at least one domain (e.g. _dev-xxxxxxxx.eu.auth0.com_)

each app has a specific client ID (e.g. _jA0EAwMCxamDRMfOGV5gyZPnyX1BBPOQ_)

## auth0-react

library: [auth0-react](https://github.com/auth0/auth0-react/)

npm package: _@auth0/auth0-react_

include a provider that manages a context:

```jsx
<Auth0Provider
  domain="YOUR_AUTH0_DOMAIN"
  clientId="YOUR_AUTH0_CLIENT_ID"
  redirectUri={window.location.origin}
>
  <App />
</Auth0Provider>
```

(see next slide for implementation in _next.js_)

## auth0-react

when using _next.js_:

```jsx
// pages/_app.js
export default function App({ Component, pageProps }) {
  return (
    <Auth0Provider
      domain="YOUR_AUTH0_DOMAIN"
      clientId="YOUR_AUTH0_CLIENT_ID"
      redirectUri="YOUR_URL"
    >
      <Component {...pageProps} />
    </Auth0Provider>
  );
}
```

## auth0-react

```jsx
const auth = useAuth0();

if (auth.isLoading) {
  return <div>...</div>;
} else if (auth.error) {
  return <div>...</div>;
} else if (!auth.isAuthenticated) {
  return (
    <button onClick={auth.loginWithRedirect}>Log in</button>
  );
} else {
  return (
    <div>
      main content
      <button onClick={auth.logout}>log out</button>
    </div>
  );
}
```

## auth0-react

entries in the return value of `useAuth0`:

- `isLoading`
- `error`
- `isAuthenticated`
- `loginWithRedirect`
- `logout`
- `user` (`user.sub`, `user.email`, `user.username`, `user.name`, ...)

## auth0-react

task: create a React app where the content is only accessible if the user is logged in and the username / email are displayed if available

## auth0-react

authentication can be verified via access tokens (these are not JWT tokens)

more entries in the return value of `useAuth0`:

- `getAccessTokenSilently`
- `getAccessTokenWithPopup`

## auth0-react

making a request with the access token:

```js
const makeRequestSilently = () => {
  auth.getAccessTokenSilently().then((token) => {
    console.log(`make API request with token ${token}`);
  });
};
```

## auth0-react

verifying an Auth0 token on the API side:

```js
const auth0Domain = 'dev-xxxxxxxx.eu.auth0.com';

fetch(`https://${auth0Domain}/userinfo`, {
  headers: {
    'Content-Type': 'application/json',
    Authorization: `Bearer ${token}`,
  },
})
  .then((res) => res.json())
  .then((userInfo) => {
    console.log(`authenticated as ${userInfo.sub}`);
  })
  .catch((error) => console.log('error'));
```

note: just sending user info (e.g. user ID) from the client is not secure since it can't be verified by the API; information can only be verified via a token

## Resources

- <https://auth0.com/docs/quickstart/spa/react>
- <https://auth0.com/blog/complete-guide-to-react-user-authentication/>
- <https://auth0.com/docs/libraries/auth0-react>
- [API reference](https://auth0.github.io/auth0-react/)

<!--
examples of hooks that handle authentication:

- https://usehooks.com/useAuth/
- https://medium.com/hackernoon/learn-react-hooks-by-building-an-auth-based-to-do-app-c2d143928b0b
-->

# PWAs

Progressive Web Apps with React

## PWAs

_Progressive Web Apps_ enable us to write applications for PC and mobile using HTML, CSS and JavaScript

## PWAs

_create-react-app_ can be used to initialize React projects with basic PWA support

```bash
npx create-react-app myapp --template cra-template-pwa
npx create-react-app myapp --template cra-template-pwa-typescript
```

## PWAs

PWA basics in _create-react-app_ projects:

- configuration in `public/manifest.json`
- PWA-Boilerplate in `src/serviceWorker.js`

## PWAs: activation

in `index.js` / `index.tsx`:

```js
serviceWorker.register();
```

## PWAs: configuration

Via `public/manifest.json`

## PWA: add to homescreen

Procedure in Chrome:

- wait until Chrome will allow the install prompt to be displayed
- display a button or the like that offers installation
- when the button is clicked, make Chrome display an installation prompt

see also: <https://developers.google.com/web/fundamentals/app-install-banners/>

## PWA: add to homescreen

TypeScript implementation:

```ts
const [canInstall, setCanInstall] = useState(false);
const installPromptEventRef = useRef<Event>();

const getInstallPermission = () => {
  window.addEventListener(
    'beforeinstallprompt',
    (ipEvent) => {
      ipEvent.preventDefault();
      installPromptEventRef.current = ipEvent;
      setCanInstall(true);
    }
  );
};
useEffect(getInstallPermission, []);
```

## PWA: add to homescreen

TypeScript implementation:

```tsx
<button
  disabled={!canInstall}
  onClick={() => {
    (installPromptEventRef.current as any).prompt();
  }}
>
  install
</button>
```

## PWA: Deployment

- `npm run build`
- upload the build folder to a static host (e.g. <https://netlify.com/drop> or <https://tiiny.host/>)

# React Native

## React Native

React Native can be used to write React applications for iOS and Android devices

## Options for development

- **Expo**: simple option, quick to get started
- **React Native CLI**: enables integration of native modules (Java / Objective-C)

## Expo tools

- **Expo Snack**: online editor / playground
- **Expo CLI**: local development
- **Expo App**: emulator for live testing on Android / iOS (available on app stores)

## Expo Snack

<https://snack.expo.io>

options:

- run web version
- emulate Andoid / iOS online (limited capacity)
- run on local device (via Expo App)

## Expo CLI

installation:

```bash
npm install -g expo-cli
```

creating a new expo project:

```bash
expo init myproject
```

running a project (will open a dashboard at _localhost:19002_):

```bash
npm run start
```

## Expo CLI

running on a device:

- select _tunnel_
- wait
- scan QR code with Expo app

## React Native components

- View (=div)
- Text
- Image
- Button
- TextInput
- ScrollView

[detailed list](https://facebook.github.io/react-native/docs/components-and-apis#basic-components)

## React Native components

Examples:

```js
<Button title="press me" onPress={handlePress} />
```

```js
<TextInput value={myText} onChangeText={setMyText} />
```

## Styling

All React Native components accept a _style_ prop that can take an object:

```js
const TodoItem = ({ title, completed }) => (
  <View style={{ margin: 5, padding: 8 }}>
    <Text>{title}</Text>
  </View>
);
```

## Styling

The style prop can also receive an array of objects which are merged (_falsy_ entries are ignored)

```js
const TodoItem = ({ title, completed }) => (
  <View
    style={[
      { padding: 8, backgroundColor: 'lightcoral' },
      completed && { backgroundColor: 'lightgrey' },
    ]}
  >
    <Text>{title}</Text>
  </View>
);
```

## Styling

Creating _stylesheets_ that define multiple sets of styles:

```js
import { StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  todoItem: {
    padding: 8,
    backgroundColor: 'lightcoral',
  },
  completedView: {
    backgroundColor: 'lightgrey',
  },
  completedText: {
    textDecoration: 'line-through',
  },
});
```

## Styling

using stylesheets:

```js
const TodoItem = ({ title, completed, onToggle }) => (
  <View
    style={[
      styles.todoItem,
      completed && styles.completedView,
    ]}
  >
    <Text style={[completed && styles.completedText]}>
      {completed ? 'DONE: ' : 'TODO: '}
      {title}
    </Text>
  </View>
);
```

## Platform-specific code

option 1 (simple cases):

```js
import { Platform } from 'react-native';
if (Platform.OS === 'web') {
  // 'web' / 'ios' / 'android'
}
```

option 2 (platform-specific components):

- `AddTodo.web.js`
- `AddTodo.ios.js`
- `AddTodo.android.js`

# Refs for storing object references

## Refs for storing object references

In function components, _refs_ can be used to store values / object references that change over time but that don't influence the rendering of a component (they don't belong in the state)

examples of what may be stored in refs:

- timer ids
- request ids
- WebSocket connections
- a PWA install prompt (see section PWA)
- DOM Elements (see next section "Ref property for accessing DOM elements")

## Refs for storing object references

In function components, _refs_ can be created / accessed via `useRef`

Values are stored in the reference's `.current` property

## Refs for storing object references

```js
const StopWatch = () => {
  const [time, setTime] = useState(0);
  const intervalRef = useRef();
  const start = () => {
    setTime(0);
    intervalRef.current = setInterval(() => {
      setTime((t) => t + 1);
    }, 1000);
  };
  const stop = () => {
    clearInterval(intervalRef.current);
  };
  return <div>...</div>;
};
```

Note: this simple example could also be implemented without refs by using the effect hook

# Ref property for accessing DOM elements

## Ref property for accessing DOM elements

Just like _key_, the _ref_ property has a special meaning in JSX - enabling direct access to rendered DOM elements

use cases:

- managing focus, text selection, or media playback
- alternative way of managing inputs (uncontrolled components)
- integrating with third-party DOM libraries

## Ref property for accessing DOM elements

Using the _ref_ property together with the _useRef_ hook:

```jsx
function RefDemo() {
  const myRef = useRef();
  return (
    <input
      ref={myRef}
      onChange={() => {
        console.log(myRef.current.value);
      }}
    />
  );
}
```

## Ref property for accessing DOM elements

**managing focus, text selection, or media playback**

some changes cannot be expressed declaratively (via state); they require direct access to a DOM element

example: there are properties like `.value` for changing a value or `.className` for changing classes, but there is no property for managing focus

## Ref property for accessing DOM elements

**alternative way of managing inputs**

using `ref` instead of `value` and `onChange` can mean less code (but is discouraged by the React documentation)

Refs are used by _react-hook-form_ to make form handling simpler and faster

## Ref property for accessing DOM elements

**integrating with third-party DOM libraries**

Third-party libraries may require a DOM element being passed in

Example: Google Maps takes an element where it will paint the map

Many third-party libraries have wrappers for React where refs are not needed

## Ref property for managing focus

Managing focus with a ref:

```js
const App = () => {
  const inputEl = useRef(null);
  return (
    <div>
      <input ref={inputEl} />
      <button onClick={() => inputEl.current.focus()}>
        focus
      </button>
    </div>
  );
};
```

## Ref property and effect hook for managing media playback

```tsx
// https://codesandbox.io/s/media-playback-x3ci4
function Video() {
  const [playing, setPlaying] = useState(false);
  const videoEl = useRef<HTMLVideoElement>(null);
  useEffect(() => {
    if (playing) {
      videoEl.current?.play();
    } else {
      videoEl.current?.pause();
    }
  }, [playing]);
  return (
    <div>
      <video
        ref={videoEl}
        width="250"
        src={
          'https://interactive-examples.mdn.mozilla.net/' +
          'media/cc0-videos/flower.mp4'
        }
      />
      <button onClick={() => setPlaying(!playing)}>
        {playing ? 'pause' : 'play'}
      </button>
    </div>
  );
}
```

## Ref property for managing inputs

Managing inputs: comparing `useState` and `useRef`:

```js
const App = () => {
  const [firstName, setFirstName] = useState('');
  const lastNameInput = useRef(null);

  return (
    <div>
      <input
        value={firstName}
        onChange={(event) =>
          setFirstName(event.target.value)
        }
      />
      <input ref={lastNameInput} />

      <button
        onClick={() => {
          console.log(firstName);
          console.log(lastNameInput.current.value);
        }}
      >
        log values
      </button>
    </div>
  );
};
```

## Callback refs

As we've seen we can pass a Ref object into the _ref_ property

We can also pass in a _callback_ function which will be called with the element as its parameter (_react-hook-form_ uses this)

```jsx
<input
  ref={(element) => {
    console.log(element);
    console.log(element.value);
    element.focus();
  }}
  type="text"
/>
```

# Higher-order components

### functions that modify component definitions

## Higher-order components

confusing terminology:

Higher-order components are **not** components 😲

Higher-order components are functions that modify / enhance a component definition (they are "component decorators")

## Higher-order components

Example:

_React_'s `memo` is a higher-order component

It receives a component and returns a memoized component:

```js
const MemoizedRating = memo(Rating);
```

## Higher-order components

Example:

`connect` from _react-redux_ returns a HOC:

```js
// connector is a HOC
const connector = connect(
  mapStateToProps,
  mapDispatchToProps
);
```

The resulting HOC receives a regular component and returns a component that is connected to the Redux store:

```js
const RatingContainer = connector(Rating);
```

# File Structure

## File Structure

<https://reactjs.org/docs/faq-structure.html>

common approaches:

- grouping by features or routes
- grouping by type (component / reducer / API interface)

# Manual setup and configuration of React

## Manual setup and configuration

Setting up React without _create-react-app_

Here we will use the _parcel_ bundler - an alternative to webpack

## package.json and dependencies

Start out with a `package.json` file that contains an empty object : `{}`

Install the required packages:

```bash
npm install react react-dom
```

```bash
npm install --save-dev parcel-bundler @babel/preset-react @babel/preset-env
```

## Configuring babel

via `babel.config.json` file:

```json
{
  "presets": ["@babel/preset-env", "@babel/react"]
}
```

## npm scripts

add scripts to `package.json`:

```json
"scripts": {
  "build": "parcel build src/index.html",
  "start": "parcel src/index.html"
},
```

## HTML entry point

Create _src/index.html_:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>React starter app</title>
  </head>
  <body>
    <div id="app"></div>
    <script src="index.js"></script>
  </body>
</html>
```

## Rendering an App component

Create _src/index.js_:

```js
import React from 'react';
import ReactDOM from 'react-dom';

function App() {
  return <div>Hello World!</div>;
}

const mountNode = document.getElementById('app');
ReactDOM.render(<App />, mountNode);
```

## Running

Execute `npm run start` for a development server or `npm run build` for a build.
