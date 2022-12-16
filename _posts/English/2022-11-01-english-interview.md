---
layout: post
title: 英文面试
date: 2022-11-01 19:30:35.000000000 +08:00
tags: English
---

## Self Introduction

Hello, My name is weiminghua. I am 35 years old | and I'm born in jiangxi province. 

I graduated from | Jiangxi Science and Technology Normal University | in 2008, and then I came to guangzhou. up till now, I've been working for 14 years. at first I worked on java development |  about three years. 

and in 2011, I have a opportunity | to switch to android development | in Xuanwu teleology corporation, delevope android apps for business customers. 

and then went to wanlong Securities Consulting/kənˈsʌlt/ corporation. I joined an stock app project team, and responsible for stock charts components. during this work period, I learnt ios, and bacame to an ios developer later, so from 2014, I work on ios about eight year. 

I went to sunflower insurance company in 2015 ｜ and joined a mobile project  ｜ which develop online insurance mall. I worked hard and later ｜ I became the ios leader, I made the team to use swift language ｜ instead of OC. 

and four years later, I went to 悦跑圈，it's a famous app in runners, at the begining I am ios developer, and in 2020, the company decide to develop another app ｜ and I was invited to joined this exciting project, it used flutter to develop, even though this projects didn't success, I have the opportunity to partise the hot Cross-platform framework. 

then I joined vip.com in 2021, there was a new oversea project, and I was the app leader, it's a nice experience for work in a big company. but the project was closed later, 

so, I went to china softy, and assigned to HSBC, worked on GPB department, I responsible for mauth framework, which is a mobile X basic framework, it used for logon and integated by many apps. except that, I did other two thing, first, ananylist private bank app lauch time. second, implement running the ios app on macos.

besides work, I learnt a lot of English durning this time, but, as the buget limited, the pod team is closed, however, My HSBC leader give me high rating ｜ for my good profermance. 

this is all my work experience.

That’s all. Thank you for giving me the chance.

## HSBC

https://hsbc.zoom.us/j/99395504363?pwd=U0pYOHlBaWhXQ0xsRjlJamVnWHBIZz09

1. The difference between Struct and class

The difference between Struct and class is, struct is a value type and Class is a reference type.

Struct instance is allocated(/'æləkeitid/) in stack space, while class instance is allocated in heap space, so struct has high efficient(/ɪˈfɪʃ(ə)nt/), and more recommended(/ˌrekəˈmendɪd/) to use.

but Classe have additional capabilities(/ˌkeɪpəˈbɪlətiz/) that struct don’t have, we can create a class by inherit(/ɪnˈherɪt/) another class, but stuct can't do this.

so we need to choose according to the situation.

2. Application and examples of Protocol

protocols are a fundamental Swift feature. it's a useful thing like interface in other languages. we use protocals to implenment Interface Segregation(/ˌseɡrɪˈɡeɪʃn/) Principle, there are a lot of protocals in uikit and other swift standard library, like uitableviewdatasourse, uitbleviewdelelate, and so on. 

and in swift. we can add an default function implementations by add protocol extensions

and except that, we often use protocol for a different programming paradigm(/ˈpærədaɪm/), Protocol Oriented(/ˈɔːrientɪd/) Programming. POP is a powerful paradigm to implement code decoupling(/diːˈkʌp(ə)lɪŋ/解藕), it's widely used in swift. for example, shakeable button

3. How to build a network library without Library

I think I will create three component: 

first: a request for assemble request，with properties like httpmethods, parameters(/pəˈræmɪtə(r)/), requesthead, request boys, and so on.

second: a response for parse received data, except for those basic http response parameters, like status code, response header, we can create a lot of parsers, to parse data to string, json, xml,images, etc.

third: a client for send request and receive response, with urlsession in it, to implemt http transmit.

I thinks these are the basic design, and I know there are many other things need to do, like implement signature(/ˈsɪɡnətʃə(r)/) for request, certification hanlder. so it always need lots of time to build, and we need take care, because a network library is most important component in app. we need make it security.

4. How to convert the data returned by the background into a model, and which APIs to use

at first, we can use JSON Serialization to convert data to json.

after that, we can let model class implement Codable protocol, and most of things will be done prefectly. if some properties is not suitable with json data, there is function to do the special property coverting by youself.

(if used old version of swift, without Codable protocol, we need write code by ourselver to convert a json to model, there are lots of open source library to do this.)

5. What should I do if the app gets stuck after launching?

an app launch consists of 2 parts: pre main  and after main, so at first, we need use instrument to analysist each part of time.

in pre main, there are dylib loading, rebase/binding, initializer, and some other process, we can improve performance by decrease frameworks and classes account, use initialize instead of load in class, and other method to do this.

and in after main, we need check if there are some blocks in appdeleate launch function, and viewdidload, viewwillappeard function in first screen view controller. 

in gpb app, I use instrument to analysist each part of time. include network time. and I used system os log to add event and caluate the time in detail. after analysis, we found out two parts block launch, fisrt, we have two serial(/ˈsɪəriəl/) task to read infomations from disk, so we change it to concurrent to execute at the same time. second, there is a request before first screen show, and it can't delay, we ask BE support to reduce http response time.

6. How to deal with memory leaks

at first, we can use instrument, or xocde memory graph(/ɡrɑːf/), to find out memory leaks and get detail infomantions. 

and As my experiance, memory leads usually caused by circle reference, we should break the cicle by make one of them weak, for example, we need use weak self in block, we need use weak delegate in viewcontroller when set uitaleviewdatasource.

7. How to prevent the app from being hacked and ensure security

this involve widely component, I want give some point, like, we usually should check if the customers device is root, we shoud signature(/ˈsɪɡnətʃə(r)/) http request to avoid someone else fake it, and we shoud add salt in some transmit, we should take care of store data in keychain, I have just handle a defect about it.

8. Which layout do you like to use: code or xib, why

I like xib, because what you see is what you get, everyone read it without running, it's quickly for everyone to know the view implemention.

but it can't inherit(/ɪnˈherɪt/). so sometimes I need to use code. and sometime it will cause conflict when more than one person work on it. but, if it's possible I choose xib first all the time.

9. What is TDD and how to do it

Test-driven development (TDD) is an iterative(/ˈɪtərətɪv/迭代的；重复的，反复的) methodology(/ˌmeθəˈdɒlədʒi/方法论), it entails(/ɪnˈteɪl/使必要，需要) the conversion of each component of the application | into a test case, before it is built, and then testing and tracking the component repeatedly. it asked developer always write unit test before implement future, this make the project reach a high code coverage, and make code quality under guarantee.

10. What are the advantages of using Swift over Objective-c

swift is faster than OC.

and Swift's simple and direct syntax(/ˈsɪntæks/) makes it easy to read and write, it's a morden language.

and Swift is safe and less prone(/prəʊn/ 有做……倾向的，易于……的) to error. It has a compiler feature that makes code writing safer and prevents a series of runtime crashes in your apps.

and options, structs, POP, guard, and a lots of things, OC haven't got.

11. The use of key chain,

the api is simple, we can use add a secitem to store data in keychain, and seach it or delete it. but it need be careful, because set see righat access of the data in keychain, there are lots of access control provided, expecially for the bank app, we need set the access control to only when useunlock and have set password, and use present, I have just handle a defect about it.

12. What do you think is the best way to write Unit test

I thinks the beat way to write unit test, is write high quality code. I always think that unit test can keep code more beautiful and strong, because bad code can't even write a unit test to test it, may be there are lots of dependence, may be the architecture is too bad. so I really like TDD.

13. Have you heard of promisekit/Rx swift, react Swift

I have, and I have used Rxswift in project before, I like MVVM, it's hard to use but it make code clean. I think the most important of RxSwift is two way binding, it‘s implemented by a pattern called observer and subscribe, this way make code decoupling(/diːˈkʌp(ə)lɪŋ/解藕)， 

14. what questions do you need to ask them

I want to know the composition of the team and the way of working. and If I pass the interview and join the team, what role will I be

## QA-work

### Difference between a struct and a class in Swift?

struct is a value type and Class is a reference type.

Struct instance are allocated in stack space, while class instance are allocated in heap space, so struct has high efficient, and more recommended to use.

but Classes have additional capabilities that structures don’t have, Inheritance enables one class to inherit the characteristics of another.

so we need to choose according to the situation.

### Access Control

Open access and public access enable entities to be used within any source file from their defining module, and also in a source file from another module that imports the defining module. You typically use open or public access when specifying the public interface to a framework. The difference between open and public access is described below.

Internal access enables entities to be used within any source file from their defining module, but not in any source file outside of that module. You typically use internal access when defining an app’s or a framework’s internal structure. (this is default access control)

File-private access restricts the use of an entity to its own defining source file. Use file-private access to hide the implementation details of a specific piece of functionality when those details are used within an entire file.

Private access restricts the use of an entity to the enclosing declaration, and to extensions of that declaration that are in the same file. Use private access to hide the implementation details of a specific piece of functionality when those details are used only within a single declaration.

Open access is the highest (least restrictive) access level and private access is the lowest (most restrictive) access level.

Open access applies only to classes and class members, and it differs from public access by allowing code outside the module to subclass and override, as discussed below in Subclassing. Marking a class as open explicitly indicates that you’ve considered the impact of code from other modules using that class as a superclass, and that you’ve designed your class’s code accordingly.

### autoclosure

An autoclosure is a closure that’s automatically created to wrap an expression that’s being passed as an argument to a function. 

An autoclosure lets you delay evaluation, because the code inside isn’t run until you call the closure. Delaying evaluation is useful for code that has side effects or is computationally expensive, because it lets you control when that code is evaluated. The code below shows how a closure delays evaluation.

the system implemention often use autoclosures, like asset function and options, this autoclosure will not run unless the front condition checked and need autoclosure to execute.

### Enum 

#### Associated Values vs Raw Values

Associated Values it’s sometimes useful to be able to store values of other types alongside these case values. This additional information is called an associated value, and it varies each time you use that case as a value in your code.

```
enum Barcode {
    case upc(Int, Int, Int, Int)
    case qrCode(String)
}
```

Raw Values

```
enum ASCIIControlCharacter: Character {
    case tab = "\t"
    case lineFeed = "\n"
    case carriageReturn = "\r"
}
```

it's different between Raw Values and Associated Values, Associated Values is a property stored in the enum case when you init it, but Raw Values isn't like that, Raw Values like a Computed Properties, when we use MemoryLayout to see the enum size and stride, we can find out that Raw Values don't take enum inner memory.

#### Recursive Enumerations

A recursive enumeration is an enumeration that has another instance of the enumeration as the associated value for one or more of the enumeration cases. You indicate that an enumeration case is recursive by writing indirect before it, which tells the compiler to insert the necessary layer of indirection.

```
enum ArithmeticExpression {
    case number(Int)
    indirect case addition(ArithmeticExpression, ArithmeticExpression)
    indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

### Subscripts

Classes, structures, and enumerations can define subscripts, You use subscripts to set and retrieve values by index without needing separate methods for setting and retrieval. it let you use index just like an array.

### mutating

Structures and enumerations are value types. By default, the properties of a value type can’t be modified from within its instance methods. but you can use mutating to declare the methos to implement it. 

### Protocol Composition

You can combine multiple protocols into a single requirement with a protocol composition. Protocol compositions behave as if you defined a temporary local protocol that has the combined requirements of all protocols in the composition. Protocol compositions don’t define any new protocol types.

func testComposition(protocols: ProtocolA & ProtocolB) { }

### Variadic Parameters

A variadic parameter accepts zero or more values of a specified type. You use a variadic parameter to specify that the parameter can be passed a varying number of input values when the function is called.

### protocol optional

There are two ways to achieve this effect. 
1. Add @objc to define the protocol, so that optional can be used to define optional methods. 
2. Add extension to the protocol and implement the method, so that the implementation of the protocol can choose not to implement the corresponding method

### Lazy Stored Properties

A lazy stored property is a property whose initial value isn’t calculated until the first time it’s used. You indicate a lazy stored property by writing the lazy modifier before its declaration.

### Delegate weak

because Swift's protocol can be implement by class, and struct or enum, but struct and enum is value type, they do not manage memory through reference counting, so it is impossible to use the concept of ARC such as weak to modify.
So to use weak delegate in Swift, we need to restrict the protocol to the class type

### @synchronized in swift

### swift atomic

### 字面量

### other questions

https://www.interviewbit.com/ios-interview-questions/

### framework lib



## QA-life

