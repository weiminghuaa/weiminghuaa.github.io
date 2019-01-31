# HLSCatalog

Apple sample code, Using AVFoundation to play and persist HTTP Live Streams. 

* Saving HLS streams for offline playback is only supported for VOD streams.  If you try to save a live HLS stream, the system will throw an exception.
* This sample does not support saving FairPlay Streaming (FPS) content.  For a version of the sample that demonstrates how to download FPS content, see the sample code available in the FairPlay Streaming Server SDK at https://developer.apple.com/streaming/fps/.

> Task List

- [√] 了解代码设计，系统API的使用
- [x] 只支持VOD的离线播放，不知道具体是什么意思？
- [x] 不v支持FPS，有下载FPS的demo，到时比较看看

## Main Files

__AssetPersistenveManager.swift__: 

- `AssetPersistenceManager` is the main class in this sample that demonstrates how to manage downloading HLS streams.  It includes APIs for starting and canceling downloads, deleting existing assets off the users device, and monitoring the download progress.

- 管理下载的，好好看看设计，下载相关的类是`AVAggregateAssetDownloadTask`

__AssetPlaybackManager.swift__:

- `AssetPlaybackManager` is the class that manages the playback of Assets in this sample using Key-value observing on various AVFoundation classes.

- 管理播放的，看看怎么设计的

__AssetListManager.swift__:

- The `AssetListManager` class is responsible for providing a list of assets to present in the `AssetListTableViewController`.

__StreamListManager.swift__:

- The `StreamListManager` class manages loading reading the contents of the `Streams.plist` file in the application bundle.

## 代码分析

> plist数据模型化

StreamListManager将plist的数据模型化，比较简单

> 播放

1. 使用AVPlayerViewController，给controller的player属性赋值就能播放

```
open class AVPlayerViewController : UIViewController {

    /*!
    	@property	player
    	@abstract	The player from which to source the media content for the view controller.
     */
    open var player: AVPlayer?
}
```

2. 由AssetPlaybackManager类的单例对象管理player，AVPlayerViewController找AssetPlaybackManager要player

3. 
