# Webpack

Here is my webpack config file (`webpack.config.js`):

```javascript
const path = require('path');

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
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
    ],
  },
};
```

Here is the explanation of the webpack configuration:

- **Entry:** The entry point for the application. Webpack will start in this file and bundle all the dependencies.
- **Output:** The output file path. Webpack will bundle dependencies and store it in this file.
- **Module:** The rules for bundling files.
- **test:** The file that is to be bundled.
- **use:** The loaders to be used for bundling the files.

All these loaders reduce the file size by bundling the huge files into a minified format, which helps save space and also doesn't add pressure on the network.

- `style-loader` and `css-loader` are used to minify CSS stylesheets.
- `file-loader` is used to minify different files such as pdf, mp4, etc. This helps in reducing file size.

# Code Splitting & Lazy Loading

Usually, when we have a large project of JS and TS files, the number of dependencies, packages, and files becomes huge. If a client system requests these files all at once, the page may have a higher loading time, which causes the user to lose interest while waiting, and there may also be pressure on the limited bandwidth. To solve this issue, we can use Code Splitting & Lazy Loading.

## What is Code Splitting?

Code splitting is a technique used to improve the performance of web applications by splitting the codebase into smaller, more manageable chunks. The idea is to load only the code that is necessary for a particular functionality or page, rather than loading the entire application code upfront. This can lead to faster initial page loads and improved user experience.

## What is Lazy Loading?

Lazy loading is a strategy in software development, particularly in web development, where resources (such as images, scripts, or modules) are loaded on demand or when they are actually needed, rather than being loaded upfront when the page initially loads. The goal is to defer the loading of non-essential content, improving the initial page load time and reducing the amount of data transferred over the network.

So basically, you are only requesting those modules which are needed for the function on the current webpage so you can load the page faster and with less data.

Here is an example:

```javascript
import { useRoutes } from "raviger";
import { lazy, Suspense } from "react";
import { ErrorPage } from "./components/ErrorPage";
import { LoadingScreen } from "./components/LoadingScreen";

// Here, all the components are loaded lazily, so they are only requested when there is a demand
const HomePage = lazy(() => import("./pages/homepage/HomePage"));
const Login = lazy(() => import("./pages/authenticate/auth/Login"));
const Signup = lazy(() => import("./pages/authenticate/auth/Signup"));
const AllTodo = lazy(() => import("./pages/dashboard/todo/AllTodo"));

const routes = {
  "/": () => <HomePage />,
  "/login": () => <Login />,
  "/signup": () => <Signup />,
  "/todo": () => <AllTodo />,
  "*": () => (
    <ErrorPage
      status={"404"}
      message={"Page not found"}
      description={
        "Oops! The page you are looking for does not exist. It might have been moved or deleted."
      }
    />
  ),
};

// The Suspense component is used for a fallback component such as a loading screen while the actual content is loaded.
const App = () => {
  return <Suspense fallback={<LoadingScreen />}>{useRoutes(routes)}</Suspense>;
};

export default App;
```

Advantages of code splitting and lazy loading:

- **Good load times:** Only necessary code is loaded so faster.
- **Better resource usage:** Unused code is loaded on demand, saving network and improving performance.
- **Good user experience:** Faster page loading.

# Import Maps

## What Are Import Maps?

Import maps are a feature in modern JavaScript that provides a way to define and manage the mapping between module specifiers and their corresponding URLs. This helps simplify the handling of module dependencies and allows developers to control how modules are resolved.

Unlike normal bundling, import maps enable the browser to load modules when asked.

The advantages are:

- **Simplifies Dependency Management:**
- **Enhances Code Readability:**
- **Facilitates Versioning and Upgrades:**
- **Reduces Configuration Errors:**
 - **Enables Dynamic Imports:**

## Implementing Import Maps

The basic idea is to have a centralized configuration, often in the form of a JSON file called an import map, where developers can specify the mappings between module names and their actual locations. This can be useful in scenarios where the actual file paths or URLs of modules may change, or when developers want to use shorter or more convenient names for modules.

Here is an example for import maps:

```json
{
  "imports": {
    "react": "https://unpkg.com/react@17/umd/react.production.min.js",
    "react-dom": "https://unpkg.com/react-dom@17/umd/react-dom.production.min.js",
    "lodash": "https://cdn.jsdelivr.net/npm/lodash@4/lodash.min.js"
  }
}
```

So here we have defined the `react`, `react-dom`, and `loadash` import URLs. Now these links can be used to only load dependencies that are required.

## Using Import Maps

We now use import maps in an actual web application:

```html
<html lang="en">
<head>
  <title>React and Lodash Example</title>
  <script type="importmap">
    {
      "imports": {
        "react": "https://unpkg.com/react@17/umd/react.production.min.js",
        "react-dom": "https://unpkg.com/react-dom@17/umd/react-dom.production.min.js",
        "lodash": "https://cdn.jsdelivr.net/npm/lodash@4/lodash.min.js"
      }
    }
  </script>
</head>
<body>
  <div id="root"></div>
  <script type="module">
    // Import React and Lodash from the import map
    import React from 'react';
    import ReactDOM from 'react-dom';
    import _ from 'lodash';

    // React component using Lodash
    const App = () => {
      const numbers = [1, 2, 3, 4, 5];
      const sum = _.sum(numbers);
      return (
        <div>
          <h1>React and Lodash Example</h1>
          <p>Sum of numbers: {sum}</p>
        </div>
      );
    };

    // Render the React component
    ReactDOM.render(<App />, document.getElementById('root'));
  </script>
</body>
</html>
```

In this example, the import map specifies the URLs for React, React DOM, and Lodash. The HTML file then uses these import maps to dynamically load React, ReactDOM, and Lodash when the application runs.

The React component (`App`) uses Lodash to calculate the sum of an array of numbers, demonstrating the dynamic import of the necessary dependencies.

This is how we use import maps in our web application.
