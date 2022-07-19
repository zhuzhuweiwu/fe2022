#### less

1. 变量

```
@width: 10px;
@height: @width + 10px;

#header {
  width: @width;
  height: @height;
}
```

2. 混合 Mixins

```
.my-hover-mixin() {
  &:hover {
    border: 1px solid red;
  }
}
button {
  .my-hover-mixin();
}

```

3. 嵌套语法

4. 运算

```
// 所有操作数被转换成相同的单位
@conversion-1: 5cm + 10mm; // 结果是 6cm
@conversion-2: 2 - 3cm - 5mm; // 结果是 -1.5cm
// conversion is impossible
@incompatible-units: 2 + 5px - 3cm; // 结果是 4px
// example with variables
@base: 5%;
@filler: @base * 2; // 结果是 10%
@other: @base + @filler; // 结果是 15%
```

5. 转义,用任意字符串作为变量或属性值

```
@min768: ~"(min-width: 768px)";
.element {
  @media @min768 {
    font-size: 1.2rem;
  }
}
```

6. 函数

内置了很多函数,转换颜色、处理字符串、算术运算
