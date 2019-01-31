# Rx playground

这个playground写的太好了，有playground格式的注释，类似markdown语法，可参考.

## content

1. Introduction
1. Creating and Subscribing to Observables
1. Working with Subjects
1. Combining Operators
1. Transforming Operators
1. Filtering and Conditional Operators
1. Mathematical and Aggregate Operators
1. Connectable Operators
1. Error Handling Operators
1. Debugging Operators

## 随便记

1. repeat take, 最后是会有completed的

```swift
example("repeatElement") {
    let disposeBag = DisposeBag()
    
    Observable.repeatElement("🔴")
        .take(3)
        .subscribe(onNext: { print($0) })
//        .subscribe { print($0) }
        .disposed(by: disposeBag)
}
```

2. sucribe是顺序执行的，之前官方文档的getting start中的交错并行，是因为哪个obersvable是有计时器的。

```swift
let thesequence = Observable<String>.create { observer in
            print("Emitting...")
            observer.onNext("🐶")
            observer.onNext("🐱")
            observer.onNext("🐵")
            return Disposables.create()
    }
    
    thesequence
        .subscribe(onNext: { print($0) })
        .disposed(by: disposeBag)
    
    thesequence
        .subscribe(onNext: { print($0) })
        .disposed(by: disposeBag)
```

```
print:
Emitting...
🐶
🐱
🐵
Emitting...
🐶
🐱
🐵
```

3. 顺便学了一下Swift.Error的用法，看Xcode自带的文档。所以，就应该搞清楚每一个遇到的问题，就能成为大神。

4. Subjects

比较特殊哦，看demo

5. Combination Operators

6. ransforming Operators

7. Filtering and Conditional Operators

8. Mathematical and Aggregate Operators

9. Connectable Operators

10. Error Handling Operators

11. Debugging Operators

