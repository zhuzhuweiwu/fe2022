# refs转发

ref: 
  获取html元素的底层dom属性 <input ref={ele => {this.inputRef = ele}}>
  获取react组件的实例 <MyComp ref={myRef}>
  注意: ref不能在组件中透传下去, 因为ref不是prop属性, 就像key一样
常用方法:
回调函数
React.createRef()
React.useRef(), 可用在函数组件中



转发:
  作用: ref通过组件转发到其子组件的技巧, 为了能获取子组件中dom元素并操作
  方案: 
    1.React.forwardRef
    
```
    const FancyButton = React.forwardRef((props, ref) => (
      <button ref={ref} className="FancyButton">
        {props.children}
      </button>
    ));

    // 你可以直接获取 DOM button 的 ref：
    const ref = React.createRef();
    <FancyButton ref={ref}>Click me!</FancyButton>;
```


    
  2.将ref作为特殊名字的prop直接传递
```
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

3.方案1和2都需要对子组件有控制权, 如果没有, 只剩下findDOMNode(), 已经不太建议使用, 有时候成为兜底方案
`findDOMNode(componentRef)`