# Realm

## 打开一个 Realm

- 向构造函数传递一个配置对象，这个配置对象指明了如何访问 Realm
- 配置对象指明了 Realm 数据库是位于什么地方
    - 本地文件系统的 URL
    - Realm 对象服务器的 URL, 同时还会包括访问的一些验证数据
    - 一个位于内存中的 Realm 标识符

## 后台刷新

在 iOS 8 后，若使用了后台刷新的特性，并且涉及了 Realm, 则需要注意了

App 内的文件在设备锁屏下，会使用 `NSFileProtection` 自动加密，对 Realm 数据库进行打开操作会抛出异常

因此，需要降低 Realm 数据库文件和它的辅助文件的保护等级，同时，也可以选择使用 Realm 自带的加密方式

降低保护等级，从相关文件的上一层文件夹入手

```swift
let realm = try! Realm()

// Get our Realm file's parent directory
let folderPath = realm.configuration.fileURL!.deletingLastPathComponent().path

// Disable file protection for this directory
try! FileManager.default.setAttributes([FileAttributeKey(rawValue: NSFileProtectionKey): NSFileProtectionNone],
                                       ofItemAtPath: folderPath)
```

## 打开一个默认 Realm

```swift
let realm = try! Realm()
```

## 设置 Realm 的配置

```swift
Realm(configuration: config)
```

或

```swift
// 设置默认的配置
Realm.Configuration.defaultConfiguration = config
```

## 错误处理

在实践中，发生异常的时刻总是会在第一次在某一个线程中，创建一个 Realm 实例。随后，在相同的线程中访问 Realm 实例总会成功，因为会复用缓存的实例

捕获异常，使用 Swift 的捕获机制就好

```swift
do {
  let realm = try Realm()
} catch let error as NSError {
  // handle error
}
```

## Realm 的文件

- `.realm` 主文件
- `.realm.lock` 与资源锁相关
- `.realm.management` 进程间通信的锁相关文件
- `.realm.note` A named pipe for notifications?

## 压缩 Realm

Realm 的架构意味着，它的文件大小会比它最后包含的数据大小要大，这与 Realm 的高性能，并行和安全的优点有关

为了避免在扩充 Realm 文件时经常调用耗时的系统操作，Realm 文件在运行时默认不会压缩

当然，可以通过 Configuration 来控制压缩

```swift
let config = Realm.Configuration(shouldCompactOnLaunch: { totalBytes, usedBytes in
    // totalBytes refers to the size of the file on disk in bytes (data + free space)
    // usedBytes refers to the number of bytes used by data in the file

    // Compact if the file is over 100MB in size and less than 50% 'used'
    let oneHundredMB = 100 * 1024 * 1024
    return (totalBytes > oneHundredMB) && (Double(usedBytes) / Double(totalBytes)) < 0.5
})
do {
    // Realm is compacted on the first open if the configuration block conditions were met.
    let realm = try Realm(configuration: config)
} catch {
    // handle error compacting or opening Realm
}
```

要注意的是：

- 实现原理就是通过创建一个文件，重新写入数据，之后再把源文件替换掉，所以，这可能是一个很耗时的操作
- 即使压缩的条件满足了，但如果另一个线程正在访问 Reaml, 压缩操作将会跳过不执行

## 删除 Realm 文件

```swift
autoreleasepool {
    // all Realm usage here
}
let realmURL = Realm.Configuration.defaultConfiguration.fileURL!
let realmURLs = [
    realmURL,
    realmURL.appendingPathExtension("lock"),
    realmURL.appendingPathExtension("note"),
    realmURL.appendingPathExtension("management")
]
for URL in realmURLs {
    do {
        try FileManager.default.removeItem(at: URL)
    } catch {
        // handle error
    }
}
```

## 数据模型 Model

### 定义 Model

使用 Swift 定义的 Realm 的数据模型是通过类属性来实现，像普通 Swift 类一样使用，只要继承了 `Object` 类，或者另一个数据模型类

### 限制

主要的限制就是，数据模型的实例，只能在创建它的线程中使用

### Model 的定义必须合法

Realm 将会在代码运行时，转换所有的 Model, 因此，它们必须是合法的，即使有些模型并没有使用到

### 所有必选项属性都需要有默认值

在 Swift 中使用 Realm, `Swift.reflect(_:)` 将会被调用来了解关于 Model 的信息，这就要求，Model 的 `init()` 方法必须能成功调用

## 支持的类型

- `Bool`
- `Int`
- `Int8`
- `Int16`
- `Int32`
- `Int64`
- `Double`
- `Float`
- `String`
- `Date`
- `Data`

> `CGFloat` 不鼓励使用，因为这不是与平台无关的类型
> 
> `String`, `Date`, `Data` 类型的属性，可以是 optional
> 若要存储 optional 的数字类型，需要使用 `RealmOptional`

## 主键

定义一个主键可以提升查找和更新数据的性能，主键一旦设置了就不可以更改

定义主键直接覆盖 `Object.primaryKey()` 方法

```swift
class Person: Object {
    @objc dynamic var id = 0
    @objc dynamic var name = ""

    override static func primaryKey() -> String? {
        return "id"
    }
}
```

## 索引 Indexing properties

cons

- 写速度稍微下降
- Realm 文件稍微大一点，因为要存储索引

pros

- 让使用比较操作符的查询速度加快

支持建立索引的数据类型

- `String`
- integer
- `Bool`
- `Date`

使用索引，直接覆盖 `Object.indexedProperties()` 方法

```swift
class Book: Object {
    @objc dynamic var price = 0
    @objc dynamic var title = ""

    override static func indexedProperties() -> [String] {
        return ["title"]
    }
}
```

## 忽略属性

若不想某些属性整合到 Realm, 就可以通过覆盖 `Object.ignoreProperties()` 方法

```swift
class Person: Object {
    @objc dynamic var tmpID = 0
    var name: String { // read-only properties are automatically ignored
        return "\(firstName) \(lastName)"
    }
    @objc dynamic var firstName = ""
    @objc dynamic var lastName = ""

    override static func ignoredProperties() -> [String] {
        return ["tmpID"]
    }
}
```

这些被忽略的属性

- 将使用 ivars 来进行支持，可以随便定义它们的 setter 和 getter
- 不会响应与 Realm 相关的特性，但仍然可以被 KVO

## 模型数据自动刷新

如果 UI 以来模型数据的话，可以订阅 Realm notifications 来监控数据的更新状态

## 集合类型

- `Results` 从查询中获取到的结果
- `List` 表示模型的"对多"关系
- `LinkingObjects` 表示模型间的反向关系
- `RealmCollection` Realm 集合类型需要实现的一个协议
- `AnyRealmCollection` 一个无类型的类，用来向具体的集合类型传递方法调用

## 写操作

> 所有对模型数据的更改（添加，修改，删除），都必须在一个写事务中进行

通过模型数据类创建的实例，在添加到 Realm 之前，可以像普通的 Swift 类实例一样浪，但当添加到 Realm 时，必须通过一个写事务

- 由于写事务会产生不可忽略的开销，因此需要尽可能减少写入数据
- Realm 的写操作都是**同步的**
- 写操作会在 `beginWrite()` 时自动刷新，所以不会产生竞争条件???
- 因为写操作会堵塞当前线程，所以最佳实践是将写操作搬到一个独立的线程中进行
- 读操作并不会由于写事务的进行而被堵塞，除非有多个写事务同时需要进行

## 更新数据

更新数据有两种方式

- 直接在写事务中对属性进行赋值
- KVC

## 通过主键来更新数据

基于主键对数据进行更新或添加，调用 `Realm().add(_:update:)`, 重点在于 `update` 这个参数

```swift
// Creating a book with the same primary key as a previously saved book
let cheeseBook = Book()
cheeseBook.title = "Cheese recipes"
cheeseBook.price = 9000
cheeseBook.id = 1

// Updating book with id = 1
try! realm.write {
    realm.add(cheeseBook, update: true)
}
```

通过这种方式更新数据，可以只传入需要更新的数据，而不是整个对象

```swift
// Assuming a "Book" with a primary key of `1` already exists.
try! realm.write {
    realm.create(Book.self, value: ["id": 1, "price": 9000.0], update: true)
    // the book's `title` property will remain unchanged.
}
```

> 对于没有定义主键的模型，不可以基于主键来添加或更新数据

## 查询

- 查询操作返回一个 `Results` 实例
    - 这个实例大抵与 Swift 的 `Array` 相同，不同之处在于，`Results` 中的元素类型都必须是一致的
- 所有的查询操作，属性访问，都是懒加载的
- `Results` 中的数据并不是数据的一份副本，所以对 `Results` 中的数据进行修改（需要在写事务中），都会直接影响到持久性存储中的数据
- 当查询操作执行之后，或一个通知 block 被添加之后，`Results` 中的数据将与最新数据保持同步

最简单的查询，返回所有数据

```swift
let dogs = realm.objects(Dog.self)
```

## 过滤

过滤调用方法 `Results().filter(_:...)`

过滤条件使用的是字符串，与 `NSPredicate` 大抵相同

```swift
// Query using a predicate string
var tanDogs = realm.objects(Dog.self).filter("color = 'tan' AND name BEGINSWITH 'B'")

// Query using an NSPredicate
let predicate = NSPredicate(format: "color = %@ AND name BEGINSWITH %@", "tan", "B")
tanDogs = realm.objects(Dog.self).filter(predicate)
```

## 排序

## 链式查询

## 分页读取

在其他的数据库中，通常使用 `LIMIT` 关键字限制一次读取的数据量，达到了分页的效果

而在 Realm 中，由于懒加载的特性，直接拿 `Results` 读取就可以了

```swift
// Loop through the first 5 Dog objects
// restricting the number of objects read from disk
let dogs = try! Realm().objects(Dog.self)
for i in 0..<5 {
    let dog = dogs[i]
    // ...
}
```

## 通知

可以注册一个监听器来接受通知，这些通知将会在 Realm 发生改变，或者 Realm 中的实体发生了变化是发出

- Realm notifications Realm 作为一个整体发生改变时发出
- collection notifications 单独的一个实例发生修改，添加或删除时发出

当一个通知的 token 还在被强引用着时，通知就会发出，所以，当需要获取通知时，必须保持对 token 的强引用，否则，通知将会被取消订阅

通知将会在它们注册订阅者的线程上进行发送，并且这个线程必须要有一个 RunLoop
    - 如果在主线程外的线程订阅通知，则需要负责对该线程创建一个 RunLoop

通知的发送是异步的，发送时机在对应的写事务 commit 之后进行

由于同时是通过 RunLoop 来尽心传递，所以通知可能会被该 RunLoop 中的其他活动延迟。当通知不能被立即传递时，多个写事务中的变化可能会合并成一个通知

### Realm 通知

通知可以被注册到一整个 Realm 中，每一次某个 Realm 中的写事务提交后，通知都会被传递

```swift
// Observe Realm Notifications
let token = realm.addNotificationBlock { notification, realm in
    viewController.updateUI()
}

// later
token.stop()
```

### 集合通知

集合通知中不会包含整个对应的 Realm, 但包含对数据变化的跟详细的叙述，如添加，删除，更改

集合通知也是异步传递，通知中的参数首先是最原始的结果，后面就是对添加，删除，更新数据的进一步说明，这些变化可以通过 `RealmCollectionChange` 参数进行获取，其中包括了 `deletions`, `insertions`, `modifications`

通知将会涵盖对实例属性的一切修改，以及一对一，一对多关系的变化，但不会包含反向关系的变化

```swift
class ViewController: UITableViewController {
    var notificationToken: NotificationToken? = nil

    override func viewDidLoad() {
        super.viewDidLoad()
        let realm = try! Realm()
        let results = realm.objects(Person.self).filter("age > 5")

        // Observe Results Notifications
        notificationToken = results.addNotificationBlock { [weak self] (changes: RealmCollectionChange) in
            guard let tableView = self?.tableView else { return }
            switch changes {
            case .initial:
                // Results are now populated and can be accessed without blocking the UI
                tableView.reloadData()
            case .update(_, let deletions, let insertions, let modifications):
                // Query results have changed, so apply them to the UITableView
                tableView.beginUpdates()
                tableView.insertRows(at: insertions.map({ IndexPath(row: $0, section: 0) }),
                                     with: .automatic)
                tableView.deleteRows(at: deletions.map({ IndexPath(row: $0, section: 0)}),
                                     with: .automatic)
                tableView.reloadRows(at: modifications.map({ IndexPath(row: $0, section: 0) }),
                                     with: .automatic)
                tableView.endUpdates()
            case .error(let error):
                // An error occurred while opening the Realm file on the background worker thread
                fatalError("\(error)")
            }
        }
    }

    deinit {
        notificationToken?.stop()
    }
}
```

### 对象通知

Realm 支持对象级别的通知。这意味着，可以为某一个特定的 Realm 数据模型注册通知

当这个数据模型实例被删除之后，通知回调将不会在被调用

```swift
class StepCounter: Object {
    @objc dynamic var steps = 0
}

let stepCounter = StepCounter()
let realm = try! Realm()
try! realm.write {
    realm.add(stepCounter)
}
var token : NotificationToken?
token = stepCounter.addNotificationBlock { change in
    switch change {
    case .change(let properties):
        for property in properties {
            if property.name == "steps" && property.newValue as! Int > 1000 {
                print("Congratulations, you've exceeded 1000 steps.")
                token = nil
            }
        }
    case .error(let error):
        print("An error occurred: \(error)")
    case .deleted:
        print("The object was deleted.")
    }
}
```

## 界面驱动存储

Realm 中的通知总是异步传送的，所以并不会堵塞主线程。但在某些情况下，数据的变化需要在主线程中同步进行，并立即反应到 UI 上

其中一种情况就是为 table view 添加一个条目，同时需要为 table view 的数据源添加一条数据，此时，通知的异步在这里并不恰当，因为异步的通知反馈将会导致 App 崩溃，因为 table view 的条目与数据源中的数据不一致

此时，我们将使用 `Realm.commitWrite(withoutNotifying:)`

```swift
// Add fine-grained notification block
token = collection.addNotificationBlock { changes in
    switch changes {
    case .initial:
        tableView.reloadData()
    case .update(_, let deletions, let insertions, let modifications):
        // Query results have changed, so apply them to the UITableView
        tableView.beginUpdates()
        tableView.insertRows(at: insertions.map({ IndexPath(row: $0, section: 0) }),
                             with: .automatic)
        tableView.deleteRows(at: deletions.map({ IndexPath(row: $0, section: 0)}),
                             with: .automatic)
        tableView.reloadRows(at: modifications.map({ IndexPath(row: $0, section: 0) }),
                             with: .automatic)
        tableView.endUpdates()
    case .error(let error):
        // handle error
        ()
    }
}

func insertItem() throws {
     // Perform an interface-driven write on the main thread:
     collection.realm!.beginWrite()
     collection.insert(Item(), at: 0)
     // And mirror it instantly in the UI
     tableView.insertRows(at: [IndexPath(row: 0, section: 0)], with: .automatic)
     // Making sure the change notification doesn't apply the change a second time
     try collection.realm!.commitWrite(withoutNotifying: [token])
}
```

## References

- [https://realm.io/docs/swift/latest/](https://realm.io/docs/swift/latest/)

