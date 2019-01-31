# AVFoundationQueuePlayer-iOS

## 播放

```sequence
AAPLPlayerViewController->AAPLPlayerViewController: asynchronouslyLoadURLAssetsWithManifestURL:
AAPLPlayerViewController->AAPLPlayerViewController: asynchronouslyLoadURLAsset:
Note right of AVURLAsset:  URLAssetWithURL:
AAPLPlayerViewController->AVURLAsset: loadValuesAsynchronouslyForKeys:
Note right of AVURLAsset: ["playable", "hasProtectedContent"]
AVURLAsset->AAPLPlayerViewController: save asset to self.loadedAssets
AAPLPlayerViewController->AAPLPlayerViewController: addItemToQueueButtonPressed:
AAPLPlayerViewController->AVPlayerItem: mc_playerItemWithRemoteURL: selected Assert url
AAPLPlayerViewController->AVURLAsset: loadValuesAsynchronouslyForKeys:
Note right of AVURLAsset: ["playable", "hasProtectedContent"]
AVURLAsset->AAPLPlayerViewController: AVQueuePlayer insert newPlayerItem and auto play
```

```
AVFoundation为我们提供了一个多媒体的载体类：AVAsset

- (void)loadValuesAsynchronouslyForKeys:(NSArray<NSString *> *)keys completionHandler:(nullable void (^)(void))handler NS_AVAILABLE(10_7, 4_2);
由于多媒体文件一般比较大，获取或计算出Asset中的属性非常耗时，apple对Asset的属性采用了懒惰加载模式。在创建AVAsset的时候，只生成一个实例，并不初始化属性。只有当第一次访问属性时，系统才会根据多媒体中的数据初始化这个属性。
由于不用同时加载所有属性，耗时问题得到了一定缓解。但是属性加载在计算量比较大的时候仍旧可能会阻塞线程。为了解决这个问题，AVFoundation提供了AVAsynchronousKeyValueLoading协议，可以异步加载属性
```

## 缓存：AVPlayerItem（MCCacheSupport）

```sequence
AAPLPlayerViewController->AVPlayerItem: mc_playerItemWithRemoteURL: selected Assert url
AVPlayerItem->AVPlayerItem: mc_checkURL:
Note right of AVPlayerItem: URL error
AVPlayerItem->AVPlayerItem: playerItemWithURL:
Note right of AVPlayerItem: URL right
Note right of AVPlayerItem: setup cacheFilePath
AVPlayerItem->MCAVPlayerItemCacheLoader: cacheLoaderWithCacheFilePath:
Note right of AVPlayerItem: create cache file failed
AVPlayerItem->AVPlayerItem: playerItemWithURL:
Note right of AVPlayerItem: create cache file successed
AVPlayerItem->AVURLAsset: URLAssetWithURL:
Note right of AVURLAsset: URL: http-stream://wvideo.spriteapp.cn/video/2016/0328/56f8ec01d9bfe_wpd.mp4
Note right of AVURLAsset: asset.resourceLoader set delegate MCAVPlayerItemCacheLoader
AVPlayerItem->AVPlayerItem: playerItemWithAsset:

```

```
cacheFilePath:
/private/var/mobile/Containers/Data/Application/579026D9-CB48-468E-A268-E81F72B8264F/tmp/AVPlayerMCCache/b740c5a227005b5ef2a546462effe4b1.mp4

AVURLAsset URL:
http-stream://wvideo.spriteapp.cn/video/2016/0328/56f8ec01d9bfe_wpd.mp4
```

### MCAVPlayerItemCacheLoader : NSObject<AVAssetResourceLoaderDelegate>

```
- (instancetype)initWithCacheFilePath:(NSString *)cacheFilePath;

- (BOOL)resourceLoader:(AVAssetResourceLoader *)resourceLoader shouldWaitForLoadingOfRequestedResource:(AVAssetResourceLoadingRequest *)loadingRequest;

Printing description of loadingRequest:
http-stream://wvideo.spriteapp.cn/video/2016/0328/56f8ec01d9bfe_wpd.mp4 
```

```sequence
MCAVPlayerItemCacheLoader->MCAVPlayerItemCacheLoader: resourceLoader:shouldWaitForLoadingOfRequestedResource:
MCAVPlayerItemCacheLoader->MCAVPlayerItemCacheLoader: startNextRequest
MCAVPlayerItemCacheLoader->MCAVPlayerItemCacheLoader: startCurrentRequest
MCAVPlayerItemCacheLoader->MCAVPlayerItemCacheLoader: addTaskWithRange:cached:
Note right of MCAVPlayerItemCacheLoader: cached yes
MCAVPlayerItemCacheLoader->MCAVPlayerItemLocalCacheTask: initWithCacheFile:loadingRequest:range:
Note right of MCAVPlayerItemCacheLoader: cached no
MCAVPlayerItemCacheLoader->MCAVPlayerItemRemoteCacheTask: initWithCacheFile:loadingRequest:range: 
```


### MCAVPlayerItemCacheFile

```
- (instancetype)initWithFilePath:(NSString *)filePath;

filePath:
/private/var/mobile/Containers/Data/Application/579026D9-CB48-468E-A268-E81F72B8264F/tmp/AVPlayerMCCache/b740c5a227005b5ef2a546462effe4b1.mp4

indexFilePath:
/private/var/mobile/Containers/Data/Application/579026D9-CB48-468E-A268-E81F72B8264F/tmp/AVPlayerMCCache/b740c5a227005b5ef2a546462effe4b1.mp4.idx!

```

```
第一次init:
BOOL fileExist = cacheFileExist && indexFileExist; // NO
创建directory、file、indexFile

第二次init:
indexFile的内容
Printing description of indexDic:
{
    responseHeaders =     {
        "Cache-Control" = "max-age=315360000";
        Connection = "keep-alive";
        "Content-Length" = 2;
        "Content-Range" = "bytes 0-1/12959651";
        "Content-Type" = "video/mp4";
        Date = "Fri, 02 Jun 2017 11:06:34 GMT";
        Expires = "Mon, 31 May 2027 11:06:34 GMT";
        "Last-Modified" = "Wed, 13 Jul 2016 02:08:47 GMT";
        Server = "NWS_VCLOUD_ACCESS";
        "X-Cache-Lookup" = "Hit From Disktank3, Hit From Inner Cluster";
        "X-Daa-Tunnel" = "hop_count=1";
    };
    size = 12959651;
    zone =     (
        "{0, 12959651}"
    );
}
```

