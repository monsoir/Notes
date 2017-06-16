# 分拆 View Controller 中的自动布局代码

- [创建一个关于自动布局的协议](#创建一个关于自动布局的协议)
- [为 UI 控件的布局代码创建一个文件](#为-ui-控件的布局代码创建一个文件)
- [View Controller 中一句话完成控件的布局](#view-controller-中一句话完成控件的布局)
- [References](#references)

当页面复杂起来的时候，或者说，当页面控件数量太多的时候，自动布局的代码也会逐渐繁琐起来，造成了 View Controller 中的代码繁重起来

因此，这里提供一种方法，将 UI 的布局代码移动到其他文件中，这个方法受到了 [👉 这篇文章](http://www.jianshu.com/p/dea57a338b23) 的启发，大部分的代码也是从这里出来 

布局的方法使用自动布局，而且还是用 SnapKit 的那种，语言使用的是 Swift 3

## 创建一个关于自动布局的协议

先创建一个文件，普通的 Swift 文件的那种，并在里面 `import SnapKit`

### 定义一个 protocol

```swift
protocol Layoutable {
    func layoutMaker() -> (ConstraintMaker) -> Void
    func layoutUpdater() -> (ConstraintMaker) -> Void
}
```

其中 `layoutMaker()` 使用来创建约束，`layoutUpdater()` 是用来更新约束

---

这里的 `(ConstraintMaker) -> Void` 是什么?

看下面一段代码，这是我们布局自定义控件的一般写法，至少是我吧

```swift
ivCalendar.snp.makeConstraints { (make) in
  make.left.equalTo(contentView).offset(PreviewCellConstants.marginToFrame)
  make.centerY.equalTo(lbDate)
  make.size.equalTo(PreviewCellConstants.LabelCalendar.size)
}
```

再看 `makeConstraints` 方法的定义

```swift
public func makeConstraints(_ closure: (_ make: ConstraintMaker) -> Void) {
   ConstraintMaker.makeConstraints(item: self.view, closure: closure)
}
```

可以看出，我们写的一大串的布局代码，其实是一个 Closure，类型是 `(ConstraintMaker) -> Void`，而这里，就是我们用来分拆代码的关键了

---

对于上面的 `func layoutUpdater() -> (ConstraintMaker) -> Void` 协议方法，很多时候，我们只需要 make，而不太需要 update，这里可以选择将它删除。但是，也可以为它实现一个默认的实现，也就是 protocol 的 extension

> 如果没用到 `func layoutUpdater() -> (ConstraintMaker) -> Void` 这个方法，而且不作处理（删除它，或者提供默认实现），那么，在 implement 协议的时候，编译器将就会报错：没有 conforms to protocol

Layoutabel 的默认实现，只是简单地返回一个空的 Closure，当我们有实现对应的方法时，调用的将会是后者

```swift
extension Layoutable {
    func layoutMaker() -> (ConstraintMaker) -> Void {
        return { make in
            
        }
    }
    
    func layoutUpdater() -> (ConstraintMaker) -> Void {
        return { make in
            
        }
    }
}
```

### 为 UIView 添加布局相关的方法

在与协议的同一个文件中，顺便向 UIView 添加一个 extension，添加布局方法，这个方法，是之后在 View Controller 中用到的一行代码实现布局的方法

```swift

// MARK: - 自动布局的 extension
extension UIView {
    func makeLayout(layouter: Layoutable) {
        snp.makeConstraints(layouter.layoutMaker())
    }
    
    func updateLayout(layouter: Layoutable) {
        snp.updateConstraints(layouter.layoutUpdater())
    }
}
```

可以看到，`snp.makeConstraints(...)` 这一句，正是我们布局时写得算是最多的一句代码了。但现在，我们将会很少看到它了

同时，我们也可以看到，`snp.makeConstraints(...)` 中传入的参数，都是通过协议来调用的（大致意思，意会一下吧），这也许就是 面向协议编程 的抽象吧 

之后，我们的工作将会聚焦在：如何创建一个实现 Layoutable 协议的类和它的实例

## 为 UI 控件的布局代码创建一个文件

下面的工作，可能就显得略烦琐，甚至看起来有点“蠢”了

### 为 UI 控件创建一个文件

为你觉得需要的 UI 控件，创建一个普通的 Swift 文件，并 `import SnapKit`

### 创建一个 struct，并声明实现 Layoutable 协议

```swift
struct ClipRecordPanelLayout: Layoutable {
    
}
```

---

为什么使用 struct，而不是使用 class ？

我的理解是这样的：

对于 class，在赋值的时候，实例会对其属性（对象的那种）默认进行了一个强引用，而这种强引用，很多时候就是造成内存问题（像循环引用）的原因

而对于 struct，在赋值的时候，实例只会对属性进行一系列的复制，不带引用的复制，因此可以避免出现内存问题

---

### 定义一些属性

```swift
struct ClipRecordPanelLayout: Layoutable {
    var views: (UIView)
    var constants: (panelHeight: CGFloat?, bottomOffset: CGFloat?)
}
```

在布局的时候，我们通常需要其他的一些 view 来作为参照物，并且需要写死一些常量，因此，在这里，使用了 views 和 constants 来分别装载这些参照物和常量

views 和 constants 都是 tuple 的类型，使用 tuple 的原因是，tuple 可以（天生）存放不同类型的数据，并且，可以为其中的值进行命名

在这个例子中，constants 存放的都是 CGFloat? 类型，但也可以存放不同的类型，如

```swift
var constants: (bottomOffset: CGFloat, size: CGSize)
```

### 实现协议方法

```swift
struct ClipRecordPanelLayout: Layoutable {
    
    var views: (UIView)
    var constants: (panelHeight: CGFloat?, bottomOffset: CGFloat?)
    
    func layoutMaker() -> (ConstraintMaker) -> Void {
        let superView = views
        let (panelHeight, _) = constants
        return { make in
            make.bottom.equalTo(superView)
            make.left.right.equalTo(superView)
            make.height.equalTo(panelHeight!)
        }
    }
    
    func layoutUpdater() -> (ConstraintMaker) -> Void {
        let superView = views
        let (_, bottomOffset) = constants
        return { make in
            make.bottom.equalTo(superView).offset(bottomOffset!)
        }
    }
}
```

这里分别实现了 `layoutMaker()` 和 `layoutUpdater()` 的方法，分别对应的是建立约束和更新约束的操作

在获取参照物和常量的时候，使用了 tuple 的解构，将数据分别存放到不同的变量中，不需要的数据，直接使用 `_` 进行忽略

在 constants 中，其中的元素的数据类型使用的 `CGFloat?`，是因为这两个数据，并不是同时都需要用到，那么，为了方便起见，在需要某一个参数的时候，直接传 `nil`

所以，这里的一个文件，只定义了一类的约束（也许有多个 UI 控件的布局方式是一样的，就是常量不同罢了）


## View Controller 中一句话完成控件的布局

定义完了一类控件的约束之后，最后，在 View Controller 中将约束应用到 UI 控件中，像这样：

```swift
panel.makeLayout(layouter: ClipRecordPanelLayout(with: (view), constants: (PanelHeight, nil)))
```

这样，就一句话实现了对 UI 控件的布局了，当然这是针对简单的情况，要是含有多个参照物和常量，还是需要将 Layoutable 的实例单独抽取出来新建，避免一行代码过长

## References

[MVVM与Controller瘦身实践](http://www.jianshu.com/p/dea57a338b23)

