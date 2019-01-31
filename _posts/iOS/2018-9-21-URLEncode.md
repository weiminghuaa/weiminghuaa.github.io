# URLEncode

魏名华  12:46:34
1: https://p.bxr.im/prod/products/collect?company_id=901&company_name=中国人寿

2: https://p.bxr.im/prod/products/collect?company_id=901&company_name=%E4%B8%AD%E5%9B%BD%E4%BA%BA%E5%AF%BF

3: https://p.bxr.im/prod/products/collect?company_id=901&company_name=%25E4%25B8%25AD%25E5%259B%25BD%25E4%25BA%25BA%25E5%25AF%25BF


```
NSString * encodedString = [string stringByAddingPercentEncodingWithAllowedCharacters:[NSCharacterSet URLQueryAllowedCharacterSet]];
```

哪位熟悉浏览器的url encode，iOS遇到的问题是，会把1解析成2，是对的。但是把2解为3，出错

魏名华  13:23:25

js的encodeURI函数也一样解不对

魏名华  13:24:08
@UED-于洁莹  js也接错？

魏名华  14:12:08

而浏览器无论怎么都是对的

魏名华  14:44:08

先decode再encode


## 查查AFNetworking

http://www.cnblogs.com/Mike-zh/p/5152073.html