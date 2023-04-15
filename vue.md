# VUE
## 安装
## 基本逻辑
el标签表示占位符，代表哪个view模板。
script中引入vue
new vue框架写数据和方法
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层 模板-->
<div id = "app">
    {{message}}
</div>

<!--导入vue.js-->
<script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
<script>

    var vm = new Vue({
        el:"#app",
        // model:数据
        data:{
            message:"hello vue"
        }
    });
</script>
</body>
</html>
```
## 判断，循环
## 事件
vm中定义methods方法
```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Title</title>
</head>
<body>
<!--view层 模板-->
<div id = "app">
  <button v-on:click="sayHi">click me</button>
</div>

<!--导入vue.js-->
<script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
<script>

  var vm = new Vue({
    el:"#app",
    // model:数据
    data:{
      message: "提交了"
    },
    methods: { // 方法必须定义在Vue中的Methods对象中
      sayHi:function () {
        alert(this.message);
      }
    }
  });
</script>
</body>
</html>
```
## vue七个常用属性
1. el属性
2. data属性
3. template属性
4. methods属性
5. render属性
6. computed属性
7. watch属性
## 双向绑定
数据发生变化，视图发生变化，视图发生变化，数据发生变化
```v-model="message"```v-model绑定属性，就可以将它显示
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层 模板-->
<div id = "app">
    输入的文本：<input type="text" v-model="message">{{message}}

    <select v-model="option">
        <option value="" disabled>--请选择--</option>
        <option>lyf</option>
        <option>zq</option>
        <option>zz</option>
    </select>
    <span>选中的值：{{option}}</span>
</div>

<!--导入vue.js-->
<script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
<script>

    var vm = new Vue({
        el:"#app",
        // model:数据
        data:{
            message: "123",
            option: ""
        }
    });
</script>
</body>
</html>
```
## vue组件
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层 模板-->
<div id = "app">
    <!--
    v-for循环取值
    v-bind绑定v-for中的值，并将它作为name通过组件中的props传递给组件
    组件：传递给组件中的值
    -->
    <frist-component v-for="item in items" v-bind:name="item"></frist-component>
</div>

<!--导入vue.js-->
<script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
<script>
    //定义组件
    Vue.component("fristComponent",{
        props:["name"],
        template:'<li>{{name}}</li>'
    });


    var vm = new Vue({
        el:"#app",
        // model:数据
        data:{
            items:["lyf","zq","zz"]
        }

    });
</script>
</body>
</html>
```
## Axios异步通信
- mounted()渲染点
- ```axios.get("data.json").then(response=>(console.log(this.info=response.data)));```
- data(){}获取axios接收到的数据
- {{}}展示数据
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层 模板-->
<div id = "vue" v-cloak>
    <div>{{info.name}}</div>
    <div>{{info.address.street}}</div>
    <a v-bind:href="info.url">点击</a>
</div>

<!--导入vue.js-->
<script src="https://cdn.jsdelivr.net/npm/vue@2.7.8/dist/vue.js"></script>
<script src="https://unpkg.com/axios/dist/axios.min.js"></script>
<script type="text/javascript">

    var vm = new Vue({
        el:"#vue",
        // 返回数据方法:数据
        data() {
            return{
                info:{
                    name:null,
                    url:null,
                    address: {
                        city: null,
                        street: null,
                        country: null
                    }
                }
            }
        },

        mounted() {
            // 通信方式
            axios.get("data.json").then(response=>(console.log(this.info=response.data)));
        }
    });
</script>
</body>
</html>
```
## 计算属性
计算结果缓存下来
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层 模板-->
<div id = "app">
    <p>currentTime1 {{currentTime1()}}</p>
    <p>currentTime2 {{currentTime2}}</p> 
</div>

<!--导入vue.js-->
<script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
<script>

    var vm = new Vue({
        el:"#app",
        data:{
            message:"hello,lyf"
        },
        methods: { //通过方法调用
            currentTime1:function () {
                return Date.now();
            }
        },
        computed: {// 通过属性调用
            currentTime2:function () {
                return Date.now();
            }
        }
    });
</script>
</body>
</html>
```
## 插槽
把原来需要放置模板的地方，用component组合起来，并且绑定数据
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层 模板-->
<div id = "app">

    <todo>
        <todo-title slot="todo-title" :title="title"></todo-title>
        <todo-items slot="todo-items" v-for="item in todoItems" :item="item"></todo-items>
    </todo>

</div>

<!--导入vue.js-->
<script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
<script>

    Vue.component("todo",{
        template:"<div>\
        <slot name='todo-title'></slot>\
        <ul>\
        <slot name='todo-items'></slot>\
        </ul>\
        </div>"
    });
    Vue.component("todo-title",{
        props: ["title"],
        template:"<div>{{title}}</div>"
    });
    Vue.component("todo-items",{
        props: ["item"],
        template:"<li>{{item}}</li>"
    });
    var vm = new Vue({
        el:"#app",
        // model:数据
        data:{
            title:"列表",
            todoItems:["java","mybatis","redis"]
        }
    });
</script>
</body>
</html>
```
## **难点slot，组件，调用**
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<!--view层 模板-->

<!--id与el标签对应，相当于组件和前端view绑定-->
<!--组件通过插槽和前端绑定，slot-->
<div id = "app">
    <!--todo是vue.component的插槽组件-->
    <todo>
        <!--第一个插槽
            slot表示要绑定的插槽的名称，
            :title是双向绑定数据，v-bind的简写
        -->
        <todo-title slot="todo-title" :title="title"></todo-title>
        <!--第二个插槽
            v-for进行item的遍历（从data的todoItems中）
            v-on:remove 自定义绑定事件remove，绑定到Vue的方法removeItems
        -->
        <todo-items slot="todo-items" v-for="(item,index) in todoItems"
                    :item="item" v-bind:index="index" v-on:remove="removeItems(index)" :key="index"></todo-items>
    </todo>

</div>

<!--导入vue.js-->
<script src="https://cdn.bootcss.com/vue/2.5.16/vue.min.js"></script>
<script>
    /*
        定义组件todo <slot>
        插槽其实类似于模板html，相当于把html抽象出来，可以动态拔插，里面的数据可以改写
        组件之间插槽用name来绑定
    */
    Vue.component("todo",{
        template:"<div>\
        <slot name='todo-title'></slot>\
        <ul>\
        <slot name='todo-items'></slot>\
        </ul>\
        </div>"
    });
    /*
        定义组件todo-title 该组件是todo的子组件
        props:[]从下面的Vue对象中接收data，此时的title与Vue中data中的title对应。
        template: 模板，把title值传递到模板
        此方式将数据变成动态，数据从data中来，放到template中，插入到模板中
    */
    Vue.component("todo-title",{
        props: ["title"],
        template:"<div>{{title}}</div>"
    });

    /*
        定义组件todo-items
        template:模板，中包含一个点击事件click，函数名称为remove
        methods:中设置函数事件
        要删除组件中元素，只能在组件中进行
    */
    Vue.component("todo-items",{
        props: ["item","index"],
        template:"<li>{{item}} <button @click='remove'>删除</button></li>",
        methods:{
            remove:function(index) {
                alert("确定删除");
                // this.$emit 自定义事件分发，绑定view的自定义事件，前端remove调用Vue对象的方法removeItems
                this.$emit("remove",index);
            }
        }
    });
    var vm = new Vue({
        el:"#app",
        // model:数据
        data:{
            title:"列表",
            todoItems:["java","mybatis","redis"]
        },
        /*
            js删除数组元素：splice（start，num）从start开始删除num个
            this代表vm对象
        */
        methods:{
            removeItems:function (index) {
                console.log("删除了"+this.todoItems[index]+"ok");
                this.todoItems.splice(index,1);
            }
        }
    });
</script>
</body>
</html>
```
## vue-cli
1. 安装node.js，查看是否安装成功```node -v```, ```npm -v```
2. 安装镜像 ```npm install -g cnpm --registry=https://registry.npm.taobao.org```
3. 安装```cnpm install vue-cli -g```, 查看是否成功```vue list```
4. 进入到项目目录，```vue init webpack myvue```
5. 4个no，用cnpm
6. 进入项目，```cd myvue``` ，该目录下有package.json 安装环境 ``` npm install``` 打包```npm run dev```