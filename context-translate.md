# Context
> `Context`提供了另外一种在组件树间传递数据的方式，相对于使用`props`逐层向下传递数据的方式，使用`Context`传递数据可以不用逐层传递。

在一个典型的React应用中，都是通过`props`置顶向下传递数据的，但是当大量组件都需要某个属性（如：UI theme）时，这样的数据传递方式是非常麻烦的。`Context`提供了一种不用逐层传递数据就能在组件间传递数据的方式

## 什么时候使用Context
Context被设计用来传递数据，这些数据可以被看做可以被组件树访问的全局变量。例如： `current authenticated user`、`theme`、`preferred language` 

示例：手动的传递属性“theme”给`Button`，用于给`Button`添加样式
```
class App extends React.Component {
  render() {
    return <ToolBar theme="dark">
  }
}

function ToolBar(props) {
  // 必须传递给ToolBar一个额外的属性：theme，
  // 这个属性会传递给ThemedButton组件。
  // 如果整个应用中有个很多个Button都需要这个theme来设置自己的样式，
  // 这种传递方式是很糟糕的。
  return (
    <div>
      <ThemedButton theme={props.theme}>
    </div>
  )
}

function ThemedButton(props) {
  return <Button theme={props.theme}>
}
```

使用`context`，我们可以避免通过中间元素来传递props
```
// 使用Context，我们可以不用逐层传递就可以将一个props传递到组件树的深处

// 为当前的theme创建一个context（默认值为“light”）
const ThemeContext = React.createContext('light')

class App extends React.Component {
  render() {
    // 使用Provider向子组件传递theme，这个Provider的所有子组件都能访问到theme
    return (
      // context当前的值为：“dark”
      <ThemeContext.Provider value="dark">
        <ToolBar />
      <ThemeContext.Provider>
    )
  }
}

// 中间的组件并没有处理theme这个属性
function ToolBar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  )
}

function ThemeButton(props) {
  // 使用Consumer去读取当前的theme context
  // React找到最接近的Provider，并使用它的值
  // 在这个例子中，当前theme的值是：“dark”
  return (
    <ThemeContext.Consumer>
      {theme => <Button {...props} theme={theme} />}
    </ThemeContext.Consumer>
  )
}
```

## API
### React.createContext
```
const {Provider, Consumer} = React.createContext(defaultValue)
```

创建了一个对象：`{Provider, Consumer}`。当React渲染一个context `Consumer`时，它会从最接近且匹配的`Provider`中读取当前context的值

在渲染一个`Consumer`，没有找到匹配的`Provider`时，会采用`defaultValue`的值。

### Provider
```
<Provider value={/* some value */}>
```

`Provider`是一个React组件，允许`Consumers`订阅context的改变

`Provider`接受一个属性：`value`，这个属性会传递给这个`Provdier`的`Consumers`后代。一个Provider可以关联多个Consumers。Providers可以嵌套并且可以重写`value`的值

### Consumer
```
<Consumer>
  {value => /* render something based on the context value */}
</Consumer>
```

`Consumer`是一个React组件，可以订阅context的改变

`Consumer`的子组件必须是一个**函数**，这个函数接受当前的context的value值并返回一个react节点。`value`参数与最接近的Provider的value属性相等，如果没有Provider，`value`等于`defaultValue`。

当Provider的value属性改变（通过使用`Object.is`来判断新旧values有没有改变）时，所有的`Consumers`都会重新渲染。
