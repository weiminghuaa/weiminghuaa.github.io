# RxSwift源码分析--share and Driver

share
Driver

## share

- share与非share的区别

share：每次subscribe，会共用create闭包的内容，即多次subscribe，create闭包不会执行多次
非share：每次subscribe，都会独立执行create闭包的内容，各不干扰

- share的作用：很多observable，会bind多次，比如SimpleValidationViewController.swift中，用户名输入框的校验，即绑定密码框是否可以，又绑定用户名提示是否显示，share可以共用事件，实现事件发出1次，多个observer监听，并实现业务逻辑绑定，避免无意义的校验事件多次发出

```swift
//  ShareReplayScope.swift

extension ObservableType {

    /**
     Returns an observable sequence that **shares a single subscription to the underlying sequence**, and immediately upon subscription replays  elements in buffer.
     
     This operator is equivalent to:
     * `.whileConnected`
     ```
     // Each connection will have it's own subject instance to store replay events.
     // Connections will be isolated from each another.
     source.multicast(makeSubject: { Replay.create(bufferSize: replay) }).refCount()
     ```
     * `.forever`
     ```
     // One subject will store replay events for all connections to source.
     // Connections won't be isolated from each another.
     source.multicast(Replay.create(bufferSize: replay)).refCount()
     ```
     
     It uses optimized versions of the operators for most common operations.

     - parameter replay: Maximum element count of the replay buffer.
     - parameter scope: Lifetime scope of sharing subject. For more information see `SubjectLifetimeScope` enum.

     - seealso: [shareReplay operator on reactivex.io](http://reactivex.io/documentation/operators/replay.html)

     - returns: An observable sequence that contains the elements of a sequence produced by multicasting the source sequence.
     */
    public func share(replay: Int = 0, scope: SubjectLifetimeScope = .whileConnected)
        -> Observable<E> {
        switch scope {
        case .forever:
            switch replay {
            case 0: return self.multicast(PublishSubject()).refCount()
            default: return self.multicast(ReplaySubject.create(bufferSize: replay)).refCount()
            }
        case .whileConnected:
            switch replay {
            case 0: return ShareWhileConnected(source: self.asObservable())
            case 1: return ShareReplay1WhileConnected(source: self.asObservable())
            default: return self.multicast(makeSubject: { ReplaySubject.create(bufferSize: replay) }).refCount()
            }
        }
    }
}
```

- 一般的使用是`.share(replay: 1)`

1. 进入到ShareReplay1WhileConnected(source: self.asObservable())，初始化时，将原来的Observable保存到source属性

2. 之后通过Connection来保证，subscribe只会调用1次

# Driver

1. `.asDriver(onErrorJustReturn: CLAuthorizationStatus.notDetermined)`

```swift
//  ObservableConvertibleType+Driver.swift

extension ObservableConvertibleType {
    /**
    Converts observable sequence to `Driver` trait.
    
    - parameter onErrorJustReturn: Element to return in case of error and after that complete the sequence.
    - returns: Driver trait.
    */
    public func asDriver(onErrorJustReturn: E) -> Driver<E> {
        let source = self
            .asObservable()
            .observeOn(DriverSharingStrategy.scheduler)
            .catchErrorJustReturn(onErrorJustReturn)
        return Driver(source)
    }
    
    /**
    Converts observable sequence to `Driver` trait.
    
    - parameter onErrorDriveWith: Driver that continues to drive the sequence in case of error.
    - returns: Driver trait.
    */
    public func asDriver(onErrorDriveWith: Driver<E>) -> Driver<E> {
        let source = self
            .asObservable()
            .observeOn(DriverSharingStrategy.scheduler)
            .catchError { _ in
                onErrorDriveWith.asObservable()
            }
        return Driver(source)
    }

    /**
    Converts observable sequence to `Driver` trait.
    
    - parameter onErrorRecover: Calculates driver that continues to drive the sequence in case of error.
    - returns: Driver trait.
    */
    public func asDriver(onErrorRecover: @escaping (_ error: Swift.Error) -> Driver<E>) -> Driver<E> {
        let source = self
            .asObservable()
            .observeOn(DriverSharingStrategy.scheduler)
            .catchError { error in
                onErrorRecover(error).asObservable()
            }
        return Driver(source)
    }
}
```

asDriver函数执行：

- self.asObservable()：将self转为Observable
- .observeOn(DriverSharingStrategy.scheduler) 选择执行的scheduler，一般是MainScheduler?
- .catchErrorJustReturn(onErrorJustReturn) 错误处理
- 初始化Driver，source数为转化好的Observable

2. `.drive(noGeolocationView.rx.isHidden)`

```swift
//  Driver+Subscription.swift

extension SharedSequenceConvertibleType where SharingStrategy == DriverSharingStrategy {
    /**
    Creates new subscription and sends elements to observer.
    This method can be only called from `MainThread`.

    In this form it's equivalent to `subscribe` method, but it communicates intent better.

    - parameter observer: Observer that receives events.
    - returns: Disposable object that can be used to unsubscribe the observer from the subject.
    */
    public func drive<O: ObserverType>(_ observer: O) -> Disposable where O.E == E {
        MainScheduler.ensureExecutingOnScheduler(errorMessage: errorMessage)
        return self.asSharedSequence().asObservable().subscribe(observer)
    }
    
    ...
}
```

- drive执行，self.asSharedSequence().asObservable()转换后的类型是ShareReplay1WhileConnected，与share一致，后续就是share的流程了

- asDriver时初始化Driver，Drive就是SharedSequence类型`public typealias Driver<E> = SharedSequence<DriverSharingStrategy, E>`

- 而SharedSequence初始化，对传入的souce进行了处理`S.share(source)`, 导致DriverSharingStrategy的share方法执行，调用了`.share(replay: 1, scope: .whileConnected)`, 所以drive执行会到ShareReplay1WhileConnected

- 综上所述，drive可以看做是一个封装的share，多了observeOn和catchErrorJustReturn
