# Code Snippets

## App Setup

```html
<!-- app/index.html -->

<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>React Blog</title>
  </head>
  <body>
    <div id="app"></div>
  </body>
</html>
```

## Webpack

```js
// webpack.config.js

var HtmlWebpackPlugin = require('html-webpack-plugin');
var HtmlWebpackPluginConfig = new HtmlWebpackPlugin({
  template: __dirname + "/app/index.html",
  filename: "index.html",
  inject: 'body'
});

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
  },
  plugins: [
     HtmlWebpackPluginConfig
  ]
}
```

```js
// .babelrc

{
  "presets": [
    "react"
  ]
}
```

<!-- Part of package.json  -->
```json
"scripts": {
  "production": "webpack -p",
  "start": "webpack-dev-server"
},
```

## React
<!-- Hello World  -->
```js
// app/index.js

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

ReactDOM.render(
  <Hello />,
  document.getElementById( "app" )
);
```

<!-- Hello World w/ Props  -->
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

<!-- State  -->
```js
// app/index.js

var Hello = React.createClass({

  // Here we define the initial values of our state. `getInitialState` returns an object with the initial state values.
  getInitialState: function(){
    return {
      // We have one state value: a counter.
      counter: 0
    }
  },

  render: function(){
    return (
      <div>
        <p>Hello { this.props.name }.</p>
        <p>You are { this.props.age } years old.</p>

        // We can reference state values just like props using `this.state.val`.
        <p>I have said hello {this.state.counter} times.</p>
          <button onClick={ this.raiseCounter }>Again.</button>
      </div>
    );
  }
});

ReactDOM.render(
  <Hello name="Tony" age="21" />,
  document.getElementById( "app" )
)
```
