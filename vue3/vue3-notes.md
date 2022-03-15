# Vue 3 笔记 

如果只有setup方法的话，可以直接在defineComponent中传入setup函数

```javascript
const Component = defineComponent(() => {
  // ....
})
```

## Refs

根据初始值推断类型
```javascript
import { defineComponent, ref } from 'vue'

export default defineComponent(() => {
  const year = ref(2020)
  const result = year.value.split('') // => Property 'split' does not exist on type 'number'
})
```

有时需要为 ref 的内部值指定复杂类型。可以在调用 ref 重写默认推理时传递一个泛型参数：
```javascript
import { defineComponent, ref } from 'vue'

export default defineComponent(() => {
  const year = ref<string | number>('2020') // year's type: Ref<string | number>
  year.value = 2020 // ok!
})
```
TIP：如果泛型的类型未知，建议将 ref 转换为 Ref&lt;T&gt;

## reactive

声明 reactive 的时候推荐使用接口

```javascript
import { defineComponent, reactive } from 'vue'

interface Student {
  name: string
  age?: number
}

export default defineComponent(() => {
  const student = reactive<Student>({ name: '唐僧' })

  // or
  const student: Student = reactive({ name: '悟空' })

  // or
  const student = reactive({ name: '八戒', age: 100 }) as Student
})
```

## computed

计算值将根据返回值自动推断类型
```javascript
import { defineComponent, ref } from 'vue'

export default defineComponent(() => {
  let count = ref(0)

  // readonly
  const doubleCount = computed(() => count.value * 2)

  const result = doubleCount.value.split('') // => Property 'split' does not exist on type 'number'
})
```

## 路由参数解耦：[出处](https://www.cnblogs.com/chanwahfung/p/12543103.html)

一般在组件内使用路由参数，大多数人会这样做
```javascript
export default {
  methods: {
   getParamsId() {
     return this.$route.params.id
   }
 }
}
```

在组件中使用 $route 会使之与其对应路由形成高度耦合，从而使组件只能在某些特定的 URL 上使用，限制了其灵活性。

正确的做法是通过 props 解耦

```javascript
const router = new VueRouter({
  routes: [{
    path: '/user/:id',
    component: User,
    props: true
  }]
})
```

将路由的 props 属性设置为 true 后，组件内可通过 props 接收到 params 参数

```javascript
export default {
  props: ['id'],
  methods: {
    getParamsId() {
      return this.id
    }
  }
}
```

另外你还可以通过函数模式来返回 props

```javascript
const router = new VueRouter({
  routes: [{
    path: '/user/:id',
    component: User,
    props: (route) => ({
      id: route.query.id
    })
  }]
})
```

## 优雅注册插件

插件通常用来为 Vue 添加全局功能。像常用的 vue-router、vuex 在使用时都是通过 Vue.use 来注册的。Vue.use 内部会自动寻找 install 方法进行调用，接受的第一个参数是 Vue 构造函数。

一般在使用组件库时，为了减小包体积，都是采用按需加载的方式。如果在入口文件内逐个引入组件会让 main.js 越来越庞大，基于模块化开发的思想，最好是单独封装到一个配置文件中。配合上 Vue.use，在入口文件使用能让人一目了然。

vant.config.js
```javascript
import { Toast, Button } from 'vant'

const components = { Toast, Button }

const componentsHandler = {
  install(Vue){
    Object.keys(components).forEach(key => Vue.use(components[key]))
  }
}

export default componentsHandler
```

main.js
```javascript
import Vue from 'vue'
import vantCompoents from '@/config/vant.config'

Vue.config.productionTip = false

Vue.use(vantCompoents)

new Vue({
  render: h => h(App)
}).$mount('#app')
```

```javascript
```

```javascript
```

```javascript
```

```javascript
```

```javascript
```

```javascript
```

```javascript
```

```javascript
```
