<!--
 * Author : FitGrace【fitingrace@gmail.com 】
 * Link   : http://www.fitgrace.com/
 * Since  : 2021-04-09
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

# Typescript 给 setTimeout 的返回值定义为 number 出错

项目中经常用会到 setTimeout，如下：

```javascript
const timeout: number = setTimeout(function () { /* snip */  }, 500);
```

这是有问题的代码，会产生编译器错误：
```javascript
// [tsserver 2322] [E] Type 'Timeout' is not assignable to type 'number'.
```

因为 setTimeout 使用的是 Node.js 下的接口定义

@types/node index.d.ts
```javascript
declare function setTimeout(callback: (...args: any[]) => void, ms: number, ...args: any[]): NodeJS.Timer;
```

通过 yarn.lock 定位依赖，发现来源于 @types/react-dom
```javascript
@types/react-dom
dependencies @types/node
```

而在 typescript 中，window 下的 setTimeout 返回的是 number：
```javascript
interface WindowTimers extends WindowTimersExtension {
  clearInterval(handle?: number): void;
  clearTimeout(handle?: number): void;
  setInterval(handler: (...args: any[]) => void, timeout: number): number;
  setInterval(handler: any, timeout?: any, ...args: any[]): number;
  setTimeout(handler: (...args: any[]) => void, timeout: number): number;
  setTimeout(handler: any, timeout?: any, ...args: any[]): number;
}
```

解决：使用 window.setTimeout
```javascript
const timeout: number = window.setTimeout(function () { /* snip */  }, 500);
```
