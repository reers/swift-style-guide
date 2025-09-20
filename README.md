# Swift 代码规范

## 概述

为了使 Swift 代码更加清晰，简洁，需要制定一份通用详细的代码规范。本规范基于 Swift 5.5 制定, 参考了其他组织公开的代码规范，并对他们进行了一定程度的修改。欢迎参与讨论，提出意见，共同建设。

- [Apple Swift API Design Guidelines](https://www.swift.org/documentation/api-design-guidelines/#parameter-names)
- [The Official Kodeco(raywenderlich) Swift Style Guide](https://github.com/kodecocodes/swift-style-guide)
- [Google Swift Style Guide](https://google.github.io/swift/) ([中文版](https://pages.swift.gg/google-swift-style-guide-in-chinese/))
- [Airbnb Swift Style Guide](https://github.com/airbnb/swift)
- [LinkedIn Swift Style Guide](https://github.com/linkedin/swift-style-guide#1-code-formatting)

符号说明:

```swift
✅ 建议使用
🟡不反对但也不推荐使用
❌ 禁止使用
```

## 代码格式

> ##### 缩进使用 4 个空格而不是 tab  `强制❗️`

不要在工程里使用 Tab，使用空格来进行缩进。在 `Xcode > Preferences > Text Editing > Indentation` 将 Tab 和自动缩进都设置为 **4** 个空格，`Tab Key` 选项设为 `Indents in leading whitespace`

> ##### 单行最大字符数 120 `建议🌵`

过长的一行代码将会导致可读性问题，业界通常建议单行最大字符数为 80，但随着现在开发设备屏幕越来越大，以及苹果推荐的描述性命名风格，120 个字符更为合适。在 `Xcode > Preferences > Text Editing > Page guide at column:` 中将最大行长设置为 **120**，来使越界更容易被发现

> ##### 函数实现最大行数 100 行 `强制❗️`

一个函数实现的行数建议**不要超过 100 行**。如果行数太多，说明需要进行逻辑拆分了。

> ##### 函数参数不宜过多  `建议🌵`

除 `init` 方法外, 函数的参数数量**不要大于 5 个**，超过之后应该封装为参数 model 或使用 builder 模式。

如果函数部分参数拥有默认值, 那么参数数量可以适当增加, 但总数也**不要超过 10 个**。

```swift
// ✅
struct IndicatorParams {
    let style: IndicatorViewStyle
    let text: String
    let image: UIImage
}
func showIndicator(withParams params: IndicatorParams)

public func popup<PopupContent: View>(
    isPresented: Binding<Bool>,
    type: Popup<Int, PopupContent>.PopupType = .`default`,
    position: Popup<Int, PopupContent>.Position = .bottom,
    animation: Animation = Animation.easeOut(duration: 0.3),
    autohideIn: Double? = nil,
    dragToDismiss: Bool = true,
    backgroundColor: Color = Color.clear,
    dismissCallback: @escaping () -> () = {},
    @ViewBuilder view: @escaping () -> PopupContent
) -> some View

// ❌
func showIndicator(
    withStyle style: IndicatorViewStyle,
    text: String,
    image: UIImage,
    maxLine: Int,
    expectedWidth: CGFloat,
    dismissDelay: TimeInterval,
    autoDismiss: Bool,
    dismissHandler: DismissHandler
)
```

> ##### 控制语句最大嵌套层级  `强制❗️`

控制语句的嵌套层级**不要超过 3 层**，否则将难以阅读和维护

```swift
// ❌
if condition1 {
    // depth = 1
    if condition2 {
        // depth = 2
        for i in 0...10 {
            // depth = 3
            if condition4 {
                // ❌ bad - depth = 4
                if condition5 {
                    // ❌ bad - depth = 5
                }
                return;
            }
        }
    }
}
```

> ##### 禁止使用分号  `强制❗️`

禁止使用分号(`;`), 无论是用于终止或者分割语句。换而言之，分号只可能出现在字符串字面量或者注释中。

```swift
// ✅
func printSum(_ a: Int, _ b: Int) {
    let sum = a + b
    print(sum)
}

func around<T>(_ closure: () throws -> T) rethrows -> T {
    lock()
    defer { unlock() }
    return try closure()
}
```

```swift
// ❌
func printSum(_ a: Int, _ b: Int) {
    let sum = a + b;
    print(sum);
}

func around<T>(_ closure: () throws -> T) rethrows -> T {
    lock(); defer { unlock() }
    return try closure()
}
```

> ##### 每行一个语句  `强制❗️`

- 每行**最多**一个语句，每个语句后换行，除非该行结尾包含代码块, 且代码块中只有 0 或 1 条语句。
- 代码块中若只有一条语句, 则允许语句和 `{}` 放在同一行, 其他所有情况需要换行
- 单行书写时, 若存在 `{}` 嵌套, 则**不允许**单行书写

```swift
// ✅
guard let value = value else { return 0 }

defer { file.close() }

switch someEnum {
case .first: return 5
case .second: return 10
case .third: return 20
}

let squares = numbers.map { $0 * $0 }

var someProperty: Int {
    get { return otherObject.property }
    set { otherObject.property = newValue }
}

var result: Bool {
    get { 
        return read { $0 } 
    }
    set {}
}

var someProperty: Int { return otherObject.somethingElse() }

required init?(coder aDecoder: NSCoder) { fatalError("no coder") }
```

```swift
// ❌
var a = 5, b = 10
guard let value = value else { completion(); return 0 }

var result: Bool {
    get { return read { $0 } } // 单行有多个大括号嵌套时可读性变差, 不推荐
    set {}
}
```

> ##### 闭包

- 增加闭包参数标签  `建议🌵`

在声明闭包时, 为闭包的每个参数增加标签会使其更加易读. 但如果是比较明确的场景, 也可以不增加参数标签.

```swift
// ✅
var lynxAction: ((_ actionType: String, _ extraParams: String) -> Void)?
var isAdult: (_ age: Int) -> Bool

var logoImageDownloadCompletion: (UIImage?) -> Void
```

- 适当省略编译器可推断的内容  `建议🌵`

闭包表达式中的参数类型, 是否抛异常, 返回值信息, 如果通过阅读上下文可以明确知晓, 又或是众所周知的, 显而易见的场景, 建议省略它们. 但如果不能轻易明确, 需要跳转或查看注释方能确认, 则建议不要省略

```swift
// ✅
let closure: (String, Int) throws -> Bool = {
    [weak obj, unowned obj2] string, int in
    // ...
    return true
}
// 🟡
let closure: (String, Int) throws -> Bool = {
    [weak obj, unowned obj2] (string: String, int: Int) throws -> Bool in
    // ...
    return true
}
```

- 参数表的括号  `强制❗️`
  - 当参数表的后面写有 `throws` 或 `->`, 那么闭包参数表需要加上小括号
  - 当每个参数标明了具体类型, 需要加上小括号 (此时不加括号会报错)
  - 其他场景都不加小括号

```swift
// ✅
titleLabel.snp.makeConstraints { make in
    // ...
}

let closure: (String, Int) throws -> Bool = {
    [weak obj, unowned obj2] string, int in
    // ...
    return true
}

let closure: (String, Int) -> Bool = {
    (string, int) -> Bool in
    // ...
    return true
}

let closure: (String, Int) throws -> Bool = {
    [weak obj, unowned obj2] string, int in
    // ...
    return true
}
```

```swift
// ❌
titleLabel.snp.makeConstraints { (make) in
    // ...
}

let closure: (String, Int) throws -> Bool = {
    [weak obj, unowned obj2] (string, int) in
    // ...
    return true
}

let closure: (String, Int) throws -> Bool = {
    string, int -> Bool in
    // ...
    return true
}
```

- 闭包换行 `强制❗️`

内容较多需要换行时, 弱引用, 闭包参数, `throws`, 返回值, `in` 关键字, 全都放在新的一行, 左边缩进一个单位, 然而此时新行与下方闭包实现有着同样的缩进, 为了区分开, 还要再空一行来书写闭包实现, 以便阅读.

```swift
// ✅ 通常情况下, 如果不写闭包参数类型, 只写参数标签名, 基本上一行就足够写下了
let src = UITableViewDiffableDataSource<Section, Item>(tableView: tableView) {
    [weak self] (tableView, indexPath, item) -> UITableViewCell? in
    此处空一行
    guard let self = self else { return nil }
    let cell = ...
    return cell
}
// ✅
let src = UITableViewDiffableDataSource<Section, Item>(tableView: tableView) {
    (tableView: UITableView, indexPath: IndexPath, item: Item) -> UITableViewCell? in
    此处空一行
    let cell = ...
    return cell
}
```

```swift
// ❌
let closure: (String, Int) throws -> Bool = { string, int 
    -> Bool in
    print("abc")
    return true
}
```

> ##### 函数声明和定义  `强制❗️`

先说基本的换行逻辑, Swift 函数的情况比较复杂, 为了讲清楚这个问题, 我们将其分为五个区段

```swift
①attributes 
②modifiers func name<generic arguments>(
③formal arguments
④) throws -> result 
⑤where generic constraints {

// 例:
@objc
@available(iOS 10.0, macOS 10.12, *)
@discardableResult
public func index<Elements: Collection, Element>(
    of element: Element,
    in collection: Elements
) throws -> Elements.Index? 
where Elements.Element == Element, Element: Equatable {
    // ...
}
```

- 每个 `attribute` 单独占用一行
- `modifier`, `func`, 函数名, 泛型参数定义, 一直到左小括号 `(` 保持在同一行
- 形参每个参数单独一行, 且保留一个缩进单位
- 右小括号 `)`, `throws`, `->`, 返回值保持在同一行
- 泛型约束和函数体左大括号 `{` 保持在同一行, 如果这部分较短可以与上面粉色区域放在同一行
- 除了 `attribute`, 其他区段如果能在一行写完, 则无需换行, 如果写不完, 则优先形参换行, 然后是泛型约束区段
- 除了 `attribute` 和形参区段, 其他每个区段**禁止**再拆开换行
- 如果没有返回值, 也没有泛型约束, 那么 `)` `throws` `{` 必须在同一行

```swift
// ✅
func foo(bar: Int) -> String {
    // ...
}

mutating func add(
    _ component: Calendar.Component, 
    value: Int
) {
    // ...
}

static func blend(
    _ color1: UIColor,
    intensity1: CGFloat = 0.5,
    with color2: Color,
    intensity2: CGFloat = 0.5
) -> Color {
    // ...                        
}

public func index<Element>(of element: Element) throws -> Elements.Index? 
where Element: Equatable {
    // ...
}

public func index<Element>(
    of element: Element
) throws -> Elements.Index? where Element: Equatable {
    // ...
}

@available(iOS 10.0, macOS 10.12, *)
@discardableResult
public func index<Elements: Collection, Element>(
    of element: Element,
    in collection: Elements
) throws -> Elements.Index? 
where Elements.Element == Element, Element: Equatable {
    // ...
}
```

```swift
// ❌
public func index<T, S>(of element: T,  // AVOID.
                        in collection: S) -> Int
    where T: Equatable {
    doSomething()
}

mutating func add(
    _ component: Calendar.Component, 
    value: Int) {
    // ...
}

func foo(bar: Int) 
-> String {
    // ...
}

public func index<Element>(of element: Element) 
-> Elements.Index? where Element: Equatable {
    // ...
}

public func index<Element>(of element: Element) 
throws -> Elements.Index? 
where Element: Equatable {
    // ...
}
```

如果返回值类型为元组, 元组中每个元素可以单独一行, 或使用 `typealias` 为元组起别名后再使用

```swift
// ✅
public func performanceTrackingIndex<Elements: Collection, Element>(
    of element: Element,
    in collection: Elements
) -> (
    Element.Index?,
    PerformanceTrackingIndexStatistics.Timings,
    PerformanceTrackingIndexStatistics.SpaceUsed
) {
    // ...
}


typealias PerformanceTrackingIndexResult = (
    Element.Index?,
    PerformanceTrackingIndexStatistics.Timings,
    PerformanceTrackingIndexStatistics.SpaceUsed
)

public func performanceTrackingIndex<Elements: Collection, Element>(
    of element: Element,
    in collection: Elements
) -> PerformanceTrackingIndexResult {
    // ...
}
```

方法中如果有多个参数且包含闭包参数，通常建议将闭包参数都放到最后

```swift
// ✅
public init(
    title: String?, 
    style: UIAlertAction.Style, 
    handler: ((UIAlertAction) -> Void)? = nil
)

// ❌ 如果闭包参数没有放在最后, 将无法使用尾随闭包的写法
struct Test {
    init(
        title: String?,
        style: Int,
        handler: ((String) -> Void)? = nil,
        name: String
    ) {}
}
// 且在调用时如果函数参数不换行, 整体看起来不美观
let test = Test(title: "", style: 2, handler: { s in
    // ...
}, name: "")
```

> ##### 函数调用  `强制❗️`

- 函数调用如果需要换行, 则每个实参单独一行, 并在原始行的基础上缩进一个单位

```swift
// ✅
func move(from start: Point, to end: Point)
// 所有参数在同一行
x.move(from: x, to: y) 

// 或者每行一个参数
x.move(
    from: x,
    to: y
)

// 复杂场景
someFunctionWithABunchOfArguments(
    someStringArgument: "hello I am a string",
    someArrayArgument: [
        "dadada daaaa daaaa dadada daaaa daaaa dadada daaaa daaaa",
        "string one is crazy - what is it thinking?"
    ],
    someDictionaryArgument: [
        "dictionary key 1": "some value 1, but also some more text here",
        "dictionary key 2": "some value 2"
    ],
    someClosure: { parameter1 in
        print(parameter1)
    }
)
```

```swift
// ❌
x.move(from: x, 
       to: y) 
       
x.move(
from: x, 
to: y) 

myObject.doFoo(with: arg1,
    name: arg2, error: arg3)  // 要换行就所有参数都换行
```

- 函数的右结束括号 `)`, 既可以和最后一个实参在同一行也可以另起一行。

```swift
// ✅
let index = index(
    of: veryLongElementVariableName,
    in: aCollectionOfElementsThatAlsoHappensToHaveALongName)

let index = index(
    of: veryLongElementVariableName,
    in: aCollectionOfElementsThatAlsoHappensToHaveALongName
)
```

- 当函数有尾随闭包时:
  - 可以采用尾随闭包的写法. 如果是多尾随闭包, 首个闭包没有标签, 后面的其他闭包标签跟在上一个闭包的后面. 此方式适用于函数**非闭包**参数较少, 第一行比较短, 不会超字符数限制的场景.
    ```swift
    // ✅
    UIView.animate(withDuration: 0.3) {
    // ...
    }
        
    UIView.animate(
        withDuration: 0.3,
        arg1: 2234234,
        arg2: "abc"
    ) {
    // ...
    }
        
    // 多尾随闭包场景: 首个闭包没有标签, 后面的其他闭包标签跟在上一个闭包的后面.
    // 此方式适用于函数前面的非闭包参数较少, 一行可以写完的场景.
    UIView.animate(withDuration: 0.5) {
    // ...
    } completion: { finished in
    // ...
    }
    ```

   - 也可以不使用尾随闭包的写法, 按照函数调用时参数换行的基本规则即可, 每个闭包的右大括号`}`与该参数标签左对齐. 此方式适用于各种场景.
    ```swift
    // ✅
    UIView.animate(
        withDuration: 0.4,
        animations: {
            // ...
        })
        
    UIView.animate(
        withDuration: 0.5,
        animations: {
            // ...
        },
        completion: { finished in
            // ...
        }
    )
    ```

    ```swift
    // ❌ 没有采用尾随闭包, `})` 写在一起不美观
    UIView.animate(withDuration: 0.3, animations: {
    // ...
    })
    
    // ❌ 不要在有多个尾随闭包的情况下采用单尾随闭包的写法
    UIView.animate(withDuration: 0.25, animations: {
    // ...
    }) { finished in
    // ...
    }
    
    // ❌ 没有采用多尾随闭包的写法, 且换行逻辑不对
    UIView.animate(withDuration: 0.5, animations: {
    // ...
    }, completion: { finished in
    // ...
    })
    ```
	- 当函数除尾随闭包外没有其他参数, 必须采用尾随闭包的写法, 且要省略`()`
    ```swift
    func getBubble(completion: (String) -> Void) {
        // ...
    }
    // ✅
    getBubble { bubble in
        // ...
    }
    
    // ❌ 
    getBubble() { bubble in
        // ...
    }
    // ❌ 
    getBubble(completion: { bubble in
        // ...
    })
    ```
    - 如果函数前面的参数换行了, 那么尾随闭包中的参数就不需要换行了

    ```swift
    // ✅ 函数前面的参数换行了, 尾随闭包中参数就不要换行了
    let src = UITableViewDiffableDataSource<Section, Item>(
        tableView: tableView,
        config: SomeConfig
    ) { [weak self] (tableView, indexPath, item) -> UITableViewCell? in
        guard let self = self else { return nil }
        let cell = ...
        return cell
    }
    ```
    
	```swift
    // ❌ 闭包整体左侧没有缩进, 使得闭包参数看起来像是函数参数,对读者产生干扰
    let src = UITableViewDiffableDataSource<Section, Item>(tableView: tableView) { [weak self] (
        tableView: UITableView,
        indexPath: IndexPath,
        item: Item
    ) -> UITableViewCell? in
        guard let self = self else { return nil }
        let cell = ...
        return cell
    }
    // ❌ 函数前面的参数换行了, 尾随闭包中的参数也换行了, 使得闭包参数看起来像是函数参数
    // 容易误解
    let src = UITableViewDiffableDataSource<Section, Item>(
        tableView: tableView,
        config: SomeConfig
    ) { [weak self] (
        tableView: UITableView,
        indexPath: IndexPath,
        item: Item
    ) -> UITableViewCell? in
        guard let self = self else { return nil }
        let cell = ...
        return cell
    }
    ```
    - 如果以上几种格式都不能很好的呈现, 建议将闭包提取为变量传递给函数
    ```swift
    // ✅ 不换行的形式
    let provider = { (tableView: UITableView, indexPath: IndexPath, item: Item) -> UITableViewCell? in
        let cell = ...
        return cell
    }
    let src = UITableViewDiffableDataSource<Section, Item>(tableView: tableView, cellProvider: provider)
    
    // ✅ 换行的形式
    let provider = { [weak self] (
        tableView: UITableView,
        indexPath: IndexPath,
        item: Item
    ) -> UITableViewCell? in
        guard let self = self else { return nil }
        let cell = ...
        return cell
    }
    let src = UITableViewDiffableDataSource<Section, Item>(tableView: tableView, cellProvider: provider)
    ```

> ##### 类型和拓展声明  `强制❗️`

- 不推荐多实现协议的情况下换行. 如果单行写不下建议使用`extension`的方式书写. (下面例子适用于 `class`、`struct`、`enum`、`extension` 和 `protocol`)

- `where`泛型约束可以自己单独一行, 但从`where`到`{` 之间的内容不允许换行

```swift
// ✅
class MyClass: MySuperclass, MyProtocol, SomeoneElsesProtocol {
    // ...
}

class MyContainer<Element>: MyContainerSuperclass, MyContainerProtocol {
    // ...
}

// ✅
class MyClass: MySuperclass {
    // ...
}
extension MyClass: MyProtocol, SomeoneElsesProtocol {
    // ...
}

// ✅
class MyContainer<BaseCollection>: MyContainerSuperclass
where BaseCollection: Collection {
    // ...
}

// ✅
class MyContainer<BaseCollection>: MyContainerSuperclass {
    // ...
}
extension MyContainer: SomeProtocol where BaseCollection: Collection {
    // ...
}
```

```swift
// 🟡
class MyContainer<Element>:
    MyContainerSuperclass,
    MyContainerProtocol,
    SomeoneElsesContainerProtocol,
    SomeFrameworkContainerProtocol {
    此处空一行
    // ...
}

// 🟡
class MyContainer<BaseCollection>:
    MyContainerSuperclass,
    MyContainerProtocol,
    SomeoneElsesContainerProtocol,
    SomeFrameworkContainerProtocol
where BaseCollection: Collection {
    // ...
}
```

```swift
// ❌ 大括号不要换行
class MyClass:
    MySuperclass,
    MyProtocol,
    SomeoneElsesProtocol,
    SomeFrameworkProtocol
{
    // ...
}

// ❌ 逗号, 冒号不要写在前面
class MyContainer<Element>
    : MyContainerSuperclass
    , MyContainerProtocol
    , SomeoneElsesContainerProtocol
    , SomeFrameworkContainerProtocol
{
    // ...
}
```

> ##### 控制流语句  `强制❗️`

- 当控制流语句（主要是 `if` 和 `guard`）需要换行时, 如果有 `&&` `||` 应该放在行首，这样可以更直观的理解他们之间的逻辑关系. 另外，必要时可以用括号或者将 condition 抽成变量来提高可读性.
- 换行后每一行的 condition 与上一行的 condition 左对齐. 这里需要注意的是, 如果是 `if let` 或者 `guard let`, 每个 condition 换行后 Xcode 会默认对齐的, 但如果是 `&&` `||` 组合的 condition 换行后, Xcode 默认是不对齐的, 这种格式也是可接受的, 毕竟这种场景很多, 如果每次都要手动调整, 是让人难以接受的.

```swift
// 🟡
// Xcode默认换行逻辑是不对齐的, 也可以接受, 但最好手动调整为对齐
if aLongcondition1
    && condition2
    && (condition3 || condition4) {
    return false
}
// ✅
// 以下格式为手动调整缩进, 也是比较理想的样式
if aLongcondition1
   && condition2
   && (condition3 || condition4) {
    return false
}

let aNameOfConditon3Or4 = condition3 || condition4
let isInvalid = aLongcondition1 && condition2 && aNameOfConditon3Or4
if isInvalid {
    return false
}

if let value = someOptionalValueFunc(),
   let value2 = anotherOptionalValueFunc() {
    doSomething()
}

guard let value = aValueReturnedByAVeryLongOptionalThing(),
      let value2 = aDifferentValueReturnedByAVeryLongOptionalThing() else {
    doSomething()
}
```

```swift
// ❌
if condition1 && 
   condition2 && 
   (condition3 || condition4) {
    return false
}
```

- `if` 和 `guard` 不允许单独一行

```swift
// ❌
if 
    let value = someOptionalValueFunc(),
    let value2 = anotherOptionalValueFunc(),
    let value3 = aOptionalValue {
    doSomething()
}

guard
    let value = someOptionalValueFunc(),
    let value2 = anotherOptionalValueFunc(),
    let value3 = aOptionalValue 
else {
    doSomething()
}
```

- `guard` `else` 场景中:
  - 如果 condition 换行了, 那 `else` 也要换行, 不允许放在最后一个 condition 同一行.
  - `else` 在任何场景下都不单独一行, 如果 condition 换行了, 那 `else` 也不换行, 与最后一个 condition 同一行, 或与最后一个 condition 的尾括号同一行
  - `else` 在任何场景下都要和 `{` 在同一行
  - `else` 块中如果只有一条语句, 可以单行实现

```swift
// ✅ 
guard index < count else { return self }

// condition不换行的场景
guard let value = aOptionalValue, let value2 = aOptionalValue2 else {
    return false
}

guard let value = aOptionalValue, 
      let value2 = aOptionalValue2 else {
    return false
}

guard let mountedVolumes = FileManager.default.mountedVolumeURLs(
    includingResourceValuesForKeys: [],
    options: [.produceFileReferenceURLs]
) else { return URL(fileURLWithPath: "/") }
```

```swift
// ❌
guard let value = aOptionalValue, let value2 = aOptionalValue2 
else {
    return false
}

guard let value = aOptionalValue, let value2 = aOptionalValue2 
else { return false }

guard let value = aOptionalValue, 
      let value2 = aOptionalValue2 
else
{
    return false
}

guard let value = aOptionalValue, 
      let value2 = aOptionalValue2 
else { return false }
```

- `if`、`guard`、`while` 或 `switch` 关键字后面的顶层表达式**不需要**使用括号。

```swift
// ✅ 
if x == 0 {
    print("x is zero")
}

if (x == 0 || y == 1) && z == 2 {
    print("...")
}
```

```swift
// ❌
while (x == 0) {
    print("x is zero")
}

if ((x == 0 || y == 1) && z == 2) {
    print("...")
}
```

> ##### 禁止水平对齐  `强制❗️`

**不要**在声明属性时，故意将其类型名左对齐，这样做会使下一个添加代码的同学极其难受，因为可能 TA 并没有安装类似的美化插件，只能手动按空格来对齐，并且这样的代码有时中间空白会过多，容易看串行(háng)，类似的场景还有赋值号对齐, 注释对齐等等.

```swift
// ✅ 
struct DataPoint {
    var value: Int
    var primaryColor: UIColor
}

let robertAge = 32
let annalouiseAge = 25
let bobAge = 250
let dorothyAge = 56

let name = "phoenix" // blabla
let temp = 123 // xxx
```

```swift
// ❌
struct DataPoint {
    var value:        Int
    var primaryColor: UIColor
}

let robertAge     = 32
let annalouiseAge = 25
let bobAge        = 250
let dorothyAge    = 56

let name = "phoenix" // blabla
let temp = 123       // xxx
```

> ##### switch 语句  `强制❗️`

`case` 关键字的缩进和 `switch` 保持一致, `case` 分支块中如果只有一个语句, 可以与 `case` 同行, 如果换行应该相对 `case` 再缩进一个单位

```swift
// ✅
switch order {
case .ascending:
    print("Ascending")
case .descending:
    print("Descending")
case .same:
    print("Same")
}

switch order {
case .ascending: print("111")
case .descending: print("222")
case .same: print("333")
}
```

```swift
// ❌
switch order {
    case .ascending:
        print("Ascending")
    case .descending:
        print("Descending")
    case .same:
        print("Same")
}

switch order {
case .ascending:
print("Ascending")
case .descending:
print("Descending")
case .same:
print("Same")
}
```

不同于 OC, Swift 中每个 `case` 执行完默认就是 `break`, 所以不要在代码中加入 `break`

```swift
// ❌
switch order {
case .ascending:
    print("Ascending")
    break
case .descending:
    print("Descending")
case .same:
    print("Same")
}
```

当 `switch` 里的多个 `case` 执行同样的语句时，这些 `case` 可以合并成一个范围或者逗号分隔的列表。声明多个 `case` 却不做任何事，只是 `fallthrough` 到后面的 `case` 是**不允许**的。也就是说，不能有只执行 `fallthrough` 语句的 `case`。包含其他语句再 `fallthrough` 到下一个 `case` 是允许的。

```swift
// ✅
switch value {
case 1: print("one")
case 2...4: print("two to four")
case 5, 7: print("five or seven")
case 8:
    print("一些单独的逻辑")
    fallthrough
case 9...: print(">= 8")
default: break
}
```

```swift
// ❌
switch value {
case 1: print("one")
case 2: fallthrough
case 3: fallthrough
case 4: print("two to four")
case 5: fallthrough
case 7: print("five or seven")
default: break
}
```

> ##### 枚举  `强制❗️`

通常来说，一个 `enum` 里每行只有一个 `case`。逗号分隔形式只能在枚举项都没有关联值或者原始值时使用，所有枚举项都能从名字明确其含义而不需要额外的注释，就可以写在同一行。

```swift
// ✅
public enum Token {
    case comma
    case semicolon
    case identifier
}

public enum Token {
    case comma, semicolon, identifier
}

public enum Token {
    case comma
    case semicolon
    case identifier(String)
}
```

```swift
// ❌
public enum Token {
    case comma, semicolon, identifier(String)
}
```

如果 `enum` 里所有枚举项都需要被声明为 `indirect`，那这个 `enum` 就声明为 `indirect`，单独枚举项前面的关键字可以省略。

```swift
// ✅
public indirect enum DependencyGraphNode {
    case userDefined(dependencies: [DependencyGraphNode])
    case synthesized(dependencies: [DependencyGraphNode])
}
```

```swift
// ❌
public enum DependencyGraphNode {
    indirect case userDefined(dependencies: [DependencyGraphNode])
    indirect case synthesized(dependencies: [DependencyGraphNode])
}
```

当 `enum` 的枚举项没有关联值时，不应该出现空的括号。

```swift
// ✅
public enum BinaryTree<Element> {
    indirect case node(element: Element, left: BinaryTree, right: BinaryTree)
    case empty
}
```

```swift
// ❌
public enum BinaryTree<Element> {
    indirect case node(element: Element, left: BinaryTree, right: BinaryTree)
    case empty()
}
```

枚举项必须遵循一定的可解释排序逻辑。如果没有明显的排序逻辑，按照枚举项名字的首字母排序。

在下面的例子中，枚举项根据其表示的 HTTP 状态码数字进行排序，并通过空行进行分组。

```swift
// ✅
public enum HTTPStatus: Int {
    case ok = 200

    case badRequest = 400
    case notAuthorized = 401
    case paymentRequired = 402
    case forbidden = 403
    case notFound = 404

    case internalServerError = 500
}
```

同样的枚举，下面这个版本的写法可读性就差一些。尽管枚举项根据字母排序，但是却失去了对原始值含义的表达。

```swift
// ❌
public enum HTTPStatus: Int {
    case badRequest = 400
    case forbidden = 403
    case internalServerError = 500
    case notAuthorized = 401
    case notFound = 404
    case ok = 200
    case paymentRequired = 402
}
```

> ##### 声明 Attribute  `强制❗️`

- attribute 修饰 `存储属性` 时, attribute 可以与属性放在同一行 (换行也没问题), 但需要注意:
  - 如果有两个以上 attribute 同时修饰该属性, 则必须要换行, 且每个 attribute 单独一行
  - 如果只有一个 attribute, 但该行整体超过了单行最大字符数限制, 也必须要换行

```swift
// ✅
public class MyViewController: UIViewController {
    @IBOutlet private var tableView: UITableView!
    @SomeWrapper var state: PageState
    @SomeWrapper(wrappedValue: 30, custom: 98.7) var context: AudioContext
    
    @SomeWrapper 
    var anotherState: PageState
    
    @SomeVeryVeryLongNameWrapper(wrappedValue: 30)
    public var detailHeaderView: TTFMAudioDetailHeaderView
    
    @Locked
    @SomeWrapper
    var trackInfo: [String: Any]
}
```

```swift
// ❌
public class MyViewController: UIViewController {
    @Locked @SomeWrapper var trackInfo: [String: Any] // 两个attribute时未换行
    
    @Locked @SomeWrapper // 两个attribute时没有各自占一行
    var trackInfo: [String: Any]
}
```

- 除了上条提到的存储属性, 其他所有场景, 包括修饰 `类型`, `extension`, `函数`, `计算属性` 等都需要换行, 且每个 attribute 单独一行
- 凡是 attribute 换行的场景, 在首个 attribute 的上方要留有一个空行, 以便与其他内容区分

```swift
// ✅
@propertyWrapper
struct SomeWrapper {
    // ...
}

@dynamicCallable
class ImageDownloader {
    // ...
    @objc
    @discardableResult
    public func set(to listItem: CPListItem) -> DownloadTask? {
        // ...
    }
}


class SwiftClass: NSObject {

    @available(*, deprecated, renamed:"snp.left")
    var snp_left: ConstraintItem { return self.snp.left }
    
    @objc
    func test() {
        // ...
    }
}

@nonobjc 
extension SwiftClass {

}
```

```swift
// ❌
@propertyWrapper struct SomeWrapper {
    // ...
}

class SwiftClass: NSObject {
    var title: String
    @SomeWrapper(wrappedValue: 30, custom: 98.7) // 上面没空行
    var context: AudioContext
    
    // @objc 应该单独一行
    @objc func test() {
        // ...
    }
}
```

> ##### 尽量使用语法糖  `强制❗️`

对于 `Array`, `Dictionary`, `Optional` 使用语法糖, 而不是采用泛型的写法

```swift
// ✅
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

```swift
// ❌
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```

> ##### 空数组和字典采用类型标注的方式初始化  `强制❗️`

```swift
// ✅
var names: [String] = []
var lookup: [String: Int] = [:]
var aSet: Set<Int> = []
```

```swift
// ❌
var names = [String]()
var lookup = [String: Int]()
var aSet = Set<Int>()
```

> ##### 适时使用类型推断来简写  **强制**

```swift
// ✅
let selector = #selector(viewDidLoad)
view.backgroundColor = .red
let toView = context.view(forKey: .to)
let view = UIView(frame: .zero)
let message = "Click the button"
let currentBounds = computeViewBounds()
let maximumWidth: CGFloat = 106.5

let host = Host()

func someDirection() -> Direction {
    return .left
}
```

```swift
// ❌
let selector = #selector(ViewController.viewDidLoad)
view.backgroundColor = UIColor.red
let toView = context.view(forKey: UITransitionContextViewKey.to)
let view = UIView(frame: CGRect.zero)
let message: String = "Click the button"
let currentBounds: CGRect = computeViewBounds()

let host: Host = Host()

func someDirection() -> Direction {
    return Direction.left
}
```

> ##### 单行表达式**不要**省略 return  `强制❗️`

当单行表达式结果需要被 `return` 时, 除了单行闭包内的场景可以省略 `return`, 其他所有场景, 如 `function`, `computed property getter`, `subscript` 等, 此时**不要**省略 `return` 关键字, 否则会影响代码可读性.

```swift
// ✅
func add(number1: Int, number2: Int) -> Int { return number1 + number2 }

public extension UserDefaults {
    subscript(_ key: String) -> Any? {
        get {
            return object(forKey: key)
        }
        set {
            set(newValue, forKey: key)
        }
    }
}

let squares = [1, 2, 3].map { $0 * $0 }

var isAdult: (_ age: Int) -> Bool
isAdult = { age in
    return age > 18
}

func isEnable(for key: String) -> Bool {
    return true
}
```

```swift
// ❌
func isEnable(for key: String) -> Bool {
    true
}

var isEnable: Bool {
    false
}

let squares = [1, 2, 3].map { return $0 * $0 }
```

> ##### `get`/`set`/`willSet`/`didSet`  `强制❗️`

- 书写顺序:  `get` `set`; `willSet` `didSet`
- 不要为 `set` `willSet` `didSet` 添加参数名, 直接使用默认的 `newValue` `oldValue`
- 当计算属性为只读时, 应该省略 `get` 关键字及大括号

```swift
// ✅
var height: CGFloat {
    get {
        return 15
    }
    set {
        view.height = newValue
    }
}

var isEnable: Bool {
    return Bool.random()
}

var test: Int = 1 {
    willSet {
        print(newValue)
    }
    didSet {
        print(oldValue)
    }
}
```

```swift
// ❌
var height: CGFloat {
    set {
        view.height = newValue
    }
    get {
        return 15
    }
}

var isEnable: Bool {
    get {
        return Bool.random()
    }
}

var test: Int = 1 {
    didSet {
        print(oldValue)
    }
    willSet(xxx) {
        print(xxx)
    }
}
```

> ##### 函数, 常量, 变量修饰符的顺序  `强制❗️`

修饰 `func` `var` 的关键字有很多, 建议顺序如下:

- 如果有 `weak`, `unowned`,  则须放在最前面, 作用域关键字次之
- 如果有 `static` / `class` 放在最后, 也就是 `func` 或 `var` 前面
- 其他关键字放中间, 顺序不要求

```swift
// ✅
(public/private/...) dynamic optional mutating (static/class) func test()
public convenience init()

open class TimeMachine {  
    private dynamic lazy var fluxCapacitor = FluxCapacitor()
    weak open var target: AnyObject?
}
```

> ##### 省略 `self`  `建议🌵`

通常情况下在调用一个类型自己的属性或方法时, 建议省略 `self`, 除非是编译器要求必须写明的场景, 比如: 逃逸闭包引用 `self`, 初始化方法中避免属性和参数歧义等.

```swift
// ✅
final class Listing {
    
    private let isFamilyFriendly: Bool
    private var capacity: Int
    
    init(capacity: Int, allowsPets: Bool) {
        self.capacity = capacity
        isFamilyFriendly = !allowsPets
    }
    
    private func increaseCapacity(by amount: Int) {
        capacity += amount
        save()
    }
}
```

```swift
// ❌
final class Listing {
    
    private let isFamilyFriendly: Bool
    private var capacity: Int
    
    init(capacity: Int, allowsPets: Bool) {
        self.capacity = capacity
        self.isFamilyFriendly = !allowsPets // `self.` not required here
    }
    
    private func increaseCapacity(by amount: Int) {
        self.capacity += amount
        self.save()
    }
}
```

> ##### 链式调用   `建议🌵`

- 基本规则是单行书写, 如需换行, 则每个方法调用都要换行, 缩进一个单位

```swift
// ✅
let value = numbers.map { $0 * 2 }.filter { $0 % 3 == 0 }.index(of: 90)

let value = numbers
    .map { $0 * 2 }
    .filter { $0 % 3 == 0 }
    .firstIndex(of: 90)
```

- 连续通过点语法获取到某个属性后再链式调用方法的场景, 属性都不换行, 方法都换行

```swift
// ✅
let value = object.proerty1.someProperty.numbers
    .map { $0 * 2 }
    .filter { $0 % 3 == 0 }
    .firstIndex(of: 90)
```

```swift
// ❌
let value = object
    .proerty1
    .someProperty
    .numbers
    .map { $0 * 2 }
    .filter { $0 % 3 == 0 }
    .firstIndex(of: 90)
```

- 链式调用方法后, 尽量不要在中间穿插属性获取, 但最后一个调用可以获取属性

```swift
// ✅
let value = object.proerty1.someProperty.numbers
    .map { $0 * 2 }
    .filter { $0 % 3 == 0 }
    .first ?? 0
```

```swift
// ❌ 中间穿插属性会降低可读性
let value = object
    .method1()
    .someMethod().user
    .someUserMethod()
```

- 当中间某个方法调用换行或尾随闭包换行后, 下一个调用不要与 `)` 或 `}` 同行, 仍然需要换行

```swift
// ✅
let value = numbers
    .map { $0 * 2 }
    .filter {
        $0 % 3 == 0
    }
    .firstIndex(of: 90)

LinearGradient(
    gradient: Gradient(colors: [.clear, .black]),
    startPoint: .top,
    endPoint: .bottom
)
.opacity(0.7)
.offset(x: 0, y: 30)
.clipped()
```

```swift
// ❌
let value = numbers
    .map { $0 * 2 }
    .filter {
        $0 % 3 == 0
    }.firstIndex(of: 90)

LinearGradient(
    gradient: Gradient(colors: [.clear, .black]),
    startPoint: .top,
    endPoint: .bottom
).opacity(0.7)
.offset(x: 0, y: 30)
.clipped()
```

> ##### **Void** 和空元组 **()** `强制❗️`

`Void` 是空元组 `()` 的 `typealias`，所以从实现来说它们是等价的。

- 在函数类型声明（例如闭包或者持有函数引用变量）的返回类型永远写作 `Void`，而不用 `()`。
- 在用 `func` 关键字声明的函数中，如果没有返回值，则应省略 `-> Void`。
- 空的实参列表永远写作 `()`，而不是 `Void`。

```swift
// ✅
func doSomething() {
  // ...
}

let callback: () -> Void

func foo() -> (Int) -> Void {
  // ...
}
```

```swift
// ❌
func doSomething() -> Void {
  // ...
}

func doSomething2() -> () {
  // ...
}

let callback: () -> ()
```

> ##### 三目运算符  `强制❗️`

三目运算符 `?:` 不要写的过长，尽量避免多组三目运算嵌套在一起，这样会影响可读性。`?` `:` 前后都要留有空格，必要时也可以加括号或换行来提高可读性.

```swift
// ✅
let value = 5
result = value != 0 ? x : y

let isHorizontal = true
result = isHorizontal ? x : y

// ✅ 多行表达式建议以下两种格式
(condition) ? x
            : y

(condition)
    ? x // 缩进一个单位
    : y
```

```swift
// ❌
result = a > b ? x = c > d ? c : d : y
```

> ##### 多行文本  `强制❗️`

多行文本使用 `"""` 包裹, 结尾的 `"""` 与每行开头对齐, 且都缩进一个单位

```swift
// ✅
let message = """
    You cannot charge the flux \
    capacitor with a 9V battery.
    You must use a super-charger \
    which costs 10 credits. You currently \
    have \(credits) credits available.
    """

var universeQuote: String {
    return """  
        In the beginning the Universe was created.  
        This has made a lot of people very angry.
        """
}
```

```swift
// ❌
let message = """
    You cannot charge the flux \
    capacitor with a 9V battery.
    You must use a super-charger \
    which costs 10 credits. You currently \
    have \(credits) credits available.
"""

let message = """
You cannot charge the flux \
capacitor with a 9V battery.
You must use a super-charger \
which costs 10 credits. You currently \
have \(credits) credits available.
"""

let message = "You cannot charge the flux " +
    "capacitor with a 9V battery.\n" +
    "You must use a super-charger " +
    "which costs 10 credits. You currently " +
    "have \(credits) credits available."
```

> ##### 括号换行  `强制❗️`

将带有分支的代码（`if`，`else`，`switch`，`while` 方法等等）自己分支的开始括号（`(`，`{`）放在同一行 ([1TBS style](https://en.m.wikipedia.org/wiki/Indentation_style#1TBS))，来保证代码的紧凑可读。

**注意**: 另外 `else` 换行也是一种不错的表达方式，因为可以认为 `if` 和 `else` 的条件分支都是同级别的，所以与 `if` 对齐显得更合理。这样写的另外一个优点是，用 `}` 单独占一行，可以将不同的分支隔开，更容易阅读。

```swift
// ✅
if enable {

} else {

}

// ✅  https://en.wikipedia.org/wiki/Indentation_style Variant: Stroustrup
if enable {

} 
else {

}
```

```swift
// ❌
if enable
{
    // ...
}
else 
{
    // ...
}

// ❌
if enable
{
    // ...
}else{
    // ...
}
```

对于字典和数组的定义，如果内容较多时也建议换行处理，并在左括号处就开始换行，而不是从第一个元素后换行

```swift
// ✅
let rowContent = [
    listingUrgencyDatesRowContent(),
    listingUrgencyBookedRowContent(),
    listingUrgencyBookedShortRowContent()
]

let dict = [
    "name": "Chris",
    "age": "32"
]
```

```swift
// ❌
let rowContent = [listingUrgencyDatesRowContent(),
                  listingUrgencyBookedRowContent(),
                  listingUrgencyBookedShortRowContent()]
```

> ##### `typealias` 换行  `强制❗️`

`typealias` 合并协议时, 如果需要换行, 应该从 `=` 之后开始换行, 且换行内容左对齐, 缩进一个单位

```swift
// ✅
public typealias AnyObjectExtensionable =
    Associatable
    & OnceExecutable
    & Swizzlable
    & DeinitObservable
```

```swift
// ❌
 public typealias AnyObjectExtensionable
    = Associatable
    & OnceExecutable
    & Swizzlable
    & DeinitObservable
    
 public typealias AnyObjectExtensionable = Associatable
    & OnceExecutable
    & Swizzlable
    & DeinitObservable
```

如果是修饰闭包或元组, 应该从 `(` 开始换行

```swift
// ✅
typealias PerformanceTrackingIndexResult = (
    Element.Index?,
    PerformanceTrackingIndexStatistics.Timings,
    PerformanceTrackingIndexStatistics.SpaceUsed
)

typealias LogInCompletion = (
    _ userName: String, 
    _ password: String
) -> Void

typealias AutofillLoginPromptViewControllerCompletion = ((
    SecureVaultModels.WebsiteAccount?
) -> Void)?
```

> ##### 空代码块  `强制❗️`

空代码块应写作 `{}`

```swift
// ✅
`struct Foo {}`
```

```swift
// ❌
`struct Foo { }`
`struct Bar {`

`}`
```

> ##### 关于空格  `强制❗️`

- 任意场景下逗号后面要有空格, 前面**没有**空格

```swift
// ✅
let a = [1, 2, 3]
```

```swift
// ❌
let a = [1,2 ,3]
```

- 任意场景左大括号 `{` 前要有空格，任何单行实现的代码块，`{` 与 `}` 内部两侧各留一个空格

```swift
// ✅
if condition {

}

numbers.map { $0 * $0 * $0 }
```

```swift
// ❌
if conddition{

}

numbers.map {$0 * $0 * $0}
```

- 冒号后面要有空格, 前面**没有**空格。但以下特殊情况除外:
  - 三目运算符 `? : ` 中的 `?` 和 `:` 他们各自的前后都要留有一个空格
  - 空字典 `[:]` 初始化和 `#selector` 中的冒号前后都没有空格
  - 自定义算符的优先级时, 冒号的前面要保留一个空格与算符隔开

```swift
// ✅
struct HashTable: Collection {
   // ...
}

let tuple: (x: Int, y: Int)

func sum(_ numbers: [Int]) {
   // ...
}

let number: Int = 5
var map: [String: Int] = [:]
let nameAgeMap = ["Ed": 40]

let a = condition ? a1 : a2

infix operator ^ : APrecedence
```

```swift
// ❌
struct HashTable :Collection {
   // ...
}

let tuple:(x : Int, y:Int)

func sum(_ numbers:[Int]) {
   // ...
}

let number:Int = 5
var map: [String:Int] = [ : ]
let nameAgeMap = ["Ed" : 40]

let a = condition ? a1:a2

infix operator ^: APrecedence
```

- 注释符号 `//` , `///` 与后面的描述之间要有空格，如果注释在代码语句行尾，前面也要有空格

```swift
// ✅
let a = 2 // Warm up modulator.
```

```swift
// ❌
let a = 2//Warm up modulator.
```

- 在二元或者三元运算符的前后都要有空格

```swift
// ✅
let a = condition ? a1 : a2
var x = 5
var c = 3 + 2 * 5
let d = boolValue1 && value2

func foo(bar: Int = 0) {}

typealias SomeFeature = AProtocol & BProtocol
```

```swift
// ❌
let a = condition?a1:a2
var x=5
var c = 3+2*5
let d = boolValue1&&value2

func foo(bar: Int=0) {}

typealias SomeFeature=AProtocol&BProtocol
```

- 函数返回值标识符 `->`, `throws` 关键字前后都有空格

```swift
// ✅
func sum(numbers: [Int]) -> Int {
    // ...
}

func parse(text: String) throws {
    guard text.isEmpty else {
        throw Parser.Error.invalidToken(text)
    }
}
```

```swift
// ❌
func sum(numbers: [Int])->Int {
    // ...
}

func parse(text: String)throws{
    guard text.isEmpty else {
        throw Parser.Error.invalidToken(text)
    }
}
```

- 点语法中的 `.` 前后都**没有**空格

```swift
// ✅
view.bounds.width
```

```swift
// ❌
view . bounds. width
```

- `..<` 或者 `...` 运算符用在范围表达式时两侧都没空格

```swift
// ✅
for number in 1...5 {
    // ...
}
```

```swift
// ❌
for number in 1 ... 5 {
    // ...
}
```

- `(` `)`, `[` `]` 内部两侧**没有**空格

```swift
// ✅
let a = (23, "abc")
let b = [1, 2]
```

```swift
// ❌
let a = ( 23, "abc" )
let b = [ 1, 2 ]
```

- 闭包表达式中的 `{` 与后面的内容之间要有空格

```swift
// ✅
let a = { [weak self] success in 
    // ...
}

let b = { _ in
    // ...
}
```

```swift
// ❌
let a = {[weak self] success in 
    // ...
}

let b = {_ in
    // ...
}
```

- 泛型定义 `<` `>` 内部与外部都**没有**空格

```swift
// ✅
struct Foo<Element>: Bar {}

func index<T>(of obj: T) -> Int {}
```

```swift
// ❌
struct Foo <Element>: Bar {}

func index <T> (of obj: T) -> Int {}
func index< T >(of obj: T) -> Int {}
```

- 函数的声明和调用, `(` 前面通常**没有**空格; 带关联值的枚举 `case` 与后面的 `(` 间也**没有**空格

```swift
// ✅
func test() {}
enum Brand {
    case apple(_ logo: UIImage)
}
```

```swift
// ❌
func test () {}
enum Brand {
    case apple (_ logo: UIImage)
}
```

- 如果函数的名字是运算符, 那么运算符要与后面 `(` 或泛型定义的 `<` 之间有空格

```swift
// ✅
static func == (lhs: MyType, rhs: MyType) -> Bool {
    // ...
}

prefix operator ±
prefix func ± <T: FloatingPoint>(number: T) -> (T, T) {
    return (0 + number, 0 - number)
}
```

```swift
// ❌
static func ==(lhs: MyType, rhs: MyType) -> Bool {
    // ...
}

prefix operator ±
prefix func ±<T: FloatingPoint>(number: T) -> (T, T) {
    return (0 + number, 0 - number)
}
```

> ##### 关于空行  `强制❗️`

空行本质上是为了让不同逻辑的代码区分开来，更方便阅读

- Import module 部分上下要空一行
- 每个类型定义, `extension` 的上下要空一行
- 单行实现的属性可视情况以空行分隔不同的逻辑
- `func` `计算属性` 如果为多行实现, 上下要空一行
- 多行实现的 `func` `计算属性` `闭包` 的内部, 可以适当用空行分隔不同的逻辑
- `// MARK:` 上下要空一行
- 任何注释与它对应描述的代码之间不空行，并在注释的上面空一行
- 通常情况下，空一行足够了，最多两行，三行以上是不必要的

```swift
//
//  BrowsingMenuViewController.swift
//  Your Organization
//
//  Created by phoenix on 2020/9/16.
//

protocol BrowsingMenu {
    func setMenuEntries(_ entries: [BrowsingMenuEntry])
}

enum BrowsingMenuEntry {
    case regular(name: String, image: UIImage)
    case separator
}

class BrowsingMenuViewController: UIViewController, BrowsingMenu {
    private enum Contants {
        static let arrowLayerKey = "arrowLayer"
    }
    
    typealias DismissHandler = () -> Void
    
    @IBOutlet weak var horizontalContainer: UIView!
    @IBOutlet weak var horizontalStackView: UIStackView!
    
    // Height to accomodate all content, can be constrained by parent view.
    @IBOutlet weak var tableViewHeight: NSLayoutConstraint!
    
    // Set to force recalculation
    public var parentConstraits = [NSLayoutConstraint]() {
        didSet {
            recalculatePreferredWidthConstraint()
            recalculateHeightConstraints()
        }
    }
    
    // MARK: - Life Cycle
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        configureHeader()
        configureTableView()
        
        applyTheme(ThemeManager.shared.currentTheme)
    }
    
    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()
        
        if tableView.bounds.height < tableView.contentSize.height + tableView.contentInset.top + tableView.contentInset.bottom {
            tableView.isScrollEnabled = true
        } else {
            tableView.isScrollEnabled = false
        }
    }
}

// MARK: - UITableViewDelegate

extension BrowsingMenuViewController: UITableViewDelegate {
    func tableView(
        _ tableView: UITableView, 
        didSelectRowAt indexPath: IndexPath
    ) {
        switch menuEntries[indexPath.row] {
        case .regular(_, _, _, _, let action):
            dismiss?()
            action()
        case .separator:
            break
        }
    }
}
```

> ##### 重载  `强制❗️`

当一个类型有多个构造器或者下标方法，或者一个文件/类型内有多个相同名字的函数（尽管可能有不同的实参标签），并且当这些重载在同一类型或者扩展作用域内时，它们应该按顺序排列，不应该在中间插入其他代码。

## 命名

> ##### 通用基本规则  `强制❗️`

- 不要使用拼音，中文，emoji 表情等特殊字符，并遵守苹果推荐的长的，完整性描述的命名规则。

```swift
// ✅
var settingsButton: UIButton
var titleLabel: UILabel
var userImageView: UIImageView
struct DatabaseMigrator {}
let variable = "abc"
let emoji = "emoji"
```

```swift
// ❌
var setBtn: UIButton
var titleLbl: UILabel
var title: UILabel
var name_label: UILabel
var userImage: UIImageView
struct TTDatabaseMigrator {}
let 变量 = "abc"
let 🤣 = "emoji"
```

- 另外，Swift 类型会以 Module 为命名空间，所以不需要给类型, `typealias`, `func`, `protocol` 等增加前缀了，如果来自不同模块的两个实体名称重复了，可以通过 `import` 具体类型来消除歧义。更多细节参考 **Swift Import Declarations**

```swift
// ModuleA
public struct Foo {
    public static test() { print("ModuleA test") }
}

// ModuleB
public struct Foo {
    public static test() { print("ModuleB test") }
}

// ModuleC
import ModuleA
import ModuleB
import struct ModuleB.Foo

Foo.test() // ModuleB test
```

- 应该结合角色和类型命名, 而不是仅仅考虑类型

```swift
// ❌ 没有表达出变量或者参数是什么角色
var string = "Hello"
protocol ViewController {
    associatedtype ViewType : View
}
class ProductionLine {
    func restock(from widgetFactory: WidgetFactory)
}
```

```swift
// ✅
var greeting = "Hello"
protocol ViewController {
    associatedtype ContentView : View
}
class ProductionLine {
    func restock(from supplier: WidgetFactory)
}
```

- 如果能够表达清楚意思, 尽量选择简单的单词, 而不是晦涩生僻的单词. 如使用 `skin`, 而不是 `epidermis`
- 使用大众所熟知的专业名词, 而不是创建一个新名词. 如果有必要新建一个术语名词, 则必须以注释的方式解释清楚其含义, 用途和用法.
- **避免**使用非标准的缩写, 如 `button` 缩写为 `btn` ，`label` 缩写为 `lbl`

> ##### 目录名 / 文件名  `强制❗️`

- 目录和文件名中不能包含空格
- 文件名通常要以大写驼峰式命名，需要反映出其实现了什么类型，或是一组类型的通用名称。
- 如果是类型扩展，要使用 `+` 连接扩展描述，如果是系统类型的分类，要为扩展描述增加前缀，前缀要尽量以三个大写字母开头，如果确定不会与系统分类重复，也可以用两个大写字母开头，如 `TT`，`BD`
- 通常建议一个类型用一个 `.swift` 文件实现，如果是若干相关的类且内容较短，如一些 `model`，可视情况共用一个 `.swift` 文件承载，但应该起一个通用的文件名。

```swift
// ✅
PlayerListView.swift
TopBar+SearchBubble.swift
String+BTDAdditions.swift
```

```swift
// ❌
ttroute.swift
TTPlayerListView.swift
TopBar+searchBubble.swift
StringAdditions.swift
```

> ##### 类型名  `强制❗️`

- 包括 `class`, `struct`, `enum`, `protocol`, 以及 `typealias` 修饰的别名，泛型类型，采用大写驼峰命名， **不加**前缀

```swift
// ✅
struct DatabaseQueue {}
enum ResponseValidationFailureReason {}
typealias Parameters = [String: Any]
typealias ProgressHandler = (Progress) -> Void
struct Stream<Success, Failure: Error> {}
```

- 描述事物的 `protocol` 应该以名词命名, 如 `Collection`
- 描述能力的 `protocol` 名字应该以 `able`, `ible` 或 `ing` 为后缀, 如 `Equatable`, `ProgressReporting`
- 泛型类型命名需要贴合其用意, 如果没有明确的意义, 通常使用 `T`, `U` 或 `V`

```swift
// ✅
struct Stack<Element> { ... }
func write<Target: OutputStream>(to target: inout Target)
func swap<T>(_ a: inout T, _ b: inout T)
```

```swift
// ✅
struct Stack<T> { ... }
func write<target: OutputStream>(to target: inout target)
func swap<Thing>(_ a: inout Thing, _ b: inout Thing)
```

- 如果不是 view controller, 禁止以 `Controller` 的方式命名

```swift
// ❌
class VolumeController {}
```

> ##### 函数名 / 方法名  `强制❗️`

- 基本规则
  - 以小写字母开头，并混合驼峰格式。每个具名参数也应该以小写字母开头
  - 应尽量读起来就像句子，这表示你应该选择与方法名连在一起读起来通顺的参数名
  - 适当为参数增加描述标签

```swift
// ✅
open func transition(
    from fromViewController: UIViewController, 
    to toViewController: UIViewController, 
    duration: TimeInterval, 
    options: UIView.AnimationOptions = [], 
    animations: (() -> Void)?, 
    completion: ((Bool) -> Void)? = nil
)

// ✅
x.insert(y, at: z)          // "x, insert y at z"
x.subViews(havingColor: y)  // "x's subviews having color y"
x.capitalizingNouns()       // "x, capitalizing nouns"

// ✅ 当前一两个参数之后的参数不影响该方法的主要调用意思, 可以接受方法语句的流畅性下降
// 如下例中方法整句并不通顺, 但主句意 `AudioUnit` instantiate with `description`
// 比较流畅. 可以忽略 `options` 和 `completionHandler` 带来的负面影响.
AudioUnit.instantiate(
    with: description, 
    options: [.inProcess], 
    completionHandler: stopProgressBar
)
```

```swift
// ❌
x.insert(y, position: z)
x.subViews(color: y)
x.nounCapitalize()

// ❌ 不必为了刻意保证句意流畅而增加介词
AudioUnit.instantiate(
    with: description, 
    and options: [.inProcess], 
    completionHandler: stopProgressBar
)
```

- 函数名应该避免歧义, 以便理解.  比如, 在集合中移除某个位置的元素:

```swift
// ✅
extension List {
    public mutating func remove(at position: Index) -> Element
}
employees.remove(at: x)
```

但如果移除 `at` 单词, 那读起来不通顺, 且使用时有歧义

```swift
// ❌
extension List {
    public mutating func remove(_ position: Index) -> Element
}
employees.remove(x) // 有歧义, 是要移除x这个元素, 还是移除第x个元素?
```

- 省略不必要的单词. 一些读者已经明显了解的信息, 没有必要再重复添加描述词, 比如下例中的 `Element` 单词就可以省略

```swift
// ❌
public mutating func removeElement(_ member: Element) -> Element?
allViews.removeElement(cancelButton)
```

```swift
// ✅
public mutating func remove(_ member: Element) -> Element?
allViews.remove(cancelButton) // 更简洁清晰
```

- 避免模糊不清的调用. 比如在一些基础库方法中, 方法参数可能是基类, 如 `NSObject`, 或一些基本类型, 如 `Int`, `String` 等, 如果不给参数增加角色描述, 使用者会不明确方法意图

```swift
// ❌
func add(_ observer: NSObject, for keyPath: String) // 从声明上看没什么问题
grid.add(self, for: graphics) // 但从调用上看不明确, add的是什么角色?
```

```swift
// ✅
func addObserver(_ observer: NSObject, forKeyPath path: String)
grid.addObserver(self, forKeyPath: graphics)
```

- 工厂方法以 `make` 开头, 如 `x.makeIterator()`
- 初始化方法和工厂方法要忽略上面提到的句意流畅性, 参数直接就是类型的属性罗列即可, 不要使用 `and` 来连接

```swift
// ✅
let foreground = Color(red: 32, green: 64, blue: 128)
let newPart = factory.makeWidget(gears: 42, spindles: 14)
let ref = Link(target: destination)

class Point {
    let x: CGFloat
    let y: CGFloat
    init(x: CGFloat, y: CGFloat) {}
}
```

```swift
// ❌
let foreground = Color(havingRGBValuesRed: 32, green: 64, andBlue: 128)
let newPart = factory.makeWidget(havingGearCount: 42, andSpindleCount: 14)
let ref = Link(to: destination)

class Point {
    let x: CGFloat
    let y: CGFloat
    init(x: CGFloat, andY: CGFloat) {}
    init(x: CGFloat, and y: CGFloat) {}
}
```

- 事件处理的方法应该以 `didChange`, `didTap` 等类似的方式命名, 相比 `changed`, `tapped` 这类方式更加清晰

```swift
// ✅
class ExperiencesViewController {
    private func didTapBookButton() {
        // ...  
    }
    
    private func modelDidChange() {
        // ...  
    }
}
```

```swift
// ❌
class ExperiencesViewController {
    private func handleBookButtonTapped() {
        // ...  
    }

    private func modelChanged() {
        // ...  
    }
}
```

- 代理方法中可以根据实际情况增加一些辅助性单词来提高可读性, 并且第一个参数应该是被代理的对象, 且参数标签以 `_` 省略

```swift
// ✅ 返回Bool值的方法可以用should来连接
func tableView(
    _ tableView: UITableView, 
    shouldHighlightRowAt indexPath: IndexPath
) -> Bool

// ✅ 可以用`did`或`will`来标识已经发生或即将发生
func tableView(
    _ tableView: UITableView, 
    willBeginEditingRowAt indexPath: IndexPath
)

func namePickerView(
    _ namePickerView: NamePickerView, 
    didSelectName name: String
)

func namePickerViewShouldReload(_ namePickerView: NamePickerView) -> Bool
```

```swift
// ❌
func didSelectName(namePicker: NamePickerViewController, name: String)
func namePickerShouldReload() -> Bool
```

- 命名方法或函数时还要考虑他们的附加作用 (side-effects)
  - 没有附加作用的应该读起来是名词短语, 如 `x.distance(to: y)` `i.successor()`
  - 有附加作用的应该读起来是谓语动词短语, 如 `x.sort()` `x.append(y)`
  - `mutating` 通常会提供一个对应的 `nonmutating` 方法, 但是它会返回一个新的对象, 而不是修改它自己

    - 当方法名为动词时, 一般来说用动词的过去分词(通常为 `ed` 结尾)来命名 `nonmutating` 的方法, 但是当动词后面跟了宾语, 则需要使用现在分词(通常为 `ing` 结尾)来命名 `nonmutating` 方法
      | **Mutating**  | **Nonmutating**      |
      | ------------- | -------------------- |
      | `x.sort()`    | `z = x.sorted()`     |
      | `x.append(y)` | `z = x.appending(y)` |

        ```swift
        // ✅
        /// 反转 self
        mutating func reverse()
        
        /// 返回一个反转后的 self 的拷贝, self 本身没有变化
        func reversed() -> Self
        
        x.reverse()
        let y = x.reversed()
        
        // ✅
        /// 删掉 self 中的所有新行
        mutating func stripNewlines()
        
        /// 复制 self, 然后删掉新对象中的所有新行, self 本身没有变化
        func strippingNewlines() -> String
        
        s.stripNewlines()
        let oneLine = t.strippingNewlines()
        ```
	- 如果方法操作是以名词描述的, 那么用这个名词来作为 `nonmutating` 方法的名字, 用 `form+名词`的组合作为 `mutating` 方法的名字
	
        |   **Nonmutating**    |     **Mutating**      |
        |----------------------|-----------------------|
        | `x = y.union(z)`     | `y.formUnion(z)`      |
        | `j = c.successor(i)` | `c.formSuccessor(&i)` |


- 返回 `Bool` 类型的的方法和属性应该使用断言式的命名, 如`x.isEmpty`,  `line1.intersects(line2)`, `hasMember`

- 基本意义相同的方法可以共享一个基础名，比如：

```swift
// ✅
extension Shape {
    /// Returns `true` iff `other` is within the area of `self`.
    func contains(_ other: Point) -> Bool { ... }

    /// Returns `true` iff `other` is entirely within the area of `self`.
    func contains(_ other: Shape) -> Bool { ... }

    /// Returns `true` iff `other` is within the area of `self`.
    func contains(_ other: LineSegment) -> Bool { ... }
}
```

但是如果他们的返回值是不同的类型, 则**不应该**使用相同的方法名

```swift
// ❌
extension Box {
    /// Returns the `Int` stored in `self`, if any, and
    /// `nil` otherwise.
    func value() -> Int? { ... }

    /// Returns the `String` stored in `self`, if any, and
    /// `nil` otherwise.
    func value() -> String? { ... }
}
```

- 选择合适的形参名

虽然形参名不会在方法或函数调用的时候体现出来, 但可以使得方法整体的读起来更通顺, 并且在注释以及方法内部使用形参, 也可以使读者更易理解

```swift
// ✅
/// Return an `Array` containing the elements of `self`
/// that satisfy `predicate`.
func filter(_ predicate: (Element) -> Bool) -> [Generator.Element]

/// Replace the given `subRange` of elements with `newElements`.
mutating func replaceRange(_ subRange: Range, with newElements: [E])
```

```swift
// ❌ 形参名不恰当, 或参数标签和形参共用导致注释读起来不流畅
/// Return an `Array` containing the elements of `self`
/// that satisfy `includedInResult`.
func filter(_ includedInResult: (Element) -> Bool) -> [Generator.Element]

/// Replace the range of elements indicated by `r` with
/// the contents of `with`.
mutating func replaceRange(_ r: Range, with: [E]) {
    let availableElements = with.filter { $0.intValue > 5 }
}
```

- 为非必要参数添加默认值可以简化调用, 也无需定义多个类似方法
  - 通常将有默认值的参数放在方法后面, 因为没有默认值的参数往往比较重要, 放前面
  - 如果需要传递文件路径, 尽量使用 `#fileID`, 而不是 `#filePath`, 因为前者更简短

```swift
// ✅
extension String {
    public func compare(
       _ other: String, 
       options: CompareOptions = [],
       range: Range? = nil, 
       locale: Locale? = nil
    ) -> Ordering
}

let order = lastName.compare(royalFamilyName)
```

```swift
// ❌ 
extension String {
    public func compare(_ other: String) -> Ordering
    
    public func compare(_ other: String, options: CompareOptions) -> Ordering
    
    public func compare(
       _ other: String, 
       options: CompareOptions, 
       range: Range
    ) -> Ordering
    
    public func compare(
         _ other: String, 
         options: StringCompareOptions,
         range: Range, 
         locale: Locale
     ) -> Ordering
}
```

- 当参数标签对可读性并没有帮助时, 应将其省略, 如: `min(number1, number2)`, `zip(s1, s2)`
- 在保值类型转换的初始化方法中

  - 低精度向高精度转换, 省略第一个参数的标签
  - 高精度向低精度转换, 不要省略参数标签

    ```swift
    // ✅
    extension UInt32 {
    /// Creates an instance having the specified `value`.
    init(_ value: Int16)            ← 拓宽精度, 省略标签
    /// Creates an instance having the lowest 32 bits of `source`.
    init(truncating source: UInt64)  ← 丢失精度, 不要省略标签
    }
    ```

- 当第一个参数是介词短语的形式时, 需要为其添加一个参数标签, 并且参数标签要从介词开始, 如:

```swift
// ✅
func move(to point: CGPoint)
a.move(to: somePoint)

x.removeBoxes(havingLength: 12)
```

如果前几个参数代表一个整体概念, 那要把介词放在括号外面

```swift
// ❌ x, y 是整体, 代表坐标, toX, y 放在一起用不优雅
a.move(toX: b, y: c)
a.fade(fromRed: b, green: c, blue: d)
```

```swift
// ✅
a.moveTo(x: b, y: c)
a.fadeFrom(red: b, green: c, blue: d)
```

- 除了上述提到的场景, 其他所有场景的函数参数必须有参数标签
- 对于无约束类型要格外小心, 如 `Any` `AnyObject`, 以及没有约束类型的泛型参数

```swift
// ❌ 为数组添加一个元素, 和一组元素采用了相同的方法名
struct Array {
    /// Inserts `newElement` at `self.endIndex`.
    public mutating func append(_ newElement: Element)

    /// Inserts the contents of `newElements`, in order, at
    /// `self.endIndex`.
    public mutating func append(_ newElements: S) 
    where S.Generator.Element == Element
}

// ❌ 添加后的结果是 [1, "a", [2, 3, 4]] 还是 [1, "a", 2, 3, 4] ? 有歧义
var values: [Any] = [1, "a"]
values.append([2, 3, 4])
```

```swift
// ✅ 通过修改方法名是语义更明确
struct Array {
    /// Inserts `newElement` at `self.endIndex`.
    public mutating func append(_ newElement: Element)

    /// Inserts the contents of `newElements`, in order, at
    /// `self.endIndex`.
    public mutating func append(contentsOf newElements: S)
    where S.Generator.Element == Element
}
```

> ##### 资源命名  `强制❗️`

- 采用 `snake_case` 方式，如 `tt_category_selector_search_icon.png`
- 如果是图片, 实际命名需要和 Assets 中的 item 保持一致

> ##### 其他场景使用小写驼峰   `强制❗️`

通常来说, 除了前面提到的场景， 其他场景都使用小写驼峰的命名方式，常见的如下：

- `let` 或 `var` 修饰的变量, 常量, 属性, 也包括全局常量, 全局变量
- 常量禁止以 `k` 开头
- 枚举的 `case` 值, 及关联值参数标签名
- 位移枚举的可选项
- 函数参数, 闭包参数, 元组元素的标签名

```swift
// ✅
open var next: UIResponder?

public let height: CGFloat = 30.0

public enum AnimationCurve: Int {
    case easeInOut = 0
    case easeIn = 1
    case easeOut = 2
    case linear = 3
}

enum Pizza {
    case small(inches: Int)
    case medium(inches: Int)
    case large(inches: Int)
}

struct NotificationOptions: OptionSet {
    static let daily = NotificationOptions(rawValue: 1)
    static let newContent = NotificationOptions(rawValue: 1 << 1)
    static let weeklyDigest = NotificationOptions(rawValue: 1 << 2)
    static let newFollows = NotificationOptions(rawValue: 1 << 3)

    let rawValue: Int8
}

func move(from start: Point, to end: Point)
x.move(from: x, to: y)

let response = (statusCode: Int, data: Data)
```

- 计算属性不要以动词开头

```swift
// ✅
var cellSize: CGSize { ... }

// ❌
var getCellSize: CGSize { ... }
var calculateCellSize: CGSize { ... }
```

> ##### `extension` 中的方法及属性命名  `强制❗️`

基本命名规则与上面章节的描述一致, 但还应注意一下几点

- 给自定义类型增加的 `extension` 方法或属性, 无需增加前缀
- 给系统类型增加的 `extension` 方法或属性, 如果仅仅在 module 内部使用, 也无需增加前缀
- 给系统类型增加的 `extension` 方法或属性, 如果需要提供给外部使用, 则需要增加前缀, 但所谓前缀并非 OC 中如 `xxx_` 此类格式的前缀, 目前比较流行且优雅的方式是增加一个中间变量, 如:    `"I am a string ".re.trimmed`, 实现方式通常如下

```swift
// 定义中间Wrapper
/// Wrapper for ReerKit compatible types. 
/// This type provides an extension point for connivence methods in ReerKit.
public struct Reer<Base> {
    public let base: Base
    public init(_ base: Base) {
        self.base = base
    }
}

/// Represents an object type that is compatible with ReerKit. 
/// You can use `re` property to get a value in the namespace of ReerKit.
public protocol ReerCompatible: AnyObject {}

/// Represents a value type that is compatible with ReerKit. 
/// You can use `re` property to get a value in the namespace of ReerKit.
public protocol ReerCompatibleValue {}

extension ReerCompatible {
    /// Gets a namespace holder for ReerKit compatible types.
    public var re: Reer<Self> {
        get { return Reer(self) }
        set {}
    }
    
    /// Gets a namespace holder for ReerKit compatible meta types.
    public static var re: Reer<Self>.Type {
        get { return Reer.self }
        set {}
    }
}

extension ReerCompatibleValue {
    /// Gets a namespace holder for ReerKit compatible types.
    public var re: Reer<Self> {
        get { return Reer(self) }
        set {}
    }
    
    /// Gets a namespace holder for ReerKit compatible meta types.
    public static var re: Reer<Self>.Type {
        get { return Reer.self }
        set {}
    }
}
```

```swift
// 添加计算属性或方法
extension Reer where Base == String {
    /// Return a String which removes whitespace from both ends of a string.
    public var trimmed: String {
        return base.trimmingCharacters(in: .whitespacesAndNewlines)
    }
    
    /// Does a String match a regex?
    /// - Parameter pattern: A regex String.
    /// - Returns: Return YES if the NSString match the regex.
    ///            Otherwise, return NO.
    public func matches(regex pattern: String) -> Bool {
        let matchRange = base.range(
            of: pattern, 
            options: [.regularExpression, .anchored], 
            range: nil, 
            locale: nil
        )
        return matchRange == self.range
    }   
}
```

```swift
// 使用
"I am a string ".re.trimmed
"Another string".re.matches(regex: "[1-9]\d{5}(?!\d)")
```

> ##### 特殊名词的大小写区分 `强制❗️`

一些名词是首字母缩写的，应该全部大写或全部小写，不要采用首字母大写的方式。以下列举了常见的 case，但不仅仅限于这些名词

```javascript
// ✅
iOS, JSON, XML, CDN, json, xml, URL, ID, HTTP, JPG, RGB, WebP, ASCII, SMTP 

// ❌
ios, IOS, Json, Xml, Cdn, Url, Id
```

```
在 Swift Foudation 中, URL 代表类型, 所以不要使用 URL 作为变量名, 
而是使用 url, 或更具体些： xxxURL。
另外对于 String 类型的 url, 
可以考虑使用 URLString, xxxURLString之类的命名。
// ❌
let URL = URL(string: "http://apple.com")!
```
```
如果特殊名词在变量名中间，如  iOS，iPhone，则需要改为 IOS,  不然首个小写 i 会和前面单词混在一起。
```

> ##### 可选值绑定  `强制❗️`

如果是 Swift 5.7 以下的版本, 在进行可选值绑定命名时, 应该使用跟可选变量一样的名字

```swift
// ✅
if let subview = subview, let volume = volume {
    // do something with unwrapped subview and volume
}

resource.request().onComplete { [weak self] response in
    guard let self = self else { return }
    let model = self.updateModel(response)
    self.updateUI(model)
}
```

```swift
// ❌
if let unwrappedSubview = optionalSubview {
    if let realVolume = volume {
        // do something with unwrappedSubview and realVolume  
    }
}

UIView.animate(withDuration: 2.0) { [weak self] in
    guard let strongSelf = self else { return }
    strongSelf.alpha = 1.0
}
```

> ##### 无需为静态 / 类属性名再添加类型名  `强制❗️`

```swift
// ✅
public class UIColor {
    public class var red: UIColor {
        // ...
    }
}
public class URLSession {
    public class var shared: URLSession {
        // ...
    }
}
```

```swift
// ❌
public class UIColor {
    public class var redColor: UIColor {
        // ...
    }
}
public class URLSession {
    public class var sharedSession: URLSession {
        // ...
    }
}
```

## 注释

> ##### 基本规则

- 理论上，在阅读一段代码时，如果不能让人快速地, 明确地读懂这段代码，Author 都需要添加注释，注释要详细的解释清楚这个类这个方法，或者这一段代码是**做什么**的，在使用的时候需要**注意什么**。甚至在一些场景要说明**为什么**要这样写。
- **为类的线程安全性作注释**。如果类的实例可以被多个线程访问，记得注释多线程条件下的使用规则。
- 当代码发生变化时，也要记得修改对应的注释。
- 当在注释中应用到类型名，方法名等，或一些专有名词如 `User-Agent`，建议用 ``Cache`` 的方式来标记，如果是同 Module 内的类型或函数引用，建议使用 ``Foo``，这样可以方便直接跳转
- 对于类类型，属性，方法，枚举 case 应该使用文档注释，这样在代码补全的提示中才会展示该注释，外部调用时，按住 `option` 单击也会展示该注释。Swift 中文档注释使用 `///`, 普通注释使用 `//`, **不要**使用 `/* ... */` 的方式。Xcode 中文档注释的快捷键是 `command` + `option` +  `/`
- 在必要的地方使用 `// MARK: - 方法集` `// TODO: 等待实现` `// FIXME: 有bug，需要修改` 以优化代码结构或避免遗忘重要的待修复问题。
- 如果代码可以自解释, 则无需注释

> ##### 格式

```swift
/// Writes the textual representation of each    ← Summary
/// element of `items` to the standard output.
///                                              ← Blank line
/// The textual representation for each item `x` ← Additional discussion
/// is generated by the expression `String(x)`.
///
///     print(1.0, 2.0, separator: " ... ")      ⎫
///                                              ⎬ Sample Code by Indent
///     // Prints "1.0 ... 2.0"                  ⎭
///
/// The output from each call to ``print(_:separator:terminator:)`` includes a
/// newline by default. To print the items without a trailing newline, pass an
/// empty string as `terminator`.
///
/// ```
/// for n in 1...5 {                             ⎫
///     print(n, terminator: "")                 ⎬ Sample Code by ```
/// }                                            ⎟
/// // Prints "12345"                            ⎭
/// ```
///
/// - Parameter                                  ⎫
///   - separator: text to be printed            ⎟
///   between items.                             ⎟
///   - terminator: text to be printed           ⎬ [Parameters section](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/SymbolDocumentation.html#//apple_ref/doc/uid/TP40016497-CH51-SW14)
///   at the end.                                ⎟
///                                              ⎭
/// - Note: To print without a trailing          ⎫
///   newline, pass `terminator: ""`             ⎟
///                                              ⎬ [Symbol commands](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/SymbolDocumentation.html#//apple_ref/doc/uid/TP40016497-CH51-SW13)
/// - SeeAlso: `CustomDebugStringConvertible`,   ⎟
///   `CustomStringConvertible`, `debugPrint`.   ⎭
public func print(
    _ items: Any..., 
    separator: String = " ", 
    terminator: String = "\n"
)
```

Example:

```swift
/// The template sizing a widget uses.
@available(iOS 14.0, macOS 11, *)
public enum WidgetFamily: Int, RawRepresentable {

    /// A small widget.
    case systemSmall

    /// A medium-sized widget.
    case systemMedium

    /// A large widget.
    case systemLarge

    /// An extra large widget.
    ///
    /// - Note: This property is only available in iPadOS.
    @available(iOS 15.0, *)
    @available(macOS, unavailable)
    case systemExtraLarge
}

public extension Date {
    /// Date by adding multiples of calendar component.
    ///
    ///     let date = Date() // "Jan 12, 2017, 7:07 PM"
    ///     let date2 = date.adding(.minute, value: -10) // "Jan 12, 2017, 6:57 PM"
    ///
    /// - Parameters:
    ///   - component: component type.
    ///   - value: multiples of components to add.
    /// - Returns: original date + multiples of component added.
    func adding(_ component: Calendar.Component, value: Int) -> Date {
        return calendar.date(byAdding: component, value: value, to: self)!
    }
    
    /// SwifterSwift: Check if date is within today.
    ///
    ///     Date().isInToday -> true
    ///
    var isInToday: Bool {
        return calendar.isDateInToday(self)
    }
}
```

> ##### Xcode 支持的注释标签

| [Attention](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Attention.html)   |  [Author](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Author.html)               |  [Authors](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Authors.html)           |  [Bug](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Bug.html)               |
| ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
|  [Complexity](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Complexity.html) |  [Copyright](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Copyright.html)         |  [Date](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Date.html)                 |  [Experiment](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Experiment.html) |
|  [Important](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Important.html)   |  [Invariant](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Invariant.html)         |  [Note](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Note.html)                 |  [Parameter](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Parameter.html)   |
|  [Parameters](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Parameters.html) |  [Postcondition](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Postcondition.html) |  [Precondition](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Precondition.html) |  [Remark](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Remark.html)         |
|  [Requires](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Requires.html)     |  [Returns](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Returns.html)             |  [SeeAlso](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/SeeAlso.html)           |  [Since](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Since.html)           |
|  [Throws](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Throws.html)         |  [ToDo](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Todo.html)                   |  [Version](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Version.html)           |  [Warning](https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/Warning.html)       |

## 优雅示范

> ##### 尽量使用方法和属性，减少使用全局的, 静态的函数，变量和常量  `建议🌵`

- 全局函数通常只用于特殊场景

  - 没有明显的 `self` 场景，如 `min(x, y, z)`
  - 当函数语法是特定领域符号，如 `sin(x)`
- 可以将全局函数，变量或常量放在某个类型下面

```swift
// ✅
extension Double {
    @inlinable public static var pi: Double
}
Double.pi

// 这里使用 struct 或 enum 都可以，
// 但没有 case 的 enum 没有初始化方法，可以避免误用
enum Constant {
    static let appName = "Weather"
}
Constant.appName

struct Utils {
    static func test() {}
}
Utils.test()
```

```swift
// ✅
let sorted = items.mergeSorted()
rocket.launch()

let tuples = zip(a, b)
let value = max(x, y, z)
```

```swift
// ❌
let sorted = mergeSort(items)
launch(&rocket)
```

> ##### 嵌套和命名空间  `建议🌵`

Swift 里允许嵌套 `enum`、`struct` 和 `class`，相比命名约定, 嵌套更适合表示作用域和类型之间的分级关系，因此推荐使用。例如，把类型相关的 `enum` 如错误类型放到这个类型内部。

```swift
// ✅
class Parser {
    enum Error: Swift.Error {
        case invalidToken(String)
        case unexpectedEOF
    }
    
    func parse(text: String) throws {
        guard text.isEmpty else {
            throw Parser.Error.invalidToken(text)
        }
        // ...
    }
}

enum Environment {

    enum Earth {
        static let gravity = 9.8
    }
  
    enum Moon {
        static let gravity = 1.6
    }
}
```

```swift
// 🟡
class Parser {
    func parse(text: String) throws {
        // ...
    }
}

enum ParseError: Error {
    case invalidToken(String)
    case unexpectedEOF
}
```

> ##### 为元组成员以及闭包参数增加标签  `建议🌵`

这样做可以提高可读性, 并且可以在注释中对其解释说明

```swift
// ✅
/// Ensure that we hold uniquely-referenced storage for at least
/// `requestedCapacity` elements.
///
/// If more storage is needed, `allocate` is called with
/// `byteCount` equal to the number of maximally-aligned
/// bytes to allocate.
///
/// - Returns:
///   - reallocated: `true` if a new block of memory
///     was allocated.
///   - capacityChanged: `true` if `capacity` was updated.
mutating func ensureUniqueStorage(
    minimumCapacity requestedCapacity: Int, 
    allocate: (_ byteCount: Int) -> UnsafePointer<Void>
) -> (reallocated: Bool, capacityChanged: Bool)
```

> ##### 初始化方法  `建议🌵`

对于 `struct`，Swift 会将实参为 `var` 属性和缺少默认值的 `let` 属性全部作为参数隐式生成 `init`, 可以直接使用，不需要写显式的构造器。除非是需要提供给其他组件使用时才需要显式的写明 `public init`

永远不要直接调用遵循 `ExpressibleByLiteral` 一类协议的构造器。

```swift
// ✅
struct Kilometers: ExpressibleByIntegerLiteral {
    init(integerLiteral value: Int) {
        // ...
    }
}
let k1: Kilometers = 10
let k2 = 10 as Kilometers
```

```swift
// ❌
struct Kilometers: ExpressibleByIntegerLiteral {
    init(integerLiteral value: Int) {
        // ...
    }
}
let k = Kilometers(integerLiteral: 10)
```

使用类型名字直接调用构造器时，省略 `.init`, 如 `Brand(name: "Apple")`, 而不是 `Brand.init(name: "Apple")`. 但以下几种情况允许使用 `.init` 来初始化:

```swift
// ✅ 使用元类型变量进行初始化时
let type = lookupType(context)
let x = type.init(arguments)

// ✅ 构造方法作为函数参数传入
let x = makeValue(factory: MyType.init)

// ✅ 类型已经明确的场景可以不写类型, 直接用 .init 初始化
let rect: CGRect? = .init(x: 0, y: 20, width: 200, height: 200)
var itemViewRect: CGRect {
    return .init(x: 0, y: 20, width: 200, height: 200)
}
```

> ##### 使用 `for-in` 和 `forEach` 相比 `while` 循环更具表达力  `建议🌵`

很多场景下, `for-in` 和 `forEach` 比 `while` 循环使用起来更方便, 可读性也更好

```swift
// ✅
for _ in 0..<3 {
    print("Hello three times")
}

for (index, person) in attendeeList.enumerated() {
    print("\(person) is at position #\(index)")
}

for index in stride(from: 0, to: items.count, by: 2) {
    print(index)
}

for index in (0...3).reversed() {
    print(index)
}

for item in collection where item.hasProperty {
    print(item)
}

array.forEach { element in
    print(element)
}
```

```swift
// 🟡
var i = 0
while i < 3 {
    print("Hello three times")
    i += 1
}


var i = 0
while i < attendeeList.count {
    let person = attendeeList[i]
    print("\(person) is at position #\(i)")
    i += 1
}
```

> ##### 集合类型判空使用 `isEmpty`  `强制❗️`

```swift
// ✅
var nums = []
nums.isEmpty
```

```swift
// ❌
var nums = []
nums.count == 0
```

> ##### 模式匹配  `强制❗️`

每个模式匹配元素前面都有单独的 `let` 和 `var` 关键字。适用于整个匹配模式的前置简写 `let`/`var` 是禁止的，因为当匹配模式的值本身是个变量时，会引入非预期行为。

```swift
// ✅
enum DataPoint {
    case unlabeled(Int)
    case labeled(String, Int)
}

let label = "goodbye"
// 因为没有前置的 `let`，`label` 在这里是一个值，
// 所以下面的模式匹配中只会匹配标签是“goodbye”的数据点。 
switch DataPoint.labeled("hello", 100) {
case .labeled(label, let value):
    // ...
}

// 每个单独的绑定使用前置 `let` 能清晰地表达引入了一个新的绑定（覆盖枚举项里的局部变量）
// 而不是匹配局部变量的值。这样，这个模式匹配会将数据点和任意字符串标签匹配。
switch DataPoint.labeled("hello", 100) {
case .labeled(let label, let value):
    // ...
}
```

```swift
// ❌ 在下面的例子中，如果作者意图是使用上面的 label 变量进行匹配，
// 那么就会因为 let 适用于整个模式匹配，导致该值会被任何绑定的字符串所覆盖。
switch DataPoint.labeled("hello", 100) {
case let .labeled(label, value):
    // ...
}
```

元组的实参标签和 `enum` 的关联值在用相同标签名字的变量来绑定值时，可以被省略。

```swift
// ✅
enum BinaryTree<Element> {
    indirect case subtree(
        left: BinaryTree<Element>, 
        right: BinaryTree<Element>
    )
    case leaf(element: Element)
}
switch treeNode {
case .subtree(let left, let right):
    // ...
case .leaf(let element):
    // ...
}
```

```swift
// ❌ 多余并缺乏有用信息的标签只会造成混淆
switch treeNode {
case .subtree(left: let left, right: let right):
    // ...
case .leaf(element: let element):
    // ...
}
```

如果带有关联值的 case 所有的参数都不需要标签, 则省略括号和下划线

```swift
// ✅
if case .done = result { ... }

switch animal {
case .dog:
    // ...
}
```

```swift
// ❌
if case .done(_) = result { ... }

switch animal {
case .dog(_, _, _):
    // ...
}
```

> ##### Golden Path  `强制❗️`

函数内尽量不要嵌套 `if` 语句，提前 `return` 会提高可读性, 且使用 `guard` 来实现会更好.

```swift
// ✅
func computeFFT(
    context: Context?, 
    inputData: InputData?
) throws -> Frequencies {
    guard let context = context else {
        throw FFTError.noContext
    }
    guard let inputData = inputData else {
        throw FFTError.noInputData
    }

    // use context and input to compute the frequencies  
    return frequencies
}
```

```swift
// ❌
func computeFFT(
    context: Context?, 
    inputData: InputData?
) throws -> Frequencies {
    if let context = context {
        if let inputData = inputData {
            // use context and input to compute the frequenciesreturn frequencies
        } else {
            throw FFTError.noInputData
        }
    } else {
        throw FFTError.noContext
    }
}
```

当多个可选值绑定时, 也是推荐使用 `guard let`, 而不是 `if let`

```swift
// ✅
guard let number1 = number1,
      let number2 = number2,
      let number3 = number3 else {
    fatalError("impossible")
}
// do something with numbers
```

```swift
// ❌
if let number1 = number1 {
    if let number2 = number2 {
        if let number3 = number3 {
            // do something with numbers    
        } else {
            fatalError("impossible")
        }
    } else {
        fatalError("impossible")
    }
} else {
    fatalError("impossible")
}
```

但需要注意的是, 一些场景使用 `guard` 会是可读性变差

```swift
// ❌ 类似下面这种双重否定, 阅读起来会变得困难
guard !operationFailed else {
    return
}

// ✅
if operationFailed { return }
```

```swift
// ❌ 如果仅仅是对状态的判断, 主要意图并非要结束当前作用域, 那么使用 if 更合适
guard isFriendly else {
    print("You have the manners of a beggar.")
    return
}
print("Hello, nice to meet you!")

// ✅
if isFriendly {
    print("Hello, nice to meet you!")
} else {
    print("You have the manners of a beggar.")
}
```

> ##### 定义新运算符  `强制❗️`

不理智地使用自定义运算符会显著影响代码可读性，因为比起标准库中更常用的运算符，这样的运算符缺乏共识，不容易被理解。

通常来说，应该避免定义自定义运算符。然而，当一个运算符在问题领域中有清晰和含义良好的定义，并且使用它会比函数调用显著提高代码的可读性时，可以使用。例如，`*` 在 Swift 里只定义为乘法运算符（不包含掩码版本）。数学矩阵库可能会定义额外的运算符来支持其他运算比如叉乘和点乘。

自定义 `<~~` 和 `~~>` 运算符来解码和编码 JSON 数据就是典型的禁止用法。这样的运算符不是 JSON 领域问题的原生处理方式，哪怕是有经验的 Swift 工程师，在没有运算符文档的情况下也可能会对这种处理代码有着不同的理解。

> ##### 使用高阶函数使代码更清晰  `建议🌵`

```swift
// ✅
let results = input.map { transform($0) }

// ❌
var results = [SomeType]()
for element in input {
    let result = transform(element)
    results.append(result)
}
```

```swift
// ✅
let results = input.compactMap { transformThatReturnsAnOptional($0) }

// ❌
var results = [SomeType]()
for element in input {
    if let result = transformThatReturnsAnOptional(element) {
        results.append(result)
    }
}
```

但是也应该注意链式调用高阶函数可能带来的性能问题

```swift
// ❌
// 这里循环了两次, 如果数据量较大且性能敏感, 则应慎重考虑
let array = [1, 2, 3]
let _ = array
    .filter { $0 % 2 == 0 }
    .map { $0 * $0 }
    
var items = [1, 2, 3]
items.filter { $0 > 1 }.first // 查找出所有大于1的元素，之后找出第一个
```

```swift
// ✅
var items = [1, 2, 3]
items.first { $0 > 1 } _// 查找出第一个大于1的元素直接返回
```

> ##### 默认使用 `final` 修饰 `class`  `建议🌵`

如题, 只有当 `class` 需要被重写的时候, 再移除 `final` 关键字

```swift
// ✅
final class SettingsRepository {
    // ...
}
```

```swift
// 🟡
class SettingsRepository {
    // ...
}
```

> ##### 可选变量如果不使用绑定值, 那么直接判断 `nil`  `强制❗️`

```swift
// ✅
var thing: Thing?
if thing != nil {
    doThing()
}
```

```swift
// ❌
var thing: Thing?
if let _ = thing {
  doThing()
}
```

> ##### 使用专门的日志库 `强制❗️`

使用如 `os_log` 或 `swift-log` 的日志库, 而不是使用 `print(…)`, `debugPrint(…)`, or `dump(…)`.

> ##### 单例的标准写法  `强制❗️`

单例应该命名为 `shared`, 而不是 `share` 或是其他单词, 且初始化方法应标记为 `private`

```swift
// ✅
class AccountManager {
    static let shared = AccountManager()
    private init() {
        // ...
    }
}
```

如果类型允许创建多个实例, 且提供默认实例, 通常以 `default` 命名, 并向外提供初始化方法

```swift
// ✅
class AccountManager {
    static let `default` = AccountManager()
    init() {
        // ...
    }
}
```

> ##### 尽量避免使用 `Any/AnyObject`  `建议🌵`

使用 `Any` `AnyObject` 确实明确的类型信息, 编译器无法检查类型, 可能会带来一些安全隐患, 或者业务问题. 另外在使用时需要用 `as?` 进行转换, 影响可读性

一些场景可以使用泛型或者协议关联类型替代 `Any`

> ##### 避免使用 Magic Number  `强制❗️`

Magic Number 会使代码可读性变差，而且如果后续修改也要在每个使用的地方修改，应该将其定义为变量, 常量或枚举再使用

```swift
// ✅
class CustomView: UIView {
    static let imageButtonHeight: CGFloat = 22
    
    override func viewDidLayoutSubviews() {
        super.viewDidLayoutSubviews()
        
        button.btd.width = Self.imageButtonHeight * 2
        button.btd.height = Self.imageButtonHeight
    }
}
```

```swift
// ❌
button.btd.width = 22 * 2
button.btd.height = 22
```

另外, 除非是需要明确的数值，否则函数参数，返回值也尽量不要使用 `String`，`Int` 等基础类型，因为这会让使用方迷惑，需要进一步阅读注释或沟通，增加了理解成本，也是一种 Magic Number 问题。正确的做法是使用枚举

```swift
// ✅
enum AudioPlayerLoadState: Int {
    case unknow
    case playable
    case stalled
    case error
}

var loadState: AudioPlayerLoadState {
    return .playable
}
```

```swift
// ❌
// unknow: 0, playable: 1, stalled: 2, error: 3
var loadState: Int {
    return 1
}
```

> ##### 使用 `defer` `建议🌵`

可以在 `defer` 中做资源释放等操作, 避免遗漏

```swift
// ✅
func method() {
    lock.lock()
    defer { lock.unlock() }
    // do some thing
}
```

> ##### 使用字符串插值 `建议🌵`

```swift
// ✅
let multiplier = 3
let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"
```

```swift
// ❌
let multiplier = 3
let message = String(multiplier) 
    + "times 2.5 is" 
    + String((Double(multiplier) * 2.5))
```

> ##### 使用 `@discardableResult` `强制❗️`

有些方法的的返回值并不一定被使用, 可以加上 `@discardableResult` 来消除 Xcode 警告

```swift
public struct Stack<E> {
    fileprivate var array = [E]()
 
    @discardableResult
    public mutating func pop() -> E? {
        return array.popLast()
    }
}
```

> ##### 避免元素数量过多的元组  `强制❗️`

当元组元素超过 **3** 个时, 应该考虑改为使用 `struct` 或 `class`

> ##### `protocol` 视情况添加约束类型 `建议🌵`

当 `protocol` 仅限于 `class` 或者某种父类才能使用时, 为其添加约束可以避免误用, 也会使得编译器不需要考虑其他类型的实现, 从而提高运行时性能

```swift
protocol ObjectProtocol: AnyObject {}
protocol SomeProtocol: UIViewController {}
```

> ##### 当函数参数为闭包时, 可以通过一些方式来兼容多种调用 `建议🌵`

一些场景下为函数增加 `rethrows`, 返回值, 为他的闭包类型的参数增加 `throws` 和返回值, 就可以兼容多种调用方式

```swift
public func locked<Result>(
    _ lock: UnfairLock, 
    execute: () throws -> Result
) rethrows -> Result {
    lock.lock()
    defer { lock.unlock() }
    return try execute()
}

// 无返回值, 不抛异常
locked(lock) {
    print("")
}

// 有返回值, 不抛异常
let result = locked(lock) {
    return ""
}

// 无返回值, 抛异常
try? locked(lock) {
    throw TestError.invalidParam
}

// 有返回值, 抛异常
let ret = try? locked(lock) { () -> String in 
    if Bool.random() {
        throw TestError.invalidParam
    }
    return ""
}
```

> ##### 对高频使用且较为简短的函数或计算属性进行内联  `建议🌵`

使用 `@inlinable` 或 `@inline(__always)` 对函数或计算属性进行内联

```swift
extension Foo {
    @inlinable
    func bar() -> Baz { ... }
}

@inline(__always)
func bar() -> Baz { ... }
```

> ##### 使用 `lazy` `建议🌵`

- 使用 `lazy` 延迟初始化属性

```swift
class View {
    lazy var nameLabel: UILabel = {
        let label = UILabel()
        label.text = ""
        label.font = .systemFont(ofSize: 12)
        return label
    }()
}
```

- 使用 `lazy` 延迟序列, 将集合元素的运算推迟到第一次使用, 避免一次性全部计算

```swift
var nums = [1, 2, 3]
var result = nums.lazy.map { String($0) }
result[0] // 对1进行map操作
result[1] // 对2进行map操作
```

> ##### 使用自动闭包 `@autoclosure` 来避免一些不必要的计算 `建议🌵`

```swift
// ✅ 比如实现`或`操作, 当左面为true时, 不需要再计算右边了
// 使用自动闭包机制, 可以让外部传入表达式以备后续执行, 而不是先计算值再传入
func || (left: Bool, right: @autoclosure () -> Bool) -> Bool {
    if left {
        return true
    } else {
        return right()
    }
}
let value = condition1 || (9 * 9 + 4 / 2 > 35)
```

> ##### Objective-C 互操作性  `强制❗️`

如果 Swift 类需要被 Objective-C 使用, 使用 `@``objc` 关键字来导出 API, 尽量不要使用 `@objcMember` 全量导出所有 API

另外, 为每个提供给 OC 使用的类起一个带前缀的名字

```swift
// ✅
@objc(BTDPriceBreakdownViewController)
public class PriceBreakdownViewController: UIViewController {
    private let acceptButton = UIButton()
    
    public func setUpAcceptButton() {
        
    }
  
    @objc
    public func didTapAcceptButton() {
        // ...  
    }
}
```

## 安全性

> ##### 禁止用 `subscript` 直接访问数组元素 `强制❗️`

使用 `subscript` 访问数组元素应该先做边界检查, 或使用 `first`, `last` 之类的访问方式, 或编写自定义的安全访问扩展, 以保证不会越界崩溃

> ##### 尽可能使用 `let` 而不是 `var`  `建议🌵`

不确定常量还是变量时, 直接使用 `let`, 当你想要修改它时, 编译器会提示你.

> ##### 访问控制  `建议🌵`

- 除非已经明确作用域, 否则尽量使用 `private`, 如有必要再改为其他.
- 除非是基础库, 否则通常不建议给 `extension` 指定为 `open` 或 `public` , 而是应该给扩展里的 `var` 或 `func` 单独指定访问控制符, 或直接使用默认的 `internal`

```swift
// ✅
extension String {
    public var isUppercase: Bool {
        // ...
    }
    
    public var isLowercase: Bool {
        // ...
    }
}
```

```swift
// 🟡
public extension String {
    var isUppercase: Bool {
        // ...
    }
    
    var isLowercase: Bool {
        // ...
    }
}
```

> ##### 类型方法默认用 `static`, 而不是 `class`  `建议🌵`

如题, 只有当需要被重写的时候, 再改为 `class` 修饰

```swift
// ✅
class Fruit {
    static func eatFruits(_ fruits: [Fruit]) { ... }
}
```

```swift
// 🟡
class Fruit {
    class func eatFruits(_ fruits: [Fruit]) { ... }
}
```

> ##### 尽可能处理掉编译器提示的警告  `建议🌵`

> ##### 保证 Range 的 lowerBound <= upperBound  `强制❗️`

对于 Range 的 lowerBound 要保证小于等于 upperBound, 否则会引起运行时崩溃

> ##### 禁止使用 `unowned` 关键字  `强制❗️`

除非是必要的场景可以使用 `unowned`, 否则通常情况下应该使用 `weak`

```swift
// ✅
resource.request().onComplete { [weak self] response in
    guard let self = self else { return }
    let model = self.updateModel(response)
    self.updateUI(model)
}
```

```swift
// ❌ 如果 self 被释放了, 然后回调 onComplete 就会crash
resource.request().onComplete { [unowned self] response in
    let model = self.updateModel(response)
    self.updateUI(model)
}

// ❌ self 释放可能发生在 updateModel 后, updateUI 前, 可能发生未知的问题
resource.request().onComplete { [weak self] response in
    let model = self?.updateModel(response)
    self?.updateUI(model)
}
```

> ##### 禁止使用 `!` 进行强制解包  `强制❗️`

除非是**特别必要**的场景, 以及**非常确定**可选变量有值, 否则禁止使用 `!` 来对可选变量进行强制解包.

```swift
// ❌
let value: String?
print(value!)
```

```swift
// ✅
let value: String?
if let value = value {
    print(value)
}
```

> ##### 禁止强制类型转换 `强制❗️`

禁止对 `Any` 类型进行强制转换, 可能会引起崩溃

```swift
// ❌
let value: Any? = ""
let result = value as! Int
```

```swift
// ✅
let value: Any? = ""        
if let intValue = value as? Int {
    print(intValue)
}
```

> ##### 禁止使用 `try!` `强制❗️`

对于抛异常的函数调用, 禁止使用 `try!`, 应该使用 `do try catch` 处理异常, 或使用 `try?` 来避免崩溃

> ##### 谨慎处理 Objective-C 传入 Swift 的值 `强制❗️`

即使在 OC 中标记为 `nonnull` 的值, 由于不规范的使用依然可能是一个 `nil` 值, 如果该值传入 Swift 中使用, 就会引发崩溃. 不过通常这需要 OC 侧来保证值的可靠性, 但 Swift 使用者也应该格外小心.
