---
title: 关于Vue生命周期的一些理解记录
date: 2017-01-17 16:34:06
categories: Vue笔记
tags:
    - Vue
---

今天遇到个问题，大致是这样的：从父组件向子组件传值，第三层数据终端报错undefined，先上测试代码：

```javascript
//父组件
<template lang="html">
    <div>
        <son :data="data1"></son>
    </div>
</template>

<script>
import Son from '../son/index.vue'
export default {
    data(){
        return {
            data1:{}
        }
    },
    mounted(){
        this.data1={
            name:'最外层',
            value1:{
                name:'第一层',
                value2:{
                    name:'第二层',
                    value3:{
                        name:'第三层'
                    }
                }
            }
        }
    },
    components:{
        Son
    }
}
</script>
```

```javascript
//子组件
<template lang="html">
  <div>
      最外层
      {{data.name}}
      <div>
          第一层
          {{data.value1.name}}
          <div>
              第二层
              {{data.value1.value2.name}}
              <div>
                  第三层
                  {{data.value1.value2.value3.name}}
              </div>
          </div>
      </div>
  </div>
</template>

<script>
export default {
    props:['data']
}
</script>


```
解决办法是将`mounted`中的赋值过程，改在`beforeMount`或者`created`中进行。或者先给`data`一个空的数据结构。
原因是子组件渲染时，`data`的赋值还没进行，所以属性都是undefined
