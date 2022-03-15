# React 最佳实践

## 组件定义

Function Component 采用 const + 箭头函数方式定义：
```javascript
import React from 'react';

interface IProps {
  title: string;
};

const App: React.FC<IProps> = ({ title }) => {
  return React.useMemo(() => <div>{title}</div>, [title]);
};

App.defaultProps = {
  title: 'Function Component'
}
```

上面的例子包含了：
- 用 React.FC 申明 Function Component 组件类型与定义 Props 参数类型。
- 用 React.useMemo 优化渲染性能。
- 用 App.defaultProps 定义 Props 的默认值。

### FAQ

> 为什么不用 React.memo?

推荐使用 React.useMemo 而不是 React.memo，因为在组件通信时存在 React.useContext 的用法，这种用法会使所有用到的组件重渲染，只有 React.useMemo 能处理这种场景的按需渲染。

> 没有性能问题的组件也要使用 useMemo 吗？

要，考虑未来维护这个组件的时候，随时可能会通过 useContext 等注入一些数据，这时候谁会想起来添加 useMemo 呢？

> 为什么不用解构方式代替 defaultProps?

虽然解构方式书写 defaultProps 更优雅，但存在一个硬伤：对于对象类型每次 Rerender 时引用都会变化，这会带来性能问题，因此不要这么做。

### 限制 props 属性的常用方法

如果 props 所有的属性值都是可选的，我们可以借助 Partial 来实现：
```javascript
import React from 'react';

interface IProps {
  title: string;
  color: 'red' | 'blue' | 'yellow';
};

const App: React.FC<Partial<IProps>> = ({ title, color }) => {
  return React.useMemo(() => <div style={{ color }}>{title}</div>, [title]);
};

App.defaultProps = {
  title: 'Function Component'
}
```

如果 props 所有的属性值都是必选的，我们可以借助 Required 来实现：
```javascript
import React from 'react';

interface IProps {
  title: string;
  color: 'red' | 'blue' | 'yellow';
};

const App: React.FC<Required<IProps>> = ({ title, color }) => {
  return React.useMemo(() => <div style={{ color }}>{title}</div>, [title]);
};

App.defaultProps = {
  title: 'Function Component'
}
```

## 局部状态

局部状态有三种，根据常用程度依次排列： useState useRef useReducer

### useState

状态函数名要表意，尽量聚集在一起申明，方便查阅
```javascript
const [hide, setHide] = React.useState(false);
const [name, setName] = React.useState('BI');
```

在 Function Component 中也可以聚合管理 State：
```javascript
const [state, setState] = useState({
  left: 0,
  top: 0,
  width: 100,
  height: 100
});
```

只是更新的时候，不再自动 merge，而需要使用 ...state 语法：
```javascript
setState(state => ({ ...state, left: e.pageX, top: e.pageY }));
```

### useRef

```javascript
const dom = React.useRef(null);
```
- useRef 尽量少用，大量 Mutable 的数据会影响代码的可维护性。
- 但对于不需重复初始化的对象推荐使用 useRef 存储，比如 new G2() 。

### useReducer

局部状态不推荐使用 useReducer ，会导致函数内部状态过于复杂，难以阅读。 useReducer 建议在多组件间通信时，结合 useContext 一起使用。

### FAQ

> 可以在函数内直接申明普通常量或普通函数吗？

不可以，Function Component 每次渲染都会重新执行，常量推荐放到函数外层避免性能问题，函数推荐使用 useCallback 申明。

### 函数

所有 Function Component 内函数必须用 React.useCallback 包裹，以保证准确性与性能。

```javascript
const [hide, setHide] = React.useState(false);
  
const handleClick = React.useCallback(() => {
  setHide(isHide => !isHide)
}, []);
```

useCallback 第二个参数必须写，eslint-plugin-react-hooks 插件会自动填写依赖项。

```javascript
```

```javascript
```

```javascript
```

```javascript
```


参考：[精读《React Hooks 最佳实践》](https://zhuanlan.zhihu.com/p/81752821)
