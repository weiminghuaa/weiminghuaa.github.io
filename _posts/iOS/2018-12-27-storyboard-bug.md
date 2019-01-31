---
layout: post
title: storyboard bug
date: 2018-10-02 15:30:35.000000000 +08:00
tags: ios
---

一个奇怪的bug：

1. storyboard有1个ViewController，内含tableview，使用prototype cells，其中一个cell是OC写的XRKOCTableViewCell，一个是swift写的XRKSwiftTableViewCell

2. 在ViewController中，tableView.dequeueReusableCell(withIdentifier: "XRKSwiftTableViewCell")能正常得到XRKSwiftTableViewCell的实例，而ableView.dequeueReusableCell(withIdentifier: "XRKOCTableViewCell")只能得到UITableViewCell的实例，导致无法使用XRKOCTableViewCell，console:

(lldb) po tableView.dequeueReusableCell(withIdentifier: "XRKSwiftTableViewCell")
▿ Optional<UITableViewCell>
  - some : <IMBXR.XRKSwiftTableViewCell: 0x7fa12820ce00; baseClass = UITableViewCell; frame = (0 0; 375 44); clipsToBounds = YES; layer = <CALayer: 0x600001c11520>>

(lldb) po tableView.dequeueReusableCell(withIdentifier: "XRKOCTableViewCell")
2018-12-27 11:51:26.043811+0800 IMBXR[12714:91248] Unknown class _TtC5IMBXR18XRKOCTableViewCell in Interface Builder file.
▿ Optional<UITableViewCell>
  - some : <UITableViewCell: 0x7fa127950c00; frame = (0 0; 375 44); clipsToBounds = YES; layer = <CALayer: 0x600001c16420>>


## 解决

暂时不在storyboard使用oc的cell，并在ViewController主动注册oc的cell来使用