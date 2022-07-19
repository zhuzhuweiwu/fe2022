# Hooks
动机:
在hooks出现前, 使用class组件开发

1. 组件间复用状态逻辑很难
2. 复杂组件变的难以理解, 生命周期把相互关联的代码强行拆分, 又把不相关的逻辑代码堆在一起
3. class语法, 需要开发者理解this的工作方式, 要进行this绑定, 没有稳定的语法提案, 代码冗余

使用规则:

* 只能在函数最外层使用, 不要在循环,条件判断和子函数中使用
* 只能在React函数组件中使用, 或者自定义hooks中


**useState**
`const [state, setState] = useState(initialState);`
返回一个state及更新state的函数


**useEffect**
`useEffect(callback, [deps])`
接收一个带有副作用的回调函数, 在组件渲染或更新到屏幕之后执行
融合了class中componentDidMount, componentDidUpdate,componentWillUnmount


**useContext**
```
const MyContext = React.createContext(initData)
useContext(MyContext)
```
接收一个context对象, 并返回该context的当前值。相当于class中的static contextType = MyContext或者<MyContext.Consumer>


## 额外的hooks

useReducer
`const [state, dispatch] = useReducer(reducer, initialArg, init);`
useState的替代方案, state逻辑较复杂,且包含多个子值。与redux用法相似

useCallback
```
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```
返回回调函数的memoized值, 回调函数在依赖项改变时才会更新
使用场景: 当把回调函数传递给子组件, 子组件里有shouldComponentUpdate优化时候, 比较有用



useMemo
`const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b])`
返回一个memoized值, 根据依赖项避免在每次渲染时进行高开销计算

useRef
`const refContainer = useRef(initialValue);`
创建一个ref对象, 在整个组件生命周期内保持不变
使用场景: ref, 保存可变的值相当于class中的实例字段


useImperativeHandle
自定义暴露给父组件的实例值
```
function FancyInput(props, ref) {
  const inputRef = useRef();
  useImperativeHandle(ref, () => ({
    focus: () => {
      inputRef.current.focus();
    }
  }));
  return <input ref={inputRef} ... />;
}
FancyInput = forwardRef(FancyInput);
```


useLayoutEffect
函数签名与useEffect相同
区别: 会在所有dom变更之后同步调用effect, 在浏览器执行绘制之前, useLayoutEffect内部的更新计划将被同步刷新
尽量用标准的useEffect,避免阻塞视觉更新

