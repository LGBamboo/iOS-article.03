### 返回目录:[全网各大厂iOS面试题-题集大全](https://github.com/LGBamboo/iOS-Advanced)

# 你会如何存储用户的一些敏感信息，如登录的token

使用keychain来存储,也就是钥匙串,使用keychain需要导入Security框架

iOS的keychain服务提供了一种安全的保存私密信息（密码，序列号，证书等）的方式，每个iOS程序都有一个独立的keychain存储。相对于 NSUserDefaults、文件保存等一般方式，keychain保存更为安全，而且keychain里保存的信息不会因App被删除而丢失，所以在 重装App后，keychain里的数据还能使用。从iOS 3.0开始，跨程序分享keychain变得可行。

如何需要在应用里使 用使用keyChain，我们需要导入Security.framework ，keychain的操作接口声明在头文件SecItem.h里。直接使用SecItem.h里方法操作keychain，需要写的代码较为复杂，为减轻 咱们程序员的开发，我们可以使用一些已经封装好了的工具类，下面我会简单介绍下我用过的两个工具类：KeychainItemWrapper和 SFHFKeychainUtils。

自定义一个keychain的类

* `CSKeyChain.h`
```
@interface CSKeyChain : NSObject

+ (NSMutableDictionary *)getKeychainQuery:(NSString *)service;

+ (void)save:(NSString *)service data:(id)data;

+ (id)load:(NSString *)service;

+ (void)delete:(NSString *)service;

@end
```

* `CSKeyChain.m`
```
#import "CSKeyChain.h"
#import<Security/Security.h>

@implementation CSKeyChain

+ (NSMutableDictionary *)getKeychainQuery:(NSString *)service {
    return [NSMutableDictionary dictionaryWithObjectsAndKeys:
            (__bridge_transfer id)kSecClassGenericPassword,(__bridge_transfer id)kSecClass,
            service, (__bridge_transfer id)kSecAttrService,
            service, (__bridge_transfer id)kSecAttrAccount,
            (__bridge_transfer id)kSecAttrAccessibleAfterFirstUnlock,(__bridge_transfer id)kSecAttrAccessible,
            nil];
}

+ (void)save:(NSString *)service data:(id)data {
    // 获得搜索字典
    NSMutableDictionary *keychainQuery = [self getKeychainQuery:service];
    // 添加新的删除旧的
    SecItemDelete((__bridge_retained CFDictionaryRef)keychainQuery);
    // 添加新的对象到字符串
    [keychainQuery setObject:[NSKeyedArchiver archivedDataWithRootObject:data] forKey:(__bridge_transfer id)kSecValueData];
    // 查询钥匙串
    SecItemAdd((__bridge_retained CFDictionaryRef)keychainQuery, NULL);
}

+ (id)load:(NSString *)service {
    id ret = nil;
    NSMutableDictionary *keychainQuery = [self getKeychainQuery:service];
    // 配置搜索设置
    [keychainQuery setObject:(id)kCFBooleanTrue forKey:(__bridge_transfer id)kSecReturnData];
    [keychainQuery setObject:(__bridge_transfer id)kSecMatchLimitOne forKey:(__bridge_transfer id)kSecMatchLimit];
    
    CFDataRef keyData = NULL;
    
    if (SecItemCopyMatching((__bridge_retained CFDictionaryRef)keychainQuery, (CFTypeRef *)&keyData) == noErr) {
        @try {
            ret = [NSKeyedUnarchiver unarchiveObjectWithData:(__bridge_transfer NSData *)keyData];
        } @catch (NSException *e) {
            NSLog(@"Unarchive of %@ failed: %@", service, e);
        } @finally {
        }
    }
    
    return ret;
}

+ (void)delete:(NSString *)service {
    NSMutableDictionary *keychainQuery = [self getKeychainQuery:service];
    SecItemDelete((__bridge_retained CFDictionaryRef)keychainQuery);
}

@end
```

* 在别的类实现存储,加载,删除敏感信息方法
```
// 用来标识这个钥匙串
static NSString * const KEY_IN_KEYCHAIN = @"com.cs.app.allinfo";
// 用来标识密码
static NSString * const KEY_PASSWORD = @"com.cs.app.password";

+ (void)savePassWord:(NSString *)password {
    NSMutableDictionary *passwordDict = [NSMutableDictionary dictionary];
    [passwordDict setObject:password forKey:KEY_PASSWORD];
    [CSKeyChain save:KEY_IN_KEYCHAIN data:passwordDict];
}

+ (id)readPassWord {
    NSMutableDictionary *passwordDict = (NSMutableDictionary *)[CSKeyChain load:KEY_IN_KEYCHAIN];
    return [passwordDict objectForKey:KEY_PASSWORD];
}

+ (void)deletePassWord {
    [CSKeyChain delete:KEY_IN_KEYCHAIN];
}
```

### 返回目录:[全网各大厂iOS面试题-题集大全](https://github.com/LGBamboo/iOS-Advanced)

***
### 更多精选大厂 · iOS面试题答案PDF文集

![](https://upload-images.jianshu.io/upload_images/17495317-e01b6f4e054727b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
* 获取加小编的iOS技术交流圈：**[937 194 184](https://jq.qq.com/?_wv=1027&k=5PARXCI)**，直接获取
