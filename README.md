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
