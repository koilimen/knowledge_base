# Пример использованая для простого проекта

1. ```npm i --save-de webpack webpack-cli```

2. webpack.config.js :

```javascript
const path = require('path');
//плаигн нужно установить 
//npm install --save-dev html-webpack-plugin

const HtmlWebpackPlugin = require('html-webpack-plugin');
const webpack = require('webpack'); //to access built-in plugins

module.exports = {
    entry: './src/game.js',
    output: {
        path: path.resolve(__dirname, 'build'),
        filename: 'game.js'
    },
    // файл ./src/index.html должен присутствовать.
    plugins:[new HtmlWebpackPlugin({ template: './src/index.html' })]    
}
```