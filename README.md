# WebpackGettingStarted

Following https://webpack.js.org/guides/getting-started/

## Getting Started

### Basic Setup

```
$ npm init
$ npm install --save-dev webpack
$ touch .gitignore
```

`.gitignore` content:
```
.DS_Store
node_modules/
.idea
*.iml
```

Initial state without webpack:
https://github.com/cintiamh/WebpackGettingStarted/tree/01-initial-state

`src/index.js`:
```javascript
function component() {
  var element = document.createElement('div');
  element.innerHTML = _.join(['Hello', 'webpack'], '');
  return element;
}
document.body.appendChild(component());
```

`index.html`
```html
<html>
  <head>
    <title>Getting Started</title>
    <script src="https://unpkg.com/lodash@4.16.6"></script>
  </head>
  <body>
    <script src="./src/index.js"></script>
  </body>
</html>
```

### Creating a Bundle

https://github.com/cintiamh/WebpackGettingStarted/tree/02-creating-bundle

Project contents:
```
webpack-demo
|- package.json
|- dist/
  |- index.html
|- src/
  |- index.js
```

Install lodash:
```
$ npm i lodash --save
```

`src/index.js`:
```javascript
import _ from 'lodash';

function component() {
  var element = document.createElement('div');
  element.innerHTML = _.join(['Hello', 'webpack'], '');
  return element;
}
document.body.appendChild(component());
```

`index.html`
```html
<html>
  <head>
    <title>Getting Started</title>
  </head>
  <body>
    <script src="bundle.js"></script>
  </body>
</html>
```

Now if you run:
```
$ ./node_modules/.bin/webpack src/index.js dist/bundle.js
```

It will make webpack create the bundle with lodash.

### Using a configuration

Create a new file called `webpack.config.js`:
```
$ touch webpack.config.js
```

`webpack.config.js` file content:
```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

Now you should be able to run with the following command, with the same results:
```
$ ./node_modules/.bin/webpack --config webpack.config.js
```

If a webpack.config.js is present, the webpack command picks it up by default. We use the --config option here only to show that you can pass a config of any name. This will come in useful for more complex configurations that need to be split into multiple files.

### npm scripts

We can set up a shortcut command inside `package.json`:
```javascript
"scripts": {
  "build": "webpack"
},
```

Now you should be able to run just with:
```
$ npm run build
```

## Asset Management

Tools like webpack will dynamically bundle all dependencies, creating what's known as a dependency graph.
