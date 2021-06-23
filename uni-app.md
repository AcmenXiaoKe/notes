# uni-app

## 脚手架搭建项目

1. 全局安装 npm install -g @vue/cli
2. 创建项目 vue create -p dcloudio/uni-preset-vue my-project
3. 启动项目 npm run dev:mp-weixin
4. 微信小程序开发者工具导入项目

## 样式和sass

- 支持小程序的rpx和 h5 的 vw vh
- 内置有sass的配置了，只需要 安装对应的依赖即可 npm install sass-loader node-sass
- vue 组件中 在style 标签 加入 属性  <style lang='scss'> 即可

## 基本语法

### 数据展示

- 在js 中 的 data 中 定义数据
- 在 template中 通过 {{数据}} 来显示
- 在标签的属性通过 :data-index = '数据' 来使用

```js
data(){
    return{
        title:"hello word"
    }
}
```

```html
<view :data-title="title">{{title}}</view>
```

### 数据循环

```HTML
<view v-for="(item) in list" :key="item.id">{{item.text}}---{{item.id}}</view>
```

```javascript
export default {
		// data 定义 数据 
		data() {
			return {
				list:[
					{
						id:1,
						text:'1'
					},
					{
						id:2,
						text:'2'
					},
					{
						id:3,
						text:'3'
					}
				]
			}
		}
	}
```

### 条件编译

- 通过 v-if 来决定显示和隐藏 不适合做频繁的切换显示
- 通过 v-show 来决定显示和隐藏 适合做频繁的切换显示

```html
<view v-if="true">显示</view>
<view v-if="false">隐藏</view>
<view v-if="isShow">显示或者隐藏</view>
```

### 计算属性

- 可以理解为是对data中的数据提供了一种加工或者过滤的能力
- 通过computed 来定义计算属性

```javascript
computed:{
	//过滤
    filterList () {
	return this.list.filter(v = v.id >=5)
    },
    // 加工
    processList () {
	return this.list.map( v=> {
        v.color= "red"
        return v
    })
    }    
}
```

### 事件使用

- 注册事件 @click = "handleClick"
- 定义事件监听函数 需要在 "methods" 中定义
- 事件传参 <view @click="handleClick(参数)">点击事件</view>

```HTMl
<view @click="handleClick">点击事件</view>
```

```javascript
// 事件回调
methods:{
	handleClick(event){
		console.log(event)
	}
}
```

## 组件

### 组件的简单使用

#### 组件的定义

- 在scr目录下 新建 文件夹 components 用来存放组件
- 在 components  目录想新建组件 *.vue

#### 组件的引入

- 在页面中引入 组件 " import 组件名 from '组件的路径' "
- 属性 components  是一个对象 把组件放进去注册

```javascript
	// 引入自定义组件
	import imgBorder from "../../components/img-border.vue"
	export default {
		// 注册组件
		components: {
			// imgBorder:imgBorder
			imgBorder: imgBorder
		}
	}
```

```html
<!-- 使用组件 -->
<img-border></imgBorder>
```

### 组件传递参数

- 父向子传递参数 通过 属性 的方式
- 子向父传递参数 通过 触发事件 的方式
- 使用全局数据传递参数
  - 通过挂载 vue 的原型上
  - 通过 globalData 的方式

#### 子向父传递数据

- 子组件通过 触发事件 的方式向父组件传递数据
- 父组件通过 监听事件 的方式来接收数据

### 组件插槽

- 标签其实也是数据的一种，想实现动态给子组件传递标签，就可以使用插槽 slot
- 通过 slot 来实现 占位符

### 全局共享数据

通过Vue的原型来定义全局数据在main.js中写入

```javascript
// name 是自定义的名字
Vue.prototype.name="Vue全局数据"
//  通过 this.名字 来使用
```

### 生命周期

- uni-app框架的生命周期结合了 vue 和 微信小程序的生命周期
- 全局的APP中使用 onLaunch 表示应用启动时
- 页面中 使用 onLoad 或者 onShow 分别表示 页面加载完毕时 和页面 显示时
- 组件中使用 mounted 组件挂载完毕时