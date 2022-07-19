# class

es5和es6的继承原理

es5先创建子类的实例对象this,再将父类的方法添加到this上(Parent.apply(this))
es6采用的是先创建父类的实例this(要先在构造函数中调用super()), 然后再用子类的构造函数修改this

react中的constructor
1. 可以不写, 默认会添加一个空的constructor
2. 如果写了, 就一定要写super(), 用来初始化this

constructor中的super
1. super()
2. super(props), 如果要在constructor中用this.props, 需要给super加参数

生命周期

componentWillMount: 可能会执行多次

render

componentDidMount: 只会执行一次, 在里面执行副作用, 进行异步操作

componentWillReceiveProps(nextProps): 接收到新的props

shouldComponentUpdate(nextProps,nextState): 接收到新的state或props, 判断组件是否要更新

componentWillUpdate(nextProps, nextState): 组件即将更新

reRender

componentDidUpdate(prevProps, prevState): 更新完成


fiber架构后新增
static getDerivedStateFromProps(nextProps,prevState)
触发时机: 首次渲染, new Props, setState
作用: 接收父组件传过来的props, 映射到当前的state上。不用再通过this.props.xxx获取属性值了，统一通过this.state.xxx获取

getSnapshotBeforeUpdate(prevProps, prevState)
返回值将作为第三个参数传递给 componentDidUpdate
触发的时机:被调用于render之后、更新DOM和refs之前
作用:它能让你在组件更新DOM和refs之前，从DOM中捕获一些信息（例如滚动位置）

参考:https://juejin.cn/post/6844904021233238024