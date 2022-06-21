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

export default function Navigation() {
    return (
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


