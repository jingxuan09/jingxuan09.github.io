# JavaScript 常用事件和事件监听

JavaScript 事件是用户或浏览器执行的某种动作，开发者可以通过事件监听器来响应这些事件。以下是常用的 JavaScript 事件和事件监听方法。

## 常用事件列表

### 鼠标事件
- `click` - 点击事件（按下并释放鼠标按钮）
- `dblclick` - 双击事件
- `mousedown` - 鼠标按钮按下
- `mouseup` - 鼠标按钮释放
- `mousemove` - 鼠标移动
- `mouseenter` - 鼠标进入元素（不冒泡）
- `mouseleave` - 鼠标离开元素（不冒泡）
- `contextmenu` - 右键菜单事件

### 键盘事件
- `keydown` - 键盘按键按下
- `keyup` - 键盘按键释放

### 表单事件
- `submit` - 表单提交
- `change` - 表单元素值改变（如 input、select 等）
- `focus` - 元素获得焦点
- `blur` - 元素失去焦点
- `input` - 输入框值变化时触发（实时）

### 窗口事件
- `load` - 页面或资源加载完成
- `DOMContentLoaded` - DOM 加载完成（不等待样式表、图片等）
- `resize` - 窗口大小改变
- `scroll` - 滚动事件  
.scrollTop
- `beforeunload` - 页面即将卸载（关闭或刷新前）

### 其他事件
- `touchstart` - 触摸开始（移动端）
- `touchmove` - 触摸移动（移动端）
- `touchend` - 触摸结束（移动端）
- `transitionend` - CSS 过渡结束
- `animationend` - CSS 动画结束

## 事件监听方法

### 1. HTML 属性方式（不推荐）

```html
<button onclick="handleClick()">点击我</button>
```

### 2. DOM 属性方式

```javascript
element.onclick = function(event) {
  // 处理点击事件
};
```

**缺点**：只能为一个事件绑定一个处理函数，后绑定的会覆盖前面的。

### 3. `addEventListener()`（推荐）

```javascript
element.addEventListener('click', function(event) {
  // 处理点击事件
}, false);
```

**参数**：
- 第一个参数：事件类型（不带"on"前缀）
- 第二个参数：事件处理函数
- 第三个参数（可选）：是否在捕获阶段触发（默认 false，冒泡阶段）

**优点**：
- 可以为同一事件添加多个监听器
- 可以控制事件在捕获或冒泡阶段触发
- 更容易移除特定的事件监听器

### 4. `removeEventListener()`

移除事件监听器：

```javascript
function handleClick(event) {
  console.log('Clicked!');
}

// 添加监听器
element.addEventListener('click', handleClick);

// 移除监听器
element.removeEventListener('click', handleClick);
```

**注意**：要移除的监听器必须是与添加时完全相同的函数引用。

## 事件对象

事件处理函数会接收一个事件对象参数，包含事件相关信息：

```javascript
element.addEventListener('click', function(event) {
  console.log(event.target); // 触发事件的元素
  console.log(event.clientX, event.clientY); // 鼠标位置
  console.log(event.key); // 键盘事件的按键
  event.preventDefault(); // 阻止默认行为
  event.stopPropagation(); // 阻止事件冒泡
});
```

## 事件委托

利用事件冒泡机制，将事件监听器添加到父元素，通过 `event.target` 判断实际触发事件的子元素：

```javascript
document.getElementById('parent').addEventListener('click', function(event) {
  if (event.target.classList.contains('child')) {
    console.log('子元素被点击');
  }
});
```

这种方法对于动态添加的子元素特别有效，且能减少内存使用。

### 阻止事件默认行为`event.preventDefault()`
### 阻止冒泡`event.stopPropagation()`