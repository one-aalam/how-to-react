# How to React?
Proven principles and rules on writing code with React


## Components

__Prefer Functional Components__

Functional components gretaly simplify the sytnax, and improve the readability of your React components. Embrace the functional approach everywhere, or almost everywhere possible as it's less code, highly focussed, and is devoid of class component boilerplate.
```jsx
// 👎 Don't
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

// 👍 Do
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

__Favor Atomic Components__
Components encapsulate the presentation logic and behavior. Focussing on components that do one thing(and one thing well), or represent one aspect of the UI leads to more maintenable code. Everytime, you observe a component doing a lot, or more than what it truly represents by its name or position in the component heirarchy, split the logic apart into separate components or hooks to favor compose-ability, reusability, debug/test-ability, readability, maintainabilty, etc. of your App as it evolves.

__Name Components__

Choose to always name your components, as it helps with their findability and debugability
```jsx
// 👎 Avoid this
export default (props) => <h1>Hello, {props.name}</h1>

// 👍 Name your functions
export default function Welcome(props) {
  return <h1>Hello, {props.name}</h1>
}
```

>What's a function without a name? _An anonymous function_.What's a component without a name? _An anonymous component_.

__Think DRY. Co-locate for efficiency__

Any logic or state that's generic, not directly related to a component or its rendering should be maintained outside the component -
- **Co-located:** Within the same file and before component definition if there's no possibility of re-use
```jsx
// 👎 Don't
function WelcomeUser(props) {
  function renderUserAvatar(name) {
    ...
  }

  return <div>Hello, {renderUserAvatar(props.name)}</div>
}

// 👍 Do
function renderUserAvatar(name) {
  ...
}

function WelcomeUser(props) {
  return  <div>Hello, {renderUserAvatar(props.name)}</div>
}
```
- **Globally available:** From a common place, if it's pretty re-usable logic
```jsx
import { renderAvatar } from '../utils'
// 👍 Do
function WelcomeUser(props) {
  return <div>Hello, {renderAvatar(props.name)}</div>
}
```
_A general rule of thumb is that you can always start with co-location, and can split the logic apart if you find yourself repeating the same logic multiple times(more than 3)_

__Prefer Dynamic, DRY, or Non Hard-coded Mark-up__

Never code what you could generate. In React, or more aptly in JSX you encapsulate mark-up(with slight changes) in functions. Though, it looks and feels like mark-up, it's purely Javascript at the end of the day and you have all of the Javascript constructs(for looping, conditionals, etc.) at your disposal. Even if you're not using APIs you should clearly separate and code for what's data(and dynamic) and what's mark-up(and static).
```jsx
// 👎 Don't
export default function Navigation() {
    return (
      <ul className="navigation">
        <li className="nav-item">
          <a href="/" title="home">Home</a>
        </li>
        <li className="nav-item">
          <a href="/about" title="about">About</a>
        </li>
        <li className="nav-item">
          <a href="/contact" title="contact">Contact</a>
        </li>
      </ul>
    )
}


// 👍 Do

// configuration object.
const NAV_ITEMS = [
  { label: "Home", path: "/" },
  { label: "About", path: "/about" }
  { label: "Contact", path: "/contact" }
]

export default function Header() {
    return (
      <h1>{/* Logo and other stuff */}</h1>
      <ul className="navigation">
      {
        NAV_ITEMS.map(navItem => {
          return (
            <li className="nav-item">
              <a href={navItem.path} title={navItem.label.toLowerCase()} >{navItem.label}</a>
            </li>
          )
        })
      }
      </ul>
    )
}

```
>This, as a side-effect also leads to portability of configuration thus helping with maintainability of component as well as the configuration

__Extract listing as a separate responsibility(and Component)__

See and treat _listing_ as a separate responsibility. For exmaple, if you're building a `Header` component with `Logo`, `Navigation`(like above), etc. it's better to organise your code as

```jsx

function NavListItem(props) {
    return (
        <li className="nav-item">
            <a href={props.path} title={props.label.toLowerCase()} >{props.label}</a>
        </li>
    )
}

function NavList() {
    return (
      <ul className="navigation">
      {
        NAV_ITEMS.map(navItem => <NavListItem path={navItem.path} label={navItem.label} />)
      }
      </ul>
    )
}

export default function Header() {
    return (
      <h1>{/* Logo and other stuff */}</h1>
      <NavList/>
    )
}

```

__Conditional Rendering__

Though more verbose, ternary operators produce more clear and readable code.
```jsx

// Don't
import { useState } from 'react'

const FollowButton = () => {
    const [ isFollowed, setIsFollowed ] = useState(false)
    return (
        <button className="follow-btn">
            { !isFollowed && `follow` }
            { isFollowed && `unfollow` }
        </button>
    )
}

export default FollowButton

// Do
import { useState } from 'react'

const FollowButton = () => {
    const [ isFollowed, setIsFollowed ] = useState(false)
    return (
        <button className="follow-btn">
            { isFollowed ? `unfollow` : `follow` }
        </button>
    )
}

export default FollowButton

```


__Add Comments for clarity__

Add comments for clarity when logic inside a component is not easily understandable.
```jsx
import { useState } from 'react'

const FollowButton = ({ }) => {
    const [ isFollowed, setIsFollowed ] = useState(false)
    return (
        <button className="follow-btn">
            {/* Show `unfollow` when the user is followed, or `follow` otherwise */}
            { isFollowed ? `unfollow` : `follow` }
        </button>
    )
}

export default FollowButton

```
JSX lets you write JS expressions alongside renderable mark-up and your mark-up is mark-up + rendering logic. Just like Javascript, you're free to express what your code is doing.

__Use Error Boundaries__

Use `<ErrorBoundary/>` to catch errors that happen during the rendering phase of a component, and not let it cascade beyond its boundary.

[More](https://reactjs.org/docs/error-boundaries.html)
Note: Use something like [react-error-boundary](https://www.npmjs.com/package/react-error-boundary) to deal with errors even more gracefully.


### Components & Props

__Check "Prop" Types__

To get what you(your component) wants, you need to tell what you want. React provides you with a way to tell the shape of the properties your components recieve, that you should prefer to enforce the _prop contract_.
```jsx
import PropTypes from 'prop-types' // external package

function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

Welcome.propTypes = {
  name: PropTypes.string
}

export default Welcome
```
> [`prop-types`](https://www.npmjs.com/package/prop-types) lets you do a runtime check of the properties a components receive and helps you avoid a lot of bugs. To catch bugs early, and always develop in a type-safe way, you obviously have [Typescript](https://www.typescriptlang.org/).

__Destructure Props__
Since the modern tooling allows for ES6+ syntax, there's no point in not conveniently accessing all of the `Props` a component receives.
```jsx

// Don't
const Button = (props) => {
    return (
        <button type={props.type} className="follow-btn" onClick={props.onClick}>
            {props.label}
        </button>
    )
}

// Do
const Button = ({ type, label, onCLick }) => {
    return (
        <button type={type} className="follow-btn" onClick={onClick}>
            {label}
        </button>
    )
}
```
> Save yourself from some `Prop-ery` and leverage ES6+ syntax wherever possible today, as long as they don't impact the legibility of your code

__Use/Apply Default Props the ES6+ way__
Assign default values directly when you're destructing the Props. Since components are just functions, this is a more natural and efficient way to apply and find the default values a component takes.

```jsx
// Don't
const Button = ({ type, label, disabled, onCLick }) => {
    return (
        <button type={type} className="follow-btn" onClick={onClick} disabled={disabled}>
            {label}
        </button>
    )
}

Button.defaultProps = {
    type: 'button',
    label: '',
    onCLick: () => {},
    disabled: false
}


// Do
const Button = ({ type = 'button', label = '', disabled = false, onCLick = ()=> {} }) => {
    return (
        <button type={type} className="follow-btn" onClick={onClick} disabled={disabled}>
            {label}
        </button>
    )
}

```

__Prefer Objects for Complex Data Props__

Prefer objects as props when it's actually objects you're working with, not primitives.

```jsx
// Don't
    <Product
        name={product.name}
        description={product.description}
        price={product.price}
        images={product.images}
    />

// Do
    <Product product={product} />
```
This has the benefit of keeping your Component's Props API simple and easily maintainable.

_Components that render the data consumed out of APIs are a good candidate for this, whereas components that abstract/encapsulate browser elements and their styling/functional behavior are not_

__Limit the number of Props__

Try to limit the number of Props a component receives to give it less reasons to change and re-render. Apply the previous pattern to decide what can be clubbed as a single parameter.

### Components & state

__`useState` for primitives, `useReducer` for objects__

Start simple, but as you find yourself doing a lot of `useState`-ery in your components, immediately switch to `useReducer`. `useReducer` is naturally good for objects, and even better for stuff that might not look related but actually is like `error`, `loading`, `data` state when calling a API.


__State Management__

Though React started as view-only library, reaching out state management libraries for external state management like Redux wasn't always desirable. This popular use case(and demand) led to React having `useState` and `useReducer` hooks out of the box. As per your preference you can either co-locate and keep the state management code in your components or easily extract them into a custom hooks. When clubbed with `Context` you can roll your own light-weight, app-wide state management structure for stuff like themes, preferences, user details, carts, etc.

When you do use a community maintained library try to first reach out to lightweight and smart state management approaches/solutions like [Zustand](https://github.com/pmndrs/zustand), [Valtio](https://github.com/pmndrs/valtio), [Jotai](https://github.com/pmndrs/jotai), etc.

### Components & types
_Coming Soon_

### App Structure
_Coming Soon_
