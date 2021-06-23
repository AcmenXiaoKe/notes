# flex

> - 操作方便，布局极为简单，移动端应用很广泛
> - PC端浏览器支持情况较差
> - IE 11 以下 不支持

## 布局原理

> flex 是 flexible box 的缩写，意为 “ 弹性布局” ，用来为盒状模型提供最大的灵活性，任何一个容器都可以指定为 flex 布局  

- 当我们为父盒子设为 flex 布局以后 ， 子元素的 float、clear 和 vertical-align 属性将失效。
- 伸缩布局 = 弹性布局 = 伸缩盒布局 = 弹性盒布局 = flex布局

> 采用 Flex 布局的元素，称为 Flex 容器 （flex container ），简称 “ 容器 ”。它的所有子元素自动成为容器成员，称为 Flex 项目 （ flex item ），简称 “ 项目 ” ；

```html
<div>
<span></span>
</div>
```



-  div 就是 flex 父容器
-  span 就是 子容器 flex 项目
- 子容器可以横向排列也可以纵向排列

> 总结 flex 布局 原理 ：就是通过给 父盒子 添加 flex 属性，来控制子盒子的位置和排列方式

## flex 常见父项属性

- flex-direction      设置主轴方向
- justify-content    设置主轴上的子元素排列方式
- flex-wrap            设置子元素是否换行
- align-content      设置侧轴上的子元素的排列方式 （ 多行 ）
- align-items         设置侧轴上的子元素排列方式（单行）
- flex-flow              复合属性，相当同时设置了 flex-direction 和 flex-wrap 

### flex-direction 

#### 1.主轴与侧轴

> 在flex布局中，是分为主轴和侧轴两个方向，同样的叫法有：行和列、X轴和Y轴

- 默认主轴方向就是 X 轴 方向，水平向右
- 默认侧轴方向就是  Y 轴方向，水平向下

#### 2.属性值

> flex-direction 属性决定主轴的方向（即项目的排列方向）
>
> 注意：主轴和侧轴是会变化的，就看 flex-direction 设置谁为主轴，剩下的就是侧轴，而我们的子元素是跟着主轴来排列的

| 属性值         | 说明            |
| -------------- | --------------- |
| row            | 默认值从左到右z |
| row-reverse    | 从右到左        |
| column         | 从上到下        |
| column-reverse | 从下到上        |

### justify-content

> justify-content 属性定义了项目在主轴上的对齐方式
>
> 注意：使用这个属性之前一定确定好主轴是那个

| 属性值        | 说明                                            |
| ------------- | ----------------------------------------------- |
| flex-start    | 默认值 从头开始，如果主轴是 X 轴，则从左到右    |
| flex-end      | 从尾部开始排列                                  |
| center        | 在主轴居中对齐 ( 如果主轴是 X 轴 则 水平居中  ) |
| space-around  | 平分剩余空间                                    |
| space-between | 先两边贴边 再平分剩余空间 （ 重要）             |

### flex-wrap 

> flex-wrap  设置子元素是否 换行 
>
> 默认情况下，项目都排在一条线（又称 " 轴线 " ）上。flex-wrap 属性定义，flex布局中默认是不换行
>
> 如果装不开，会缩小子元素的宽度放到父元素里边

| 属性值 | 说明         |
| ------ | ------------ |
| nowrap | 默认值不换行 |
| wrap   | 换行         |

### align-content 

> 设置子项在侧轴上的排列方式并且只能用于子项 出现 换行 的情况下 （ 多行），在单行下是没有效果的

| 属性值        | 说明                                   |
| ------------- | -------------------------------------- |
| flex-start    | 默认值在侧轴的头部开始排列             |
| flex-end      | 在侧轴的尾部开始排列                   |
| center        | 在侧轴中间显示                         |
| space-around  | 子项在侧轴平分剩余空间                 |
| space-between | 子项在侧轴先分布在两头，在平分剩余空间 |
| stretch       | 设置子项元素高度平分父元素高度         |



### align-items  

> align-items  设置侧轴上的子元素排列方式（单行）
>
> 该属性是控制子项在侧轴（默认是 Y 轴） 上的排列方式 在子项为单项的时候使用

| 属性值     | 说明                                       |
| ---------- | ------------------------------------------ |
| flex-start | 从上到下                                   |
| flex-end   | 从下到上                                   |
| center     | 挤在一起居中 ( 垂直居中 )                  |
| stretch    | 拉伸 ( 默认值 ) 如果子元素有高度则不起效果 |

### align-content 于 align-items  的区别

- align-items  适用于单行情况下，只有上对齐、下对齐、居中和拉伸
- align-content 适应于换行（多行）的情况下（单行情况下无效），可以设置上对齐、下对齐、居中、拉伸以及平均分配剩余空间等属性值
- 总结：单行找 align-items 多行找 align-content

### flex-flow

> flex-flow 是 flex-direction flex-wrap 的复合写法

## flex 常见子项属性

- flex 子项目占的份数
- aling-self 控制子项自己在侧轴的排列方式
- order 属性定义子项的排列顺序（前后顺序）

### flex

> flex 属性定义子项目分配剩余空间，用 flex 来表示占多少份数

```css
.item {
	flex: <number>; /* default 0  */
}
```

### aling-self 

> aling-self 控制子项自己在侧轴上的排列方式

> aling-self 属性允许单个项目有于其他项目不一样的对齐方式，可覆盖 aling-items 属性
>
> 默认值 是 auto ，表示继承 父元素的aling-items 属性，如果没有父元素，则等于 stretch

### order 

> order 属性定义项目的排列顺序
>
> 数值越小，排列越靠前，默认为0
>
> 注意：跟 z-index 不一样

```css
.item {
	order : <number>; /* default 0  */
}
```

