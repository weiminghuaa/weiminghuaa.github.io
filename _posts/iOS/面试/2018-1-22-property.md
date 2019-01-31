---
layout: post
title: Property
date: 2018-12-29 15:30:35.000000000 +08:00
tags: ios
---

尽量多的使用属性（property）而不是实例变量（attribute）因为属性（property）相比实例变量有很多的好处:

1. 自动合成getter和setter方法。当声明一个属性(property)的时候编译器默认情况下会自动生成相关的getter和setter方法
2. 更好的声明一组方法。因为访问方法的命名约定，可以很清晰的看出getter和setter的用处。
3. 属性(property)关键词能够传递出相关行为的额外信息。属性提供了一些可能会使用的特性来进行声明，包括assign(vscopy),weak,strong,atomic(vsnonatomic),readwrite,readonly等。

布尔类型的属性还可以定义一个以is开头的getter方法，如：
`@property (readonly, getter=isBlue) BOOL blue;`
如果按照上面的方法声明则以下所有访问方式都正确:
```
if (color.blue) {}
if (color.isBlue) {}
if ([color isBlue]) {}
```

## @property指示符
在声明属性的时候一般会带上几个指示符，常用指示符有

atomic nonatomic
readwrite readonly
assign
strong
weak
copy
unsafe_unretained
retain

### atomic/nonatomic

atomic：原子性，这个属性是默认的，系统生成的 getter/setter 会保证 get、set 操作的完整性，不受其他线程影响，通过在getter/setter中加@synchronized(self)保证数据数据的读写安全。

需要注意的是，但它不是线程安全的，如果线程 A 调了 getter，与此同时线程 B 、线程 C 都调了 setter——那最后线程 A get 到的值，3种都有可能：可能是 B、C set 之前原始的值，也可能是 B set 的值，也可能是 C set 的值。同时，最终这个属性的值，可能是 B set 的值，也有可能是 C set 的值，所有这些不同线程上的操作都将依次顺序执行——也就是说，如果一个线程正在执行 getter/setter，其他线程就得等待。因此，属性 name 是读/写安全的。但是，如果有另一个线程 D 同时在调[name release]，那可能就会crash，因为 release 不受 getter/setter 操作的限制。也就是说，这个属性只能说是读/写安全的，但并不是线程安全的，因为别的线程还能进行读写之外的其他操作。线程安全需要开发者自己来保证。https://www.jianshu.com/p/7288eacbb1a2


指定合成存取方法是否为原子操作，可以理解为是否线程安全，但在iOS上即时使用atomic也不一定是线程安全的，要保证线程安全需要使用锁机制，超过本文的讲解范围，可以自行查阅。
可以发现几乎所有代码的属性设置都会使用nonatomic，这样能够提高访问性能，在iOS中使用锁机制的开销较大，会损耗性能。

### readwrite/readonly

readwrite是编译器的默认选项，表示自动生成getter和setter，如果需要getter和setter不写即可。

readonly表示只合成getter而不合成setter。

### assign、weak、unsafe_unretained

assign表示对属性只进行简单的赋值操作，不更改所赋的新值的引用计数，也不改变旧值的引用计数，常用于标量类型，如NSInteger，NSUInteger，CGFloat，NSTimeInterval等。

**assign也可以修饰对象如NSString等类型对象，上面说过使用assign修饰不会更改所赋的新值的引用计数，也不改变旧值的引用计数，如果当所赋的新值引用计数为0对象被销毁时属性并不知道，编译器不会将该属性置为nil，指针仍旧指向之前被销毁的内存，这时访问该属性会产生野指针错误并崩溃，因此使用assign修饰的类型一定要为标量类型。**

使用weak修饰的时候同样不会增加所赋的新值的引用计数，也不减少旧值的引用计数，但当该值被销毁时，weak修饰的属性会被自动赋值为nil，这样就可以避免野指针错误。

使用unsafe_unretained修饰时效果与assign相同，不会增加引用计数，当所赋的值被销毁时不会被置为nil可能会发生野指针（BAD_ACCESS）错误。unsafe_unretained与assign的区别在于，unsafe_unretained只能修饰对象，不能修饰标量类型，而assign两者均可修饰。

### strong、weak

strong表示属性对所赋的值持有强引用表示一种“拥有关系”(owning relationship)，会先保留新值即增加新值的引用计数，然后再释放旧值即减少旧值的引用计数。只能修饰对象。如果对一些对象需要保持强引用则使用strong。

weak表示对所赋的值对象持有弱引用表示一种“非拥有关系”(nonowning relationship)，对新值不会增加引用计数，也不会减少旧值的引用计数。所赋的值在引用计数为0被销毁后，weak修饰的属性会被自动置为nil能够有效防止野指针错误。
weak常用在修饰delegate等防止循环引用的场景。

copy
copy修饰的属性会在内存里拷贝一份对象，两个指针指向不同的内存地址。
一般用来修饰有对应可变类型子类的对象。
如：NSString/NSMutableString,NSArray/NSMutableArray,NSDictionary/NSMutableDictionary等。
为确保这些不可变对象因为可变子类对象影响，需要copy一份备份，如果不使用copy修饰，使用strong或assign等修饰则会因为多态导致属性值被修改。

**copy还被用来修饰block，在ARC环境下编译器默认会用copy修饰， 一般情况下在block需要捕获外界数据时该block就会被分配在堆区，但在MRC环境下由于手动管理引用计数，block一般被分配在栈区，需要copy到堆区来防止野指针错误。由于牵扯block相关知识，有兴趣可以看博客另一篇文章iOS block探究(二): 深入理解**

**对于可变对象类型，如NSMutableString、NSMutableArray等则不可以使用copy修饰，因为Foundation框架提供的这些类都实现了NSCopying协议，使用copy方法返回的都是不可变对象，如果使用copy修饰符在对可变对象赋值时则会获取一个不可变对象，接下来如果对这个对象进行可变对象的操作则会产生异常，因为OC没有提供mutableCopy修饰符，对于可变对象使用strong修饰符即可。**

### unsafe_unretained
使用unsafe_unretained修饰时效果与assign相同，不会增加新值的引用计数，也不会减少旧值的引用计数(unretained)当所赋的值被销毁时不会被置为nil可能会发生野指针错误(unsafe)。unsafe_unretained与assign的区别在于，unsafe_unretained只能修饰对象，不能修饰标量类型，而assign两者均可修饰。

### retain
在ARC环境下使用较少，在MRC下使用效果与strong一致。

## @property深入代码理解


**@property = ivar + getter + setter**

ivar就是实例变量，编译器会帮我们自动生成名字为'_属性名'这样的实例变量，同时也会自动生成getter和setter方法。

[@property深入代码理解](https://www.jianshu.com/p/44d12884e24e)
copy在底层实现上，与assign、unsafe_unretained、weak、Strong可以看出区别，以string为例，copy是通过objc_setProperty将值重新设置到对象，而strong等是直接使用等号赋值