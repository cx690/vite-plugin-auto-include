- [中文](https://github.com/cx690/vite-plugin-auto-include/blob/main/README_zh_CN.md "中文")

`A tool that can auto add babel-plugin-import's result to vite's Pre-bundling dependencies.`
## Why vite-plugin-auto-include
When we use [babel-plugin-import](https://www.npmjs.com/package/babel-plugin-import "babel-plugin-import") or the same tool to minimize our bandle,the vite(server mode) always reload page because of it [find new dependencies](https://vitejs.dev/guide/dep-pre-bundling.html#customizing-the-behavior "find new dependencies").
Use this tool,it can auto add to vite's Pre-bundling dependencies.

## Install

`yarn add vite-plugin-auto-include --dev or npm install vite-plugin-auto-include -D`

## Usage

```javascript
//vite.config.ts or vite.config.js
import autoInclude from 'vite-plugin-auto-include';

export default defineConfig({
  ...
  plugins: [
    autoInclude({
      libraryName: 'antd',
      libraryDirectory: 'es',
      style: true
    }),
    ...
  ], 
  ...
})
```
### options
```javascript
//options can be AutoIncludeOpt or AutoIncludeOpt[]
interface AutoIncludeOpt {
    libraryName: string;
    /** default es */
    libraryDirectory?: string;
    /** default style */
    styleLibraryDirectory?: string;
    /** 
     * - true => index.js;
     * - string => string.js;  for example: if set css , will return css.js;
     * - function => path; notice : can't return .less or .scss or .stylus;
     */
    style?: true | string | ((name: string, path?: string) => string | null | undefined);
    /** ignore some conponet's directory */
    ignoreDirs?: string[];
}
```
For Example:
```javascript
  autoInclude({
    libraryName: 'antd',
    libraryDirectory: 'es',
    style: (name) => {
      if (name === '_util' || name === 'locale'|| name==='style') return null;
      return `antd/es/${name}/style`;//real fiel：`antd/es/${name}/style/index.js`
    },
  }),
  //or
  autoInclude([
    {
      libraryName: 'antd',
      libraryDirectory: 'es',
      ignoreDirs: ['_util', 'locale', 'style'],
      style: true
    },
	  {
      libraryName: 'antd-mobile',
      libraryDirectory: 'es',
      style: 'css'
  	}
  ]),
```