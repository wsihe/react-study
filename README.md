# React 学习笔记
> react study demo

### HTML 模板

React 页面模版：

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Title</title>
	<script src="../build/react.js"></script>
	<script src="../build/react-dom.js"></script>
	<script src="../build/browser.min.js"></script>
</head>
<body>
	<div id="example"></div>
	<script type="text/babel">
		//后面只贴这里的主要代码
	</script>
</body>
</html>

```
- react.js 是 React 的核心库
- react-dom.js 是提供与 DOM 相关的功能
- Browser.js 的作用是将 JSX 语法转为 JavaScript 语法 (线上放在服务器完成)

### ReactDOM.render()

```
ReactDOM.render(
  <div className="react">hello,react</div>,
  document.getElementById("example")
)

```

ReactDOM.render 将模板转为 HTML 语言，并插入指定的 DOM 节点

### JSX 语法
JSX 的语法：HTML 语言直接写在 JavaScript 语言之中，不加任何引号

```
ReactDOM.render(
  <div>
  {
    names.map(function (name) {
      return <div>Hello, {name}!</div>
    })
  }
  </div>,
  document.getElementById('example')
);

```

JSX 的基本语法规则：
- 遇到 HTML 标签（以 < 开头），就用 HTML 规则解析；
- 遇到代码块（以 { 开头），就用 JavaScript 规则解析

### React 组件

React 可以将代码封装成组件（component），然后像插入普通 HTML 标签一样，在网页中插入这个组件
React.createClass 方法就用于生成一个组件类

```
var HtmlCompent = React.createClass({
  render:function () {
    return (
      <div>
    	<h1>hello,{this.props.name}</h1>
    	<ol>
    	{
    	  React.Children.map(this.props.children, function (child) {
    	    return <li>{child}</li>;
    	  })
    	}
    	</ol>
      </div>
    )
  }
});
ReactDOM.render(
  <HtmlCompent name="react">
  	<span>hello</span>
  	<span>world</span>
  </HtmlCompent>,
  document.getElementById("example")
);

```
- 组件类都必须有自己的 render 方法
- 组件类的第一个字母必须大写，否则会报错，HtmlCompent
- 组件类只能包含一个顶层标签，否则也会报错。
- 组件的属性可以在组件类的 this.props 对象上获取；this.props.name
- 添加组件属性：class 属性需要写成 className ，for 属性需要写成 htmlFor（JavaScript 保留字）
- this.props.children：组件的所有子节点，值有三种可能
 1. 当前组件没有子节点，它就是 undefined
 2. 有一个子节点，数据类型是 object
 3. 有多个子节点，数据类型就是 array
- this.props.children 用 React.Children 来处理，React.Children.map 来遍历子节点

### PropTypes && 获取真实的DOM节点
```
var Htmlcompent = React.createClass({
  propTypes:{
    name:React.PropTypes.string.isRequired
  },
  getDefaultProps : function () {
      return {
        name : 'react'
      };
  },
  handleClick:function () {
	his.refs.myInput.focus()
  },
  render:function () {
	return (
	  <div>
		<h1> hello, {this.props.name}</h1>
		<input type="text" ref="myInput"/>
		<input type="button" value="11111" onClick={this.handleClick}/>
	  </div>
	);
  }
});
ReactDOM.render(
  <Htmlcompent name="react"/>,
  document.getElementById("example")
);
```
- 组件类的PropTypes属性，就是用来验证组件实例的属性是否符合要求
- getDefaultProps 方法可以用来设置组件属性的默认值；
- 用到 ref 属性，从组件获取真实 DOM 的节点，this.refs.[refName]

this.refs.[refName] 属性获取的是真实 DOM ，所以必须等到虚拟 DOM 插入文档以后，才能使用

### this.state && 表单

```
var Htmlcomponent = React.createClass({
  getInitialState: function() {
	return {value: 'React!'};
  },
  handleChange: function(event) {
	this.setState({value: event.target.value});
  },
  render: function () {
	var value = this.state.value;
	return (
	  <div>
		<input type="text" value={value} onChange={this.handleChange} />
		<p>{value}</p>
	  </div>
	);
  }
});
ReactDOM.render(<Htmlcomponent/>, document.body);
```
1. getDefaultProps()
设置默认的props，也可以用dufaultProps设置组件的默认属性。
2. getInitialState()
在使用es6的class语法时是没有这个钩子函数的，可以直接在constructor中定义this.state。此时可以访问this.props。
3. render()
react最重要的步骤，创建虚拟dom，进行diff算法，更新dom树都在此进行。此时就不能更改state了。

### 组件的生命周期


组件的生命周期分成三个状态：
```
Mounting：已插入真实 DOM
Updating：正在被重新渲染
Unmounting：已移出真实 DOM
```
React 为每个状态都提供了两种处理函数，will 函数在进入状态之前调用，did 函数在进入状态之后调用，三种状态共计五种处理函数:

1. componentWillMount()
组件初始化时只调用，以后组件更新不调用，整个生命周期只调用一次，此时可以修改state。
2. componentDidMount()
组件渲染之后调用，可以通过this.getDOMNode()获取和操作dom节点，只调用一次。
3. componentWillUpdate(nextProps, nextState)
组件初始化时不调用，只有在组件将要更新时才调用，此时可以修改state
4. componentDidUpdate()
组件初始化时不调用，组件更新完成后调用，此时可以获取dom节点。
5. componentWillUnmount()
组件将要卸载时调用，一些事件监听和定时器需要在此时清除。

React 还提供两种特殊状态的处理函数:

6. componentWillReceivePorps(nextProps)
组件初始化时不调用，组件接受新的props时调用。
7. shouldComponentUpdate(nextProps, nextState)
react性能优化非常重要的一环。组件接受新的state或者props时调用，我们可以设置在此对比前后两个props和state是否相同，如果相同则返回false阻止更新，因为相同的属性状态一定会生成相同的dom树，这样就不需要创造新的dom树和旧的dom树进行diff算法对比，节省大量性能，尤其是在dom结构复杂的时候。不过调用this.forceUpdate会跳过此步骤。







