`这是一个可以自动将babel-plugin-import的解析结果添加到 vite的依赖与构建中的工具`
## 功能用途
当我们使用 [babel-plugin-import](https://www.npmjs.com/package/babel-plugin-import "babel-plugin-import") 或者类似的工具使我们打包最小化的时候,vite(开发模式) 因为 [发现新的依赖包](https://cn.vitejs.dev/guide/dep-pre-bundling.html#customizing-the-behavior "发现新的依赖包") 而总是重载当前网页.
使用此工具,将自动添加转换后的结构到依赖与构建中，从而防止页面没玩没了的重载.

## 安装

`yarn add vite-plugin-auto-include --dev 或者 npm install vite-plugin-auto-include -D`

## 用法

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
### 参数
```javascript
//参数可以是 AutoIncludeOpt 或者 AutoIncludeOpt[]
interface AutoIncludeOpt {
    libraryName: string;
    /** default es */
    libraryDirectory?: string;
    /** default style */
    styleLibraryDirectory?: string;
    /** 
     * - true => index.js;
     * - string => string.js;  for example: if set css , will return css.js;
     * - function => path; 注意：如果要直接返回css，请不要返回除了.css以外的其他css预处理器文件，如less，stylus等;
     */
    style?: true | 'css' | ((name: string, path?: string) => string | null | undefined);
    /** 有的目录不是ui组件目录，可以在此忽略 */
    ignoreDirs?: string[];
}
```
示例:
```javascript
  autoInclude({
    libraryName: 'antd',
    libraryDirectory: 'es',
    style: (name) => {
      if (name === '_util' || name === 'locale'|| name==='style') return null;
      return `antd/es/${name}/style`;//实际文件：`antd/es/${name}/style/index.js`
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