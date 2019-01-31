#重写 https://github.com/objcio/app-architecture.git 中的Recording-MVC项目

## 与保险人项目对比

### Model

Recording-MVC: model同时处理数据操作，负责数据维护等业务逻辑，如addFloder
YueDu: model不做任何事，非常干净，数据操作和维护逻辑由Controller处理，如addFlowup(添加跟进记录)

### tableview 刷新

Recording-MVC: 传递刷新操作，区分移动，新增，删除
YueDu: 全部调用reloaddata

### UnitTest

> 只做了1个controller的单元测试，测试覆盖率58.77%
> 为了单元测试，好像特意加了个store的placeholder
> 单元测试的setup，值得参考
> 一些单元测试很有价值，如testChangeNotificationHandling
> testChangeNotificationHandling,了解到数据变化是立刻的,也许绘制不是


## 知识点

### Swift Codable协议 

实现json到model转换
http://www.rockerhx.com/2017/09/25/2017-09-25-Swift4-Codable-Basic/

### fileprivate与类静态变量之字符串

fileprivate: 文件内字符串的使用，类似OC中对cell identifier

```Swift
modalTextAlert(title: .createFolder, accept: .create, placeholder: .folderName) {
    
}

fileprivate extension String {
    static let uuidPathKey = "uuidPath"
    
    static let createFolder = NSLocalizedString("Create Folder", comment: "Header for folder creation dialog")
}
```

类静态变量之字符

```Swift
final class Store {
    static let changedNotification = Notification.Name("StoreChanged")
    static private let documentDirectory = try! FileManager.default.url(for: .libraryDirectory, in: .userDomainMask, appropriateFor: nil, create: true)
}

extension Item {
    static let changeReasonKey = "reason"
}
```

### final

final 关键字可以用在 class，func 或者 var 前面进行修饰，表示不允许对该内容进行继承或者重写操作。

### UISplitViewControllerDelegate

下面的代理方法，return true 和 false，效果不一样:
return true: 显示FolderViewController
return false: 显示PlayViewController

```Swift
func splitViewController(_ splitViewController: UISplitViewController, collapseSecondary secondaryViewController: UIViewController, onto primaryViewController: UIViewController) -> Bool {
        guard let topAsDetailController = (secondaryViewController as? UINavigationController)?.topViewController as? PlayViewController else { return false }
        if topAsDetailController.recording == nil {
            // Don't include an empty player in the navigation stack when collapsed
            return true
        }
        return false
    }
```

### fatalError

在遇到确实因为输入的错误无法使程序继续运行的时候，我们一般考虑以产生致命错误 (fatalError) 的方式来终止程序。

### UIStateRestoring

APP程序状态保存和恢复，后面用到在研究吧

### ArraySlice

http://www.karsa.info/blog/?p=320

### flatMap

https://swift.gg/2015/12/10/reduce-all-the-things/

### 很奇怪的内存泄漏问题

UIAletController 和 UIAlertAction 循环引用

```Swift
extension UIViewController {
    func modalTextAlert(title: String, accept: String = .ok, cancel: String = .cancel, placeholder: String, callback: @escaping (String?) -> ()) {
        let alert = UIAlertController(title: title, message: nil, preferredStyle: .alert)
        alert.addTextField { $0.placeholder = placeholder }
        alert.addAction(UIAlertAction(title: cancel, style: .cancel) { _ in
            callback(nil)
        })

        // 下面不写[weak alert]的话，造成UIAletController 和 UIAlertAction 循环引用
        alert.addAction(UIAlertAction(title: accept, style: .default) { [weak alert] _ in  
            callback(alert?.textFields?.first?.text)
        })

        present(alert, animated: true)
    }
}
```