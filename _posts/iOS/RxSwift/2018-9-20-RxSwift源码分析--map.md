# RxSwift源码分析--map

```swift 
    let disposeBag = DisposeBag()
    Observable.of(1, 2, 3)
        .map { $0 * $0 }
        .subscribe(onNext: { print($0) })
        .disposed(by: disposeBag)
```

## map

```sequence
ObservableType->ObservableType: map
ObservableType->Observable: composeMap
Observable->全局函数: _map(source: self, transform: transform)
全局函数->Map: Map(source: source, transform: transform)
Note right of Map: in Map.swift
Note right of Map: 中间订阅后的流程省略，之后
订阅->Map: run
Map->MapSink: MapSink(transform: _transform, observer: observer, cancel: cancel)
Map->Observable: _source.subscribe(sink)
Note right of Observable: subscribe会调用observer.on发出事件
Note right of Observable: observer.on实际就是sink.on
Observable->MapSink: func on(_ event: Event<SourceType>)
Note right of MapSink: in Map.swift
Note right of MapSink: 调用_transform闭包
Note right of MapSink: forwardOn向下传递event
```