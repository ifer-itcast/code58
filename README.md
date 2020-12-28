1\. 监听 input 框的 keyup 事件，根据输入的内容发送 JSONP 请求获取【搜索建议】


2\. 根据得到的【搜索建议】，利用模板引擎渲染出列表


## 防抖

节流和防抖都是性能优化的一种手段，能降低事件执行的频率！

<!-- 原型链：多个对象之间通过 __proto__ 链接起来的这种关系就是原型链 -->

防抖（debounce）：持续触发（事件）不执行，不触发的一段时间后才执行！

节流（throttle）：持续触发（事件）也执行，只不过执行的频率变低了！


lodash 是一个工具库，_.debounce，_.throttle