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
