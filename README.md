# Webpack React from Scratch

## Quick High Level Overview

1. Webpack is a static module bundler for modern javascript application.
2. It builds a dependency graph to bundle all the modules that our application needs and then produces one or more bundles.js files.
3. The core of webpack revolves around 6 concepts:
   1. Entry
   2. Output
   3. Loaders
   4. Plugins
   5. Mode
   6. Browser Compatibility
   
4. **Entry:** Webpack requires an entry point which it uses to build its internal dependency graph. This is basically an entry module [in simple terms a js file]. It will automatically detect all the other modules that our module depends on from there on. It defaults to "./src/index.js" from webpack 4 onwards.
   ```js
   module.exports = {
      entry: "./path/file.js"
   }
   ```
5. **Output:** This is as it sounds, a directory/folder where we want our generated bundles to be placed at. This defaults to "./dist/main.js" for main file and "./dist" for other files as needed. We can change it using _output_ option in our webpack.config.js file. In short, this is where webpack _emits_ the bundles.
   ```js
   const path = require('path') // this is in-built node.js module for resolving path [different OS have different path structure. It solves that]
   
   module.exports = {
      entry: "./path/file.js",
      output: {
        path: path.resolve(__dirname, "public"),
        filename: "changedBundler.js"        
      }
   }
   ```
6. **Loaders:** This is important to understand so understand it well. Webpack, if you see, only understands JavaScript and JSON out of the box. It doesn't understand CSS, SCSS, images, and all other sorts of files. Yet, you may have come across webpack setups, say CRA in react, which allows us to _import_ images, scss, css, etc. This feature extension is what _Loaders_ do.
 
   In short, loaders extend the functionality of webpack before bundling happens. For instance,
   ```js
   const path = require('path');
   module.exports = {
      entry: "./path/file.js",
      output: {
        path: path.resolve(__dirname, 'public'),
        filename: "changed-bundler-filename.js"
      },
      module: {
        rules: [
          {
            test: /\.scss$/,
            use: "some-sass-loader"
          }
        ]  
      }
   }
   ```
   You put the _rules_ of loaders inside module.rules property. **_rules_** will be an array of objects, each objects indicating different loaders for different file types. In our example above, we are saying, _"Hey webpack! Whenever you come across any import/require statement which is trying to get .scss files, make sure to use that some-sass-loader to transform that file before bundling it"._ "test" property here is without quotes as it is regex, so don't put "" around /\.scss$/.

7. **Plugins:** Another important webpack concept is this plugin concept. While loaders work before "bundling" happens, plugins take control after "bundling". They are used to extend the feature and perform much wider range of operation on our bundlers. For instance, bundler optimization, asset management, and injection of environment. Let's say you want to extract the SCSS into separate file after bundling. Plugins will come to resque here not the loader. Loader will simply help webpack to read the SCSS file before bundling[dependency graph mapping] happens whereas plugins will do anything you want after that process is done and dusted.
    ```js
   const path = require('path');
   const HTMLWebpackPlugin = require('html-webpack-plugin'); // this is plugin from npm-library
   
   module.exports = {
      entry: "./path/file.js",
      output: {
        path: path.resolve(__dirname, 'public'),
        filename: "changed-bundler-filename.js"
      },
      module: {
        rules: [
          {
            test: /\.scss$/,
            use: "some-sass-loader"
          }
        ]  
      },
      plugins: [
        new HTMLWebpackPlugin({template: "./src/index.html"}),
      ]
   }
   ```
   Here, as we can see that the plugins are required like any other modules in node. Moreover, it is put under _plugins_ property as an array and utilized using _new_ keyword. Different plugins will have their own documentation as to how to use them. _new_ keyword is also used to instantiate plugin as we can use a plugin multiple times in our configuration.
8. **Mode:** This has 3 options only - "development", "production", "none". It helps webpack perform some sort of optimization for different mode.
9. **Browser Compatibility:** Webpack supports all browsers that are ES5 compliant. So most probably, yours will work.