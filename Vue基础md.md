# Vue基础

## Vue简介

1. JavaScript框架
2. 简化DOM操作
3. 响应式数据驱动

## Vue指令

- v-text   设置标签的文本值( textContent )
- v-html  设置标签的innerHTML（就是文本里面可以包含html标签）
- v-on 为元素绑定事件  不用加 on

```javascript
   		<!-- 可以使用@click="click"  -->
  <input type="button" value="事件绑定 v-on" v-on:click="click" id="app">
  var app = new Vue({
   el: '#app',
   data:{
   	click: function () {
       console.log('ok');
    },
   }
})
      
```

- v-show 根据表达值得真假，切换元素的显示和隐藏

```javascript
<div id="app">
<img src="" v-show="true">    
<img src="" v-show="isShow">    
<img src="" v-show="age>=18">    
</div>

var app = new Vue({
el:"#app",
    data:{
        isShow:false,
        age:18
    }
})
```

- v-if  根据表达值的真假，切换元素的显示和隐藏（操作dom元素）

```javascript
<div id="app">
<img src="" v-if="true">    
<img src="" v-if="isShow">    
<img src="" v-if="表达式">    
</div>

var app = new Vue({
el:"#app",
    data:{
        isShow:false,
        age:18
    }
})
```

- v-bind 设置元素的属性（比如：src title calss）

```js
<div id="app"> 
<img v-bind:src="imgSrc">    
<img :src="imgSrc">    
</div>

var app = new Vue({
el:"#app",
    data:{
        imgSrc
    }
})
```

- v-for 循环数据

```javascript
<div id="app">
        <ul>
            <li v-for="item in list">{{item.name}}</li>
        </ul>
    </div>
    
    <script>
    var app = new Vue({
        el: '#app',
        data: {
            list: [
                {
                    id: 0,
                    name: '小花',
                    age: 15
                },
                {
                    id: 1,
                    name: '李强',
                    age: 11
                },
                {
                    id: 2,
                    name: '小白',
                    age: 16
                },
                {
                    id: 3,
                    name: '小黑',
                    age: 12
                },
            ]
        }
    })
</script>
```

- v-model 获取设置表达元素的值（双向数据绑定）

```javascript
<div id="app">
        <input type='text' v-model='message'>
    </div>
    
    <script>
    var app = new Vue({
        el: '#app',
        data: {
            message: "你好啊"
        }
    })
</script>
```

