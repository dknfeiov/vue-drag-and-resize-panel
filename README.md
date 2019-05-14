# drag-and-resize-panel

> 基于 vue2.0 ，可自由拖拽、自由调整大小、收缩展开的 panel 组件

## 运行示例

``` bash

# install vue global expand
npm install -g @vue/cli-service-global

# install dependencies
npm install

# serve => demo at localhost:8080
npm start

```

## API

### props:

属性            |  说明                     |  类型                 |  默认值
:-------:       | -------                  |  :-------:            |  :-------:
resizeable      |  是否可以调整大小，默认x、y轴都可，也可单独设置x('horizontal'),y ('vertical')          |  Boolean、String              |  false
collapsable     |  是否允许收缩扩展          | Boolean              |  true   
width           |  面板初始宽度             |  Number               |  100
height          |  面板初始高度             |  Boolean              |  100
maxWidth        |  面板最大宽度             |  Boolean              |  视口宽度（100vw）
maxHeight       |  面板最大高度             |  Boolean              |  视口高度（100vh）
minWidth        |  面板最小宽度, 值必须>=36  |  Array                |  36 
minHeight       |  面板最小高度, 值必须>=36  |  Number               |  36


### Event:

事件名  |  说明  |  返回值
:-------: | -------  |  :-------:
toggle-expand  |  面板切换展开、收缩状态时触发  | expandState (当前是否展开,返回 Boolean )



### 注意：
> panel 元素为绝对定位（absolute），注意父元素 position 属性的设置。