## How to tensorflow in the browser

### Webpack setup

* source: https://webpack.js.org/guides/getting-started/

* ```bash
  npm init -y
  ```

* ```bash
  npm install webpack webpack-cli --save-dev
  ```

* `package.json`

  * add `"private": true`, remove `main` entry

* ```bash
  npm install <package> --save
  ```

* import the package in your script file: `import stuff from 'package';`

* run `npx webpack` to create a `main.js` in the `dist` folder

  * this will automatically assume `src/index.js` as entry point, for more control, add a `webpack.config.js` to the main project folder

* `webpack.config.js` example:

  * ```javascript
    const path = require('path');
    module.exports = {
      entry: './src/index.js',
      output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist'),
      },
    };
    ```

* this config file will be used automatically, but you can also use a specific file: `npx webpack --config my-webpack.config.js`

* to build the project via `npm run build`, add the following to the scripts section of the `package.json`:

  * ```json
    "build": "webpack"
    ```

### Tensorflow.js

Install the `tensorflow.js` package: `npm install @tensorflow/tfjs`

* `index.js`:

  ```javascript
  import * as tf from '@tensorflow/tfjs';
  
  // Define a model for linear regression.
  const model = tf.sequential();
  model.add(tf.layers.dense({units: 1, inputShape: [1]}));
  
  model.compile({loss: 'meanSquaredError', optimizer: 'sgd'});
  
  // Generate some synthetic data for training.
  const xs = tf.tensor2d([1, 2, 3, 4], [4, 1]);
  const ys = tf.tensor2d([1, 3, 5, 7], [4, 1]);
  
  // Train the model using the data.
  model.fit(xs, ys, {epochs: 10}).then(() => {
    // Use the model to do inference on a data point the model hasn't seen before:
    model.predict(tf.tensor2d([5], [1, 1])).print();
    // Open the browser devtools to see the output
  });
  ```

*  `npm run build`

### Using Typescript

Locally install typescript transpiler and typescript loader for webpack:

* ```bash
  npm install --save-dev typescript ts-loader
  ```

* add tsconfig.json to project root:

  ```json
  {
    "compilerOptions": {
      "outDir": "./dist/",
      "noImplicitAny": true,
      "module": "es6",
      "target": "es5",
      "allowJs": true,
      "moduleResolution": "node"
    }
  }
  ```

* this will finally result in es5 code.

* Extend/change the `webpack.config.js`:

  * ```js
    ...  
    entry: './src/index.ts',
      module: {
        rules: [
          {
            test: /\.ts?$/,
            use: 'ts-loader',
            exclude: /node_modules/,
          },
        ],
      },
      resolve: {
        extensions: [ '.ts', '.js' ],
      },
    ...
    ```

### File watcher

If a file changes, webpack will rebuild the files. Add this to the scripts section of yout package.json: `"watch": "webpack --watch"`

Can be nicely combined with the Live Server Plugin of Visual Studio Code: https://github.com/ritwickdey/vscode-live-server.

### This readme

To convert this file to other formats, install `pandoc` and run following commands:

* html:
  
  * ```
    pandoc -c pandoc.css --metadata title="test" -s readme.md -f markdown -t html -o test.html
    ```
  
* pdf:
  * install `wkhtmltopdf` and add the bin folder of the installation to path
  
  * ```bash
    pandoc readme.md --css=pandoc.css --pdf-engine=wkhtmltopdf -f markdown -t pdf -o test.pdf
    ```
  
  * example with different css and page footer:
    
    * ```bash
      pandoc readme.md --css=minist.css --pdf-engine=wkhtmltopdf -f markdown -t pdf -o test.pdf --pdf-engine-opt='--footer-center' --pdf-engine-opt='"[page]/[toPage]"'
      ```
  
* source for markdown styles: http://markedstyle.com/styles?sort=popularity