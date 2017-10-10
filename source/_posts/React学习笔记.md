---
title: React.js学习笔记
date: 2017-9-13
tag: [框架学习,React.js]
---

## ReactComponent的生命周期

### Mounting - 生成

getDefaultProps() => getInitialState() => componentWillMount => render => componentDidMount

```javascript
getInitialState: function () {
	alert('init');
	return {
		opacity: 1.0,
		fontSize: '12px'
	}
}
```

```javascript
componentWillMount: functiong() {
  	alret('will')
}
```

```javascript
componentDidMount: functiong() {
  	alret('did')
    var _self = this;
  	window.setTimeout( function () {
      	_self.setState ({
          opacity: 0.5,
          fontSize: '44px'
      	},1000);
  	})
}
```

### Updating - 更新

componentWillReciveProps => shouldComponentUpdate( true or false ) => componentWillUpdate => render => componentDidUpdate

### Unmounting - 删除

componentWillUnmount

## React事件绑定

```javascript
var TestClickComponent = React.createClass({
  getInitialState: function() {
    return {
      inputContent: ''
    }
  },
  handleClick: function (event) {
    var tipEle = React.findDOMNode(this.refs.tip);
	tipEle.style.display === 'none' ? tipEle.style.display === 'inline' : tipEle.style.display === 'none'
    event.stopPropagation();
    event.preventDefault();
  },
  handleChange: function (event) {
    this.setState({
      inputContent: event.target.value
    })
    event.stopPropagation();
    event.preventDefault();
  }
  render: function () {
    return (
    	<div>
      		// 注意是驼峰形式
    		<button onClick={this.handleClick}> Show|Hide </button>
      		<span>测试点击</span ref="tip">
        </div>
      	<div>
      		<input type="text" onChange={this.handleChange}>
      		// React中是通过state来控制状态
      		<span>{this.state.inputContent}</span>
      	</div>
    )
  }
})
```