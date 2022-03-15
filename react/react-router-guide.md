# React Router 使用指南

路由其实是根据不同的 URL 地址展示不同的内容或页面，在前端中我们可以理解为是 URL 和视图的一种映射关系，URL 的更新引起页面的更新。

## 关于路由

路由的概念起源于服务端，在前后端不分离的时代，由后端来控制路由，当接收到客户端发来的 HTTP 请求，就会根据所请求的 URL，通过后端路由匹配之后再返回给浏览器。不管是什么语言的 Web 后端框架，都会有一个专门的路由模块或路由区域，用于匹配用户给出的 URL 地址，遇到无法匹配的路由，后端会返回一个 404 状态码，这也是404 NOT FOUND 的由来。后端路由的好处与缺点非常明显：
* 好处：安全性好，SEO 好
* 缺点：加大服务器的压力，不利于用户体验，代码冗合不好维护

由于后端路由的不足，前端路由才有了发展空间。Ajax 的诞生，让浏览器可以实现异步加载数据，能够极大的提高交互体验，前端路由的概念也随之而产生。对于前端路由来说，路由的映射通常是进行一些 DOM  的显示和隐藏操作。当访问不同路径的时候，会显示不同的页面组件。前端路由主要有以下两种实现方案：
* Hash
* History

## Hash 模式

在 HTML5 标准落地之前，前端路由是基于 location.hash 来实现的。其现原理也很简单，location.hash 的值就是 URL 中 # 后面的内容。
```html
http://test.com/#a 
http://test.com/#b
```

URL 中 hash 值只是客户端的一种状态，也就是说当向服务器端发出请求时，hash 部分不会被发送。hash 值的改变，都会在浏览器的访问历史中增加一个记录。因此我们能通过浏览器的回退、前进按钮控制 hash 的切换。

使用 hashchange 事件来监听 hash 的变化，我们可以通过两种方式触发 hash 变化，一种是通过 a 标签，并设置 href 属性，当用户点击这个标签后，URL 就会发生改变，也就会触发 hashchange 事件了
```html
<a href="#search">search</a>
```

另一种方式就是直接使用 JavaScript 来对 loaction.hash 进行赋值，从而改变 URL，触发 hashchange 事件
```javascript
location.hash="#search"
```

## History 模式

History 模式路由地址没有 #：
```html
http://test.com/a 
http://test.com/b
```

在 HTML4 中，已经支持 window.history 对象来控制页面历史记录跳转
```javascript
// 在历史栈中前进一步
history.forward()

// 在历史栈中后退一步
history.back()

// 在历史栈中跳转 n 步骤，n=0为刷新本页，n=-1为后退一页
history.go(n)
```

HTML5 标准中，又对 window.history 对象进行了扩展，其中最主要的 API 有 history.pushState() 和 history.repalceState()。这两个 API 可以在不进行刷新的情况下，操作浏览器的历史纪录（改变 URL ）。
```javascript
// 向历史栈中追加一条记录
window.history.pushState(data [,title] [,url])

// 替换当前页在历史栈中的记录
window.history.replaceState(data [,title] [,url])

// 是一个属性，可以得到当前页的 state 信息
history.state

// 当前历史栈中的记录数
history.length
```

### popstate 事件

每当同一个文档的浏览历史（即history对象）出现变化时，就会触发 popstate 事件。

注意，仅仅调用 pushState() 方法或 replaceState() 方法 ，并不会触发该事件，只有用户点击浏览器倒退按钮和前进按钮，或者使用 JavaScript 调用 History.back()、History.forward()、History.go() 方法时才会触发。另外，该事件只针对同一个文档，如果浏览历史的切换，导致加载不同的文档，该事件也不会触发。

使用的时候，可以为 popstate 事件指定回调函数
```javascript
window.onpopstate = function (event) {
  console.log('location: ' + document.location);
  console.log('state: ' + JSON.stringify(event.state));
};

// 或者
window.addEventListener('popstate', function(event) {
  console.log('location: ' + document.location);
  console.log('state: ' + JSON.stringify(event.state));
});
```

### 注意
* IE9及其以下版本浏览器是不支持的，IE10开始支持
* pushState()/replaceState() 虽然可以改变历史栈，让浏览器地址栏中的 URL 发生变化，但并不会向后端发起请求！
* pushState()/replaceState() 对 URL 的修改受同源策略限制，防止恶意脚本模仿其他网站的 URL 欺骗用户，所以当违背同源策略时将会报错
* 现在大多浏览器忽略 Title 这个参数，可以直接用 null 代替

## Hash、History 如何抉择

Hash 模式相比于 History 模式的优点：
* 兼容性更好，可以兼容到 IE8
* 无需服务端配合处理非单页的 URL 地址

Hash 模式相比于 History 模式的缺点：
* 看起来更丑
* 会导致锚点功能失效
* 相同 Hash 值不会触发动作将记录加入到历史栈中，而 onpopstate 则可以

当我们不需要兼容老版本IE浏览器，并且可以控制服务端覆盖所有情况的候选资源时，我们可以愉快的使用 History 模式，反之，只能使用丑陋的 Hash 模式~


## React-Router

主流的前端框架都有自己的路由，比如 Backbone、Ember、Angular、React 等等。

React-Router 是 React 生态的基础路由库，它可以让你向应用中快速地添加视图和数据流，同时保持页面与 URL 间的同步。

从 v4 开始 React-Router 仓库被拆分成了多个包进行发布：
* react-router：路由基础库
* react-router-dom：适用于浏览器环境的再次封装
* react-router-native：适用于react-native环境的再次封装
* react-router-config：静态路由配置助手

### 静态路由

所谓“静态路由”，就是说路由规则是固定的。只要应用程序一启动，映射关系就固定不变。无论 express、Angular 还是 Rails 等业界响当当的框架，都用的是静态路由。以 express 为例，路由规则差不多是这么写的:
```javascript
app.get('/', Home);
app.get('/product/:id', Product);
app.get('/about', About);
```

### 动态路由

所谓动态路由，指的是路由规则不是预先确定的，而是在渲染过程中确定的。

既然 React 组件渲染是动态发生的，那么就让 Route 变成一个 React 组件，和其他组件一样被渲染，在运行时完全可以决定某个 Route 渲染还是不渲染，也可以决定渲染这个 Route 的参数 props 是怎样，如此一来，路由规则也就完全动态了。

```javascript
// react-router v4 例子
import { BrowserRouter, Route } from 'react-router-dom'

const PrimaryLayout = () => (
  <div className="primary-layout">
    <header>
      Our React Router 4 App
    </header>
    <main>
      <Route path="/" exact component={HomePage} />
      <Route path="/users" component={UsersPage} />
    </main>
  </div>
)
const HomePage =() => <div>Home Page</div>
const UsersPage = () => <div>Users Page</div>

const App = () => (
  <BrowserRouter>
    <PrimaryLayout />
  </BrowserRouter>
)

render(<App />, document.getElementById('root'))
```

路由穿插在了各组件中，在嵌套路由（Nested Routes）的场景尤为明显。

## 安装

> 注意：以下源码是基于 v6.0.0版本的

```
$ yarn add history react-router-dom@next
```

如果是 TypeScript 项目，还需要安装对应的声明文件
```
$ yarn add @types/react-router-dom --dev
```

### BrowserRouter

用于包装不同的路线，它使用 HTML5 history API 来跟踪 React 应用程序中的路由历史记录。理解为路由容器，被包裹在里面的子组件就可以使用自己定义的路由组件了。建议在 React 应用的组件层次结构中的顶级组件上导入和使用它：
```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';

import Home from '@/components/Home';

import './style/index.css';

ReactDOM.render(
  <BrowserRouter>
    <Home />
  </BrowserRouter>,
  document.getElementById('root'),
);
```

### Routes

这个新的元素是以前 Switch 组件的升级版，Routes 组件是整个路由中最核心的，决定了当前路径应匹配显示那个页面组件。它包括相对路由和链接、自动路由排名、嵌套路由和布局等功能。

### Route

路由组件，路由匹配时这个位置被渲染成相应的内容。
* path：需要匹配的路径
* element：匹配成功渲染的组件

Route 组件的 element 属性现在允许你传递一个React组件，而不仅仅是该React组件的名称。这样就可以很方便的把属性传到路由上：

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter, Routes, Route } from 'react-router-dom';

import Home from '@/components/Home';
import About from '@/components/About';

import './style/index.css';

ReactDOM.render(
  <BrowserRouter>
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
    </Routes>
  </BrowserRouter>,
  document.getElementById('root'),
);
```

### Link

封装了 a 标签的组件进行路由跳转。
```javascript
<Link to="/home">Home</Link>
```

### 嵌套路由

当路由被嵌套时，一般认为网页的某一部分保持不变，只有网页的子部分发生变化。

V5 中必须明确定义嵌套路由，V6 并非如此，增加了一个新 API：名为 Outlet 的最佳元素，为特定路由呈现任何匹配的子元素。
```javascript
// index.tsx
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

import Home from '@/components/Home';
import About from '@/components/About';
import Users from '@/components/Users';
import UserMe from '@/components/UserMe';
import UserOther from '@/components/UserOther';

import './style/index.css';

ReactDOM.render(
  <BrowserRouter>
    <nav>
      <Link to="/">Home</Link>
      <Link to="/about">About</Link>
      <Link to="/users">Users</Link>
    </nav>
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/about" element={<About />} />
      <Route path="/users" element={<Users />}>
        <Route path="me" element={<UserMe />} />
        <Route path="other" element={<UserOther />} />
      </Route>
    </Routes>
  </BrowserRouter>,
  document.getElementById('root'),
);


// Users.tsx
import React from 'react';
import { Link, Outlet } from 'react-router-dom';

const Users = () => (
  <div>
    <h1>用户中心 ~</h1>
    <nav>
      <Link to="me">Me</Link>
      <Link to="other">Other</Link>
    </nav>
    <Outlet />
  </div>
);

export default Users;
```

### useNavigate

主动跳转路由可以通过 useNavigate 直接实现这个常用操作，这是一个 useHistory 的代替品

```javascript
import { useNavigate } from "react-router-dom";
 
function MyButton() {
  let navigate = useNavigate();
  function handleClick() {
    navigate("/home");
  }
  return <button onClick={handleClick}>Submit</button>;
}
```

对 history 进行了封装，如果需要 history.replace，可以通过 { replace: true } 参数指定
```javascript
// 追加一条记录
navigate("/home");

// 替换当前页在历史栈中的记录
navigate("/home", { replace: true });
```

需要注意的一点是：V6 使用简化的路径格，仅支持2种占位符：动态 :id 样式参数和 * 通配符
```
# 以下都是v6中的有效路由路径
/groups
/groups/admin
/users/:id
/users/:id/messages
/files/*
/files/:id/*
/files-*

# 使用RegExp正则匹配的路径将无效
/users/:id?
/tweets/:id(\d+)
/files/*/cat.jpg
```

参考：

[React router dom v6 从浅到浅](https://www.geekschool.org/2020/09/10/28099.html)

[React Router v6 使用指南](https://juejin.cn/post/6862305213148381198)

[React-Router 入门教程](https://zhuanlan.zhihu.com/p/78176856)

[React-router-dom | 原理解析](https://medium.com/手寫筆記/a-little-bit-of-react-router-dom-e5b809fcb127)

[react-router的3种路由按需加载介绍](http://jacktesla.xyz/Technology/React/2018.6.10.html)

[react-router三种传参方式](https://www.kelede.win/posts/react-router三种传参方式)
