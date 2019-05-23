# 小程序开发注意事项

## 移动体验

### 点击区域过小，多次点击无法触发事件

由于触屏关系，如果某些点击区域过小，将导致多次点击而无法触发行为响应，因此在实际开发过程中，需要将点击区域放大。

## 小程序

### `wx.navigateTo` 最多打开10个页面

小程序通过 `wx.navigateTo` 最多打开10个页面，超过10个页面将无法正常打开页面。

请避免多层级的交互方式，或者使用 `wx.redirectTo`。

### 不能使用 window 等BOM、DOM对象

页面的脚本逻辑是在JsCore中运行，JsCore是一个没有窗口对象的环境，所以不能在脚本中使用window，也无法在脚本中操作组件。

而vue的第三方组件都或多或少使用了BOM、DOM中的对象，所以无法直接在mpvue中进行使用。

### 本地资源无法通过 WXSS 获取

background-image：可以使用网络图片，或者 base64，或者使用`<image/>`标签

### 小程序内置组件以及自定义组件无法更改样式

小程序虽然提供了功能丰富的内置组件，但是无法进行样式自定义，常常不能满足实际项目中的视觉需求。

而自定义组件，则是一个类似独立封闭的webview，无法从外部覆盖其内部的样式。

因此对于第三方小程序组件库的使用，常常因为需要满足项目的视觉需求而无法使用。

### 为什么 map 组件总是在最上层

map、canvas、video、textarea 是由客户端创建的原生组件，

原生组件的层级是最高的，所以页面中的其他组件无论设置 z-index 为多少，都无法盖在原生组件上。

原生组件暂时还无法放在 scroll-view 上，也无法对原生组件设置 css 动画。

### 页面初始data声明

微信小程序、以及在wepy中，页面的初始data的定义用的都是对象字面量，当小程序被加载时，所有页面的js文件都会被执行。

自此之后，每次进入一个页面，其data的初始内容都是小程序加载时被执行好的（不考虑分包加载）。

所以我们在使用过程中，切勿在data={}声明中放置一些预期会动态更新的值，以避免对页面逻辑造成影响。

### 页面每次setState传输的数据不能超过1024KB

微信小程序:VM6260:1 vdSyncBatch 数据传输长度限制为1024KB

## 表单部分

### 小程序 button 组件去除 border 边框

微信小程序中的button组件有特定的css，背景可以用“background：none”去掉，但是边框用“border : none”则无法移除。

我们可以使用 `button::after{ border: none; }` 来去除边框。

### 小程序 label 组件的 for 特性，对 input 无效

小程序中的label组件的for特性，目前可以绑定的控件有：`<button/>`, `<checkbox/>`, `<radio/>`, `<switch/>`。

所以在H5中对input的联动特性，在小程序中将表现无效。

### 小程序 input 组件设置 placeholder 的样式表现

小程序的input组件的placeholder的样式表现，无法使用css的伪类进行设置。

需要通过input组件独有的 `input-placeholder` 类名进行设置，该类名可以通过组件的`placeholder-class` 属性进行设置（建议保持默认）。

需要注意的是，`input-placeholder` 类名不能使用交集选择器进行设置，否则样式将不能生效。

### 小程序输入框 focus 时，placeholder 字体会闪动

小程序的input组件获得焦点时，placeholder的文字内容会出现闪动的情况。

该问题在“京东购物”和“携程酒店机票火车票”等小程序中都存在这个问题。

目前没有找到相关的解决方案。

### 小程序自定义组件中使用表单控件，父级form的submit事件将无法获取这些表单控件的内容

这是由于小程序自定义组件的限制导致的，不过官方提供的behaviors特性可以帮助解决这一问题。

具体可查阅官方文档，了解 [wx://form-field 使自定义组件有类似于表单控件的行为。](#https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/behaviors.html#wxform-field)

### 小程序input与H5的属性异同表现

**相同属性：**

`value` - 输入框的初始值
`maxlength` - 限制输出长度
`placeholder` - 占位文本
`disabled` - 禁用属性，Boolean，建议无值书写

**type属性的差异性：**

| 值 | 说明 | h5类型
| ------ | ------ | ------ |
| text | 文本输入键盘 | text
| number | 数字输入键盘 | number
| idcard | 身份证输入键盘 | number
| digit | 带小数点的数字键盘 | number

输入电话号码：
小程序 - `type="number"`
h5 - `type="tel"`

**密码输入框的差异性：**

小程序 - 通过指定 `password="true"` 属性来设置
h5 - 通过指定 `type="password"` 属性来设置

**注意事项：**

h5中 `type="number"` 时，设置的 `maxlength` 长度控制无效，需要注意判断以及处理。

mpvue中input的 'type' 属性无法设置动态值，需要通过 v-if 代替，而vue中可以。
