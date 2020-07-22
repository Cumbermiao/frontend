# vue-router分享

## vue-router原理
vue-router的核心原理就是：通过改变 URL，在不重新请求页面的情况下，更新页面视图。

vue-router在实现单页前端路由时，提供了三种方式：hash模式，history模式，abstract模式，根据模式（mode）参数来决定采用哪种方式。
```javascript
import VueRouter from 'vue-router'
Vue.use(VueRouter)

const router = new VueRouter({
  mode: 'history',
  routes: [...]
})

new Vue({
  router
  ...
})
```
### 路由的三种模式
1. hash模式  
    hash即浏览器url中#后面的内容，包含#。hash是URL中的锚点，代表的是网页中的一个位置，单单改变#后的部分，浏览器只会加载相应位置的内容，不会重新加载页面。也就是说：
    * 即#是用来指导浏览器动作的，对服务器端完全无用，HTTP请求中，不包含#。
    * 每一次改变#后的部分，都会在浏览器的访问历史中增加一个记录，使用”后退”按钮，就可以回到上一个位置。
    例如：xxx.com/#/id=5 请求地址为 xxx.com
2. History模式
    HTML5 History API提供了一种功能，能让开发人员在不刷新整个页面的情况下修改站点的URL，就是利用 history.pushState API 来完成 URL 跳转而无须重新加载页面；
    由于hash模式会在url中自带#，如果不想要很丑的 hash，我们可以用路由的 history 模式，只需要在配置路由规则时，加入"mode: 'history'",这种模式充分利用 history.pushState API 来完成 URL 跳转而无须重新加载页面。
    例如：xxx.com/id=5 请求地址为 xxx.com/id=5，如果后端没有对应的路由处理，就会返回404错误；
3. abstract模式
    abstract模式是使用一个不依赖于浏览器的浏览历史虚拟管理后端。

### hash模式和History模式区别
History模式的pushState、replaceState参数中的新URL可为同源的任意URL（可为不同的html文件），而hash只能是同一文档
history.pushState/replaceState(state, title, url);
* state：存储JSON字符串，可以用在popstate事件中。
* title：现在大多数浏览器不支持或者忽略这个参数，最好用null代替
* url：任意有效的URL，用于更新浏览器的地址栏，并不在乎URL是否已经存在地址列表中。更重要的是，它不会重新加载页面。

## 导航守卫
1. 全局前置守卫
    ```javascript
    const router = new VueRouter({ ... })

    router.beforeEach((to, from, next) => {
        // ...
    })
    ```   
    * to:Router:即将要进入的目标路由对象
    * form:Router:当前导航真要离开的路由
    * next:Function：一定要调用该方法来resolve这个钩子。执行效果依赖next方法的调用参数。   
        + `next()`: 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。  
        + `next(false)`: 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。
        + `next('/')` 或者 `next({ path: '/' })`: 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 next 传递任意位置对象，且允许设置诸如 replace: true、name: 'home' 之类的选项以及任何用在 router-link 的 to prop 或 router.push 中的选项。
        + `next(error)`: (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调。
2. 全局解析守卫
    在2.5.0+你可以用`router.beforeResolve`注册一个全局守卫。这和`router.beforeEach`类似，区别就是在导航被确认之前，同时在所有组件内守卫和异步路由组件被解析之后，解析守卫就被调用。
3. 全局后置钩子
    你也可以注册全局后置钩子，然而和守卫不同的是，这些钩子不会接受 next 函数也不会改变导航本身：
    ```javascript
    router.afterEach((to, from) => {
        // ...
    })
    ``` 
4. 路由独享的守卫
    你可以在路由配置上直接定义`beforeEnter`守卫：
    ```javascript
    const router = new VueRouter({
        routes: [
            {
            path: '/foo',
            component: Foo,
            beforeEnter: (to, from, next) => {
                // ...
            }
            }
        ]
    })
    ``` 
5. 组件内的守卫  
    * beforeRouteEnter  
    * beforeRouteUpdate (2.2 新增)  
    * beforeRouteLeave  
    ```javascript
    const Foo = {
        template: `...`,
        beforeRouteEnter (to, from, next) {
            // 在渲染该组件的对应路由被 confirm 前调用
            // 不能 获取组件实例 `this`
            // 因为当守卫执行前，组件实例还没被创建
        },
        beforeRouteUpdate (to, from, next) {
            // 在当前路由改变，但是该组件被复用时调用
            // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
            // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
            // 可以访问组件实例 `this`
        },
        beforeRouteLeave (to, from, next) {
            // 导航离开该组件的对应路由时调用
            // 可以访问组件实例 `this`
        }
    }
    ```
    `beforeRouteEnter` 守卫 不能 访问 `this`，因为守卫在导航确认前被调用，因此即将登场的新组件还没被创建。不过，你可以通过传一个回调给 `next`来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。
    ```javascript
    beforeRouteEnter (to, from, next) {
        next(vm => {
            // 通过 `vm` 访问组件实例
        })
    }
    ```
    注意 `beforeRouteEnter` 是支持给 `next` 传递回调的唯一守卫。对于 `beforeRouteUpdate` 和 `beforeRouteLeave` 来说，`this` 已经可用了，所以不支持传递回调，因为没有必要了。
    ```javascript
    beforeRouteUpdate (to, from, next) {
        // just use `this`
        this.name = to.params.name
        next()
    }
    ```
    这个离开守卫通常用来禁止用户在还未保存修改前突然离开。该导航可以通过 `next(false)` 来取消。
    ```javascript
    beforeRouteLeave (to, from, next) {
        const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
        if (answer) {
            next()
        } else {
            next(false)
        }
    }  
    ```

## 完整的导航  
1. 导航被触发。       
2. 在失活的组件里调用 beforeRouteLeave 守卫。  
3. 调用全局的 beforeEach 守卫。  
4. 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。  
5. 在路由配置里调用 beforeEnter。  
6. 解析异步路由组件。  
7. 在被激活的组件里调用 beforeRouteEnter。  
8. 调用全局的 beforeResolve 守卫 (2.5+)。  
9.  导航被确认。  
10. 调用全局的 afterEach 钩子。  
11. 触发 DOM 更新。     
12. 用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。

## 路由组件传参
路由传参可以通过/hello/:id、query、params和props，下面着重介绍一下props传参。   
优点：使用props传参的方式，就可以将此组件放入别的组件里面用，而不是单独作为一个页面存在了。因为使用/hello/:id、query、params这些方法传参的时候，将其作为组件来用的时候，我们无法将id传递给它，而使用props的话是可以的。使用props传参，这样在同一个路由下面，你显示的所有页面，拿到的 this.$route 都是一样的。
### Hello.vue
```javascript
    <template>
    <div>
        <h2 class="hello">Hello {{ name }} {{ $attrs }}</h2>
    </div>
    </template>

    <script>
    export default {
        name: 'TestHello',
        props: {
            name: {
                type: String,
                default: 'Vue!'
            }
        },
        mounted() {
            console.log(this.name)
        }
    }
    </script>
```
### router.js
```javascript
    //方法1
    {
        path: '/hello/:name',
        name: 'TestHello',
        component: () => import('@/view/hello'),
        props: { name: 'world' },
        meta: {
            title: 'TestHello',
            asideHidden: true
        }
    }
    //方法2： props 要根据 query 进行传递
    {
        path: '/hello/:name',
        name: 'TestHello',
        component: () => import('@/view/hello'),
        props: (route) => ({ query: route.query.q }),
        meta: {
            title: 'TestHello',
            asideHidden: true
        }
    }
```



## addRoutes
本项目中有运用，可以参考或者自行百度了解

## 响应路由参数的变化
    当使用路由参数时，例如从 /user/foo 导航到 /user/bar，原来的组件实例会被复用。因为两个路由都渲染同个组件，比起销毁再创建，复用则显得更加高效。不过，这也意味着组件的生命周期钩子不会再被调用（created、mounted）。

复用组件时，想对路由参数的变化作出响应的话，有以下两种方法：   
1. watch (监测变化) $route 对象   
    ```javascript
    const User = {
        template: '...',
        watch: {
            $route(to, from) {
            // 对路由变化作出响应...
            }
        }
    }
    ```  
2. 使用 2.2 中引入的 beforeRouteUpdate 导航守卫:  
    ```javascript
    const User = {
        template: '...',
        beforeRouteUpdate (to, from, next) {
          if(to.fullPath!=from.fullPath){
            next()
            // 对路由变化作出响应...
          }
        }
    }
    ```  
3. 用 :key 来阻止“复用”
    ```javascript
    <router-view :key="$route.fullPath"></router-view>
    ```

## 案例
1. 项目v2.1中，需求：点击head中消息通知弹框中，点击更多时，跳转到通知消息列表页（/info/list）。            
    * 原本使用的是  
        `<router-link to='/info/list'>更多</router-link>`    
        此种写法，可以在InfoList页面多次点击更多，页面无接口请求，且不会报错
    * 但是现在需求，点击更多的时候，隐藏通知弹框。所以可做如下修改：  
        `<span onClick={this.toMore}>更多</span>`     
        ```javascript
            toMore() {
                this.isShowPopover = false
                this.$router.push({
                    name: 'InfoList'
                })
            }
        ```  
        此种写法，页面报错Uncaught (in promise) Error: Avoided redundant navigation to current location: "/info/list".(路由重复)
    1. 判断当前路由，控制是否进行页面跳转
        ```javascript
        toMore() {
            this.isShowPopover = false
            // 不加判断会报错，路由重复
            if (this.$route.name != 'InfoList') {
                this.$router.push({
                    name: 'InfoList'
                })
            }
        }
        ```  
    2. 在router的配置文件中（router -> index.js）加上以下代码。本项目加在index_back.js。   
        ```javascript
        const originalPush = Router.prototype.push
        Router.prototype.push = function push (location) {
            return originalPush.call(this, location).catch(err => err)
        }
        ```  
2. ` beforeEach`通过`next`来控制路由跳转的场景。    
    1. 验证用户是否登录（若未登录，且当前非登录页面，则自动重定向登录页面）；   
    2. 判断用户权限；    
    3. 用户输入的路路径是否存在，不存在的情况下如何处理，重定向到哪个页面。   
    ```javascript
    router.beforeEach((to, form, next) => {
        if (to.name === 'ZoneOrderIndex') {
            // 判断否登陆
            if (getUserId()) {
                next()
            }else{
                router.replace('/login')
            }
        } else {
            next()
        }
    })
    ```
3. 在列表页面进行条件筛选查询或者切换页数，查询到对应的数据后查询详情，跳转详情页面，然后在返回，列表默认会清空查询条件，并且显示第一页所有数据。这两种场景给用户体验都不好，所以就需要使用keep-alive对列表页面进行页面缓存。
    1. `<keep-alive>`包裹的组件会新增两个生命周期函数`activated`和`deactivated`。 
    2. `<keep-alive>`包裹的组件，加载过的页面，再次进入时，是不会执行页面第一次进入时的部分生命周期函数。(页面初次进入时，钩子触发顺序是created->mounted->activated。退出时触发deactivated。当再次进入（前进或者后退）时，只触发activated。事件挂载的方法等，只执行一次的放在 mounted 中；组件每次进去执行的方法放在 activated 中)
    3. 两个属性`include`和`exclude`可以让`<keep-alive>`实现有条件的进行缓存。`include`包含的组件会被进行缓存，`exclude`包含的组件不会被缓存。

    ### 实现方法:
    1. 使用include控制需要缓存的页面
    ```javascript
    // layout.vue
    <template>
      <div>
        <keep-alive :include='cashViews'>
          <router-view></router-view>
        </keep-alive>
      </div>
    </template>  
    <script>
    export default{
      data() {
        return {
          // 要缓存的组件
          cashViews: ['list']
        }
      }
    }
    </script>  
    ```
    ```javascript
    // list.vue
    <script>
      export default{
        // 在组件内路由守卫钩子函数中处理逻辑
        beforeRouteEnter (to, from, next) {
          if (from.name === 'Index') { 
            // 处理页面缓存后，返回首页再次进入缓存页时数据为更新
            // 该生命周期无法获取到this，因此把vm实例当作参数传递
            next(vm => {
              vm.pages.pageNum = 1
              // 因为我这里查询表单是动态的，所以首页进入时需要调用一次，在该方法中请求返回后调用了获取列表数据getTableDatas方法
              vm.getQueryList()
            })
          } else if (!from.name) { 
              // 处理刷新页面时，获取动态表单方法未执行，导致表单无法加载
            next(vm => {
              vm.getQueryList()
            })
        } else {
            // 详情返回时只更新列表数据，因为如果在详情页面做了操作，列表数据状态会改变，其他使用缓存
          next(vm => {
            vm.getTableDatas()
          })
        }
      }
    }
    </script>
    ```
    2. 根据v-if控制显示的router-view
        1. 在路由表routes中配置meta属性，新增keepAlive属性，需要缓存的页面设置keepAlive为true.
        ```javascript
        //index_back.js
        {
          path: '/list',
          name: 'List',
          component: List,
          meta: {
            keepAlive: true
          }
        }
        ```
        2. 根据当前访问路由的keepAlive值控制是否缓存组件
        ```javascript
        //layou.vue
        <template>
          <keep-alive>
            <router-view v-if="$route.meta.keepAlive"></router-view>
          </keep-alive>
          <router-view v-if="!$route.meta.keepAlive"></router-view>
        </template>
        ```
        3. 在组件的守卫钩子中修改keepAlive的值，控制页面缓存与否
        ```javascript
        // List.vue
        export default {
           /* 从List组件离开时,修改keepAlive值为true，确保返回List页面时使用缓存不刷新页面*/
          beforeRouteLeave(to, from, next) {
            from.meta.keepAlive = true
            next()
          }
        }
        ```