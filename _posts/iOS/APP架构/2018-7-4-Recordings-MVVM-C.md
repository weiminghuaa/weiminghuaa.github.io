#重写 https://github.com/objcio/app-architecture.git 中的Recording-MVVM-C项目

## 与Recording-MVC对比

### 状态绑定

> PlayViewController

Recording-MVC: 封装3个方法updateForChangedRecording、updateProgressDisplays、updatePlayButton，外界根据需要统一调用，比如点击播放，播放状态切换，方法内，再根据需要，显示状态。

Recording-MVVM-C: viewDidLoad，绑定ViewModel的信号，点击播放，向ViewModel发送信号，ViewModel处理信号，之后通过双向绑定，自动同步显示出来。

> FolderViewController

Recording-MVC: 数据修改，发送通知，FolderViewController接收通知，各种刷新tableview调用。

Recording-MVVM-C: viewDidLoad，tableview绑定ViewModel的信号，数据修改，数据改变等等全部自动化产生相关信号，之后通过双向绑定，自动同步显示出来。

### 测试

基本只针对ViewModel

## 知识点

### Coordinator

在 MVVM 中，协调器并非是强制需要的部件，但是它可以帮助 controller 从两个额外的职责中解放出来: controller 可以不再需要管理其他 controller 的展示，它也不再需要去协调 model 数据和 controller 之间的通讯。协调器负责维护 controller 的层级，这样一来，controller 和 view-model 就只需要专注处理它们在特定场景的使用了。