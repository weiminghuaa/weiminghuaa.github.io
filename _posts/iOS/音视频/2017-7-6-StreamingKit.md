# 文件下载

 
-> STKCoreFoundationDataSource ReadStreamCallbackProc 
-> [datasource dataAvailable] 
-> [STKHTTPDataSource parseHttpHeader] 
-> [super dataAvailable] 
-> [self.delegate dataSourceDataAvailable:self] 
-> [STKAutoRecoveringHTTPDataSource dataSourceDataAvailable:dataSource]
-> [super dataSourceDataAvailable:dataSource]
-> [STKDataSourceWrapper dataSourceDataAvailable:dataSource]
-> [self.delegate dataSourceDataAvailable:self] 
-> [STKAudioPlayer dataSourceDataAvailable:dataSource]
-> STKAutoRecoveringHTTPDataSource readIntoBuffer:readBuffer withSize:readBufferSize]
-> [STKDataSourceWrapper readIntoBuffer:readBuffer withSize:readBufferSize]
-> [STKHTTPDataSource readIntoBuffer:readBuffer withSize:readBufferSize]
-> [STKHTTPDataSource privateReadIntoBuffer:readBuffer withSize:readBufferSize]
-> [super readIntoBuffer:buffer withSize:size]
-> [STKCoreFoundationDataSource readIntoBuffer:buffer withSize:size]
-> return (int)CFReadStreamRead(stream, buffer, size);
-> AudioFilestStreamParseBytes...

```
(lldb) po httpHeaders
{
    "Accept-Ranges" = bytes;
    Age = 1;
    Connection = close;
    "Content-Length" = 5271013;
    "Content-Type" = "audio/mpeg";
    Date = "Tue, 16 May 2017 03:20:20 GMT";
    Etag = "\"560a51a3-506de5\"";
    "Last-Modified" = "Tue, 29 Sep 2015 08:53:55 GMT";
    Server = Tengine;
    "X-Via" = "1.1 jinzhong198:2 (Cdn Cache Server V2.0), 1.1 gdjy14:4 (Cdn Cache Server V2.0)";
}

(lldb) po ticksWhenLastDataReceived
207073125

(lldb) po read
1068
```


