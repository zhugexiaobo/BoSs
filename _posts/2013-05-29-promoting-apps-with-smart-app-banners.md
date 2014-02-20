---
title: Promoting Apps with Smart App Banners
layout: post
tags: iOS 
---

> 注：该功能为在 iOS 设备上用浏览器浏览设置了该功能的 Web 页面时，最上方会有 App 信息提示，点击可直接跳转到 App Store 进行下载或打开设备上的该应用。[文档](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html)


Safari has a new Smart App Banner feature in iOS 6 and later that provides a standardized method of promoting apps on the App Store from a website, as shown in [Figure 7-1](https://developer.apple.com/library/ios/documentation/AppleApplications/Reference/SafariWebContent/PromotingAppswithAppBanners/PromotingAppswithAppBanners.html#//apple_ref/doc/uid/TP40002051-CH6-SW7).

Figure 7-1  A Smart App Banner of the Apple Store app

>Note: Smart App Banners only show on iOS, not OS X.

Smart App Banners vastly improve users’ browsing experience compared to other promotional methods. As banners are implemented in iOS 6, they will provide a consistent look and feel across the web that users will come to recognize. Users will trust that tapping the banner will take them to the App Store and not a third-party advertisement. They will appreciate that banners are presented unobtrusively at the top of a webpage, instead of as a full-screen ad interrupting the web content. And with a large and prominent close button, a banner is easy for users to dismiss.

If the app is already installed on a user's device, the banner intelligently changes its action, and tapping the banner will simply open the app. If the user doesn’t have your app on his device, tapping on the banner will take him to the app’s entry in the App Store. When he returns to your website, a progress bar appears in the banner, indicating how much longer the download will take to complete. When the app finishes downloading, the View button changes to an Open button, and tapping the banner will open the app while preserving the user’s context from your website.

Smart App Banners automatically determine whether the app is supported on the user’s device. If the device loading the banner does not support your app, or if your app is not available in the user's location, the banner will not display.

### Implementing a Smart App Banner on Your Website

To add a Smart App Banner to your website, include the following meta tag in the head of each page where you’d like the banner to appear:

```
<meta name="apple-itunes-app" content="app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL">
```
You can include three comma-separated parameters in the content attribute:

- *app-id*: (Required.) Your app's unique identifier. To find your app ID from the [iTunes Link Maker](http://itunes.apple.com/linkmaker/), type the name of your app in the Search field, and select the appropriate country and media type. In the results, find your app and select iPhone App Link in the column on the right. Your app ID is the nine-digit number in between id and ?mt.
- *affiliate-data*: (Optional.) Your iTunes affiliate string, if you are an iTunes affiliate. If you are not, find out more about becoming an iTunes affiliate at [http://www.apple.com/itunes/affiliates/](http://www.apple.com/itunes/affiliates/).
- *app-argument*: (Optional.) A URL that provides context to your native app. If you include this, and the user has your app installed, she can jump from your website to the corresponding position in your iOS app. Typically, it is beneficial to retain navigational context because:
    - If the user is deep within the navigational hierarchy of your website, you can pass the document’s entire URL, and then parse it in your app to reroute her to the correct location in your app.
    - If the user performs a search on your website, you can pass the query string so that she can seamlessly continue the search in your app without having to retype her query.
    - If the user is in the midst of creating content, you can pass the session ID to download the web session state in your app so she can nondestructively resume her work.

You can generate the *app-argument* of each page dynamically with a server-side script. You can format it however you'd like, as long as it is a valid URL.
> Note: You cannot display Smart App Banners inside of a frame.

### Providing Navigational Context to Your App

In your app, implement the [application:openURL:sourceApplication:annotation:](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/Reference/Reference.html#//apple_ref/occ/intfm/UIApplicationDelegate/application:openURL:sourceApplication:annotation:) method in your app delegate, which fires when your app is launched from a URL. Then provide logic that can interpret the URL that you pass. The value you set to the app-argument parameter is available as the [NSURL](https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSURL_Class/Reference/Reference.html#//apple_ref/occ/cl/NSURL) *url* object.

The example in Listing 7-1 illustrates a website that passes data to a native iOS app. To accomplish this, detect if the URL contains the string `/profile`. If it does, then open the profile view controller and pass the profile ID number that is in the query string.

Listing 7-1  Routing the user to the correct view controller

```
- (BOOL)application:(UIApplication *)application openURL:(NSURL *)url sourceApplication:(NSString *)sourceApplication annotation:(id)annotation
{
    // in this example, the URL from which the user came is http://example.com/profile/?12345
    // determine if the user was viewing a profile
    if ([[url path] isEqualToString:@"/profile"]) {
        // switch to profile view controller
        [self.tabBarController setSelectedViewController:profileViewController];
        // pull the profile id number found in the query string
        NSString *profileID = [url query];
        // pass profileID to profile view controller
        [profileViewController loadProfile:profileID];
    }
    return YES;
}
```
