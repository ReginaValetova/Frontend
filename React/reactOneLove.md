# React

## Table of Contents

1. [Introducing JSX](#introJSX)
2. [Rendering Elements](#render)
3. [Components and Props](#props)
4. [State and Lifecycle](#state)
5. [Handling Events](#HE)
6. [Conditional Rendering](#CR)
7. [Lists and Keys](#l&k)
8. [Forms](#forms)
9. [Lifting State Up](#LSU)
10. [Composition vs Inheritance](#CvsI)
11. [Thinking In React](#TiR)


<a name="introJSX"></a>
## Introducing JSX

Consider this variable declaration:

```JS
const element = <h1>Hello, world ^.^</h1>
```

This funny tag syntax is neither a string nor HTML.

It is called JSX, and it is a syntax extension to JavaScript. We recommend using it with React to describe what the UI should look like. JSX may remind you of a template language, but it comes with the full power of JavaScript.

JSX produces React “elements”. We will explore rendering them to the DOM in the next section. Below, you can find the basics of JSX necessary to get you started.

### Embedding Expressions in JSX

You can put any valid JavaScript expression inside the curly braces in JSX. For example, 2 + 2, user.firstName, or formatName(user) are all valid JavaScript expressions.

In the example below, we embed the result of calling a JavaScript function, formatName(user), into an __'h1'__ element.

```JS
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

const element = (
  <h1>
    Hello, {formatName(user)}!
  </h1>
);

ReactDOM.render(
  element,
  document.getElementById('root')
);
```

### JSX is an Expression Too

```JS
function getGreeting(user) {
  if (user) {
    return <h1>Hello, {formatName(user)}!</h1>;
  }
  return <h1>Hello, Stranger.</h1>;
}
```

### Specifying Attributes with JSX
You may use quotes to specify string literals as attributes:

```JS
const element = <div tabIndex="0"></div>;
```
You may also use curly braces to embed a JavaScript expression in an attribute:

```JS 
const element = <img src={user.avatarUrl}></img>;
```
Don’t put quotes around curly braces when embedding a JavaScript expression in an attribute. You should either use quotes (for string values) or curly braces (for expressions), but not both in the same attribute.

### Specifying Children with JSX
If a tag is empty, you may close it immediately with __'/>'__, like XML:
```JS
const element = <img src={user.avatarUrl} />;
```
JSX tags may contain children:

```JS
const element = (
  <div>
    <h1>Hello!</h1>
    <h2>Good to see you here.</h2>
  </div>
);
```

### JSX Represents Objects
Babel compiles JSX down to __JS React.createElement()__ calls.

These two examples are identical:

```JX
const element = (
  <h1 className="greeting">
    Hello, world!
  </h1>
);
```

```JS
const element = React.createElement(
  'h1',
  {className: 'greeting'},
  'Hello, world!'
);
```
React.createElement() performs a few checks to help you write bug-free code but essentially it creates an object like this:

```JS
// Note: this structure is simplified
const element = {
  type: 'h1',
  props: {
    className: 'greeting',
    children: 'Hello, world!'
  }
};
```

These objects are called “React elements”. You can think of them as descriptions of what you want to see on the screen. React reads these objects and uses them to construct the DOM and keep it up to date.

<a name="render"></a>
## Rendering Elements

An element describes what you want to see on the screen: 

```JS
const element = <h1>Hello, world!</h1>;
```

### Rendering na Element into te DOM

Let’s say there is a <div> somewhere in your HTML file:

```JS
<div id="root"></div>
```
We call this a “root” DOM node because everything inside it will be managed by React DOM.

To render a React element into a root DOM node, pass both to ReactDOM.render():

```Js
const element = <h1>Hello, world!</h1>
ReactDOM.render(element,
document.getElementById('root'));

### Updating the Render Element 

React elements are immutable. Once you create an element, you can’t change its children or attributes. An element is like a single frame in a movie: it represents the UI at a certain point in time.

With our knowledge so far, the only way to update the UI is to create a new element, and pass it to ReactDOM.render().

Consider this tickind clock example: 

```JS
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>Now is is {new Date().toLocalTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(element, 
  document.getElementById('root'));
}

setInterval(tick, 1000);
```
### React only Updates What's Necessary

React DOM compares the element and its children to the previous one, and only applies the DOM updates necessary to bring the DOM to the desired state.

Even though we create an element describing the whole UI tree on every tick, only the text node whose contents has changed gets updated by React DOM.

<a name="props"></a>
## Conponents and Props

### Function and Class Components

The simplest way to define a component is to write a JavaScript function:

```JS
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```
This function is a valid React component because it accepts a single __“props”__ (which stands for properties) object argument with data and returns a React element. We call such components __“function components”__ because they are literally JavaScript functions.

You can also use an ES6 class to define a component:

```JS
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

The above two components are equivalent from React’s point of view.

### Rendering a Components 

Previously, we only encountered React elements that represent DOM tags:

```JS
const element = <div />;
```

However, elements can also represent user-defined components:

```JS
const element = <Welcome name="Sara" />;
```

When React sees an element representing a user-defined component, it passes JSX attributes to this component as a single object. We call this object __“props”__.

For example, this code renders “Hello, Sara” on the page:

```JS
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}

const element = <Welcome name="Sara" />;
ReactDOM.render(
  element,
  document.getElementById('root')
);
```

Let’s recap what happens in this example:

* We call ReactDOM.render() with the <Welcome name="Sara" /> element.
* React calls the Welcome component with {name: 'Sara'} as the props.
* Our Welcome component returns a __'h1'Hello, Sara'/__h1' element as the result.
* React DOM efficiently updates the DOM to match __'h1'Hello, Sara'/h1'__.

### Extracting Components
Don’t be afraid to split components into smaller components.

For example, consider this Comment component:

```JS
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <img className="Avatar"
          src={props.author.avatarUrl}
          alt={props.author.name}
        />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

It accepts author (an object), text (a string), and date (a date) as props, and describes a comment on a social media website.

This component can be tricky to change because of all the nesting, and it is also hard to reuse individual parts of it. Let’s extract a few components from it.

First, we will extract Avatar:

```JS
function Avatar(props) {
  return (
    <img className="Avatar"
      src={props.user.avatarUrl}
      alt={props.user.name}
    />

  );
}
```

The Avatar doesn’t need to know that it is being rendered inside a Comment. This is why we have given its prop a more generic name: user rather than author.

We recommend naming props from the component’s own point of view rather than the context in which it is being used.

We can now simplify Comment a tiny bit:

```JS
function Comment(props) {
  return (
    <div className="Comment">
      <div className="UserInfo">
        <Avatar user={props.author} />
        <div className="UserInfo-name">
          {props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```
Next, we will extract a UserInfo component that renders an Avatar next to the user’s name:

```JS
function UserInfo(props) {
  return (
    <div className="UserInfo">
      <Avatar user={props.user} />
      <div className="UserInfo-name">
        {props.user.name}
      </div>
    </div>
  );
}
//This lets us simplify Comment even further:

function Comment(props) {
  return (
    <div className="Comment">
      <UserInfo user={props.author} />
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
        {formatDate(props.date)}
      </div>
    </div>
  );
}
```

Extracting components might seem like grunt work at first, but having a palette of reusable components pays off in larger apps. A good rule of thumb is that if a part of your UI is used several times (Button, Panel, Avatar), or is complex enough on its own (App, FeedStory, Comment), it is a good candidate to be a reusable component.

### Props are Read-Only
Whether you declare a component as a function or a class, it must never modify its own props. Consider this sum function:

```JS
function sum(a, b) {
  return a + b;
}
```

Such functions are called “pure” because they do not attempt to change their inputs, and always return the same result for the same inputs.

In contrast, this function is impure because it changes its own input:

```JS
function withdraw(account, amount) {
  account.total -= amount;
}
```

React is pretty flexible but it has a single strict rule:

**All React components must act like pure functions with respect to their props.**

<a name="state"></a>
## State and Lifecycle

Consider the ticking clock example from one of the previous sections. In Rendering Elements, we have only learned one way to update the UI. We call __ReactDOM.render()__ to change the rendered output:

```JS
function tick() {
  const element = (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {new Date().toLocaleTimeString()}.</h2>
    </div>
  );
  ReactDOM.render(
    element,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```

In this section, we will learn how to make the Clock component truly reusable and encapsulated. It will set up its own timer and update itself every second.

We can start by encapsulating how the clock looks:

```JS
function Clock(props) {
  return (
    <div>
      <h1>Hello, world!</h1>
      <h2>It is {props.date.toLocaleTimeString()}.</h2>
    </div>
  );
}

function tick() {
  ReactDOM.render(
    <Clock date={new Date()} />,
    document.getElementById('root')
  );
}

setInterval(tick, 1000);
```
However, it misses a crucial requirement: the fact that the __Clock__ sets up a timer and updates the UI every second should be an implementation detail of the __Clock__.

Ideally we want to write this once and have the __Clock__ update itself:

```JS
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

To implement this, we need to add “state” to the __Clock__ component.

State is similar to props, but it is private and fully controlled by the component.

We mentioned before that components defined as classes have some additional features. Local state is exactly that: a feature available only to classes.

### Converting a Function to a Class

You can convert a function component like Clock to a class in five steps:

* Create an ES6 class, with the same name, that extends React.Component.

* Add a single empty method to it called __render()__.

* Move the body of the function into the *render()* method.

* Replace *props* with this.props in the *render()* body.

* Delete the remaining empty function declaration.

```JS
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

*Clock* is now defined as a class rather than a function.

The *render* method will be called each time an update happens, but as long as we render <Clock /> into the same DOM node, only a single instance of the *Clock* class will be used. This lets us use additional features such as local state and lifecycle methods.

### Adding Local State to a Class

We will move the date from props to state in three steps:

1. Replace *this.props.date* with *this.state.date* in the *render()* method:

```JS
class Clock extends React.Component {
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

2. Add a class constructor that assigns the initial *this.state*:

```JS
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

Note how we pass *props* to the base 


constructor:

```JS
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
```

Class components should always call the base constructor with *props*.

3. Remove the *date* prop from the <Clock /> element:

```JS
ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

We will later add the timer code back to the component itself.

The result looks like this:

```JS
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

### Adding Lifecycle Methods to a Class

In applications with many components, it’s very important to free up resources taken by the components when they are destroyed.

We want to set up a timer whenever the *Clock* is rendered to the DOM for the first time. This is called “mounting” in React.

We also want to clear that timer whenever the DOM produced by the *Clock* is removed. This is called “unmounting” in React.

We can declare special methods on the component class to run some code when a component mounts and unmounts:

```JS
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {

  }

  componentWillUnmount() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

These methods are called __“lifecycle methods”__.

The *componentDidMount()* method runs after the component output has been rendered to the DOM. This is a good place to set up a timer:

```JS
  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }
```

Note how we save the timer ID right on *this*.

While *this.props* is set up by React itself and *this.state* has a special meaning, you are free to add additional fields to the class manually if you need to store something that doesn’t participate in the data flow (like a timer ID).

We will tear down the timer in the *componentWillUnmount()* lifecycle method:

```JS
  componentWillUnmount() {
    clearInterval(this.timerID);
  }
```

Finally, we will implement a method called *tick()* that the *Clock* component will run every second.

It will use *this.setState()* to schedule updates to the component local state:

```JS
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

Now the clock ticks every second.

Let’s quickly recap what’s going on and the order in which the methods are called:

1. When <Clock /> is passed to *ReactDOM.render()*, React calls the constructor of the *Clock* component. Since *Clock* needs to display the current time, it initializes *this.state* with an object including the current time. We will later update this state.

2. React then calls the *Clock* component’s *render()* method. This is how React learns what should be displayed on the screen. React then updates the DOM to match the *Clock*’s render output.

3. When the *Clock* output is inserted in the DOM, React calls the *componentDidMount()* lifecycle method. Inside it, the *Clock* component asks the browser to set up a timer to call the component’s *tick()* method once a second.

4. Every second the browser calls the tick() method. Inside it, the *Clock* component schedules a UI update by calling *setState()* with an object containing the current time. Thanks to the *setState()* call, React knows the state has changed, and calls the *render()* method again to learn what should be on the screen. This time, *this.state.date* in the *render()* method will be different, and so the render output will include the updated time. React updates the DOM accordingly.

5. If the *Clock* component is ever removed from the DOM, React calls the *componentWillUnmount()* lifecycle method so the timer is stopped.

<a name="HE"></a>

## Handling Events

Handling events with React elements is very similar to handling events on DOM elements. There are some syntactic differences:

* React events are named using camelCase, rather than lowercase.

* With JSX you pass a function as the event handler, rather than a string.

For example, the HTML:

```JS
<button onclick="activateLasers()">
  Activate Lasers
</button>
```

is slightly different in React:

```JS
<button onClick={activateLasers}>
  Activate Lasers
</button>
```

Another difference is that you cannot return *false* to prevent default behavior in React. You must call *preventDefault* explicitly. For example, with plain HTML, to prevent the default link behavior of opening a new page, you can write:

```JS
<a href="#" onclick="console.log('The link was clicked.'); return false">
  Click me
</a>
```
In React, this could instead be:

```JS
function ActionLink() {
  function handleClick(e) {
    e.preventDefault();
    console.log('The link was clicked.');
  }

  return (
    <a href="#" onClick={handleClick}>
      Click me
    </a>
  );
}
```

Here, *e* is a synthetic event. React defines these synthetic events according to the W3C spec, so you don’t need to worry about cross-browser compatibility. See the SyntheticEvent reference guide to learn more.

When using React you should generally not need to call *addEventListener* to add listeners to a DOM element after it is created. Instead, just provide a listener when the element is initially rendered.

When you define a component using an ES6 class, a common pattern is for an event handler to be a method on the class. For example, this *Toggle* component renders a button that lets the user toggle between “ON” and “OFF” states:

```JS
class Toggle extends React.Component {
  constructor(props) {
    super(props);
    this.state = {isToggleOn: true};

    // This binding is necessary to make `this` work in the callback
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(state => ({
      isToggleOn: !state.isToggleOn
    }));
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.isToggleOn ? 'ON' : 'OFF'}
      </button>
    );
  }
}

ReactDOM.render(
  <Toggle />,
  document.getElementById('root')
);
```

You have to be careful about the meaning of *this* in JSX callbacks. In JavaScript, class methods are not bound by default. If you forget to bind *this.handleClick* and pass it to *onClick*, *this* will be *undefined* when the function is actually called.

This is not React-specific behavior; it is a part of how functions work in JavaScript. Generally, if you refer to a method without *()* after it, such as *onClick={this.handleClick}*, you should bind that method.

If calling *bind* annoys you, there are two ways you can get around this. If you are using the experimental public class fields syntax, you can use class fields to correctly bind callbacks:

```JS
class LoggingButton extends React.Component {
  // This syntax ensures `this` is bound within handleClick.
  // Warning: this is *experimental* syntax.
  handleClick = () => {
    console.log('this is:', this);
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        Click me
      </button>
    );
  }
}
```

This syntax is enabled by default in Create React App.

If you aren’t using class fields syntax, you can use an arrow function in the callback:

```JS
class LoggingButton extends React.Component {
  handleClick() {
    console.log('this is:', this);
  }

  render() {
    // This syntax ensures `this` is bound within handleClick
    return (
      <button onClick={(e) => this.handleClick(e)}>
        Click me
      </button>
    );
  }
}
```

The problem with this syntax is that a different callback is created each time the *LoggingButton* renders. In most cases, this is fine. However, if this callback is passed as a prop to lower components, those components might do an extra re-rendering. We generally recommend binding in the constructor or using the class fields syntax, to avoid this sort of performance problem.

### Passing Arguments to Event Handlers

Inside a loop it is common to want to pass an extra parameter to an event handler. For example, if *id* is the row ID, either of the following would work:

```JS
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```

The above two lines are equivalent, and use arrow functions and Function.prototype.bind respectively.

In both cases, the *e* argument representing the React event will be passed as a second argument after the ID. With an arrow function, we have to pass it explicitly, but with *bind* any further arguments are automatically forwarded.

<a name="CR"></a>

## Conditional Rendering

In React, you can create distinct components that encapsulate behavior you need. Then, you can render only some of them, depending on the state of your application.

Conditional rendering in React works the same way conditions work in JavaScript. Use JavaScript operators like if or the conditional operator to create elements representing the current state, and let React update the UI to match them.

Consider these two components:

```JS
function UserGreeting(props) {
  return <h1>Welcome back!</h1>;
}

function GuestGreeting(props) {
  return <h1>Please sign up.</h1>;
}
```

We’ll create a *Greeting* component that displays either of these components depending on whether a user is logged in:

```JS
function Greeting(pro ps) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

ReactDOM.render(
  // Try changing to isLoggedIn={true}:
  <Greeting isLoggedIn={false} />,
  document.getElementById('root')
);
```

This example renders a different greeting depending on the value of *isLoggedIn* prop.

### Element Variables

You can use variables to store elements. This can help you conditionally render a part of the component while the rest of the output doesn’t change.

Consider these two new components representing Logout and Login buttons:

```JS
function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Login
    </button>
  );
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Logout
    </button>
  );
}
```

In the example below, we will create a stateful component called *LoginControl*.

It will render either *LoginButton />* or *LogoutButton />* depending on its current state. It will also render a *Greeting />* from the previous example:

```JS
class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;

    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

ReactDOM.render(
  <LoginControl />,
  document.getElementById('root')
);
```

While declaring a variable and using an if statement is a fine way to conditionally render a component, sometimes you might want to use a shorter syntax. There are a few ways to inline conditions in JSX, explained below.

### Inline If with Logical && Operator
You may embed any expressions in JSX by wrapping them in curly braces. This includes the JavaScript logical *&&* operator. It can be handy for conditionally including an element:

```JS
function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];
ReactDOM.render(
  <Mailbox unreadMessages={messages} />,
  document.getElementById('root')
);
```

It works because in JavaScript, *true && expression* always evaluates to *expression*, and *false && expression* always evaluates to *false*.

Therefore, if the condition is *true*, the element right after *&&* will appear in the output. If it is *false*, React will ignore and skip it.

### Inline If-Else with Conditional Operator
Another method for conditionally rendering elements inline is to use the JavaScript conditional operator condition ? true : false.

In the example below, we use it to conditionally render a small block of text.

```JS
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      The user is <b>{isLoggedIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}
```

It can also be used for larger expressions although it is less obvious what’s going on:

```JS
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  );
}
```
Just like in JavaScript, it is up to you to choose an appropriate style based on what you and your team consider more readable. Also remember that whenever conditions become too complex, it might be a good time to extract a component.

### Preventing Component from Rendering
In rare cases you might want a component to hide itself even though it was rendered by another component. To do this return *null* instead of its render output.

In the example below, the *WarningBanner />* is rendered depending on the value of the prop called *warn*. If the value of the prop is *false*, then the component does not render:

```JS
function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Warning!
    </div>
  );
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = {showWarning: true};
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick() {
    this.setState(state => ({
      showWarning: !state.showWarning
    }));
  }

  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Hide' : 'Show'}
        </button>
      </div>
    );
  }
}

ReactDOM.render(
  <Page />,
  document.getElementById('root')
);
```

Returning *null* from a component’s *render* method does not affect the firing of the component’s lifecycle methods. For instance *componentDidUpdate* will still be called.

<a name="l&k"></a>

## Lists and Keys

First, let’s review how you transform lists in JavaScript.

Given the code below, we use the map() function to take an array of *numbers* and double their values. We assign the new array returned by *map()* to the variable *doubled* and log it:

```JS
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map((number) => number * 2);
console.log(doubled);
```

This code logs *[2, 4, 6, 8, 10]* to the console.

In React, transforming arrays into lists of elements is nearly identical.

### Rendering Multiple Components

You can build collections of elements and include them in JSX using curly braces *{}*.

Below, we loop through the *numbers* array using the JavaScript map() function. We return a *'li'* element for each item. Finally, we assign the resulting array of elements to *listItems*:

```JS
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li>{number}</li>
);
```

We include the entire *listItems* array inside a *'ul'* element, and render it to the DOM:

```JS
ReactDOM.render(
  <ul>{listItems}</ul>,
  document.getElementById('root')
);
```

This code displays a bullet list of numbers between 1 and 5.

### Basic List Component

Usually you would render lists inside a component.

We can refactor the previous example into a component that accepts an array of *numbers* and outputs a list of elements.

```JS
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li>{number}</li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

When you run this code, you’ll be given a warning that a key should be provided for list items. A “key” is a special string attribute you need to include when creating lists of elements. We’ll discuss why it’s important in the next section.

Let’s assign a *key* to our list items inside *numbers.map()* and fix the missing key issue.

```JS
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <li key={number.toString()}>
      {number}
    </li>
  );
  return (
    <ul>{listItems}</ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

### Keys

Keys help React identify which items have changed, are added, or are removed. Keys should be given to the elements inside the array to give the elements a stable identity:

```JS
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map((number) =>
  <li key={number.toString()}>
    {number}
  </li>
);
```

The best way to pick a key is to use a string that uniquely identifies a list item among its siblings. Most often you would use IDs from your data as keys:

```JS
const todoItems = todos.map((todo) =>
  <li key={todo.id}>
    {todo.text}
  </li>
);
```

When you don’t have stable IDs for rendered items, you may use the item index as a key as a last resort:

```JS
const todoItems = todos.map((todo, index) =>
  // Only do this if items have no stable IDs
  <li key={index}>
    {todo.text}
  </li>
);
```

We don’t recommend using indexes for keys if the order of items may change. This can negatively impact performance and may cause issues with component state. Check out Robin Pokorny’s article for an in-depth explanation on the negative impacts of using an index as a key. If you choose not to assign an explicit key to list items then React will default to using indexes as keys.

Here is an in-depth explanation about why keys are necessary if you’re interested in learning more.

### Extracting Components with Keys

Keys only make sense in the context of the surrounding array.

For example, if you extract a *ListItem* component, you should keep the key on the *ListItem />* elements in the array rather than on the *'li'* element in the *ListItem* itself.

**Example: Incorrect Key Usage**

```JS
function ListItem(props) {
  const value = props.value;
  return (
    // Wrong! There is no need to specify the key here:
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Wrong! The key should have been specified here:
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

**Example: Correct Key Usage**

```JS
function ListItem(props) {
  // Correct! There is no need to specify the key here:
  return <li>{props.value}</li>;
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // Correct! Key should be specified inside the array.
    <ListItem key={number.toString()}
              value={number} />

  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);
```

A good rule of thumb is that elements inside the *map()* call need keys.

### Keys Must Only Be Unique Among Siblings

Keys used within arrays should be unique among their siblings. However they don’t need to be globally unique. We can use the same keys when we produce two different arrays:

```JS
function Blog(props) {
  const sidebar = (
    <ul>
      {props.posts.map((post) =>
        <li key={post.id}>
          {post.title}
        </li>
      )}
    </ul>
  );
  const content = props.posts.map((post) =>
    <div key={post.id}>
      <h3>{post.title}</h3>
      <p>{post.content}</p>
    </div>
  );
  return (
    <div>
      {sidebar}
      <hr />
      {content}
    </div>
  );
}

const posts = [
  {id: 1, title: 'Hello World', content: 'Welcome to learning React!'},
  {id: 2, title: 'Installation', content: 'You can install React from npm.'}
];
ReactDOM.render(
  <Blog posts={posts} />,
  document.getElementById('root')
);
```

Keys serve as a hint to React but they don’t get passed to your components. If you need the same value in your component, pass it explicitly as a prop with a different name:

```JS
const content = posts.map((post) =>
  <Post
    key={post.id}
    id={post.id}
    title={post.title} />
);
```

With the example above, the *Post* component can read *props.id*, but not *props.key*.

### Embedding map() in JSX

In the examples above we declared a separate *listItems* variable and included it in JSX:

```JS
function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    <ListItem key={number.toString()}
              value={number} />

  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```

JSX allows embedding any expression in curly braces so we could inline the *map()* result:

```JS
function NumberList(props) {
  const numbers = props.numbers;
  return (
    <ul>
      {numbers.map((number) =>
        <ListItem key={number.toString()}
                  value={number} />

      )}
    </ul>
  );
}
```

Sometimes this results in clearer code, but this style can also be abused. Like in JavaScript, it is up to you to decide whether it is worth extracting a variable for readability. Keep in mind that if the *map()* body is too nested, it might be a good time to extract a component.



<a name="forms"></a>

## Forms

```JS
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

This form has the default HTML form behavior of browsing to a new page when the user submits the form. If you want this behavior in React, it just works. But in most cases, it’s convenient to have a JavaScript function that handles the submission of the form and has access to the data that the user entered into the form. The standard way to achieve this is with a technique called “controlled components”.

### Controlled Components

n HTML, form elements such as **'input'**, **'textarea'**, and **'select'** typically maintain their own state and update it based on user input. In React, mutable state is typically kept in the state property of components, and only updated with *setState()*.
We can combine the two by making the React state be the “single source of truth”. Then the React component that renders a form also controls what happens in that form on subsequent user input. An input form element whose value is controlled by React in this way is called a “controlled component”.
For example, if we want to make the previous example log the name when it is submitted, we can write the form as a controlled component:

```JS
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```
Since the *value* attribute is set on our form element, the displayed value will always be *this.state.value*, making the React state the source of truth. Since *handleChange* runs on every keystroke to update the React state, the displayed value will update as the user types.

### The textarea Tag

In HTML, a *'textarea'* element defines its text by its children:

```JS
<textarea>
  Hello there, this is some text in a text area
</textarea>
```

In React, a *'textarea'* uses a *value* attribute instead. This way, a form using a *'textarea'* can be written very similarly to a form that uses a single-line input:

```JS
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Please write an essay about your favorite DOM element.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('An essay was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

Notice that this.state.value is initialized in the constructor, so that the text area starts off with some text in it.

### The select Tag

In HTML, *'select'* creates a drop-down list. For example, this HTML creates a drop-down list of flavors:

```JS
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option selected value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>
```
Note that the Coconut option is initially selected, because of the *selected* attribute. React, instead of using this *selected* attribute, uses a *value* attribute on the root *select* tag. This is more convenient in a controlled component because you only need to update it in one place. For example:

```JS
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Your favorite flavor is: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Pick your favorite flavor:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

Overall, this makes it so that *'input type="text"'*, *'textarea'*, and *'select'* all work very similarly - they all accept a *value* attribute that you can use to implement a controlled component.

### The file input Tag

In HTML, an *'input type="file"'* lets the user choose one or more files from their device storage to be uploaded to a server or manipulated by JavaScript via the File API.

```JS
<input type="file" />
```

Because its value is read-only, it is an **uncontrolled** component in React. It is discussed together with other uncontrolled components later in the documentation.


### Handling Multiple Inputs
When you need to handle multiple controlled *input* elements, you can add a *name* attribute to each element and let the handler function choose what to do based on the value of *event.target.name*.
For example:

```js
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

Note how we used the ES6 computed property name syntax to update the state key corresponding to the given input name:

```JS
this.setState({
  [name]: value
});
```

It is equivalent to this ES5 code:

```JS
var partialState = {};
partialState[name] = value;
this.setState(partialState);
```

Also, since *setState()* automatically merges a partial state into the current state, we only needed to call it with the changed parts.

### Controlled Input Null Value

Specifying the value prop on a controlled component prevents the user from changing the input unless you desire so. If you’ve specified a *value* but the input is still editable, you may have accidentally set *value* to *undefined* or *null*.
The following code demonstrates this. (The input is locked at first but becomes editable after a short delay.)

```JS
ReactDOM.render(<input value="hi" />, mountNode);

setTimeout(function() {
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);
```
### Alternatives to Controlled Components

It can sometimes be tedious to use controlled components, because you need to write an event handler for every way your data can change and pipe all of the input state through a React component. This can become particularly annoying when you are converting a preexisting codebase to React, or integrating a React application with a non-React library. In these situations, you might want to check out *uncontrolled components*, an alternative technique for implementing input forms.

### Fully-Fledged Solutions

If you’re looking for a complete solution including validation, keeping track of the visited fields, and handling form submission, *Formik* is one of the popular choices. However, it is built on the same principles of controlled components and managing state — so don’t neglect to learn them.



<a name="LSU"></a>

<a name="CvsI"></a>

<a name="TiR"></a>
