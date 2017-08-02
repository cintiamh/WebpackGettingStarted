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

https://github.com/cintiamh/WebpackGettingStarted/tree/03-asset-management

Tools like webpack will dynamically bundle all dependencies, creating what's known as a dependency graph.

### Loading CSS

In order to import a CSS file from within a JavaScript module, you need to install and add the `style-loader` and `css-loader` to your module configuration.
```
$ npm i --save-dev style-loader css-loader
```

`webpack.config.js`:
```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      }
    ]
  }
};
```

Create a new style.css file:
```
$ touch src/style.css
```

`src/style.css`:
```css
.hello {
  color: red;
}
```

And use this file inside `index.js`:
```javascript
import _ from 'lodash';
import './style.css';

function component() {
  var element = document.createElement('div');
  element.innerHTML = _.join(['Hello', 'webpack'], '');
  element.classList.add('hello');
  return element;
}

document.body.appendChild(component());
```

### Loading images

Using `file-loader` we can incorporate images like background and icons.
```
$ npm i --save-dev file-loader
```

`webpack.config.js`:
```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: ['file-loader']
      }
    ]
  }
};
```

Now, when you `import MyImage from './my-image.png'`, that image will be processed and added to your output directory and the `MyImage` variable will contain the final url of that image after processing.

When using the `css-loader`, as shown above, a similar process will occur for `url('./my-image.png')` within your CSS. The loader will recognize this is a local file, and replace the `'./my-image.png'` path with the final path to the image in your output directory.

The `html-loader` handles `<img src="./my-image.png" />` in the same manner.

`index.js`
```javascript
import _ from 'lodash';
import './style.css';
import Icon from './fb.jpg';

function component() {
  var element = document.createElement('div');
  element.innerHTML = _.join(['Hello', 'webpack'], '');
  element.classList.add('hello');
  var myIcon = new Image();
  myIcon.src = Icon;
  element.appendChild(myIcon);
  return element;
}

document.body.appendChild(component());
```

`style.css`
```css
.hello {
  color: red;
  background: url('./fb.jpg');
}
```

A logical next step from here is minifying and optimizing your images. Check out the `image-webpack-loader` and `url-loader` for more on how you can enhance your image loading process.

### Loading Fonts

The file-loader and url-loader can be used for any kind of files, including fonts.

`webpack.config.js`
```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: ['file-loader']
      },
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/,
        use: ['file-loader']
      }
    ]
  }
};
```

`style.css`
```css
@font-face {
  font-family: 'MyFont';
  src: url('./my-font.woff2') format('woff2'), url('./my-font.woff') format('woff');
  font-weight: 600;
  font-style: normal;
}

.hello {
  color: red;
  font-family: 'MyFont';
  background: url('./fb.jpg');
}
```

### Loading data

Load files like JSON, CSVs, TSVs, and XML files.
JSON support is already built in (just do `import Data from './data.json'`).

For other files you can use `csv-loader` or `xml-loader`.

```
$ npm install --save-dev csv-loader xml-loader
```

`webpack.config.js`
```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.(png|svg|jpg|gif)$/,
        use: ['file-loader']
      },
      {
        test: /\.(woff|woff2|eot|ttf|otf)$/,
        use: ['file-loader']
      },
      {
        test: /\.(csv|tsv)$/,
        use: ['csv-loader']
      },
      {
        test: /\.(xml)$/,
        use: ['xml-loader']
      }
    ]
  }
};
```

This can be especially helpful when implementing some sort of data visualization using a tool like d3. Instead of making an ajax request and parsing the data at runtime you can load it into your module during the build process so that the parsed data is ready to go as soon as the module hits the browser.

### Global Assets

This packaging allows you to group modules and assets together in a more intuitive way. You can group assets with the code that uses them.

```
|- components
  |- my-component/
    |- index.jsx
    |- index.css
    |- icon.svg
    |- img.png
```

## Output Management

https://github.com/cintiamh/WebpackGettingStarted/tree/04-output-management

Add a second file `src/print.js`
```
$ touch src/print.js
```

`src/print.js`
```javascript
export default function printMe() {
  console.log('I get called from print.js!');
}
```

And use this inside `index.js`:
```javascript
import _ from 'lodash';
import printMe from './print.js';

function component() {
  var element = document.createElement('div');
  var btn = document.createElement('button');

  element.innerHTML = _.join(['Hello', 'webpack'], '');

  btn.innerHTML = 'Click me and check the console!';
  btn.onclick = printMe;

  element.appendChild(btn);

  return element;
}

document.body.appendChild(component());
```

### Setting up HtmlWebpackPlugin

In the case we change the bundled package name or the title, we need to update `dist/index.html` file as well.

In order to dynamically update these values is to use HtmlWebpackPlugin.

```
$ npm install --save-dev html-webpack-plugin
```

Update `webpack.config.js`:
```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: {
    app: './src/index.js',
    print: './src/print.js'
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: 'Output Management'
    })
  ],
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

Once you run `npm run build`, it will update the `dist/index.html` file as well with all the bundles automatically added.

### Cleaning up the `/dist` folder

Webpack will generate the files and put them in the `/dist` folder for you, but it doesn't keep track of which files are actually being used.

We can use `clean-webpack-plugin` to clean the `/dist` folder before each build.

```
$ npm install clean-webpack-plugin --save-dev
```

Update `webpack.config.js` plugins:
```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  entry: {
    app: './src/index.js',
    print: './src/print.js'
  },
  plugins: [
    new CleanWebpackPlugin(['dist']),
    new HtmlWebpackPlugin({
      title: 'Output Management'
    })
  ],
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

## Development

https://github.com/cintiamh/WebpackGettingStarted/tree/05-development

### Using source maps

Source maps maps your compiled code back to your original source code.

Just include devtool into `webpack.config.js`:
```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  entry: {
    app: './src/index.js',
    print: './src/print.js'
  },
  devtool: 'inline-source-map',
  plugins: [
    new CleanWebpackPlugin(['dist']),
    new HtmlWebpackPlugin({
      title: 'Output Management'
    })
  ],
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

### Choosing a Development Tool

Automatically compile your code whenever it changes:

1. webpack's Watch mode
2. webpack-dev-server
3. webpack-dev-middleware

In most cases you probably would want to use `webpack-dev-server`.

#### Using watch mode

There is a `webpack --watch` option that if files are updated, the code will be recompiled so you don't have to run the full build manually.

So update `package.json`:
```javascript
{
  "scripts": {
    // ...
    "watch": "webpack --watch",
    // ...
  }
}
```

#### Using webpack-dev-server

Install `webpack-dev-server`:
```
$ npm install --save-dev webpack-dev-server
```

Changes on `webpack.config.js`:
```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  entry: {
    app: './src/index.js',
    print: './src/print.js'
  },
  devtool: 'inline-source-map',
  devServer: {
    contentBase: './dist'
  },
  plugins: [
    new CleanWebpackPlugin(['dist']),
    new HtmlWebpackPlugin({
      title: 'Output Management'
    })
  ],
  output: {
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
};
```

And include the script on `package.json`:
```javascript
{
  "scripts": {
    "start": "webpack-dev-server --open",
  }
}
```

## Hot module replacement
