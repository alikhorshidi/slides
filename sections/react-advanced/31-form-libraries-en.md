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
- `validate` (custom validation function)

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
useForm({ mode: 'onTouched' });
```

`mode`: when should the value be validated initially?

- `onSubmit` (default)
- `onTouched` - when the input loses focus or on submit
- `onBlur` - when the input loses focus (does not switch to `reValidateMode`) or on submit
- `onChange` - when the input changes or on submit
- `all` - when the input changes or when it loses focus without being changed

## react-hook-form: mode

`reValidateMode`: if the user has submitted the form and there was an error, when should the value be re-validated?

- `onSubmit`
- `onBlur`
- `onChange` (default)

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
