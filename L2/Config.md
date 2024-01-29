# Configurations

## Webpack

Here is my webpack config file (`webpack.config.js`)

```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  // This part will convert the index.js file to a bundle.js file in the dist folder
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
      // This part will bundle CSS files
      {
        test: /\.(png|jpg|gif)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[ext]',
              outputPath: 'images',
            },
          },
        ],
      },
      // This part will bundle image files
    ],
  },
};
```

This webpack configuration bundles CSS files using `style-loader` and `css-loader`, and image files using `file-loader`.

Here is the explanation of the webpack configuration:

- **Entry:** The entry point for the application. Webpack will start in this file and bundle all the dependencies.
- **Output:** The output file path. Webpack will bundle dependencies and store it in this file.
- **Module:** The rules for bundling files.
- **test:** The file that are to be bundled
- **use:** The loaders to be used for bundling the files.

## Code Splitting & Lazy Loading

Code splitting and lazy loading are methods for optimizing JS files loading.

For example, if you have a large JS file, you can split it into smaller files and load them when asked. This is called code splitting.

Lazy loading is a method for loading JS files on ask or when required, instead of loading them all at once all at once. This can improve performance

For example:

```javascript
//index.js
document.getElementById('button').addEventListener('click', () => {
  import('./lazyModule.js')
    .then((module) => {
      // Using the module
      module.doSomething();
    })
    .catch((error) => {
      console.error('Error loading module:', error);
    });
});
```

Advantages of code splitting and lazy loading:

- **Good load times:** Only necessary code is loaded so faster.
- **Better resource usage:** Unused code is loaded on demand, saving network and improving performance.
- **Good user experience:** Faster page loading.

## Import Maps

Import maps provide a way to map modules to URLs, allowing for more dynamic and flexible loading. Unlike normal bundling, import maps enable the browser to load modules when asked.

The advantages are:

- **Simpler dependency management**
- **Dynamic imports**

## Implementing Import Maps

To use import maps, we first create an `importmap.json` file:

```json
{
  "imports": {
    "module-name": "/path/to/module.js"
  }
}
```

In your HTML:

```html
<script type="importmap" src="/path/to/importmap.json"></script>
<!-- We need to tell the browser that we are injecting importmaps -->
<script type="module">
  import { someFunction } from 'module-name';
  someFunction();
</script>
```

## Using Import Maps

Lets say there is an existing project using bundling, We now refactor the project to use import maps.

```javascript
// Before
import { someFunction } from './module';
 
// After
import { someFunction } from 'module-name';
```

In HTML:

```html
<!-- Before -->
<script src="dist/bundle.js"></script>

<!-- After -->
<script type="importmap" src="/path/to/importmap.json"></script>
<script type="module">
  import { someFunction } from 'module-name';
  someFunction();
</script>
```

This refactoring adds a new `importmap.json` file and changes the script tag to use the import map. Which helps in making the user experience better.
