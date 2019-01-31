# RxSwift源码分析--Subject

```swift 
extension ObservableType {
    
    /**
     Add observer with `id` and print each emitted event.
     - parameter id: an identifier for the subscription.
     */
    func addObserver(_ id: String) -> Disposable {
        return subscribe { print("Subscription:", id, "Event:", $0) }
    }
    
}

let disposeBag = DisposeBag()
let subject = PublishSubject<String>()

subject.addObserver("1").disposed(by: disposeBag)
subject.onNext("🐶")
subject.onNext("🐱")

subject.addObserver("2").disposed(by: disposeBag)
subject.onNext("🅰️")
subject.onNext("🅱️")

/* print
Subscription: 1 Event: next(🐶)
Subscription: 1 Event: next(🐱)
Subscription: 1 Event: next(🅰️)
Subscription: 2 Event: next(🅰️)
Subscription: 1 Event: next(🅱️)
Subscription: 2 Event: next(🅱️)
*/
```

## Subject

1. Subject: Observables are a fundamental part of RxSwift, but a common need when developing apps is to **manually add new values** onto an observable at runtime that will then be emitted to subscribers.What you want is something that can act as both an observable and as an observer. And that something is called a Subject. 

实际开发上，Subject必不可少，比如列表数据的observable，需要再请求后，手动add new values，发给UI。

Observable的create或just等，是通过匿名AnonymousObserver来发出事件，而Subject自己就可以

```swift
// observerOfString是一个AnonymousObserver
Observable<String>.create { observerOfString in  
            observerOfString.on(.next("😉"))
            observerOfString.on(.completed)
            return Disposables.create()
        }

// PublishSubject自己就可以发出事件
subject.onNext("🐶")
```

2. can act as both an observable and as an observer，SubjectType表明即是Observable又是Observer，继承自ObservableType，但又有asObserver方法。这么做是为什么呢：

- 需要给外部subscribe，所以必须是Observable

- 内部需要能手动add new values，这个又只能Observer做


```swift
//  PublishSubject.swift

/// Represents an object that is both an observable sequence as well as an observer.
///
/// Each notification is broadcasted to all subscribed observers.
public final class PublishSubject<Element>
    : Observable<Element>
    , SubjectType
    , Cancelable
    , ObserverType
    , SynchronizedUnsubscribeType {

}
```
```swift
//  SubjectType.swift

public protocol SubjectType : ObservableType {
    /// The type of the observer that represents this subject.
    ///
    /// Usually this type is type of subject itself, but it doesn't have to be.
    associatedtype SubjectObserverType : ObserverType

    /// Returns observer interface for subject.
    ///
    /// - returns: Observer interface for subject.
    func asObserver() -> SubjectObserverType
    
}
```

### PublishSubject

PublishSubject：Each notification is broadcasted to all subscribed observers. 有一个_observers属性记录observer，事件会发出到各个observer

```swift

private var _observers = Observers()

/// Notifies all subscribed observers about next event.
///
/// - parameter event: Event to send to the observers.
public func on(_ event: Event<Element>) {
    #if DEBUG
        _synchronizationTracker.register(synchronizationErrorMessage: .default)
        defer { _synchronizationTracker.unregister() }
    #endif
    dispatch(_synchronized_on(event), event)
}

/**
Subscribes an observer to the subject.

- parameter observer: Observer to subscribe to the subject.
- returns: Disposable object that can be used to unsubscribe the observer from the subject.
*/
public override func subscribe<O : ObserverType>(_ observer: O) -> Disposable where O.E == Element {
    _lock.lock()
    let subscription = _synchronized_subscribe(observer)
    _lock.unlock()
    return subscription
}
```
