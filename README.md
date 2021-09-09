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

## Deep Dive into topics

### Entry
Entry points can be single as shown above or multiple if we have multi-page scenario. Moreover, we can also have multiple entry points if we are working with modules that do not require modification like bootstrap, jquery, etc.

```js
// empty entry object when entries are generated by plugins
module.exports = {
  entry: {}
}

// separate app and vendor entries - both build their own dependency graphs
// webpack.config.js
module.exports = {
  entry: {
    app: "./path/app.js", 
    vendor: "./path/vendor.js" // this contains the non-modified libraries
  }
}

// webpack.prod.js
module.exports = {
  output: {
    filename: "[name].[contenthash].bundle.js" // this is substitution of filenames. It allows for dynamic filename in output. ContentHash remains same and should be used in production as this allows the browser to cache the js files in production.
  }
}

// webpack.dev.js
module.exports = {
  output: {
    filename: "[name].bundle.js"
  }
}


// separate entries for normal app entry and adminside app entry
module.exports = {
  entry: {
    app: "./path/app.js",
    adminapp: "./path/admin.js" 
  }
}

// separate entries based on different html pages - its usually good to do it
module.exports = {
  entry: {
    page1: "./page1.js",
    page2: "./page2.js" 
  }
}
```
This approach is scalable in a way that it is easy to add more files down the line if we require it and then can be used with tools like webpack-merge for merging.

We can also make one entry point depend on another entry point. For more on that, always refer to [documentation](https://webpack.js.org/concepts/entry-points/#entrydescription-object)

### Output
There are various ways to use this option. By default, webpack creates only 1 _bundle.js_ file in the _dist_ folder.
```js
module.exports = {
  entry: {
    app: "./app.js", 
    adminapp: "./adminapp.js"
  },
  output: {
    filename: "[name].bundle.js", // substitution of names in case of mutliple entries.
    pathname: __dirname + "./public" // generates two chunks rather than 1 bundle.js in the public folder rather than dist. app.bundle.js and adminapp.bundle.js
  } 
}

// cdn usecase
module.exports = {
  path: "/home/.../cdn/[fullhash]",
  publicPath: "https://.../cdn/[fullhash]" 
}

// we can also set this public path in the module entry file at the top using __webpack_public_path__ in a scenario where the publicPath is left blank
```

### Loaders
Loaders are used to transform our files which aren't supported out of the box by webpack. Files like CSS, Images, TypeScript, etc. We can use different loaders to tell the webpack to use these loaders while "loading" or "importing" the files before processing them to build dependency graphs/bundling.

In order to install loaders, we have to use npm or yarn to install them like any other npm packages.
```shell
npm install --save css-loader ts-loader some-other-loader

// or

yarn add css-loader ts-loader some-other-loader
```

```js
module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' }, // this allows us to load CSS directly inside our JS modules. There is another sass-loader as well.
      { test: /\.ts$/, use: 'ts-loader' }, // this allows us to load TypeScript files inside our JS modules.
    ],
  },
};
```

The _use_ property above, basically _**module.rules[i].use**_ can take an array of loaders. In such scenario, loaders are evaluated from **_right to left_** or **_bottom to top_** in visual sense. Whatever loader is placed at the end will be loaded first, then the one above that and so on.
```js
module.exports = {
  module: {
    rules: [
       {
         test: /\.css$/,
         use: [
            {loader: "style-loader"}, // finally this gets loaded at the end
            {loader: "css-loader", options: {modules: true}}, // this gets loaded second to transform the scss files to css
            {loader: "scss-loader"} // this gets loaded first to allow the imports of scss files in our modules.
         ] 
       }
    ]
  }
}
```
As visible, it kind of bubbles up in a sense of loading "loaders"! Loaders are chained one after the other. The first loader passes down its transformation to another. The last loader must return a JavaScript to the webpack.

Now, there are 3 types of loaders in webpack:

1. PreLoaders
2. Loaders | Normal Loaders
3. PostLoaders

Also, there is another way to load the loaders in our modules in webpack: Inline loading of loaders
```js
// js module/entry file - let's say app.js
import Styles from "style-loader!css-loader?module!scss-loader!./styles.scss" // ! is used to separate the loaders from each other. Here to the loaders are placed in right to left manner.

// adding ! before the import statement disables all configured normal loaders
import Styles from "!style-loader!css-loader?module!scss-loader!./styles.scss"

// adding !! before, will disable all 3 types of loaders that are configured.
import Styles from "!!style-loader!css-loader?module=true!scss-loader!./styles.scss"

// adding -! before, will disable preLoaders and normalLoaders but not the postLoaders
import Styles from "-!style-loader!css-loader?{\"module\": \"true\"}!scss-loader!./style.scss"

// notice that I passed "module" options in the query parameter in 3 ways - directly as it was boolean, key=value way, and then in objectJSON manner
```
It's always recommended to use webpack.config.js file to load loaders in order to maintain the code in the scenario of errors and future scalability.

These loaders can be found in the [loaders webpack page](https://webpack.js.org/loaders/) or directly from [npm site](https://www.npmjs.com/)



