# AFNetworking 底层原理分析

AFNetworking主要是对NSURLSession和NSURLConnection(iOS9.0废弃)的封装,其中主要有以下类:

1). AFHTTPRequestOperationManager：内部封装的是 NSURLConnection, 负责发送网络请求, 使用最多的一个类。(3.0废弃)

2). AFHTTPSessionManager：内部封装是 NSURLSession, 负责发送网络请求,使用最多的一个类。

3). AFNetworkReachabilityManager：实时监测网络状态的工具类。当前的网络环境发生改变之后,这个工具类就可以检测到。

4). AFSecurityPolicy：网络安全的工具类, 主要是针对 HTTPS 服务。

5). AFURLRequestSerialization：序列化工具类,基类。上传的数据转换成JSON格式
(AFJSONRequestSerializer).使用不多。

6). AFURLResponseSerialization：反序列化工具类;基类.使用比较多:

7). AFJSONResponseSerializer; JSON解析器,默认的解析器.

8). AFHTTPResponseSerializer; 万能解析器; JSON和XML之外的数据类型,直接返回二进制数据.对服务器返回的数据不做任何处理.

9). AFXMLParserResponseSerializer; XML解析器;
