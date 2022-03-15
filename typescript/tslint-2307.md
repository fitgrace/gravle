<!--
 * File   : filename.md
 * Author : FitGrace【fitingrace@gmail.com 】
 * Link   : http://www.fitgrace.com/
 * Since  : 创建日期
 *
 * Description【作用描述】
 *
 * [+] Data by author
 *     添加描述
 * [*] Data by author
 *     修改描述
 * [!] Data by author
 *     删除描述
 -->

# webpack 设置别名后 tslint 报 TS(2307)错误

项目中使用路径别名可以使代码简洁清晰，但当 webpack 别名与 TypeScript 一起使用时问题就来了，报出如下错误：

```
[tsserver 2307] [E] Cannot find module 'xxx' or its corresponding type declarations.
```

在 webpack 中设置的别名为：
```
// webpack.config.js
resolve: {
  alias: {
   '@': path.resolve(__dirname, 'src')
  }
}
```

在 tsconfig.js 也要设置 tslint 能识别的针对 webpack 别名的别名设置，如下：
```
// tsconfig.json:
compilerOptions: {
  "baseUrl": ".",
  "paths": {
    "@/*": ["src/*"]
  }
}
```

注意： baseUrl 和 paths 这两个字段都是关键点
