# State

## State

Components may have an internal _state_

The state can be referenced in the template. The view will automatically update if parts of the state are changed.

## State hook

In function components we make use of the _state hook_:

```js
import { useState } from 'react';
```

## State hook

`useState` may be called (repeatedly) inside the component function

- `useState` takes one parameter - the initial state value
- on each call `useState` returns an array with two entries: the current state and a function to set the state to a new value

```js
const App = () => {
  const [count, setCount] = useState(0);
  const [title, setTitle] = useState('React app');

  return ...
};
```

## Example: Counter

We will add a button to our application. At the start this button will display the value 0. On each click the value will increment by 1.

## Example: Counter

```jsx
const Counter = () => {
  const [count, setCount] = useState(0);

  return (
    <button
      onClick={() => {
        setCount(count + 1);
      }}
    >
      {count}
    </button>
  );
};
```

## Exercise: Slideshow

implement a slideshow that shows images like the following:

`https://picsum.photos/200?image=10`

- buttons for _previous_ and _next_
- button for _back to start_
- prevent the index from becoming negative

## Exercise: Prime number quiz

Create a quiz that shows an _odd_ number from 1-99. The user has to guess if it is a prime number or not.

Show statistics on correct / incorrect answers the user has given so far.
