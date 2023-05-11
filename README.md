# Как настроить webpack
1. Прочитайте о webpack на [webpack.js.org](https://webpack.js.org/)
2. Инициализируйте npm проект: `npm init -y`
3. Установите webpack `npm i -D webpack webpack-cli`
4. Установите typescript `npm i -D typescript ts-loader`
5. Добавьте tsconfig.json `tsc --init` (читайте о tsconfig опции [здесь](https://www.typescriptlang.org/tsconfig))
6. Создайте webpack.config.js [webpack с typescript](https://webpack.js.org/guides/typescript/)
```js
  const path = require('path');

  module.exports = {
    entry: './src/index.ts',
    output: {
      filename: 'index.js',
      path: path.resolve(__dirname, 'dist'),
    },
    module: {
      rules: [
        {
          test: /\.[tj]s$/,
          use: 'ts-loader',
          exclude: /node_modules/,
        },
      ],
    },
    resolve: {
      extensions: ['.ts', '.js'],
    },
  };
```
7. Настройте assets
```js
module.exports = {
  output: {
    filename: '[name].[contenthash].js',
    path: path.resolve(__dirname, 'dist'),
    assetModuleFilename: 'assets/[hash][ext]',
  },
  module: {
    rules: [
      {
        test: /\.(?:ico|gif|png|jpg|jpeg|svg)$/i,
        type: 'asset/resource',
      },
      {
        test: /\.(woff(2)?|eot|ttf|otf)$/i,
        type: 'asset/resource',
      },
    ],
  },
}
```
8. Добавьте _HtmlWebpackPlugin_ `npm i -D html-webpack-plugin`. Документация [здесь](https://github.com/jantimon/html-webpack-plugin#options)
```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      title: 'Hello world',
      // template: './src/index.html',
    }),
  ]
}
```
9. Добавьте css and Sass  `npm i -D css-loader sass sass-loader mini-css-extract-plugin`
```js
  const MiniCssExtractPlugin = require('mini-css-extract-plugin');

  module.exports = {
    module: {
      rules: [
        {
          test: /\.css$/i,
          use: [MiniCssExtractPlugin.loader, 'css-loader'],
        },
        {
          test: /\.s[ac]ss$/i,
          use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader']
        }
      ],
    },
    plugins: [
      new MiniCssExtractPlugin({ filename: '[name].[contenthash].css' }),
    ],
  }
```
10. Добавьте Clean Webpack Plugin `npm i -D clean-webpack-plugin`
```js
  const { CleanWebpackPlugin } = require('clean-webpack-plugin');

  module.exports = {
    plugins: [
      new CleanWebpackPlugin({ cleanStaleWebpackAssets: false }),
    ]
  };
```
 13. Добавить dev-server. Подробнее [здесь](https://webpack.js.org/configuration/dev-server/)
 ```js
  const devServer = (isDev) => !isDev ? {} : {
    devServer: {
      open: true,
      hot: true,
      port: 8080,
      contentBase: path.join(__dirname, 'public'),
    },
  };

  module.exports = (env) => ({
    ...devServer(env.development)
  });
```

# В итоге, мы имеем следующий webpack.config файл:
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
  mode: 'development',
  entry: {
    main: './src/index.ts',
  },
  output: {
    filename: '[name].[contenthash].js',
    path: path.resolve(__dirname, 'dist'),
    assetModuleFilename: 'assets/[hash][ext]',
  },
  module: {
    rules: [
      {
        test: /\.[tj]s$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
      {
        test: /\.(?:ico|gif|png|jpg|jpeg|svg)$/i,
        type: 'asset/resource',
      },
      {
        test: /\.(woff(2)?|eot|ttf|otf)$/i,
        type: 'asset/resource',
      },
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
      {
        test: /\.s[ac]ss$/i,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader']
      }
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({template: 'src/index.html'}),
    new CleanWebpackPlugin({cleanStaleWebpackAssets: false}),
    new MiniCssExtractPlugin({filename: 'style.css'}),
  ],
  resolve: {
    extensions: ['.ts', '.js'],
  },
  devServer: {
    open: false,
    hot: true,
    port: 8080
   },
};

```
