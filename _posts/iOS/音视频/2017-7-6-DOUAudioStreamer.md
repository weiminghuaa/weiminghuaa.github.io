# 播放流程

## UI -> DOUAudioStreamer
```sequence
ViewController->ViewController: viewWillAppear
ViewController->ViewController: resetStreamer
ViewController->ViewController: 清空流cancelStreamer
Note right of ViewController:  <DOUAudioFile>)audioFile
ViewController->DOUAudioStreamer: 初始化AudioStreamer
DOUAudioStreamer->DOUAudioFileProvider: 初始化FileProvider:_audioFile
DOUAudioFileProvider->DOUAudioStreamer: 计算缓存bufferingRatio
ViewController->DOUAudioStreamer: play
ViewController->ViewController: 更新缓存updateBufferingStatus
ViewController->ViewController: setupHintForStreamer
ViewController->DOUAudioStreamer: 设置下一曲setHintWithAudioFile
DOUAudioStreamer->DOUAudioFileProvider: setHintWithAudioFile
```

## DOUAudioStreamer内部实现
```sequence
DOUAudioStreamer->DOUAudioStreamer: play
DOUAudioStreamer->DOUAudioEventLoop: setCurrentStreamer
Note right of DOUAudioEventLoop:  单例
Note right of DOUAudioEventLoop:  初始化持有DOUAudioRenderer
DOUAudioStreamer->DOUAudioEventLoop: play
DOUAudioEventLoop->DOUAudioEventLoop: _sendEvent:event_play
DOUAudioEventLoop->DOUAudioEventLoop: _handleEvent:withStreamer:
DOUAudioEventLoop->DOUAudioRenderer: setInterrupted:NO
Note right of DOUAudioEventLoop:  播放，暂停等都通过_renderer实现
```

## 音频文件种类
```sequence
DOUAudioStreamer->DOUAudioFileProvider: fileProviderWithAudioFile
Note right of DOUAudioFileProvider:  网络音频
DOUAudioFileProvider->_DOUAudioRemoteFileProvider: _initWithAudioFile
Note right of DOUAudioFileProvider:  本地文件
DOUAudioFileProvider->_DOUAudioLocalFileProvider: _initWithAudioFile
Note right of DOUAudioFileProvider:  ipod-library
DOUAudioFileProvider->_DOUAudioMediaLibraryFileProvider: _initWithAudioFile
```

## 文件流下载
```sequence
_DOUAudioRemoteFileProvider->_DOUAudioRemoteFileProvider: _initWithAudioFile
_DOUAudioRemoteFileProvider->_DOUAudioRemoteFileProvider: _openAudioFileStream
_DOUAudioRemoteFileProvider->_DOUAudioRemoteFileProvider: _openAudioFileStreamWithFileTypeHint
Note right of _DOUAudioRemoteFileProvider: AudioFileStreamOpen
_DOUAudioRemoteFileProvider->_DOUAudioRemoteFileProvider: _createRequest
_DOUAudioRemoteFileProvider->DOUSimpleHTTPRequest: requestWithURL
Note right of DOUSimpleHTTPRequest: 请求通过Block回调给provider
DOUSimpleHTTPRequest->_DOUAudioRemoteFileProvider: _requestDidReceiveData
Note right of _DOUAudioRemoteFileProvider: AudioFileStreamParseBytes
_DOUAudioRemoteFileProvider->_DOUAudioRemoteFileProvider: _openAudioFileStreamWithFileTypeHint
Note right of _DOUAudioRemoteFileProvider: AudioFileStreamOpen
```
AudioFileStreamOpen: Creates and opens a new audio file stream parser.

## 播放
```sequence
DOUAudioEventLoop->DOUAudioEventLoop: _eventLoop
DOUAudioEventLoop->DOUAudioEventLoop: _handleStreamer
DOUAudioStreamer->DOUAudioDecoder: decodeOnce
DOUAudioDecoder->DOUAudioLPCM: writeBytes
DOUAudioDecoder->DOUAudioLPCM: readBytes
DOUAudioEventLoop->DOUAudioRenderer: renderBytes
Note right of DOUAudioRenderer: AudioOutputUnitStart
DOUAudioRenderer->DOUAudioRenderer: initWithBufferTime
DOUAudioRenderer->DOUAudioRenderer: _setupPropertyListenerForDefaultOutputDevice
Note right of DOUAudioRenderer: AudioObjectAddPropertyListener
DOUAudioRenderer->DOUAudioRenderer: property_listener_default_output_device
DOUAudioRenderer->DOUAudioRenderer: _handlePropertyListenerForDefaultOutputDevice
Note right of DOUAudioRenderer: AudioOutputUnitStart
```

## DOUAudioFileProvider

seek爆音，跟其他没关系，重要的是DOUAudioDecoder的decodeOnce方法里面，decodingContext.outputPos是正确的，但是_decodingContext.outputBuffer到底是怎么回事呢？

## DOUAudioFileProvider

### 文件缓存

```
+ (NSString *)_cachedPathForAudioFileURL:(NSURL *)audioFileURL
{
  NSString *filename = [NSString stringWithFormat:@"douas-%@.tmp", [self _sha256ForAudioFileURL:audioFileURL]];
  return [NSTemporaryDirectory() stringByAppendingPathComponent:filename];
}
```

## DOUAudioStreamer

```
初始化，接收音频文件audioFile <DOUAudioFile>
+ (instancetype)streamerWithAudioFile:(id <DOUAudioFile>)audioFile
{
  return [[[self class] alloc] initWithAudioFile:audioFile];
}

- (instancetype)initWithAudioFile:(id <DOUAudioFile>)audioFile
{
  self = [super init];
  if (self) {
    _audioFile = audioFile;
    _status = DOUAudioStreamerIdle;

    _fileProvider = [DOUAudioFileProvider fileProviderWithAudioFile:_audioFile]; // 初始化_fileProvider
    if (_fileProvider == nil) {
      return nil;
    }

    _bufferingRatio = (double)[_fileProvider receivedLength] / [_fileProvider expectedLength];
  }

  return self;
}
```


