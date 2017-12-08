# Event Handlers and the Component Lifecycle in React

## Learning Objectives
* Understand how to use React's lifecycle methods
* Retrieve data from an API inside of a component
* Handle events in React
* Look at the React documentation to learn more

## Framing
> 10 min / 0:10

Up to this point, we've used react to build out components that make up simple applications. We added state and props to these components and controlled data flow through them. With this alone, we do have the capacity to build out an application but they'll be limited.

How do we get data from an API? Well we could drop in an AJAX call to fetch some data, but our component would likely render before the AJAX request returned with our data. Our component would see that our data is `undefined` and either render a blank/empty component or throw an error.

How would we animate a component? (i.e. a sidebar that usually lives off the page, except for when a hamburger menu is pressed.) We could write some code to animate the position of the sidebar, but how could we guarantee it was running after our Sidebar component's render method had been called?

This lesson will walk us through the Component Lifecycle: hooks that are fired at different stages of a components "life" for solving the problems described above, as well as many others.

Throughout the course of this lesson, we'll build out a simple flashcard app with vocabulary keywords pulled form the Oxford Dictionary API. Our flashcard app will cycle through a set of flashcards, giving us 10 seconds to think of the definition before moving on to the next card.

But first, what is the Component Lifecycle?

## The Component Lifecycle
> 10 min / 0:20

Class-based components provide several lifecycle methods that you can use to control your application based on the state of the UI.

If defined, these methods will be invoked automatically so you can define the ones you need.

These methods are called at specific points in the rendering process. You can use these methods to perform actions based on what's happening on the DOM.

* `componentDidMount`, for example, is called immediately *after* a component is rendered to the DOM.
* `componentWillUnmount` is called immediately *before* a component is removed from the DOM.

Some common uses of lifecycle methods are making asynchronous requests, binding event listeners, and optimizing for performance.

### At a very high level

There are two types of component lifecycle methods, categorized by when the occur in the lifecycle: **mounting** lifecycle methods and **updating** lifecycle methods

* **Mounting** lifecycle methods. e.g. What happens when the component is created? Was an initial state set? Methods:
  - `constructor()`
  - `componentWillMount()`
  - `render()`
  - `componentDidMount()`
  - `componentWillUnmount()`

* **Updating** lifecycle methods. e.g. Has state changed? Methods:
  - `componentWillReceiveProps()`
  - `shouldComponentUpdate()`
  - `componentWillUpdate()`
  - `render()`
  - `componentDidUpdate()`

[Check out the documentation on components!](https://facebook.github.io/react/docs/react-component.html)

### We do: Exploring the Lifecycle methods
> 20 min / 0:40

Let's clone down [this repository](https://git.generalassemb.ly/ga-wdi-exercises/react-component-lifecycle) with a short exercise for exploring the lifecycle methods.

This exercise is a simple, 2 "page" website where each page is a component. We'll be adding the component lifecycle methods to each page-component. As we do consider the following questions:

* When is the method get invoked in relation to when the content is rendered?
* How many times is the method invoked?
* What causes the method to be (re)invoked?

## An Aside: Axios
> 15 min / 0:55

For our first example of working with the component lifecycle methods, we'll me retrieving data from an API using AJAX. AJAX calls are asynchronous, so we have to be mindful of how long our request will take and when our components will render.

We're going to use a module named `axios` to make our calls. Axios is a node module commonly used with React to send HTTP requests to an API. It functions much like jQuery's Ajax method. Some benefits to using Axios:

  - It is a promise-based library with an interface for simpler and cleaner syntax
  - It is lightweight and focused solely on handling HTTP requests (as opposed to jQuery which brings in an extensive set of new functions and methods)
  - It is flexible and has a number of very useful methods for doing more complex requests from one or multiple API endpoints

Read more at the [Axios Documentation](https://github.com/mzabriskie/axios)

> Note: Axios is just one of many Javascript libraries that we could use for handling requests. One of the big selling points of Node is the ability to mix and match technologies according to preference. Other commonly-used tools for handling requests are Fetch and jQuery.

To load in the Axios module:

```js
// If you are using Babel to compile your code
import axios from 'axios'

// In standard vanilla Javascript
let axios = require('axios')
```

To use Axios to query an API at a given url endpoint:
```js
  axios.get('url')
    .then((response) => {
      console.log(response)
    })
    .catch((error) => {
      console.log(error)
    })
```

You can also append values to the parameters by passing in a second input to `.get()`:

```js
  axios.get('url', {
    params: {
      key1: value1,
      key2: value2
    }
  })
  .then((response) => {
    console.log(response)
  })
  .catch((error) => {
    console.log(error)
  })
```

Which would result in a GET request to: `url?key1=value1&key2=value2`.

We will be using Axios to query the PokéAPI in [this exercise](https://git.generalassemb.ly/ga-wdi-exercises/react-components-axios).

## Break
> 10 min / 1:05

## Flashcards
> 85 min / 2:30

As we dive deeper in to each of the component lifecycle methods and what they're used for, we'll work through the following exercise to create a simple flashcards app.

The starter code for this exercise can be found [here](https://git.generalassemb.ly/ga-wdi-exercises/flashcards).

Let's go ahead and clone the repository:

```bash
$ git clone git@git.generalassemb.ly:ga-wdi-exercises/flashcards.git
$ npm install
$ npm start
```

The app we're going to build will pull word definitions from the Oxford Dictionary API and create a flashcard for each word. The app will then cycle through each word, giving the user 10 seconds to think of the definition before moving on to the next card.

### We Do: Adding the Flashcard Container

<details>
    <summary>Solution</summary>

```js
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

</details>

In this example, we have added a constructor that has a few initial state properties. We also add an event listener that switches to the next card on right arrow press. Finally, we add an API pull to fetch the dynamic data we will use for the app.

### You Do: Adding a Timer to the Flashcard

Add a timer to the Flashcard Detail component. Initialize the timer to have 10 seconds on it. Every second the same flashcard is still on the board, remove a second from it (hint: use `window.setTimeout()`). When the `FlashcardDetail` component receives a new card, restart the timer (hint: use `componentWillReceiveProps`).

#### We Do: Adding the Definition Component

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
        <div className="card text-center"
            style={styles}>
            <h5>Definition {idx + 1}</h5>
            <p>{ def.definitions[0] }</p>
        </div>
    )
}
```

## You Do: Show or Hide the Definition

Add a button that, when clicked, toggles whether or not the definition card is displayed on the page.

## React Documentation

* What is an uncontrolled component in React? Why would we use it instead of a controlled one?
* Describe 1-way and 2-way data binding. Which model does React use? Explain and compare to other popular front-end frameworks.
* Describe how to best gather information from a form in React. Be prepared to show code!
* Compare and contrast stateful, stateless, and functional components in React. List the pros and cons of each. When would we use one over the other?

## Bonus

### Binding

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

## Event Handlers

Throughout the last few classes, you've seen a couple event handlers. These look similar to including them inline in HTML.

In the React Intro class, we went over how we are not actually interacting directly with the DOM when we write React code, rather, we are interacting with the virtualDOM. Because of this, when we call an event listener, we use `SyntheticEvent`'s instead of the usual event objects we are used to dealing with in JQuery events. We still get all of the same properties attached to them, and some additional ones. Because of this, the traditional documentation on event handlers is sometimes not accurate. Instead, use the React event handling documentation found [here](https://facebook.github.io/react/docs/events.html). Spend a few minutes looking at the different events on listed here.
