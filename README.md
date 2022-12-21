<div align="center">
  <a href="https://github.com/webpack/webpack">
    <img width="200" height="200" src="https://webpack.js.org/assets/icon-square-big.svg">
  </a>
  <h1>Transpile Webpack Plugin</h1>
</div>

[![npm][npm]][npm-url]
[![node][node]][node-url]
[![download][download]][npm-url]
[![license][license]][license-url]
[![size][size]][size-url]
[![cicd][cicd]][cicd-url]

# Transpile Webpack Plugin

The webpack plugin that transpiles input files into output files individually without bundling together.

Input files are collected from files directly or indirectly imported by the [entry](https://webpack.js.org/configuration/entry-context/#entry), then get compiled and ouputted keeping the same directory structure in the output directory.

Transpiling with webpack could be especially helpful when building SSR(server side rendering) in a heavyweight NodeJS server that involves source file path based logics. Please make best of it.

Note that this plugin replies on features of webpack v5. The latest webpack is supposed to be used when possible.

## Getting Started

To begin, you'll need to install `transpile-webpack-plugin`:

```sh
npm i -D transpile-webpack-plugin
```

Or, with any package manager you prefer.

Then, add the plugin to your webpack config. For example:

```js
const TranspilePlugin = require('transpile-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  output: {
    path: __dirname + '/dist',
  },
  target: 'node',
  plugins: [new TranspilePlugin(/* options */)],
};
```

The [target](https://webpack.js.org/configuration/target/) should be node-compatible (because output files are imported by each other with `require` at runtime and output with other value is not runnable in most cases).

Assuming the entry `src/index.js` imports another file `src/constants/greeting.js`, input files will be `src/index.js` and `src/constants/greeting.js`. And after compilation, output files will be `dist/index.js` and `dist/constants/greeting.js`. The common dir of input files is used as the base dir to evaluate the relative paths of output files in output dir.

## Options

- **[exclude](#exclude)**
- **[hoistNodeModules](#hoistNodeModules)**
- **[longestCommonDir](#longestCommonDir)**

### `exclude`

Type: `{string|RegExp|((p: string) => boolean)|string[]|RegExp[]|((p: string) => boolean)[]}`

Default: `[]`

Option `exclude` indicates files to be excluded. Import statements to the excluded in input files are properly adjusted in output files to keep workable. It's useful when you copy third-party files and want to use them as they are.

Though, don't use it to exclude `node_modules` because some helpers of webpack loaders living there are not runnable before compiled. For this case, use the [externals](https://webpack.js.org/configuration/externals/) instead.

With this option as string, input files whose aboslute paths begin with it will be excluded. With this option as regular expression, input files whose absolute paths match it will be excluded. With this option as function, input files whose absolute paths are passed into the call of it and end up with `true` will be excluded.

### `hoistNodeModules`

Type: `{boolean}`

Default: `true`

Option `hoistNodeModules` indicates whether to evaluate output paths for input files from or not from `node_modules` separately, and keep input files from `node_modules` outputted into `node_modules` just under output dir. It's usable to flatten the output directory structure a little bit.

Given input files `src/index.js` `node_modules/lodash/lodash.js` and output dir `dist`, with this option `true`, output files will be `dist/index.js` `dist/node_modules/lodash/lodash.js`. But with this option `false`, output files will be `dist/src/index.js` `dist/node_modules/lodash/lodash.js`.

### `longestCommonDir`

Type: `{string|undefined}`

Default: `undefined`

Option `longestCommonDir` indicates the limit of the common dir to evaluate relative paths of output files in output dir. When this option is shorter than the common dir of input files, this option is used against input files to evaluate relative paths of output files in output dir. Otherwise, the common dir of input files is used.

Given input files `src/server/index.js` `src/server/constants/greeting.js` and output dir `dist`, with this option `undefined`, output files will be `dist/index.js` `dist/constants/greeting.js`. But with this option `'./src'`, output files will be `dist/server/index.js` `dist/server/constants/greeting.js`.

Though, given input files `src/index.js` `src/server/constants/greeting.js` and output dir `dist`, with this option `'./src/server'`, output files will still be `dist/index.js` `dist/server/constants/greeting.js` because the common dir of input files is shorter than this option.

## Contributing

Please take a moment to read our contributing guidelines if you haven't yet done so.
[CONTRIBUTING][contributing-url]

## License

[MIT][license-url]

[npm]: https://img.shields.io/npm/v/transpile-webpack-plugin.svg
[npm-url]: https://npmjs.com/package/transpile-webpack-plugin
[node-url]: https://nodejs.org/
[node]: https://img.shields.io/node/v/transpile-webpack-plugin.svg
[download]: https://img.shields.io/npm/dw/transpile-webpack-plugin
[license]: https://img.shields.io/github/license/licg9999/transpile-webpack-plugin
[license-url]: https://github.com/licg9999/transpile-webpack-plugin/blob/master/LICENSE
[size]: https://packagephobia.com/badge?p=transpile-webpack-plugin
[size-url]: https://packagephobia.com/result?p=transpile-webpack-plugin
[cicd]: https://github.com/licg9999/transpile-webpack-plugin/actions/workflows/verify-and-release.yml/badge.svg
[cicd-url]: https://github.com/licg9999/transpile-webpack-plugin/actions/workflows/verify-and-release.yml
[contributing-url]: https://github.com/licg9999/transpile-webpack-plugin/blob/master/CONTRIBUTING.md
