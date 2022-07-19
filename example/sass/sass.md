#### sass

.scss, .sass 两种文件的语法格式不同, 一般用.scss

##### css 功能扩展

1. 允许嵌套
2. &被代替成外层父级选择器
3. 属性嵌套

```
.funky {
  font: {
    family: fantasy;
    size: 30em;
    weight: bold;
  }
}
```

4. 占位符选择器 %foo

```
%rowItem {
  display: inline-block;
  vertical-align: middle;
}
.rowLabel {
  @extend %rowItem;
  flex: 0 1 auto;
}
```

##### sassScript

1. 变量,以$开头

```
$width: 5em;
#main {
width: $width;
}
```

2. 支持运算

拓展了@import
被导入的文件中所包含的变量或者混合指令 (mixin) 都可以在导入的文件中使用

3. @entend 继承

```
.error {
  border: 1px #f00;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

4. 混合指令 Mixin,用于定义可重复使用的样式

```
@mixin large-text {
  font: {
    family: Arial;
    size: 20px;
    weight: bold;
  }
  color: #ff0000;
}
.page-title {
  @include large-text;
  padding: 4px;
  margin-top: 10px;
}
```

5. 自定义函数

```
$grid-width: 40px;
$gutter-width: 10px;

@function grid-width($n) {
  @return $n * $grid-width + ($n - 1) * $gutter-width;
}

#sidebar { width: grid-width(5); }
```
