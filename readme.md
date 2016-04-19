## Learning Objectives

* Explain what ReactJS is.
* Explain the component model of web development.
* Create and render React components in the browser.
* Pass in data to a React component via `props`.
* Nest React components.
* Modify the `state` of a React component through events.

## What is ReactJS?

React is a library used to craft modern day UI and create views for a the front-end in web, client and native applications.

> **Selling Point:** By modeling small compatible components that focus on just rendering a view, we as developers can move business logic out of the DOM, and therefore improve our app's performance, maintainability, modularity, and readability.

### Some History
The first thing most people hear about React is "Facebook uses it."
* First used by Facebook in 2011. Then Instagram in 2012.
* Went open source in May 2013.

React was born out of Facebook's frustration with the traditional MVC model and how...
  * Re-rendering something meant re-rendering everything (or just a lot).
  * That had negative implications on processing power and ultimately user experience, which at times became glitchy and laggy.

> If you want to get a taste of what React's all about, [here's an introduction from React.js Conf 2015.](https://www.youtube.com/watch?v=KVZ-P-ZI6W4&feature=youtu.be&t=510)  

### React in the MVC

**React only concerns our "Views".**

<details>
  <summary>What is a "view" mean in front-end Javascript?</summary>

  > Visual representations of our models - not the entire page.

</details>

React can coexist with Models and Controllers. The user can set those up however they see fit.
* This means that React can also coexist with other Javascript frameworks. Let them handle the models and controllers, and have React sort out the views.

# Initial Setup

> **NOTE:** We're about to go through a lot of set-up that may not make 100% sense after going through it once. Don't worry if you don't understand the "Why?" quite yet. You will get more practice with this during the second React lesson.

Our example app for this lesson is going to be a very simple blog. Let's start by creating a directory for the app and initializing `npm`...

```bash
$ mkdir wdi-weather && cd wdi-weather && npm init -y
```

> `npm init -y` accepts all the defaults that, without the flag, you would have to hit the return key to accept.

Now, we're going to install **A LOT** of stuff. Bear with us for a second and run the following command in the terminal...

```bash
$ npm install --save react react-dom && npm install --save-dev html-webpack-plugin webpack webpack-dev-server babel-{core,loader} babel-preset-react
```

You'll know installation went A-OK if your `package.json` file looks like this...

```json
"dependencies": {
  "react": "^15.0.1",
  "react-dom": "^15.0.1"
},
"devDependencies": {
  "babel-core": "^6.7.6",
  "babel-loader": "^6.2.4",
  "babel-preset-react": "^6.5.0",
  "html-webpack-plugin": "^2.15.0",
  "webpack": "^1.13.0",
  "webpack-dev-server": "^1.14.1"
}
```

Some of the terms in here should look familiar. We'll go over everything in more detail as we encounter them in the code throughout this lesson.
* **React:** The library itself.
* **React-DOM:** An additional module that allows us to update the DOM using React components.
* **Webpack:** You learned about this "code bundler" in your [Build Tools lesson](https://github.com/ga-wdi-lessons/build-tools).
* **Babel:** This one's actually new. We'll be using Babel to compile an HTML-like syntax called JSX into Javascript.

> **Q:** What's the difference between `devDependencies` and `dependencies`?  

Let's continue building out the app skeleton...

```bash
$ mkdir app
$ touch app/index.html app/index.js
```

Inside our `index.html` file, let's add some boilerplate html...
​
```html
<!-- app/index.html -->

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>WDI Weather</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.6/css/bootstrap.min.css">
  </head>
  <body>
    <div id="app"></div>
  </body>
</html>
```

> Here we're (1) linking to the Bootstrap CDN and (2) creating a `div` with an id of `app`, which is the DOM element to which we'll be attaching our React application later on.

### Webpack
​
Now we need to setup up Webpack for our application so that we can bundle and serve our static assets.
​
In your terminal run...

```bash
$ touch webpack.config.js
```
​
In that file, go ahead a define an initial object to export...
​
```js
// webpack.config.js

module.exports = {
​
}
```

There are things to we need to account for when defining our Webpack configuration...
  1. **`entry`**: The location of the app's root javascript file (specifying the app's point of entry).
  2. **`output`**: Where we want the bundled up output to go.
  3. **`loaders`**: The specific transformations to apply to our code.
​
```js
// webpack.config.js​

module.exports = {
  // What file to feed into webpack
  entry: [
    "./app/index.js"
  ],
  // Where we want our outputted files to go
  output: {
    path: __dirname + '/dist',
    filename: "index_bundle.js"
  },
  // What transformations we want to apply to our code
  module: {
    loaders: [
      {
        test: /\.js$/, // target any file ending in js
        exclude: /node_modules/, // except for node_modules
        loader: 'babel-loader' // apply the babel loader
      }
    ]
  }
}
```

​Next we need to setup Babel to specify which transformations should be run by the loader. In our app's root directory, we need to create a babel configuration file...
​
```bash
$ touch .babelrc
```
​
Inside `.babelrc`...
​
```js
// .babelrc

{
  "presets": [
    "react"
  ]
}
```

​Everything inside the `presets` array will be the specific transformations applied by Babel. For now, however, we are only adding the `react` preset, which will convert our JSX code into regular Javascript.  

Another thing we have to do is configure webpack to produce an `html` file that loads our bundled code. At the top of `webpack.config.js`, let's utilize `html-webpack-plugin`...
​
```js
// webpack.config.js

var HtmlWebpackPlugin = require('html-webpack-plugin');
var HtmlWebPackPluginConfig = new HtmlWebpackPlugin({
  template: __dirname + "/app/index.html",
  filename: "index.html",
  inject: 'body'
});
```

Now we can go ahead and add that as a plugin in our webpack config...
​
```js
// webpack.config.js

  // Add this code after `module: {...}`
  plugins: [
     HtmlWebPackPluginConfig
  ]
}
```

​We're using `html-webpack-plugin` to look into our `app/` directory and copy the contents of `index.html` there so that we can be sure to create another `index.html` in the `dist/` directory.

This new `index.html` be the file that is loading in our bundled code and the one we will be serving our app from. Webpack will automatically sync the files after every change.

> `dist/` is the directory that will store all of our "bundled" code.​

In order to actually run webpack, let's define a script in `package.json` to test our app's configuration...
​
```js
// package.json

"scripts": {
  "production": "webpack -p"
}
```

> There should already be a `scripts` object in `package.json`. Add this new key-value pair to that.

​Now from the command line, we can run a script that will launch Webpack and start the bundling...
​
```bash
$ npm run production
```

​If this command executes without any errors, it will create a new directory `dist`. In that directory, we will find...
* **`index_bundle.js`**: our app's minified Javascript code.
* **`index.html`**: a new index file that will link to `index_bundle.js`

### Stop / Catch Up (5 minutes)

# Components

## You Do: Identifying Components

> Insert exercise prompt.

## Hello World: A Very Basic Component

The basic unit you'll be working with in ReactJS is a **component**.
* It sounds like a simple word, but using "components" is a pretty different way of approaching web development.
* Components can be thought of as functional elements that takes in data via `props` and a `state` -- more on those later -- and as a result produce a dynamic HTML template.

Throughout class we have separated HTML, CSS and Javascript.
* With components, the lines between those three become a bit blurry.
* Instead, we organize our web apps according to small, reusable components that define their own content, presentation and behavior.

What does a component look like? Let's start with a simple "Hello World" example...

```js
// Load React and React-DOM modules.
var React = require("react");
var ReactDOM = require("react-dom");

// React component definition.
var Hello = React.createClass({
  render: function(){
    return (
      <p>Hello world.</p>
    );
  }
})
```

#### What's that HTML doing in my Javascript?

Often times we write out React components in **JSX**.
* JSX is an alternate Javascript syntax that allows us to write code that strongly resembles HTML. It is eventually compiled to lightweight JavaScript objects.
* React then uses these objects to build out a "Virtual DOM" -- more on that in just a bit.

> React can be written without JSX. If you want to learn more, [check out this blog post](http://jamesknelson.com/learn-raw-react-no-jsx-flux-es6-webpack/).  

Let's break down the things we see here...

##### `var Hello`
This is the component we're creating. In this example, we are creating a "Hello" component.

##### `React.createClass`
This is the React library method we use to create our component definition.
  * Takes an object as an argument.

##### `render`
Every component has, at minimum, a render method. It generates a **Virtual DOM** node that will be added to the actual DOM.
* Looks just like a regular ol' DOM node, but it's not yet attached to the DOM.

#### Virtual DOM? How is that different from the usual DOM?

The Virtual DOM is a Javascript representation of the actual DOM.
* Because of that, React can keep track of changes in the actual DOM by comparing different instances of the Virtual DOM.
* React then isolates the changes between old and new instances of the Virtual DOM and then only updates the actual DOM with the necessary changes.
* By only making the "necessary changes," as opposed to re-rending an entire view altogether, we save up on processing power.

> If you're interested in learning more about the Virtual DOM, [check this video out](https://www.youtube.com/watch?v=-DX3vJiqxm4).

So we've created the template for our component. But how do we actually render it?

```js
// app/index.js

var React = require("react");
var ReactDOM = require("react-dom");

var Hello = React.createClass({
  render: function(){
    return (
      <p>Hello world.</p>
    );
  }
})

ReactDOM.render(
  <Hello />,
  document.getElementById( "app" )
);
```

> Many tutorials will use React.renderComponent, which has been phased out. Change outlined here: http://bit.ly/1E81Whs

`React.render` takes the Virtual DOM node created by `.createClass` and adds it to the actual DOM. It takes two arguments...
  1. The component.
  2. The DOM element we want to append it to.

What language is `<Hello />` written in? **JSX.**
* Similar to XML.
* When we say `<Hello />`, in plain Javascript we are actually saying `React.DOM.div( null, "Hello world.")`
  * Basically, a string of React methods that create a virtual DOM node.

> **NOTE:** Whenever you use a self-closing tag in JSX, you **MUST** end it with a `/` like `<Hello />` in the above example.

## Hello World: A Little Dynamic

Our `Hello` component isn't too helpful. Let's make it more interesting.
* Rather than simply display "Hello world", let's display a greeting to the user.
* So the question is, how do we feed a name to our `Hello` component without hardcoding it into our render method?

```js
// app/index.js

var React = require("react");
var ReactDOM = require("react-dom");

var Hello = React.createClass({
  render: function(){
    return (
      <p>Hello { this.props.name }</p>
    );
  }
});

ReactDOM.render(
  <Hello name="Tony" />,
  document.getElementById( "app" )
)
```

In the above example, we replaced "world" with `{this.props.name}`.

#### What are `.props`?
Properties! Every component has a `.props` property.
* Properties are immutable and cannot be changed while your program is running.
* We define properties in development and pass them in as attributes to the JSX element in our `.render` method.

We can create multiple properties for a component...

```js
// app/index.js

var React = require("react");
var ReactDOM = require("react-dom");

var Hello = React.createClass({
  render: function(){
    return (
      <div>
        <p>Hello { this.props.name }.</p>
        <p>You are { this.props.age } years old.</p>
      </div>
    );
  }
});

ReactDOM.render(
  <Hello name="Tony" age="21" />,
  document.getElementById( "app" )
)
```

> **NOTE:** The return statement in `render` can only return one DOM element. You can, however, place multiple elements within a parent DOM element, like we do in the previous example with `<div>`.

## Exercise: A Blog Post

Let's have some practice creating a React component for scratch. How about a blog post?
* Define a Post constructor in `index.js` that has the below properties. Then use it to generate an instance of a Post with the content of your choosing.
  1. `title`
  2. `author`
  3. `body`
  4. `comments` (array of strings)
* Render these properties using a Post component.
* The HTML (or more accurately, JSX) composition of your Post is up to you.

#### [Solution](https://gist.github.com/amaseda/efeb9d6ee266f8fa3178)

## Nested Components

**Q:** What problems did you encounter when trying to add multiple comments to your Post?
* It would be a pain to have to explicitly define every comment inside of `<PostView />`, especially if each comment itself had multiple properties.

We can nest Comment components within a PostView component.
* We create these comments the same way we did with posts: `.createClass` and `.render`
* Then we can reference a comment using `<Comment />` inside of PostView's render method.

## Exercise: Add Nested Comments To Blog

1. Create a `CommentView` component in the same way we did for `PostView`. Its `render` method should render a `commentBody` property.

2. Amend your `PostView`'s render method so that its return value generates three `<CommentView />` elements.

> Make sure to pass in the comment body as an argument to each component.

#### [Solution](https://gist.github.com/amaseda/0eba4f73b1ec8073ea0a)

## State

So we know about React properties. The thing about properties is, you can't change them!
* What do we do about values that need to be changed after a component is rendered?
* That's where **state** comes in.

Values stored in a component's state are mutable attributes.
* Like properties, we can access state values using `this.state.val`.
* Setting up and modifying state is not as straightforward as properties. It requires multiple methods that we must define ourselves.

Lets implement state in our earlier `Hello` example by incorporating a counter into our greeting.

```js
// app/index.js

var Hello = React.createClass({

  // Here we define the initial values of our state. `getInitialState` returns an object with the initial state values.
  getInitialState: function(){
    return {
      // We have one state value: a counter. It's initial value happens to be the same as one of our `props`: `count`.
      counter: this.props.count
    }
  },

  render: function(){
    return (
      <div>
        <p>Hello { this.props.name }.</p>
        <p>You are { this.props.age } years old.</p>

        // We can reference state values just like props using `this.state.val`.
        <p>It is {this.state.date}</p>
      </div>
    );
  }
});

// This is where we define our `count` (not `counter` property).
React.render(
  <Hello name="Tony" age="21" count="1"/>,
  document.getElementById( "container" )
)
```

Ok, we set an initial state. But how do we go about changing it?
* We need to set up some sort of trigger event to change our counter.
* **Q:** Let's do that via a button click event -- where should we initialize it?

```js
var Hello = React.createClass({

  getInitialState: function(){
    return {
      counter: this.props.count
    }
  },

  raiseCounter: function(){
    this.setState({
      counter: parseInt(this.state.counter) + 1
    })
  },

  render: function(){
    return (
      <div>
        <p>Hello { this.props.name }.</p>
        <p>You are { this.props.age } years old.</p>
        <p>I have said hello {this.state.counter} times.</p>
        <button onClick={ this.raiseCounter }>Again.</button>
      </div>
    );
  }
});

React.render(
  <Hello name="Tony" age="21" count="1"/>,
  document.getElementById( "container" )
)
```

Whenever we run `.setState`, our component "diff's" the current DOM, and compares the Virtual DOM node with the updated state to the current DOM.
* Only replaces the current DOM with parts that have changed.
* This is super important! Using React, we only change parts of the DOM that need to be changed. This has strong implications on performance.

## Exercise: Implement State

Let's create a state for our earlier blog example. We want to be able to edit the body of our post.

1. Initialize a state for our PostView. It should save a value for post body.
2. Create a method inside your PostView component definition that sets state to a new value.
  * Keep it simple: have your method generate a prompt through which you can create a new PostView body.
3. Create a button that triggers the above function.

### Solution

[https://gist.github.com/amaseda/0118e488caad10030e6d](https://gist.github.com/amaseda/0118e488caad10030e6d)

## What's Next?

* [Router](https://github.com/reactjs/react-router)
* [API/Axios](https://www.npmjs.com/package/axios)
* [Events](https://facebook.github.io/react/tips/dom-event-listeners.html)
* [Forms](https://facebook.github.io/react/docs/forms.html)

## Questions / Closing

Having learned the basics of React, what are some benefits to using it vs. a different framework or plain ol' Javascript?
* Clear HTML structure of rendered components.
* Nicely compartmentalized properties via props and state.
* Easily pass in values from model to view.
* Swift rendering of update state values.

## Additional Reading

* [Tyler McGinnis' React.js Program](http://www.reactjsprogram.com/)
* [Raw React: No JSX, Webpack, ES6, etc.](http://jamesknelson.com/learn-raw-react-no-jsx-flux-es6-webpack/)
* [React DC (Meetup)](http://www.meetup.com/React-DC/)
* [React Tic-Tac-Toe (by Jesse Shawl)](https://github.com/jshawl/react-tic-tac-toe)
