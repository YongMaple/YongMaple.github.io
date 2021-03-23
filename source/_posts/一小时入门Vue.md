---
title: 一小时入门Vue
date: 2017-02-09 18:09:19
categories:
    - 教程
    - Vue
tags:
    - Vue
---
## 引言
- 我是一个Vue的狂热粉丝，使用Vue半年左右时间，写这个文章主要是为了给自己做个系统的回顾，顺便给同样有需要的人参考一下，如果文中有不对的地方，欢迎留言指出。   
- 我一直认为兴趣是最好的老师，在深入学习Vue之前，先跑起来一个简单的Vue的项目，先感受到Vue的魅力，再结合项目看文档会更加简单。   
- 这会是一个简单（极其丑陋）的图书馆里系统，包含服务端和客户端。   
- 跟着我动手写，光看不写学不好

## 准备工作
再开始教程之前，我们先配置好环境
- 安装node，包括npm [https://nodejs.org](https://nodejs.org)
- 安装vue-cli   `npm install -g vue-cli`
> vue-cli 是vue.js的脚手架，用于自动生成vue.js模板工程的
- 安装好yarn（非必须，推荐使用） [https://yarnpkg.com](https://yarnpkg.com)
> yarn是Facebook推出了一款开源JavaScript包管理器，比npm更可靠，且速度更快

## 项目创建
首先执行`vue init webpack-simple vue-curd`，`vue-curd`是文件夹名，会自动创建，会有3个设置项，分别是项目名、描述和作者，一路回车就行了。由于是教学项目，不会使用有太多的依赖，所以这里我们使用`webpack-simple`模版来创建，在实际使用中可以使用`webpack`来创建   

![](http://okmneu7zl.bkt.clouddn.com/vue-cli%E5%88%9D%E5%A7%8B%E5%8C%96%E9%A1%B9%E7%9B%AE.png)

现在能看到vue-cli为我们建好了项目，***后面文中所有的路径根目录都表示该项目目录***

## 服务端搭建
这里我们借助json-server这个工具快速搭建后台管理系统的服务端程序：
- 执行`npm i json-server -g`，Mac加上`sudo`
- 新建`/server`目录，用于防止服务端的文件
- 新建`/server/db.json`文件，作为服务端的数据库文件
- 在`/server/db.json`文件中写入以下数据：
```json
{
  "user": [
    {
      "id": 10000,
      "name": "Jimmy",
      "age": 28,
      "gender": "male"
    },
    {
      "id": 10001,
      "name": "张三",
      "age": 30,
      "gender": "female"
    }
  ],
  "book": [
    {
      "name": "前端从入门到精通",
      "price": 9300,
      "owner_id": 10000,
      "id": 10000
    },
    {
      "id": 10001,
      "name": "Java从入门到放弃",
      "price": 1990,
      "owner_id": 10001
    }
  ]
}

```
- 最后在`/server`目录执行`json-server db.json -w -p 3000`   

现在打开浏览器，访问网址`http://localhost:3000`，可以看到如下界面：  

![](http://okmneu7zl.bkt.clouddn.com/localhost3000.png)

点击Routes下面的user会跳转到`http://localhost:3000/user`，可以看到我们之前在db.json中写入的user数组   

![](http://okmneu7zl.bkt.clouddn.com/json.png)

我们在db.json文件中写入了标准的JSON格式数据，这个json里有一个user数组和一个book数组，这就告诉json-server，我们的数据库里有一个名为user的“表”和一个名为book的“表”，并且表里的数据为xxx，然后json-server就会启动服务器，并且以每个”表”为单位为我们注册一系列标准的RESTFull形式的API接口（路由），以user为例：
- [GET] /user #获取用户列表的接口
- [GET] /user/:id #获取单个用户的接口
- [POST] /user #新增用户的接口
- [PUT] /user/:id #修改用户的接口
- [DELETE] /user/:id #删除用户的接口   

在获取列表的接口中也可以追加查询参数，来限定查询的结果，比如：
- 查询所有男性用户： /user?gender=male
- 查询年龄大于等于20并且小于等于29的用户：/user?age_gte=20&age_lte=29

此外还有分页、排序、匹配、关系查询等查询方式，可以在[这里查看更多](https://github.com/typicode/json-server#routes)   

至此我们就拥有了一套强大的数据接口。

## 客户端搭建
之前我们创建好了项目，但是还没下载依赖，现在根目录执行`yarn install`   

安装好依赖后，执行`yarn run dev`，就能看到下图的欢迎页了   

![](http://okmneu7zl.bkt.clouddn.com/vue-welcome.png)

### 开始改造模版项目
- 删除`/src/assets/`下的图片
- 修改`/src/main.js`改名为`app.js`，***个人习惯，可以不改，不改的话下两步也跳过***
- 修改`/webpack.config.js`中第5行`entry: './src/main.js',`为`entry: './src/app.js',`，这个是入口文件。修改第8行`publicPath: '/dist/',`为`publicPath: '/build/',`，这个是编译后文件的存放路径
- 修改`/index.html`中第9行`<script src="/dist/build.js"></script>`为`<script src="/build/build.js"></script>`
- 将`/src/App.vue`中的内容删除，只留下下面这些
```javascript
<template>
</template>

<script>
</script>

<style>
</style>
```
在Vue中`<template>`中写html，`<script>`中写脚本，`<style>`中写样式

### 添加用户页面
`/src/App.vue`添加如下代码：
```javascript
<template>
    <div>
        <label>姓名：</label>
        <input type="text"><br>
        <label>年龄：</label>
        <input type="number"><br>
        <label>性别：</label>
        <select>
            <option value="">请选择</option>
            <option value="male">男</option>
            <option value="female">女</option>
        </select><br>
        <button type="button">添加</button>
    </div>
</template>
```
可以看到添加页面的样子了，在`<template>`中有一点需要注意，***每个组件只能有一个根节点***，所以我们用`<div>`包起来   

![](http://okmneu7zl.bkt.clouddn.com/1.png)

现在我们需要获取用户输入的数据，在Vue中可以采取双向绑定的方式来实现，我们先在`<script>`中添加如下代码：
```javascript
export default {
    data(){
        return {
            form:{
                name:'',
                age:'',
                gender:''
            }
        }
    }
}
```
这里我们定义了一个`form`对象   

> `data`是个`funciton`，要设置的值是`retrun`回来的一个对象中的属性

下面将实现数据的绑定，改造`<template>`中代码为如下：

```javascript
<div>
    <label>姓名：</label>
    <input type="text" v-model="form.name"><br>
    <label>年龄：</label>
    <input type="number" v-model="form.age"><br>
    <label>性别：</label>
    <select v-model="form.gender">
        <option value="">请选择</option>
        <option value="male">男</option>
        <option value="female">女</option>
    </select><br>
    <button type="button">添加</button>
</div>
```
> 通过`v-model`实现了数据的绑定   

此时`data`中的值就是用户输入的值了，那么我们如何来确认呢？

我们来给添加按钮绑定事件，通过点击添加按钮来弹框显示输入的内容，修改`<button type="button">添加</button>`为`<button type="button" @click="submit">添加</button>`
> Vue中通过`@click`来监听点击事件

并在`<script>`中添加如下代码：

```javascript
export default {
    data(){ ... },
    methods:{
        submit(){
            alert(`输入的姓名：${ this.form.name }，输入的年龄：${ this.form.age }，选择的性别：${ this.form.gender }`)
        }
    }
}
```
来试一下吧

![](http://okmneu7zl.bkt.clouddn.com/2.png)

现在，让我们把这条数据存到我们的数据库中吧，改写`submit`为如下：
```javascript
submit(){
    fetch('http://localhost:3000/user',{
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(this.form)
    }).then(()=>{
        alert("添加成功")
    }).catch(response =>{
        console.log(response);
    })
}
```
这里我们使用`fetch`来提交数据

> Fetch被称为下一代Ajax技术,采用Promise方式来处理数据。是一种简洁明了的API，比XMLHttpRequest更加简单易用。

然后看一下`http://localhost:3000/user`，刚才添加的数据就在里面了   

![](http://okmneu7zl.bkt.clouddn.com/3.png)

### 表单校验
我们的现在可以在应用里添加用户了。但是还存在不小的问题：
- 用户可以输入任意长的名字
- 用户可以输入任意的年龄
- 用户可以不选择性别

盲目信任用户输入的数据，会给系统埋下不小的隐患。这回我们就来把这隐患扼杀在摇篮之中。

表单验证无非就是对用户输入的数据进行有效性、非空性验证，验证失败会在相应的地方显示错误信息，并阻止用户提交表单。

我们需要记录每一个字段当前的有效状态，有效时隐藏错误信息，无效时显示错误信息。

而这个有效/无效，可以在表单值改变的时候进行判断。

要做校验，在Vue中我们第一时间想到的肯定是`computed`、`watch`、`get`、`set`

> [https://cn.vuejs.org/v2/guide/computed.html](https://cn.vuejs.org/v2/guide/computed.html)   

思路大致上就是在form值发生改变时去同步的执行校验，如果校验失败，显示错误信息，有效则隐藏

让我们来试试，改造一下代码：

```html
<template>
    <div>
        <label>姓名：</label>
        <input type="text" v-model="form.name.value"><br>
        <label>年龄：</label>
        <input type="number" v-model="form.age.value"><br>
        <label>性别：</label>
        <select v-model="form.gender.value">
            <option value="">请选择</option>
            <option value="male">男</option>
            <option value="female">女</option>
        </select><br>
        <button type="button" @click="submit">添加</button>
    </div>
</template>

<script>
export default {
    data(){
        return {
            form:{
                name:{
                    value:'',
                    valid:false
                },
                age:{
                    value:'',
                    valid:false
                },
                gender:{
                    value:'',
                    valid:false
                }
            }
        }
    },
    methods:{
        submit(){
            fetch('http://localhost:3000/user',{
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(this.form)
            }).then(()=>{
                alert("添加成功")
            }).catch(response =>{
                console.log(response);
            })
        }
    },
    computed:{
        'form.name.valid':function(){
            return this.form.name.value.length > 10 ? false : true
        }
    }
}
</script>

<style>
</style>
```
尝试了没有？这段代码是无效的，用`watch`也是无效的，这里就不写出来了

~~受ES5的限制，Vuejs不能检测到对象属性的变化~~`这句话是错的，下面组件化的原因就改成使代码看着简洁点吧。所以百度出来的东西不一定是对的。。`

那么，我们该如何来做呢？

既然Vue只能检测到data的属性，那么我们可以使用组件化来实现，同时使我们的代码简洁，考虑到之后的修改页面也有类似的页面结构，我们将整个form封成组件

> 组件（Component）是 Vue.js 最强大的功能之一。组件可以扩展 HTML 元素，封装可重用的代码。在较高层面上，组件是自定义元素， Vue.js 的编译器为它添加特殊功能。在有些情况下，组件也可以是原生 HTML 元素的形式，以 is 特性扩展。

- 新建`/src/components/`目录
- 在`/src/components/`下新建`form.vue`和`formItem.vue`
- 将之前的代码搬到`formItem.vue`，如下：

> 组件实例的作用域是孤立的。这意味着不能并且不应该在子组件的模板内直接引用父组件的数据。可以使用 props 把数据传给子组件。

```html
<template>
<div>
    <label>{{label}}：</label>
    <input v-if="type === 'String'" type="text" v-model="value">
    <input v-if="type === 'Number'" type="number" v-model="value">
    <select v-if="type === 'Select'" v-model="val">
        <option value="">请选择</option>
        <option value="male">男</option>
        <option value="female">女</option>
    </select>
    <br>
</div>
</template>
<script>
export default {
    props: {
        label: {
            type: String,
            default: ''
        },
        type: {
            type: String,
            default: ''
        },
        value: {
            type: null,
            default: ''
        }
    }
}
```

我们使用`props`接收父组件传递过来的参数，用`v-if`来判断该显示什么，像在`data`中使用一样用`v-model`来绑定数据

下面来写`formItem.vue`的父组件`form.vue`

```html
<template>
<div>
    <form-item v-for="(item,key) in form"
    :value="item.value"
    :type="item.type"
    :label="item.label">
    </form-item>
</template>
<script>
import formItem from './formItem.vue'
export default {
    props: {
        form: Object
    },
    components: {
        formItem
    }
}
</script>
```

这里我将`formItem.vue`引入，并通过`components`在Vue中注册，这里我使用驼峰命名，在使用时直接通过`<form-item>`来使用

通过`props`来获取父组件`App.vue`传来的数据，然后使用`v-for`遍历对象的属性，通过`:value`、`:type`、`:label`将值传给子组件`formItem.vue`

最后修改一下`App.vue`

```html
<template>
<div>
    <my-form :form="form"></my-form>
    <button type="button" @click="submit">提交</button>
</div>
</template>
<script>
import form from './components/form.vue'
export default {
    data() {
        return {
            form: {
                name: {
                    label: '姓名',
                    value: '',
                    type: 'String'
                },
                age: {
                    label: '年龄',
                    value: '',
                    type: 'Number'
                },
                gender: {
                    label: '性别',
                    value: '',
                    type: 'Select'
                }
            }
        }
    },
    methods: { ... },
    components: {
        'my-form': form
    }
}
</script>
```
现在组件封装好了，但是数据还是没校验。我们先来设定一下规则，继续修改`/src/App.vue`

```html
<template>
<div>
    <my-form :form="form" :rules="rules"></my-form>
    <button type="button" @click="submit">提交</button>
</div>
</template>
<script>
import form from './components/form.vue'
export default {
    data() {
        return {
            form: {
                name: {
                    label: '姓名',
                    value: '',
                    type: 'String'
                },
                age: {
                    label: '年龄',
                    value: '',
                    type: 'Number'
                },
                gender: {
                    label: '性别',
                    value: '',
                    type: 'Select'
                }
            },
            rules: {    //校验规则
                name: [{
                        max: 10,
                        min: 2,
                        message: '名字必须在2-10个字符之间'
                    },
                    {
                        type: 'String',
                        message: '名字格式不正确'
                    }
                ],
                age: [{
                        max: 100,
                        min: 0,
                        message: '年龄必须在0-100岁之间'
                    },
                    {
                        type: 'Number',
                        message: '年龄只能是数字'
                    }
                ],
                gender: [{
                    value: ['male', 'female'],
                    message: '请选择性别'
                }]
            },
            validateState: true //表单校验状态
        }
    },
    methods: { ... },
    components: {
        'my-form': form
    }
}
</script>

<style>

</style>

```
在`components`中，这次我们没有和`form.vue`中一样注册组件，而采取自定义名称的方式`my-form`

在这里，我们设定每个属性的校验规则都是一个数组，根据不同的情况可以显示不同的错误信息
- `max`字符串最大长度或number最大值
- `min`字符串最小长度或number最小值
- `type`指定类型
- `value`值只能是该数组中的值
- `message`错误信息

然后通过`:rules`将规则传给`form.vue`，下面是`form.vue`的代码

```html
<template>
<div>
    <form-item
    v-for="(item,key) in form"
    :field="item.field"
    :value="item.value"
    :type="item.type"
    :rule="item.rule"
    :label="item.label">
    </form-item>
</template>
<script>
import formItem from './formItem.vue'
export default {
    props: {
        form: Object,
        rules: Object
    },
    created: function() {
        let form = this.form
        let rules = this.rules
        for (let key in form) {
            form[key].rule = rules[key]
            form[key].field = key
            form[key].validate = false
        }
        this.form = form
    },
    components: {
        formItem
    }
}
</script>

```
> 每个 Vue 实例在被创建之前都要经过一系列的初始化过程。例如，实例需要配置数据观测(data observer)、编译模版、挂载实例到 DOM ，然后在数据变化时更新 DOM 。在这个过程中，实例也会调用一些***生命周期钩子*** ，这就给我们提供了执行自定义逻辑的机会。例如`created`这个钩子在实例被创建之后被调用

生命周期：

![](http://okmneu7zl.bkt.clouddn.com/lifecycle.png)

在组件`created`中，我们将rules整合到form中，方便后面使用，并添加field(属性名)，validate（校验状态)字段

在`<form-item>`中，像之前一样将值传给`formItem.vue`

下面来看下`formItem.vue`

```html
<template>
<div>
    <label>{{label}}：</label>
    <input v-if="type === 'String'" type="text" v-model="val">
    <input v-if="type === 'Number'" type="number" v-model="val">
    <select v-if="type === 'Select'" v-model="val">
            <option value="">请选择</option>
            <option value="male">男</option>
            <option value="female">女</option>
        </select>
    <span>{{message}}</span>
    <br>
</div>
</template>
<script>
export default {
    data() {
        return {
            message: '',
            val: ''
        }
    },
    props: {
        field: {
            type: String
        },
        label: {
            type: String,
            default: ''
        },
        type: {
            type: String,
            default: ''
        },
        value: {
            type: null,
            default: ''
        },
        rule: {
            type: Array
        },
    },
    watch: {
        val() {
            let _this = this //试试不这么做会怎样，注释掉这行，将下面的_this都换成this，看看this指的是谁
            let valid_msg = ''
            let validateState = true
            if (_this.rule) {
                _this.rule.forEach(function(v, i) {
                    var keys = Object.keys(v)
                    if (keys) {
                        keys.forEach(function(k) {
                            if (k != 'message') {
                                let valide = eval(k)
                                if (!valide(_this.val, v[k])) {
                                    valid_msg = v.message ? v.message : '系统异常'
                                    validateState = false
                                }
                            }
                        })
                    } else {
                        validateState = false
                        valid_msg = '系统异常'
                    }
                })
            }
            _this.message = valid_msg
        },
        value(){
            this.val = this.value
        }
    }
}

function max(v, r) {
    if (typeof v == 'string')
        return v.length > r ? false : true
    else if (typeof v == 'number')
        return v > r ? false : true

}

function min(v, r) {
    if (typeof v == 'string')
        return v.length < r ? false : true
    else if (typeof v == 'number')
        return v < r ? false : true
}

function type(v, r) {
    return typeof v == r.toLowerCase() ? true : false
}

function value(v, r) {
    return r.indexOf(v) != -1 ? true : false
}
</script>

```
***尝试一下注释中说的***，看看this指的是什么

到现在，我们已经能够实现校验了，但是问题又来了，现在`App.vue`中的`form`里面的属性值是没有的（动手试试），如`form.name.value`值一直是空的

> prop 是单向绑定的：当父组件的属性变化时，将传导给子组件，但是不会反过来。这是为了防止子组件无意修改了父组件的状态——这会让应用的数据流难以理解。

那么我们如何将`formItem.vue`中的值传给`App.vue`，这里是最终形态的`formItem.vue`

```html
<template>
<div>
    <label>{{label}}：</label>
    <input v-if="type === 'String'" type="text" v-model="val">
    <input v-if="type === 'Number'" type="number" v-model="val">
    <select v-if="type === 'Select'" v-model="val">
            <option value="">请选择</option>
            <option value="male">男</option>
            <option value="female">女</option>
        </select>
    <span>{{message}}</span>
    <br>
</div>
</template>
<script>
export default {
    data() {
        return {
            message: '',
            val: ''
        }
    },
    props: {
        field: {
            type: String
        },
        label: {
            type: String,
            default: ''
        },
        type: {
            type: String,
            default: ''
        },
        value: {
            type: null,
            default: ''
        },
        rule: {
            type: Array
        },
    },
    watch: {
        val() {
            let _this = this //试试不这么做会怎样，注释掉这行，将下面的_this都换成this，看看this指的是谁
            let valid_msg = ''
            let validateState = true
            if (_this.rule) {
                _this.rule.forEach(function(v, i) {
                    var keys = Object.keys(v)
                    if (keys) {
                        keys.forEach(function(k) {
                            if (k != 'message') {
                                let valide = eval(k)
                                if (!valide(_this.val, v[k])) {
                                    valid_msg = v.message ? v.message : '系统异常'
                                    validateState = false
                                }
                            }
                        })
                    } else {
                        validateState = false
                        valid_msg = '系统异常'
                    }
                })
            }
            _this.message = valid_msg
            _this.$emit("transferValue", [_this.field, _this.val,validateState])
        },
        value(){
            this.val = this.value
        }
    }
}

function max(v, r) {
    if (typeof v == 'string')
        return v.length > r ? false : true
    else if (typeof v == 'number')
        return v > r ? false : true

}

function min(v, r) {
    if (typeof v == 'string')
        return v.length < r ? false : true
    else if (typeof v == 'number')
        return v < r ? false : true
}

function type(v, r) {
    return typeof v == r.toLowerCase() ? true : false
}

function value(v, r) {
    return r.indexOf(v) != -1 ? true : false
}
</script>

```
我们使用自定义事件`$emit`将值传递出去，这里事件名是`transferValue`，在父组件中，只要监听`transferValue`事件就行了

下面是完整的`form.vue`

```html
<template>
<div>
    <form-item
    v-for="(item,key) in form"
    v-on:transferValue="transferValue"
    :field="item.field"
    :value="item.value"
    :type="item.type"
    :rule="item.rule"
    :label="item.label">
    </form-item>
</template>
<script>
import formItem from './formItem.vue'
export default {
    props: {
        form: Object,
        rules: Object
    },
    created: function() {
        let form = this.form
        let rules = this.rules
        for (let key in form) {
            form[key].rule = rules[key]
            form[key].field = key
            form[key].validate = false
        }
        this.form = form
    },
    methods: {
        transferValue(args) {
            let [field, value, validate] = [...args]
            this.form[field].validate = validate
            let flag = true
            for (var key in this.form) {
                if (!this.form[key].validate)
                    flag = false
            }
            this.$emit('changeValue', [field, value, flag])
        }
    },
    components: {
        formItem
    }
}
</script>

```
我们看一下`<form-item>`中，`v-on:transferValue="transferValue"`就是上面说的监听事件，后面的`transferValue`是`methods`中的方法名

下面是完整的`App.vue`

```html
<template>
<div>
    <my-form :form="form" :rules="rules" v-on:changeValue="changeValue"></my-form>
    <button type="button" @click="submit">提交</button>
</div>
</template>
<script>
import form from './components/form.vue'
export default {
    data() {
        return {
            form: {
                name: {
                    label: '姓名',
                    value: '',
                    type: 'String'
                },
                age: {
                    label: '年龄',
                    value: '',
                    type: 'Number'
                },
                gender: {
                    label: '性别',
                    value: '',
                    type: 'Select'
                }
            },
            rules: {     //校验规则
                name: [{
                        max: 10,
                        min: 2,
                        message: '名字必须在2-10个字符之间'
                    },
                    {
                        type: 'String',
                        message: '名字格式不正确'
                    }
                ],
                age: [{
                        max: 100,
                        min: 0,
                        message: '年龄必须在0-100岁之间'
                    },
                    {
                        type: 'Number',
                        message: '年龄只能是数字'
                    }
                ],
                gender: [{
                    value: ['male', 'female'],
                    message: '请选择性别'
                }]
            },
            validateState: true //表单校验状态
        }
    },
    methods: {
        changeValue(args) {
            let [field, value, validateState] = [...args]
            this.form[field].value = value
            this.validateState = validateState
        },
        submit() {
            if(this.validateState){
                let form = this.form
                let parms = {
                    name: form.name.value,
                    age: form.age.value,
                    gender: form.gender.value
                }
                fetch('http://localhost:3000/user', {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(parms)
                }).then(() => {
                    alert("添加成功")
                }).catch(response => {
                    console.log(response);
                })
            }else{
                alert('数据错误，请修改后提交')
            }
        }
    },
    components: {
        'my-form': form
    }
}
</script>

<style>

</style>

```

现在，来回顾下流程：
- `formItem.vue`中`watch`了`val`
- 在`val`发生变化时，遍历对应的`rule`
- 当有不匹配规则时，将错误信息改成`rule`中对应的错误信息
- 将属性名、输入值、校验状态通过`$emit`发送给`form.vue`
- `form.vue`中将通过`transferValue`收到传递的参数
- 将`form`中对应属性名的属性校验状态修改为传递来的值
- 遍历`form`中所有属性的校验状态，最后将属性名、输入值、整个`form`的校验状态通过`changeValue`传给`App.vue`
- `App.vue`中通过`changeValue`接受参数，将`form`对应属性的值更新，并修改整个`form`的校验状态`validateState`
- 最后在`submit`中判断`validateState`，来判断是否允许提交

现在我们有了一个完整的添加页面了

### 加入路由及列表页面

现在，我们需要做一个列表页面来展示数据库中的数据，那么，该写在哪呢？添加页面我们目前是直接写在`App.vue`里的

之前我们只使用了`json-server`的`POST`请求，现在我们还要用到`GET`请求，所以对我们所有的`request`进行封装
- 新建`/src/services/`目录并新建`request.js`
- 将下面的代码复制到`request.js`中

```javascript
export default function request(method, url, body) {
    method = method.toUpperCase();
    if (method === 'GET') {
        body = undefined;
    } else {
        body = body && JSON.stringify(body);
    }

    return fetch(url, {
            method,
            headers: {
                'Content-Type': 'application/json',
            },
            body
        })
        .then((res) => {
            if (res.status >= 200 && res.status < 300) {
                return res.json();
            } else {
                alert('系统异常')
            }
        });
}

export const get = url => request('GET', url);
export const post = (url, body) => request('POST', url, body);
export const put = (url, body) => request('PUT', url, body);
export const del = (url, body) => request('DELETE', url, body);

```

- 新建目录`/src/views`
- 新建文件`/src/views/list.vue`
- 新建文件`/src/views/add.vue`
- 将`/src/App.vue`中的代码复制到`/src/views/add.vue`
- 修改`add.vue`如下：


```html
<template>
...
</template>
<script>
import form from '../components/form.vue'   //注意修改路径为../
import { post } from '../services/request.js'
export default {
    data() { ... },
    methods: {
        changeValue(args) { ... },
        submit() {
            if(this.validateState){
                let form = this.form
                let parms = { ... }
                post('http://localhost:3000/user',parms).then(() => {
                    alert("添加成功")
                })
            }else{ ... }
        }
    },
    components: { ... }
}
</script>

<style>
</style>

```

***注意：*** 修改路径`./`为`../`

将下面的代码复制到`list.vue`

```html
<template>
<div>
    <table>
        <thead>
            <tr>
                <th>序号</th>
                <th>用户ID</th>
                <th>用户名</th>
                <th>性别</th>
                <th>年龄</th>
            </tr>
        </thead>

        <tbody>
            <tr v-for="(item,index) in list">
                <td>{{index+1}}</td>
                <td>{{item.id}}</td>
                <td>{{item.name}}</td>
                <td>{{item.gender}}</td>
                <td>{{item.age}}</td>
            </tr>
        </tbody>
    </table>
</div>
</template>

<script>
import { get } from '../services/request.js'
export default {
    data() {
        return {
            list: []
        }
    },
    created() {
        get('http://localhost:3000/user').then(response => {
            this.list = response
        })
    }
}
</script>

```

现在，我们拥有了一个list页面，一个add页面。但是，我们并不能看到，为什么？
回顾一下：

- 我们在`/webpack.config.js`中配置了入口是`./src/app.js`
- 然后我们在`/app.js`引入了`/App.vue`

然后咧？`list.vue`和`add.vue`怎么办？

这里我们引入`vue-router`路由

> 用 Vue.js + vue-router 创建单页应用，是非常简单的。使用 Vue.js 时，我们就已经把组件组合成一个应用了，当你要把 vue-router 加进来，只需要配置组件和路由映射，然后告诉 vue-router 在哪里渲染它们

终端在项目路径下执行`npm install vue-router -S`

`vue-router`添加到了`package.json`的`dependencies`中

![](http://okmneu7zl.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-02-14%20%E4%B8%8B%E5%8D%881.40.44.png)

> `devDependencies`  里面的插件只用于开发环境，不用于生产环境，而 `dependencies`  是需要发布到生产环境的。

- 新建`/src/router`目录，并在目录中新建`index.js`，写入以下代码：

```javascript
import Vue from 'vue'
import Router from 'vue-router'
import List from '../views/list.vue'
import Add from '../views/add.vue'

Vue.use(Router)

let router = new Router({
    mode: 'history',
    base: __dirname,
    routes: [{
            name: 'list',       //名称
            path: '/',          //路径
            component: List     //组件
        },
        {
            name: 'add',
            path: '/add',
            component: Add
        }
    ]
})
router.beforeEach((to, from, next) => {     //在每次路由跳转之前都会先执行其中的代码，再通过调用next()执行后续操作
    next()
})

router.afterEach((to, from) => {})      //每次路由跳转之后都会执行其中的代码
export default router

```

这里我们给列表页指定路径为`/`，添加页为`/add`，那么，我们在浏览器中输入`http://localhost:8080`时，默认的就是列表页，`http://localhost:8080/add`时，就是添加页

- 修改`/src/app.js`

```javascript
import Vue from 'vue'
import App from './App.vue'
import router from './router'

new Vue({
  el: '#app',
  router,
  render: h => h(App)
})
```

- 将`/src/App.vue`修改为如下：

```html
<template>
  <div id="app">
      <!-- 路由匹配到的组件将渲染在这里 -->
      <router-view class="view"></router-view>
  </div>
</template>

```

路由匹配到的组件将在`<router-view>`中渲染

好了，试试吧，现在可以看到效果了

![](http://okmneu7zl.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-02-14%20%E4%B8%8B%E5%8D%882.07.58.png)

但是我们现在只能通过在浏览器输入地址来切换页面，这可不行，我们来加个按钮

在`list.vue`中添加如下：

```html
<template>
<div>
    <table>
       ...
    </table>
    <router-link to="/add">添加用户</router-link>
</div>
</template>

<script>
    ...
</script>

```

在`add.vue`中添加如下：
```html
<template>
<div>
    <my-form :form="form" :rules="rules" v-on:changeValue="changeValue"></my-form>
    <button type="button" @click="submit">提交</button>
    <router-link to="/">返回列表</router-link>
</div>
</template>
<script>
...
</script>
```

`<router-link>`中`to`写的是要跳转的路径

好了，添加和列表页完成了，下面来做编辑页面

### 编辑页面和路由传参

- 新建`src/views/edit.vue`，并将`add.vue`中的代码复制过来
- 在`/src/router/index.js`中添加`edit`组件的路由映射，代码如下

```javascript
import Vue from 'vue'
import Router from 'vue-router'
import List from '../views/list.vue'
import Add from '../views/add.vue'
import Edit from '../views/edit.vue'

Vue.use(Router)

let router = new Router({
    mode: 'history',
    base: __dirname,
    routes: [{
            name: 'list',       //名称
            path: '/',          //路径
            component: List     //组件
        },
        {
            name: 'add',
            path: '/add',
            component: Add
        },
        {
            name: 'edit',
            path: '/edit/:id',
            component: Edit
        }
    ]
})
router.beforeEach((to, from, next) => {     //在每次路由跳转之前都会先执行其中的代码，再通过调用next()执行后续操作
    next()
})

router.afterEach((to, from) => {})      //每次路由跳转之后都会执行其中的代码
export default router

```
`:id`是什么？下面讲到

- 在`/src/views/list.vue`中添加如下编辑按钮，代码如下

```html
<template>
<div>
    <table>
        <thead>
            ...
        </thead>

        <tbody>
            <tr v-for="(item,index) in list">
                <td>{{index+1}}</td>
                <td>{{item.id}}</td>
                <td>{{item.name}}</td>
                <td>{{item.gender}}</td>
                <td>{{item.age}}</td>
                <td><a @click="toEdit(item.id)">编辑</a></td>
            </tr>
        </tbody>
    </table>
    <router-link to="/add">添加用户</router-link>
</div>
</template>

<script>
import { get } from '../services/request.js'
export default {
    data() { ... },
    created() { ... },
    methods: {
        toEdit(id) {
            this.$router.push({
                name: 'edit',
                params: {
                    id: id
                }
            })
        }
    }
}
</script>

```

这里我们使用了`router`的另一种跳转方式，编程式路由

其中`name`就是我们在`/src/router/index.js`中设置的`name`，还记得吗？

```javascript
{
    name: 'edit',
    path: '/edit/:id',
    component: Edit
}
```

`params`就是我们在路由跳转时传递给`edit.vue`页面的参数，`id`对应的就是上面`path: '/edit/:id',`中的`id`

下面来处理编辑页面，代码修改如下：

```html
<template>
<div>
    <my-form :form="form" :rules="rules" v-on:changeValue="changeValue"></my-form>
    <button type="button" @click="submit">提交</button>
    <router-link to="/">返回列表</router-link>
</div>
</template>
<script>
import form from '../components/form.vue'
import { get , put } from '../services/request.js'
export default {
    data() {
        return {
            form: {
                name: {
                    label: '姓名',
                    value: '',
                    type: 'String'
                },
                age: {
                    label: '年龄',
                    value: '',
                    type: 'Number'
                },
                gender: {
                    label: '性别',
                    value: '',
                    type: 'Select'
                }
            },
            rules: {     //校验规则
                name: [{
                        max: 10,
                        min: 2,
                        message: '名字必须在2-10个字符之间'
                    },
                    {
                        type: 'String',
                        message: '名字格式不正确'
                    }
                ],
                age: [{
                        max: 100,
                        min: 0,
                        message: '年龄必须在0-100岁之间'
                    },
                    {
                        type: 'Number',
                        message: '年龄只能是数字'
                    }
                ],
                gender: [{
                    value: ['male', 'female'],
                    message: '请选择性别'
                }]
            },
            validateState: true, //表单校验状态
            id:''
        }
    },
    created(){
        this.id = this.$route.params.id  //路由中传递过来的参数
        get(`http://localhost:3000/user/${this.id}`).then(response =>{
            for (let key in response) {
                if(key != 'id'){
                    this.form[key].value = response[key]
                }
            }
            console.log(this.form);
        })
    },
    methods: {
        changeValue(args) {
            let [field, value, validateState] = [...args]
            this.form[field].value = value
            this.validateState = validateState
        },
        submit() {
            if(this.validateState){
                let form = this.form
                let parms = {
                    name: form.name.value,
                    age: form.age.value,
                    gender: form.gender.value
                }
                put(`http://localhost:3000/user/${this.id}`,parms).then(() => {
                    alert("修改成功")
                })
            }else{
                alert('数据错误，请修改后提交')
            }
        }
    },
    components: {
        'my-form': form
    }
}
</script>

```

编辑页面和添加的区别在于进入页面时需要通过`this.$route.params.id`获取列表页传过来的`id`，然后通过`id`查询出该用户的信息，并填充在form中

这里我们还修改了`import { get , put } from '../services/request.js'`和`submit()`方法，注意不要漏改了，或者直接整个复制过去

好了，现在编辑页面也完成了

### 删除

现在添加删除功能，有了前面的编辑，删除相信你不看我的代码也能写了，自己先试试吧

```html
<template>
<div>
    <table>
        <thead>
            ...
        </thead>

        <tbody>
            <tr v-for="(item,index) in list">
                <td>{{index+1}}</td>
                <td>{{item.id}}</td>
                <td>{{item.name}}</td>
                <td>{{item.gender}}</td>
                <td>{{item.age}}</td>
                <td><a @click="toEdit(item.id)">编辑</a></td>
                <td><a @click="toDel(item.id)">删除</a></td>
            </tr>
        </tbody>
    </table>
    <router-link to="/add">添加用户</router-link>
</div>
</template>

<script>
import { get , del } from '../services/request.js'
export default {
    data() { ... },
    created() { ... },
    methods: {
        toEdit(id) { ... },
        toDel(id){
            del(`http://localhost:3000/user/${id}`).then(()=>{
                get('http://localhost:3000/user').then(response => {
                    this.list = response
                })
                alert('删除成功')
            })
        }
    }
}
</script>

```

现在我们完成用户的增删改查所有功能了，但是现在所有人都可以直接操作数据，这肯定不行，我们还需要加入登陆验证才行

### 登陆和状态管理(Vuex)

***未完待续***
