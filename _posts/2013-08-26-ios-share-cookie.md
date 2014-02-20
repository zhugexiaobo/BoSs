---
title: iOS cookie 共享
layout: post
tags: iOS 
---

最近有个需求：应用中用户登录后，在 UIWebView 载入的相关网站不需要再次输入帐号密码登录，提升幸福感<(=╯▽╰=)>

这里涉及到 cookie 共享。应用使用的是 RESTful API，在登录成功后返回的数据中会有一个用户信息的 cookie，只要把这个 cookie 存下来，保证在用户登录状态下一直有该 cookie 就可以了。应用之间由于沙箱，cookie 是不共享的；应用内，一个 cookie 对应一个 NSHTTPCookie 实例，通过 NSHTTPCookieStorage 进行管理。需要持久化的 cookie 存放在 `Library/Cookies/Cookie.binarycookies` 文件中，但是有时会丢失，为了保证可靠，我们把这个 cookie 存到 `NSUserDefaults`。

```
/**
* Check if UID cookie exist
*
* @return UID Cookie
*/
- (NSHTTPCookie *)hasUidCookie
{
    // 取出 `kAFUrl` 域名下的所有 cookie
    NSArray *cookies =[[NSHTTPCookieStorage sharedHTTPCookieStorage] cookiesForURL:[NSURL URLWithString:kAFUrl]];
    for(NSHTTPCookie *cookie in cookies){
        if ([cookie.name isEqualToString:@"uid"]) {
            return cookie;
        }
    }
    return nil;
}

// 存储 uid cookie
if ([Qtool hasUidCookie]) {
    [[NSUserDefaults standardUserDefaults] setObject:[NSKeyedArchiver archivedDataWithRootObject:[Qtool hasUidCookie]] forKey:Notification_UserCookie];
}
[userDefaults synchronize];

 // 注入 uidCookie
if (![Qtool hasUidCookie]) {
    NSHTTPCookie *uidCookie = [NSKeyedUnarchiver unarchiveObjectWithData:[[NSUserDefaults standardUserDefaults] objectForKey:Notification_UserCookie]];
    [[NSHTTPCookieStorage sharedHTTPCookieStorage] setCookie:uidCookie];
}
```

-----------------------
附加，手动设置一个 cookie。

```
NSMutableDictionary *cookieProperties = [NSMutableDictionary dictionary];
[cookieProperties setObject:@"name" forKey:NSHTTPCookieName];
[cookieProperties setObject:@"value" forKey:NSHTTPCookieValue];
[cookieProperties setObject:@"www.taobao.com" forKey:NSHTTPCookieDomain];
[cookieProperties setObject:@"/" forKey:NSHTTPCookiePath];
[cookieProperties setObject:@"0" forKey:NSHTTPCookieVersion];
[cookieProperties setObject:@"30000" forKey:NSHTTPCookieMaximumAge];
NSHTTPCookie *cookie = [NSHTTPCookie cookieWithProperties:cookieProperties];
[[NSHTTPCookieStorage sharedHTTPCookieStorage] setCookie:cookie];
```