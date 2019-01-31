# RxSwift源码分析--Observable

```swift
  _ = Observable<String>.create { observerOfString in
            print("Observable created")
            observerOfString.on(.next("😉"))
            observerOfString.on(.completed)
            return Disposables.create()
        }
        .subscribe { event in
            print(event)
        }

/* print
Observable created
next(😉)
completed
*/
```

**时刻注意Observable和Observer是不同的，一个是消息来源，是一个订阅的处理**

## class Observable

1. 类本身没有什么内容，Observable.swift

2. 继承自protocol ObservableType

### protocol ObservableType

1. 继承自ObservableConvertibleType，没什么内容

2. **最主要的方法：subscribe，将event发给subscribe闭包**

```swift
//  ObservableType.swift
func subscribe<O: ObserverType>(_ observer: O) -> Disposable where O.E == E
}
```

3. 有几个扩展

#### create

```swift
//  create.swift

extension ObservableType {
    // MARK: create

    /**
     Creates an observable sequence from a specified subscribe method implementation.

     - seealso: [create operator on reactivex.io](http://reactivex.io/documentation/operators/create.html)

     - parameter subscribe: Implementation of the resulting observable sequence's `subscribe` method.
     - returns: The observable sequence with the specified implementation for the `subscribe` method.
     */
    public static func create(_ subscribe: @escaping (AnyObserver<E>) -> Disposable) -> Observable<E> {
        return AnonymousObservable(subscribe)
    }
}
```

#### 多种subscribe func

subscribe方式

```swift
// 1. 
.subscribe { event in
    print(event)
}

// 2. 
.subscribe(onNext: { string in
    print(string)
})
```

内部实现：**最终，都是创建observer对象，调用原本的subscribe方法**

```swift
//  ObservableType+Extensions.swift

extension ObservableType {
    /**
     Subscribes an event handler to an observable sequence.
     
     - parameter on: Action to invoke for each event in the observable sequence.
     - returns: Subscription object used to unsubscribe from the observable sequence.
     */
    public func subscribe(_ on: @escaping (Event<E>) -> Void)
        -> Disposable {
            let observer = AnonymousObserver { e in
                on(e)
            }
            return self.asObservable().subscribe(observer)
    }
    
    
    /**
     Subscribes an element handler, an error handler, a completion handler and disposed handler to an observable sequence.
     
     - parameter onNext: Action to invoke for each element in the observable sequence.
     - parameter onError: Action to invoke upon errored termination of the observable sequence.
     - parameter onCompleted: Action to invoke upon graceful termination of the observable sequence.
     - parameter onDisposed: Action to invoke upon any type of termination of sequence (if the sequence has
     gracefully completed, errored, or if the generation is canceled by disposing subscription).
     - returns: Subscription object used to unsubscribe from the observable sequence.
     */
    public func subscribe(onNext: ((E) -> Void)? = nil, onError: ((Swift.Error) -> Void)? = nil, onCompleted: (() -> Void)? = nil, onDisposed: (() -> Void)? = nil)
        -> Disposable {
            let disposable: Disposable
            
            if let disposed = onDisposed {
                disposable = Disposables.create(with: disposed)
            }
            else {
                disposable = Disposables.create()
            }
            
            #if DEBUG
                let synchronizationTracker = SynchronizationTracker()
            #endif
            
            let callStack = Hooks.recordCallStackOnError ? Hooks.customCaptureSubscriptionCallstack() : []
            
            let observer = AnonymousObserver<E> { event in
                
                #if DEBUG
                    synchronizationTracker.register(synchronizationErrorMessage: .default)
                    defer { synchronizationTracker.unregister() }
                #endif
                
                switch event {
                case .next(let value):
                    onNext?(value)
                case .error(let error):
                    if let onError = onError {
                        onError(error)
                    }
                    else {
                        Hooks.defaultErrorHandler(callStack, error)
                    }
                    disposable.dispose()
                case .completed:
                    onCompleted?()
                    disposable.dispose()
                }
            }
            return Disposables.create(
                self.asObservable().subscribe(observer),
                disposable
            )
    }
}
```

## class ObserverBase

1. 并没有Observer类
2. 继承自ObserverType

```swift
//  ObserverType.swift

/// Supports push-style iteration over an observable sequence.
public protocol ObserverType {
    /// The type of elements in sequence that observer can observe.
    associatedtype E

    /// Notify observer about sequence event.
    ///
    /// - parameter event: Event that occurred.
    func on(_ event: Event<E>)
}

/// Convenience API extensions to provide alternate next, error, completed events
extension ObserverType {
    
    /// Convenience method equivalent to `on(.next(element: E))`
    ///
    /// - parameter element: Next element to send to observer(s)
    public func onNext(_ element: E) {
        on(.next(element))
    }
    
    /// Convenience method equivalent to `on(.completed)`
    public func onCompleted() {
        on(.completed)
    }
    
    /// Convenience method equivalent to `on(.error(Swift.Error))`
    /// - parameter error: Swift.Error to send to observer(s)
    public func onError(_ error: Swift.Error) {
        on(.error(error))
    }
}
```

3. **最重要的方法：on，产生event**

## create and subscribe

1. create闭包，实际的执行是

```sequence
Observable->Observable: create
Observable->ObservableType: create(_ subscribe: @escaping (AnyObserver<E>) -> Disposable) -> Observable<E>
Note right of ObservableType: in Create.swift
ObservableType->AnonymousObservable: init(_ subscribeHandler: @escaping SubscribeHandler)
Note right of AnonymousObservable: in Create.swift
Note right of AnonymousObservable: init方法内，用成员变量保存subscribeHandler
```

**整个create流程总结为：** 
- 调用Observable的create函数传入闭包，实际上是生成一个匿名observable: AnonymousObservable，**这个匿名observable，保存传入的subscribeHandler，所以create仅仅是初始化Observable，create闭包内的代码不会执行**

2. Observable调用subscribe后，实际执行：

```sequence
Observable->Observable: subscribe
Observable->ObservableType: subscribe(_ on: @escaping (Event<E>) -> Void)
Note right of ObservableType: in ObservableType+Extensions.swift
ObservableType->AnonymousObserver: init(_ eventHandler: @escaping EventHandler)
Note right of AnonymousObserver: AnonymousObserver.swift
Note right of AnonymousObserver: init方法内，用成员变量保存eventHandler
ObservableType->Observable: asObservable() -> Observable<E>
Note right of Observable: in Observable.swift
Note right of Observable: return self
ObservableType->Producer: subscribe<O : ObserverType>(_ observer: O) -> Disposable where O.E == Element 
Note right of Producer: in Producer.swift
Note right of Producer: 为什么会调用到Producer呢，因为前面分析create的时序图，分析了create的Observable，实际是AnonymousObservable，而AnonymousObservable继承自Producer（ps: Producer继承自Observable）
Producer->CurrentThreadScheduler: schedule<StateType>(_ state: StateType, action: @escaping (StateType) -> Disposable) -> Disposable
Note right of CurrentThreadScheduler: in CurrentThreadScheduler.swift
Note right of CurrentThreadScheduler: schedule方法内执行action(state)
CurrentThreadScheduler->Producer: run(observer, cancel: disposer)
Producer->AnonymousObservable: run<O : ObserverType>(_ observer: O, cancel: Cancelable) -> (sink: Disposable, subscription: Disposable) where O.E == Element
Note right of Producer: in Create.swift
AnonymousObservable->AnonymousObservableSink: init(observer: O, cancel: Cancelable)
AnonymousObservable->AnonymousObservableSink: run(self)
Note right of Producer: in Create.swift
AnonymousObservableSink->Observable: _subscribeHandler(AnyObserver(self))
Note right of AnonymousObservableSink: 这里就是执行create闭包，其中给handler传入的参数是AnyObserver
Note right of AnonymousObservableSink: AnyObserver初始化时，将AnonymousObservableSink的on方法，保留为属性
Note right of AnonymousObservableSink: 执行create闭包，调用observerOfString.on(.next("😉"))
Closure->AnyObserver: on(_ event: Event<Element>)
AnyObserver->AnonymousObservableSink: on(_ event: Event<E>)
AnonymousObservableSink->Sink: forwardOn(_ event: Event<O.E>)
Sink->ObserverBase: on(_ event: Event<E>)
Note right of ObserverBase: AnonymousObserver继承自ObserverBase
ObserverBase->AnonymousObserver: onCore(_ event: Event<Element>)
Note right of AnonymousObserver: onCore函数内执行_eventHandler(event)，也即最开始的subscribe闭包
```

**整个subscribe流程总结为：**
- Observable调用subscribe，生成AnonymousObserver，并subscribe(observer)
- subscribe函数内，通过CurrentThreadScheduler，调用run方法
- run方法生成AnonymousObservableSink，并run(self)
- 然后在AnonymousObservableSink内，执行create闭包
- create闭包发出的event，通过AnyObserver中转，最终，回到AnonymousObserver
- AnonymousObserver执行eventHandler，将event传给subscribe闭包执行
