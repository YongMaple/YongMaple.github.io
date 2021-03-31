---
title: Mongoose入门
date: 2017-02-07 17:05:05
categories: 后端
tags:
    - mongodb
    - mongoose
---
## 准备工作
### 安装Node.js和MongoDB
安装Node.js，地址：[https://nodejs.org/](https://nodejs.org/)   
安装MongoDB，地址：[https://www.mongodb.com/](https://www.mongodb.com/download-center?jmp=nav#community)    
### 启动MongoDB
cd到mongodb下的bin文件夹，执行`./mongod`即可，但是我们执行后会出如下错误    
![](http://okmneu7zl.bkt.clouddn.com/WX20170131-131657.png)    
这里提示我们没有/data/db文件夹，需要我们自己来新建，这里我在mongodb-3.4.0下新建了mydata文件夹，并通过`./mongod --dbpath ../mydata`启动了MongoDB，`--datapath`是用来指定路径的     
![](http://okmneu7zl.bkt.clouddn.com/WX20170131-133558@2x.png)   
那么让我们来体验一下mongodb吧，首先要新建一个tab，然后同样cd到bin目录下，执行`./mongo`   
![](http://okmneu7zl.bkt.clouddn.com/WX20170131-134433.png)   
然后我们切回之前的tab可以看到有一个新的连接   
![](http://okmneu7zl.bkt.clouddn.com/WX20170131-134649.png)   
### MongoDB基础命令
![](http://okmneu7zl.bkt.clouddn.com/WX20170131-135808.png)   
因为我们后期基本不会在shell里来操作，所有这里只讲几个基本的，下面的test可以随意命名，是集合名   
- `db.test.insert()`在集合中插入一条数据   
- `db.test.find()`查找集合中所有的，可以加入参数，如`db.test.find({name:"Jimmy"})`  
- `db.test.remove({})`删除集合中所有元素，`{}`必须写   
- `show collections`查看所有集合   

### mongoose的安装
新建一个文件夹，通过`npm init`初始化，通过`npm install mongoose --save`安装mongoose      
![](http://okmneu7zl.bkt.clouddn.com/WX20170131-142334.png)   
### 通过mongoose连接数据库   
新建一个js文件，复制以下代码并在shell运行`node xxx.js`，xxx为你的文件名
```javascript
var mongoose = require("mongoose");
mongoose.connect("mongodb://localhost/test")
```
运行之后在之前的mongod的tab中看到有了一个新的连接   

## Mongoose入门
### 创建类Class的方式
```javascript
var User = mongoose.model("User",userSchema)
```

`"User"`表示类名，在数据库中集合名会自动转化为小写加s`users`      

`userSchema`定义了对象的原型   ·

***举个栗子***      
```javascript
var mongoose = require("mongoose");
mongoose.connect("mongodb://localhost/test")

userSchema = new mongoose.Schema({  //如果只需要指定类型的话可以直接写，如果需要其他条件，需要写成对象的形式
    name:String,
    qq:{
        type:String,
        default:"740534248" //默认
    }
});
const User = mongoose.model("User",userSchema)

const user = new User({
    name:"YongMaple"
})
user.save()
```
### 指定对象的属性类型
- javascript内置类型
    - String
    - Number
    - Boolean
    - Date
    - Array   

- node.js内置类型
    - Buffer

- mongoose特有类型
    - Mixed
    - ObjectId

***举个栗子***   
```javascript
var mongoose  = require("mongoose")
mongoose.connect("mongodb://localhost/test")

const ObjSchema = new mongoose.Schema({
    str : String,
    num : Number,
    bool : Boolean,
    arr : Array,    //[]
    arr2 : [Date],
    arr3 : [String],
    time : Date,

    buf : Buffer,

    mxo : mongoose.Schema.Types.Mixed,  //{}    混合数据类型
    oid : mongoose.Schema.Types.ObjectId    //唯一的
})
const obj = mongoose.model("obj",ObjSchema)
const o = new obj({
    time : new Date( 42342342344),  //内部自动转化为Date类型，转化失败将报错
    arr3 : [1234,"jimmy",new Date],     //内部自动转化 String
    buf : new Buffer([22,33,44]),

    oid : mongoose.Types.ObjectId()
})

o.save(function(err){
    console.log(err);
})
```
### 自定义类型
使用自定义类型，需要：
- 继承mongoose.SchemaType(key,options,typeName)
- 重写cast(value)方法
- 加入mongoose.Schema.Types

***举个栗子***   
创建User.js文件
```javascript
const mongoose = require("mongoose")

//es5版
// function User(key,options){
//     mongoose.SchemaType.call(this,key,options,"User")
// }
//
// User.prototype = Object.create(mongoose.SchemaType.prototype)
//
// User.prototype.cast = function(val){
//     if(val.loginname && val.password && val.loginname.length > 3){
//         return {
//             loginname : val.loginname,
//             password : val.password
//         }
//     }else{
//         throw new Error("user has error!")
//     }
// }
//
// mongoose.Schema.Types.User = User;
//
// module.exports = User;

//es6版
class User extends mongoose.SchemaType{
    constructor(key,opts){
        super(key,opts,"User")
    }

    cast(val){
        if(val.loginname && val.password && val.loginname.length > 3){
            return {
                loginname : val.loginname,
                password : val.password
            }
        }else{
            throw new Error("user has error!")
        }
    }
}
```
再创建一个demo.js文件来使用上面的User.js
```javascript
const mongoose = require("mongoose")

mongoose.connect("mongodb://localhost/test")

const User = require("./User")

const People = mongoose.model("People",new mongoose.Schema({
    user:User
}))

const o = new People({
    user:{
        loginname:"jimmy",
        password:"123456"
    }
})

o.save(function callback(err,result){
    console.log(err);
})

```
### 所有类型都具有的配置
- 强制不得为空（验证器）   
    - required:true
- 设置属性默认值   
    - default:"qq740534248"
- 是否包括在查询结果中   
    - select:false
- getter 和 setter 拦截器   
    - get:v => v   
    - set:v => v   

***举个栗子***   
```javascript
const mongoose = require("mongoose")

mongoose.connect("mongodb://localhost/test")

const Obj = mongoose.model("Obj",new mongoose.Schema({
    str:{
        type:String,
        default:"YongMaple",
        get(v){
            return `my name is ${v}`
        },
        set(v){
            return 123+v
        }
    },

    loginname:{
        type:String,
        required:true
    },

    str2:{
        type:String,
        select:false
    }
}))

const o = new Obj({
    str:"Jimmy",
    str2:"111111",
    loginname:"333333"
})
console.log(o.str);     //my name is 123Jimmy

o.save(function(err){
    console.log(err);
    Obj.findOne({},function(err,result){
        console.log(result);    //{ _id: 58993a84a184201ce8ebf5f5, __v: 0, str: '123Jimmy' }
    })
})

```
### 对属性的验证和转换
**验证**
- 枚举
    - enum:Array
- 正则表达式验证
    - match
- 限制字符串最大和最小长度
    - maxlength & minlength   
- Number和Date的最大最小值
    - max & min   

**转换**
- 字母大小写转化
    - lowercase & uppercase
- 去除字符串前后空白字符
    - trim:true/false

***举个栗子***   
```javascript
const mongoose =require("mongoose")

mongoose.connect("mongodb://localhost/test")

const baseDateNum = Date.now()

const Obj = mongoose.model("Obj",new mongoose.Schema({

    str:{
        type:String,
        enum:["aaa","bbb"]
    },

    qq:{
        type:String,
        match:/^\d*$/,
        maxlength:20,
        minlength:5
    },

    lowerstr:{
        type:String,
        lowercase:true
    },
    upperstr:{
        type:String,
        uppercase:true
    },
    trimstr:{
        type:String,
        trim:true
    },
    num:{
        type:Number,
        min:6,
        max:20
    },
    date:{
        type:Date,
        min:new Date(baseDateNum - 1000),
        max:new Date(baseDateNum + 2000)
    }
}))

const o = new Obj({
    str:"bbb",
    qq:"740534248",
    lowerstr:"aaaAAAA",
    upperstr:"bbbBBBB",
    trimstr:"    aaa  aaff",
    num:6,
    date:new Date(baseDateNum+1000)
})
console.log(o);
const err = o.validateSync()    //同步校验，后面会讲

console.log(err);

```
### 自定义验证器
有三种方式来自定义验证器
- validate:{ validator:Function }
- Class.schema.path(xxx).validate(fn)    //xxx 表示要获取的属性名，然后对其验证
- validator(v,[callback])同步验证器，加入第二个参数callback变为异步验证器   
***举个栗子***   
```javascript
const mongoose = require("mongoose")

mongoose.connect("mongodb://localhost/test")

const User = mongoose.model("User",new mongoose.Schema({
    name:{
        type:String,
        // validate:{   //同步验证器，也可以是数组形式，同时多个验证器
        //     validator(value){
        //         return value.length <= 9
        //     }
        // }
    }
}))

User.schema.path("name").validate(function(v){
    return v.length <= 9
})
User.schema.path("name").validate(function(v){  //可以加入多个
    return v.length > 3
})
const u = new User({
    name:"1234"
})

const err = u.validateSync()
console.log(err);

```
***再举个异步的栗子***   
```javascript
const mongoose = require("mongoose")

mongoose.connect("mongodb://localhost/test")

const User = mongoose.model("User",new mongoose.Schema({
    loginname:{
        type:String,
        validate:{
            validator(value,cb){
                User.findOne({loginname:value},function(err,result){
                    cb(!result)
                })
            }
        }
    }
}))

var user = new User({
    loginname:"hahahaha"
})
user.save(function(err){
    console.log(err);   //运行2遍，第二遍会抛出异常
})

```
### 获取错误信息
- doc.validate( error => error )    ***异步获得错误信息***
- error = doc.validateSync()        ***获取同步错误信息***
- doc.save( error => error )        ***回调获得错误信息***
- promise = doc.save()              ***promise方式获得错误信息***
- Class.update( query , update , { runValidators:true } , callback)   
***举个栗子***   
```javascript
const mongoose = require("mongoose")

mongoose.connect("mongodb://localhost/test")

const User = mongoose.model("User",new mongoose.Schema({
    loginname:{
        type:String,
        required:true,
        validate:{
            validator(value,cb){
                setTimeout(function() {
                    cb(false)
                })
            }
        }
    }
}))

const u = new User({
    loginname:"aaa11"
})

//1 只能获得同步
const err = u.validateSync()
console.log(err);

// 2 同步异步都能
// u.validate(function(err){
//     console.log(err);
// })


//3 同步异步都能  有可能是底层传上来的错误
// u.save(function(err){
//     console.log(err);
// })

//4 同步异步都能  有可能是底层传上来的错误
// const promise = u.save()
// promise.catch(err => console.log(err))

//5 同步异步都能  有可能是底层传上来的错误
// User.update({},{loginname:"123aaa"},{runValidators:true},function(err){
//     console.log(err);
// })
```
### 错误信息
- err.errors    ***错误集合***
- err.errors.qq   ***得到qq属性值的错误信息***
- { path , value , message } = err.errors.qq   
***举个栗子***   
```javascript
const mongoose = require("mongoose")

mongoose.connect("mongodb://localhost/test")

const User = mongoose.model("User",new mongoose.Schema({
    loginname:{
        type:String,
        required:[true,"必须输入 {PATH} - {VALUE}"], //除了enum以外所有内置验证器都是这样写
        enum:{values:["aaa","bbb"],message:"没在枚举中。。。 {PATH} - {VALUE}"},     //枚举时，自定义错误信息比较特殊，是对象形式
        validate:{      //自定义校验器要这样写，也是对象
            validator(value,cb){
                setTimeout(function() {
                    cb(false)   //模拟异步错误
                })
            },
            message:"校验错误 {PATH} - {VALUE}"
        }
    },
    password:{
        type:String,
        required:[true,"密码必须填写"],
        minlength:[6,"密码至少6位"]
    }
}))

const u = new User({
    loginname:"aaa11"
})

u.validate(function(err){
//     const {path,value,message} = err.errors.loginname
//     console.log(path);
//     console.log(value);
//     console.log(message);

    if(err && err.errors){
        const keys = Object.keys(err.errors)
        let str = ""
        keys.map((v,i) => {
            str += `${v}:${err.errors[v]}\n`
        })
        console.log(str);
    }
    //....
})
```
例子中`{PATH}`和`{VALUE}`是属性名和属性值
### 加入方法
- 加入实例方法 schema.methods.xxx
- 加入静态方法 schema.statics.xxx
- 加入虚拟方法 getter/setter方法
    - virtual('xxx').get(handle)   
    - virtual('xxx').set(handle)   
***举个栗子***   
```javascript
const mongoose = require("mongoose")
mongoose.connect("mongodb://localhost/test")
const userSchema = new mongoose.Schema({
    firstName: String,
    secondName: String
})

//methods和statics的区别在于this是谁
userSchema.methods.getAllName = function(){
    return this.firstName + '.' + this.secondName   //这里的this指的是对象本身
}

userSchema.statics.getAll = function(){             //可以加入回调函数  function(cb){
    return this.find({})     //这里的this指的是User类   可以加入回调函数  this.find({},cb)
}

userSchema.virtual("allname").get(function(){
    return this.firstName + '.' + this.secondName
})  //getter method

userSchema.virtual("allname").set(function(v){
    //Yong.Maple
    const names = v.split(".");
    this.firstName = names[0];
    this.secondName = names[1];
});  //setter method

const User = mongoose.model("User",userSchema)

// const u = new User({
//     firstName:"Yong ",
//     secondName:"Maple"
// })
//
// console.log(u.getAllName());


// User.remove({}).then(()=>{
//     User.insertMany([
//         {firstName:"Yong",secondName:"Maple"},
//         {firstName:"Zhang",secondName:"San"},
//         {firstName:"Li",secondName:"Si"}
//     ]).then(()=>{
//         User.getAll().then(result=> console.log(result))
//     })
// })


const user = new User({
    allname:"Yong.Maple"
})
user.save()
console.log(user);

```
### 对象的增删改
- obj.save / Class.create / Class.insertMany
- obj.remove / Class.remove
- obj.update / Class.update    

***举个栗子***   
```javascript
const mongoose = require("mongoose")

mongoose.connect("mongodb://localhost/test")

const User = mongoose.model("User",{
    name:{type:String,maxlength:10}
})


User.remove({}).then(function(){
    // --------------save
    // const user = new User({name:"JiaYongfeng"})
    // const promise = user.save()
    // promise
    // .then(u => console.log(u))
    // .catch(err => console.log(err))
    //
    //
    // user.save(function (err,u){
    //     console.log(err,u);
    // })

    // --------------create     成功的保存，失败的抛出异常
    // User.create({name:"Jia"},{name:"Yong"})
    // .then(function(u1,u2){
    //     console.log(u1,u2);
    // }).catch(err => console.log(err))

    // User.create([{name:"Jia"},{name:"Yong1111"}])
    // .then(function(arr){
    //     console.log(arr);
    // }).catch(err => console.log(err))


    // ------------------insertMany     必须是数组，一条失败，全部失败
    // User.insertMany([{name:"Jia"},{name:"Yong"}])
    // .then(function(arr){
    //     console.log(arr);
    // }).catch(err => console.log(err))


    // --------------------remove
    // User.insertMany([{name:"Jia22"},{name:"Jia33"},{name:"Jiasasd"},{name:"Yong"}])
    // .then(function(arr){
    //     User.remove({name:/^Jia\d*$/}).then(r => console.log(r))

        // User.remove({name:"Jia"}).then(r => console.log(r))

        // arr[0].remove()
        //     .then(obj => User.create({name:obj.name + " ----- reborn"}))
        //     .catch(err => console.log(err))

        // arr[0].remove(function(err,obj){
        //     console.log(err,obj);
        // })

    // }).catch(err => console.log(err))


    // -----------------------update
    User.insertMany([{name:"Jia22"},{name:"Jia33"},{name:"Jiasasd"},{name:"Yong"}])
    .then(function(arr){

        User.update({},{name:"YongFeng"},{multi:true},function(err,result){      //默认只修改第一个符合条件的值,需要修改多个要加{multi:true}
            console.log(err,result);
        })

        // console.log("before update  ==>",arr[0]);
        // arr[0].update({name:"123123123123fsdsdfddfsdfdsasdas"},{runValidators:true}).then(function(result){
        //     console.log(result);
        // }).catch(err => console.log(err))
    }).catch(err => console.log(err))
})

```
### 字段更改参数
- $inc 加法运算
- $mul 乘法运算
- $min / $max 数值和日期的限定
- $currentDate 设定当前日期
***举个栗子***   
```javascript
var mongoose = require("mongoose")

mongoose.connect("mongodb://localhost/test")

var Topic = mongoose.model("Topic", new mongoose.Schema({
    title: {
        type: String,
        required: true
    },
    body: {
        type: String,
        required: true
    },
    money: {
        type: Number,
        default: 0.5
    },
    accessNum: {
        type: Number,
        default: 0
    },
    createTime: Date,
    updateTime: Date
}))

Topic.remove({}, function() {

    const date = new Date
    var topic = new Topic({
        title: "aa",
        body: "content",
        createTime: date,
        updateTime: date
    })

    topic.save(function(err, result) {

        Topic.update({}, {
            title: "my js",

            $currentDate:{  //使用当前时间修改updateTime的值
                updateTime:true
            },

            $min: { //最小值，和平时理解的不一样，必须小于15，不超过15变成15
                accessNum: 15   //15
            },
            // $max: { //最大值，和平时理解的不一样，必须大于15，不满15变成15
            //     accessNum: 15   //15
            // }

            //每个参数只能在一个算法当中
            // $mul: { //乘
            //     money: 3    //1.5
            // },
            // $inc: { //加
            //     accessNum: 2    //2
            // }
        }, function(err) {

        })

    })

})

```
### 数组类型字段的更改参数
- $pop 删除一条数据
- $addToSet 添加去重数据
- $pull / $pullAll 清除
- $push 加入数据
***举个栗子***   
```javascript
var mongoose = require("mongoose")

mongoose.connect("mongodb://localhost/test")

const Topic = mongoose.model("Topic", {
    arr: []
})

Topic.remove({}, function() {

    Topic.create({
        arr: [11, 22,11, 33, 44, 55, 66]
    }).then(function() {

        //  $pop删除一个
        // Topic.update({}, {
        //     $pop: {
        //         arr: 1  //[11, 22, 33, 44, 55]  正数从后往前，负数从前往后
        //         //arr: -1 //[ 22, 33, 44, 55, 66]
        //     }
        // }).exec()


        //  $addToSet添加不重复
        // Topic.update({}, {
        //     $addToSet: {
        //         // arr: 66     //如果重复，无法添加
        //         // arr: [77,88]    //[11, 22, 33, 44, 55, 66, [77,88]]并不是预期的效果
        //         arr: {
        //             $each: [77, 88]     //[ 11, 22, 33, 44, 55, 66, 77, 88 ]
        //         }
        //     }
        // }).exec()

        //  $pull删除一种数据
        // Topic.update({},{
        //     $pull:{
        //         arr:11  //[ 22, 33, 44, 55, 66 ]
        //         // arr:[11,22] //无效的
        //     }
        // }).exec()

        //  $pullAll删除多种数据
        // Topic.update({},{
        //     $pullAll:{
        //         arr:[11,22]  //[ 33, 44, 55, 66 ]
        //     }
        // }).exec()

        //  $push添加数据，可以重复
        Topic.update({},{
            $push:{
                // arr:66  //[ 11, 22, 11, 33, 44, 55, 66, 66 ]
                arr:{
                    $each: [77, 88],
                    $position:0,     //从指定位置开始添加[ 77, 88, 11, 22, 11, 33, 44, 55, 66 ]
                    $slice:-3        //截取数组，正数从前截取，负数从后截取
                }
            }
        }).exec()
    })

})

```
### 对象的基础查询
- find(conditions , [projection] , [options] , [callback])
- findById
- findByIdAndRemove / findByIdAndUpdate
- findOne
- findOneAndRemove / findOneAndUpdate
***举个栗子***   
```javascript
var mongoose = require("mongoose")
mongoose.connect("mongodb://localhost/test")

const Topic = mongoose.model("Topic", {
    name: {
        type: String,
        maxlength: 10
    }
})

const arr = []

for (let i = 0; i < 50; i++) {
    arr.push({
        name: "Jimmy" + i
    })
}
Topic.remove({}, function() {
    Topic.create(arr).then(function(objs) {
        let id = objs[0].id
        // Topic.findById(id, function(err, t) {
        //     console.log(t);
        // })

        // Topic.findByIdAndRemove(id, function(err, t) {
        //     console.log(t);
        // })

        // Topic.findByIdAndUpdate(id, {
        //     name: "hahahaha111111111"
        // }, {
        //     runValidators: true
        // }, function(err, t) {
        //     console.log(err, t);
        // })
        // 上面这个和这个一样的效果
        // Topic.findByIdAndUpdate(id, {
        //     name: "hahahaha111111111"
        // }, {
        //     runValidators: true
        // }).then(function(t) {
        //     console.log(t);
        // }).catch(err => console.log(err))
        // 效果还是一样的，错误交由外部处理
        // return Topic.findByIdAndUpdate(id, {
        //     name: "hahahaha111111111"
        // }, {
        //     runValidators: true
        // }).then(function(t) {
        //     console.log(t);
        // })

        // Topic.findOne({name:/^Jimmy1\d$/},function(err,t){  //返回满足条件的第一个
        //     console.log(t);
        // })

        // Topic.find({},function(err,ts){
        //     console.log(ts);
        // })
        // Topic.find({name:/^Jimmy1\d$/},function(err,ts){
        //     console.log(ts);
        // })
        // Topic.find({name:/^Jimmy1\d$/},"_id name",function(err,ts){  //第二个参数决定返回结果带有的属性，用空格分割，如果在属性前加-，则表示不返回这个属性
        //     console.log(ts);
        // })
        Topic.find({name:/^Jimmy1\d$/},null,{skip:5,limit:3},function(err,ts){ //第三个参数是对结果的筛选，skip指跳过几个，limit指最多返回几个，结合这两个可以实现翻页功能
            console.log(ts);
        })

    }).catch(err => console.log(err))
})

```
### 比较式查询
- 大小判断
    - $eq 等于
    - $gt 大于
    - $gte 大于等于
    - $lt 小于
    - $lte 小于等于
- $ne 不是
- $in 包含
- $nin 不包含
***举个栗子***   
```javascript
var mongoose = require("mongoose")
mongoose.connect("mongodb://localhost/test")

const Topic = mongoose.model("Topic", {
    name: {
        type: String,
        maxlength: 10
    },
    num: Number
})

const arr = []

for (let i = 0; i < 50; i++) {
    arr.push({
        name: "Jimmy" + i,
        num: i
    })
}
Topic.remove({}, function() {
    Topic.create(arr).then(function(objs) {

        Topic.find({
            num: {
                // $lt: 20,    //小于20
                // $lte:20,    //小于等于20
                // $gt:10,      //大于10
                // $gte:10,     //大于等于10
                // $in: [11, 15], //值等于11或15
                // $nin: [11, 15], //值不等于11或15
                // $ne: 2, //不等于2的，只能一个值
                $eq: 2, //等于2的
            }
        }, function(err, ts) {
            console.log(ts);
        })
    })
})

```
### 逻辑式查询
- $or 或
- $nor ($or相反)
- $and 和
- $not 否
***举个栗子***   
```javascript
var mongoose = require("mongoose")
mongoose.connect("mongodb://localhost/test")

const Topic = mongoose.model("Topic", {
    name: {
        type: String,
        maxlength: 10
    },
    num: Number
})

const arr = []

for (let i = 0; i < 50; i++) {
    arr.push({
        name: "Jimmy" + i,
        num: i
    })
}
Topic.remove({}, function() {
    Topic.create(arr).then(function(objs) {

        Topic.find({
            // $or: [{     //满足一个条件就会返回
            //     name: "Jimmy1"
            // }, {
            //     num: 4
            // },{
            //     num: 6
            // }]

            // $nor: [{        //不满足所有条件就返回
            //     name: "Jimmy1"
            // }, {
            //     num: 4
            // }, {
            //     num: 6
            // }]

            // $and: [{        //满足所有条件才返回
            //     name: /^Jimmy1\d$/
            // }, {
            //     num: {
            //         $lte:15
            //     }
            // }]
            // $and: [{        //可以混合使用
            //     $or: [{
            //         name: "Jimmy1"
            //     }, {
            //         num: 4
            //     }, {
            //         num: 6
            //     }]
            // }, {
            //     num: 4
            // }]

            num: {
                $not: { //不满足条件的返回
                    $gte: 10
                }
            }

        }, function(err, ts) {
            console.log(ts);
        })
    })
})

```
### 数组专用查询
- $all 是否包含查询条件
- $elemMatch 数组元素查询条件
- $size 数组大小查询条件
- $where 其他
***举个栗子***   
```javascript
var mongoose = require("mongoose")
mongoose.connect("mongodb://localhost/test")
//$all&$elemMatch
// const Topic = mongoose.model("Topic", {
//     arr: []
// })

//$all
// Topic.remove({}, function() {
//     Topic.create([{
//             arr: [11, 22, 33]
//         },
//         {
//             arr: [33, 44, 55]
//         },
//         {
//             arr: [55, 66, 77, 88]
//         }
//     ]).then(function() {
//         Topic.find({
//             arr: {
//                 $all: [33, 44] //都存在的返回
//             }
//         }).then((result) => {
//             console.log(result);
//         })
//     })
// })

//$elemMatch
// function generatorArr(size) {
//     let arr = []
//     for (let i = 0; i < size; i++) {
//         arr.push({
//             name: "Jimmy" + i,
//             num: i
//         })
//     }
//     return arr
// }
//
// Topic.remove({}, function() {
//     Topic.create([{
//             arr: generatorArr(5)
//         },
//         {
//             arr: generatorArr(10)
//         },
//         {
//             arr: generatorArr(15)
//         }
//     ]).then(function() {
//         Topic.find({
//             arr: {
//                 // $elemMatch: {       //匹配数组内部元素的数据
//                 //     name: "Jimmy9"
//                 // }
//                 $size: 5        //匹配数组length
//             }
//         }).then((result) => {
//             console.log(result);
//         })
//     })
// })

//$where
const Topic = mongoose.model("Topic", {
    obj: {}
})
Topic.remove({}, function() {
    Topic.create([{
        obj: {
            name: "java",
            num: 1
        }
    }, {
        obj: {
            name: "javascript",
            num: 2
        }
    }, {
        obj: {
            name: "nodejs",
            num: 3
        }
    }]).then(function() {
        Topic.find({
            // $where: "this.obj.name === 'java'"      //直接写js
            $where: function() {
                return /^java\w*$/.test(this.obj.name) && this.obj.num === 2
            }
        }, null, { //同样可以混合使用
            limit: 2,
            skip: 0
        }).then(function(result) {
            console.log(result);
        })
    })
})

```
### Query查询风格
另一种风格的写法，作为了解，下面例子将会改写上面的例子
***举个栗子***   
```javascript
Topic.create([{
        arr: [11, 22, 33]
    },
    {
        arr: [33, 44, 55]
    },
    {
        arr: [55, 66, 77, 88]
    }
]).then(function() {
    let query = Topic.find()
    query.where("arr")
        .all([33,44])
        .limit(3)
        .skip(10)
        .select("name age")
        .exec(function (err,result){
            console.log(result)
        })
})
//elemMatch的query写法
Topic.create([{
        arr: generatorArr(5)
    },
    {
        arr: generatorArr(10)
    },
    {
        arr: generatorArr(15)
    }
]).then(function() {
    Topic.find()
    .where("arr")
    .elemMatch({name:"Jimmy9"})
    .exec(function (err,result){
        console.log(result)
    })

    //另一种写法
    Topic.find()
    .where("arr")
    .elemMatch(function (elem){
        elem.where("name").eq("Jimmy9").where("num").gte() ....
    })
    .exec(function (err,result){
        console.log(result)
    })
})
```
具体可以查看文档[http://mongoosejs.com/docs/queries.html](http://mongoosejs.com/docs/queries.html)
### 中间件
会触发中间件的：
- validate
- doc.save
- Class.create / Class.insertMany
- doc.update
- doc.remove
- find
- findOne
- findAndRemove / findOneAndUpdate
***举个栗子***   
```javascript
var mongoose = require("mongoose")

mongoose.connect("mongodb://localhost/test")

const userSchema = new mongoose.Schema({
    name: {
        type: String,
        maxlength: 5
    }
})
userSchema.pre("validate", function(next) {
    console.log("pre validate");
    console.log(this.errors);
    next()
})

userSchema.post("validate", function() {
    // console.log(this.errors?this.errors.name.message:this.errors);
    console.log("post validate");
})

// userSchema.pre("save", function(next) {
//     console.log("pre save one");
//     next()
// })
userSchema.pre("save",true, function(next,done) {    //默认不加true，加true表示并行,在执行完所有的pre之后在执行后面的，没什么卵用的功能，完全能用next来控制
    console.log("pre save one");
    next()
    setTimeout(done,2000)
})

userSchema.pre("save", function(next) {
    console.log("pre save two");
    setTimeout(next,2000)
})

userSchema.post("save", function() {
    console.log("post save");
})

userSchema.pre("update", function(next) {
    console.log("pre update");
    next()
})

userSchema.post("update", function() {
    console.log("post update");
})

userSchema.pre("remove", function(next) {
    console.log("pre remove");
    next()
})
userSchema.post("remove", function() {
    console.log("post remove");
})
const User = mongoose.model("User", userSchema)

User.remove({}, function() {
    var user = new User({
        name: "Jia"
    })

    user.save(function(err, u) {
        u.update({
            name: "Jimmy"
        }).then(function(result){

            u.remove()
        })
    })
})

```





***END***
