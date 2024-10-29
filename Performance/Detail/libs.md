## 组件库lib打包指南

### 1. 项目结构

确保你的项目结构大致如下：

```
/my-vue-lib
|-- /src
|   |-- /components
|   |   |-- ComponentA.vue
|   |   |-- ComponentB.vue
|   |-- index.js
|-- /build
|   |-- webpack.config.js
|-- package.json
```

### 2. 配置 Webpack

在 `build/webpack.config.js` 中配置 Webpack：

```javascript
const path = require('path');

module.exports = {
  mode: 'production',
  entry: {
    index: './src/index.js',
  },
  output: {
    path: path.resolve(__dirname, '../lib'),
    filename: '[name].js',
    libraryTarget: 'commonjs2',
  },
  externals: {
    vue: 'vue',
  },
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue-loader',
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: ['@babel/preset-env'],
          },
        },
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
  resolve: {
    extensions: ['.js', '.vue'],
  },
};
```

### 3. 入口文件

在 `src/index.js` 中导出你的组件：

```javascript
import ComponentA from './components/ComponentA.vue';
import ComponentB from './components/ComponentB.vue';

export { ComponentA, ComponentB };
```

### 4. Babel 配置

确保有一个 Babel 配置文件 `.babelrc` 或 `babel.config.js`：

```json
{
  "presets": ["@babel/preset-env"]
}
```

### 5. 安装依赖

确保安装了必要的依赖：

```bash
npm install vue-loader vue-template-compiler babel-loader @babel/core @babel/preset-env css-loader style-loader webpack webpack-cli --save-dev
```

### 6. 添加打包脚本

在 `package.json` 中添加一个打包脚本：

```json
"scripts": {
  "build": "webpack --config build/webpack.config.js"
}
```

### 7. 按需加载

为了支持按需加载，你需要确保在使用组件时可以单独导入。例如：

```javascript
import { ComponentA } from 'my-vue-lib/lib/index';
```

### 8. 发布到 npm

确保在 `package.json` 中正确配置了 `main` 和 `files`：

```json
"main": "lib/index.js",
"files": [
  "lib"
]
```

### 9. 运行打包

执行以下命令来生成 `lib` 目录：

```bash
npm run build
```

这样，`lib` 目录将包含打包后的组件，支持按需加载。确保在项目中正确配置和测试每个步骤，以确保组件库能够正常工作。