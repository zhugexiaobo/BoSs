---
title: UIWebView 的使用
layout: post
tags: iOS 
---

- 执行 JS 代码 `stringByEvaluatingJavaScriptFromString`
    
```
// 从网页获取URL
NSString *urlString = [self stringByEvaluatingJavaScriptFromString:@"location.href"];
// 从网页获取标题
NSString *titleString = [self stringByEvaluatingJavaScriptFromString:@"document.title"];
// 网页的滚动位置
CGPoint pt;
pt.x = [[self stringByEvaluatingJavaScriptFromString:@"window.pageXOffset"] integerValue];
pt.y = [[self stringByEvaluatingJavaScriptFromString:@"window.pageYOffset"] integerValue];
// 网站的图标Icon下载地址
NSURL *iconUrl = [[NSURL alloc] initWithScheme:[web.request.URL scheme] host:[web.request.URL host] path:@"/favicon.ico"];
```
- 调整 webView 里的字体大小 [link](http://www.cocoachina.com/bbs/read.php?tid=29707)
- 判断 url 是否相同

```
// in NSURL+uriEquivalence.m

- (BOOL)isEquivalent:(NSURL *)aURL
{
    if ([self isEqual:aURL]) return YES;
    if ([[self scheme] caseInsensitiveCompare:[aURL scheme]] != NSOrderedSame) return NO;
    if ([[self host] caseInsensitiveCompare:[aURL host]] != NSOrderedSame) return NO;

    // NSURL path is smart about trimming trailing slashes
    // note case-sensitivty here
    if ([[self path] compare:[aURL path]] != NSOrderedSame) return NO;

    // at this point, we've established that the urls are equivalent according to the rfc
    // insofar as scheme, host, and paths match

    // according to rfc2616, port's can weakly match if one is missing and the
    // other is default for the scheme, but for now, let's insist on an explicit match
    if ([[self port] compare:[aURL port]] != NSOrderedSame) return NO;

    if ([[self query] compare:[aURL query]] != NSOrderedSame) return NO;

    // for things like user/pw, fragment, etc., seems sensible to be
    // permissive about these.  (plus, I'm tired :-))
    return YES;
}
```
    
- 自定义网页上的长按弹出菜单：[link](http://www.icab.de/blog/2010/07/11/customize-the-contextual-menu-of-uiwebview/)
- 网页内关键字搜索与高亮：[link](http://www.icab.de/blog/2010/01/12/search-and-highlight-text-in-uiwebview/)
- 清除 UIWebVIew 的内存占用和泄露：[link](http://www.codercowboy.com/code-uiwebview-memory-leak-prevention/)
- 自动根据网页打开新标签和新窗口：[part1](http://www.icab.de/blog/2009/07/27/webkit-on-the-iphone-part-1/)，[part2](http://www.icab.de/blog/2009/07/27/webkit-on-the-iphone-part-1/)。只是上面2个还不够，有些网页的 `_blank` 不是写在链接上的，而是全局都是 `_blank`，所以再补上下面的部分才行：

```
function MyIPhoneApp_isBlankInBaseElement() {         var baseElements = document.getElementsByTagName('base');
if(baseElements.length > 0){
        if(baseElements[0].getAttribute('target') == '_blank'){
            return 'yes';
        }
    }
return 'no';
}
```
- 离线缓存网页：[link](http://www.keakon.net/2011/08/14/%E4%B8%BAUIWebView%E5%AE%9E%E7%8E%B0%E7%A6%BB%E7%BA%BF%E6%B5%8F%E8%A7%88)
- 复制原生滚动行为(add 8.26) 

```
webView.scrollView.decelerationRate = UIScrollViewDecelerationRateNormal;
```
  