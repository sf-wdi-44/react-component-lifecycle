# Event Handlers and the Component Lifecycle in React

## Learning Objectives
* Learn how to utilize and understand React's lifecycle methods.
* Learn how to handle events in React.
* Show how to use functional components in React.
* Show how to use JSX attributes to style components conditionally.
* Look at the React documentation a bit more closely.

## Framing
So far, we have worked with data flow and the component architecture in React. Today, we are going to look into interactivity in React and how we make our components dynamic! Throughout this class, we are going to be using an API that contains vocabulary words from the Oxford Dictionary API. 

The goal of our app will be to cycle through flashcards and build a timer so that you can only spend 10 seconds thinking of a definition for the word.

Let's go ahead and clone the repository:
```bash
$ git clone git@git.generalassemb.ly:ga-wdi-exercises/flashcards.git
$ npm install
$ npm run start
```

Some of the code is already written for us: lets do a quick walk-through.


## The Component Lifecycle

React class components provide several lifecycle methods that you can use to control your application based on the state of the UI.

These methods happen automatically - but you can call them to modify them.

These methods are called at specific points in the rendering process. You can use these methods to perform actions based on what's happening on the DOM.

* `componentDidMount`, for example, is called immediately *after* a component is rendered to the DOM.
* `componentWillUnmount` is called immediately *before* a component is removed from the DOM.

Some common uses of lifecycle methods are making asynchronous requests, binding event listeners, and optimizing for performance.

## At a very high level

React components' lifecycle events fall into three broad categories:


* **Initializing / Mounting** e.g. What happens when the component is created? Was an initial state set? Methods:
  - `constructor()`
    - This is sometimes referred to as a combination of `getInitialState()` and `getDefaultProps()`
  - `componentWillMount()`
  - `componentDidMount()`
  - `render()`


* **Updating** e.g. Did an event happen that changed the state? Methods:
  - `componentWillReceiveProps()`
  - `shouldComponentUpdate()`
  - `componentWillUpdate()`
  - `componentDidUpdate()`
  - `render()`


* **Destruction / Unmounting** e.g. What needs to happen when we're done with the component? Method:
  - `componentWillUnmount()`

Let's go through them. Again, you don't need to write these methods - they happen automatically, just like constructors did before we explicitly wrote one. You only have to worry about these if you want to change something in them - but if you do, it's important to understand them!

[Check out the documentation on components!](https://facebook.github.io/react/docs/react-component.html)

<img width='500px' src="https://kunigami.files.wordpress.com/2016/01/react.png">

Open this image in a new window next to this one, and follow along as we go through the methods.

## `constructor()`

This is something we've already seen when we were learning `state`. Like any JavaScript class, the `constructor` method is called when a component is instantiated (when it's first rendered).  
In a class constructor, you must call `super` before you do anything else. So a React component constructor in its most basic form looks like this:

```javascript
constructor(props) {
  super(props)
}
```

You don't need to define a constructor if that's all it does, though. This happens automatically when your component is invoked. A common use of the constructor is to initialize state using the props passed to the component - as we have been doing!

```javascript
constructor(props) {
  super(props)

  this.state = {
     flashcards: [],
     currentIndex: 0,
     show: false
  }
}
```

> This constructor sets the initial `flashcards` state of the component to an empty array. Then, using `setState`, you can add flashcards, delete them, or whatever else your component allows. We also set the `currentIndex` to 0. This will be the index of the card we are currently on. `show` will represent whether the definition of the card is visible or not.

#### Binding
In React, when we use event methods, we usually have to bind the `this` keyword to the method so that it works properly. Class methods are not bound by default, so `this` is undefined unless we specify otherwise. We could bind `this` to the event each time we call it; however, it is usually more efficient to do so by default in the constructor... 

```js
constructor (props) {
  super(props)
  this.handleClick = this.handleClick.bind(this)
}
```

Then when we add the event handler, we can just do so just like this:

```js
<button onClick={this.handleClick}/>
```
This is not a React specific behavior, it applies to JavaScript classes in general.


## `componentWillMount()`

This method is called immediately before a component is rendered to the DOM. You generally won't need to use this method. Advanced use-cases like server-rendering are usually the only ones in which `componentWillMount` is needed.

## `componentDidMount()` and `componentWillUnmount()`

The `componentDidMount` method is called once, immediately after your component is rendered to the DOM. If you want to make an AJAX request when your component first renders, this is where to do it (_not_ in the constructor, or in `componentWillMount`). `componentWillMount` shouldn't be used for server requests because it may be invoked multiple times before render in future versions of React. [Side effects](https://en.wikipedia.org/wiki/Side_effect_(computer_science) should be avoided in the `constructor`, and so server requests shouldn't be made there. The accepted answer on [this Stack Overflow](http://stackoverflow.com/questions/41612200/in-react-js-should-i-make-my-initial-network-request-in-componentwillmount-or-co) from a member of the React team at Facebook gives more detail. 

Another common use for `componentDidMount` is to bind event listeners to your component. You can then remove the event listeners in `componentWillUnmount`. For example, you could bind and unbind an event listener for a drag-drop component.

### We Do: Adding the Flashcard Container
```javascript
class FlashcardContainer extends React.Component {
    constructor(props) {
      super(props)
      this.state = {
        flashcards: [],
        currentIndex: 0
      }
    }

    next () {
      if (this.state.show) 
        this.setState(currState => ({currentIndex: currState.currentIndex + 1}))
    }

    componentDidMount () {
      window.addEventListener('keyup', (event) => {
        // move to next card on right arrow
        if (event.keyCode === 39) 
          this.next()
      })

      axios
        .get(`${CLIENT_URL}/api/words`)
        .then(response => this.setState({flashcards: response.data}))
        .catch(err => console.log(err))
    }
    
    render() {
      let flashcard = this.state.flashcards[this.state.currentIndex]
      return (
        <div>
            <main>
              <div className="container">
              </div>
            </main>
        </div>
      )
    }
}

```

In this example, we have added a constructor that has a few initial state properties. We also add an event listener that switches to the next card on right arrow press. Finally, we add an API pull to fetch the dynamic data we will use for the app.

## `render()`

This is the one method that every React class component **must** have. In render, you return JSX using the component's props and state. You should never set state in render - render should only react to changes in state or props, not create those changes. 

## `componentWillReceiveProps(newProps)`

This method is called any time your component receives new props. It is _not_ called with the initial props when your component initially mounts. If you need to change the state of your component based on changes in the props, this is where you do it. In a simple app, you generally won't need `componentWillReceiveProps`.

## `shouldComponentUpdate`, `componentWillUpdate`, `componentDidUpdate`

These methods are called when a component's props or state change, and are generally used for performance optimizations. React is quite fast by itself, and you usually don't need to concern yourself with these methods outside of a large app with many dynamic components.


### You Do: Adding a Timer to the Flashcard
Add a timer to the Flashcard Detail component. Initialize the timer to have 10 seconds on it. Every second the same flashcard is still on the board, remove a second from it (hint: use `window.setTimeout()`). When the `FlashcardDetail` component receives a new card, restart the timer (hint: use `componentWillReceiveProps`).


## Styling Components

When it comes to adding styles to React, there is a bit of debate over what's the best practice. Facebook's official docs and recommendations are to write stylesheets that treat your CSS rule declarations as properties on one big Javascript object that can be passed into components via inline styles.

From the [Docs](https://facebook.github.io/react/tips/inline-styles.html)...

>  "In React, inline styles are not specified as a string. Instead they are specified with an object whose key is the camelCased version of the style name, and whose value is the style's value, usually a string"

However, this kind of rethinking the wheel feels like a step backwards for a lot of designers and developers who cringe at the notion of inline styles. For them, they choose to build React apps through a more traditional flow of adding ids and classes and then targeting elements via external stylesheets.

Also, via Webpack and other custom loaders, it is possible to use many third-party libraries or processors such as SASS, LESS, and Post-CSS.

Interesting to note, this problem has not been universally solved, and thus the debate will most likely continue to rage on until [somebody](https://medium.com/@jviereck/modularise-css-the-react-way-1e817b317b04#.61qgjgdu3) figures it out. Therefore, its often left to a team decision when choosing the best option for the application.

> Interested in learning more? Check out some excellent [blog posts](http://jamesknelson.com/why-you-shouldnt-style-with-javascript/) on the [subject](http://stackoverflow.com/questions/26882177/react-js-inline-style-best-practices) from the [front-end community](https://css-tricks.com/the-debate-around-do-we-even-need-css-anymore/)

### [Example of Object Literal Styles with React](https://github.com/ga-wdi-exercises/react-omdb/commit/830697fc68dcdccafcae9f73e711103de8d93fc9)

> **Reminder**: `class` is a protected keyword in React, in order to add a class attribute to an element use the keyword `className`

## We Do: Adding the Definition Component

Now that we have our flashcard word displayed, lets add the definition as well.

For this, lets use a functional component -- or a component created by a function instead of a class -- and then change its style dynamically based on its index.

```js
const COLORS = ['#673ab7', '#2196f3', '#26a69a', '#e91e63']

let Definition = props => {
    let def = props.def
    let idx = props.idx

    let styles = {
        color: 'white',
        padding: '10px',
        backgroundColor: COLORS[idx]
    }

    return (
        <div 
            className="card text-center"
            style={styles}>
            <h5>Definition {idx + 1}</h5>
            <p>{ def.definitions[0] }</p>
        </div>
    )
}
```

## Event Handlers
Throughout the last few classes, you've seen a couple event handlers. These look similar to including them inline in HTML. 

In the React Intro class, we went over how we are not actually interacting directly with the DOM when we write React code, rather, we are interacting with the virtualDOM. Because of this, when we call an event listener, we use `SyntheticEvent`'s instead of the usual event objects we are used to dealing with in JQuery events. We still get all of the same properties attached to them, and some additional ones. Because of this, the traditional documentation on event handlers is sometimes not accurate. Instead, use the React event handling documentation found [here](https://facebook.github.io/react/docs/events.html). Spend a few minutes looking at the different events on listed here.

## You Do: Show or Hide the Definition
Add a button that, when clicked, toggles whether or not the definition card is displayed on the page. 

## React Documentation

* What is an uncontrolled component in React? Why would we use it instead of a controlled one?

* Describe 1-way and 2-way data binding. Which model does React use? Explain and compare to other popular front-end frameworks.

* Describe how to best gather information from a form in React. Be prepared to show code!

* Compare and contrast stateful, stateless, and functional components in React. List the pros and cons of each. When would we use one over the other?


## Conclusion
