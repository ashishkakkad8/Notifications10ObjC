# Push Notifications in iOS 10 [Objective-C]

<p align="justify">The new framework called "UserNotifications"	is introduced with iOS 10 SDK. The <a href="https://developer.apple.com/reference/usernotifications" target="_blank">UserNotifications framework</a> (UserNotifications.framework) supports the delivery and handling of local and remote notifications.

So, Let see what we have to change to get the push notifications in iOS 10.
</p>

##Steps for implement code to handle push notifications in iOS 10

###Import UserNotifications.framework in your AppDelegate file

```objective-c
#import <UserNotifications/UserNotifications.h>
```
Also add UNUserNotificationCenterDelegate.

```objective-c
#import <UserNotifications/UserNotifications.h>
@interface AppDelegate : UIResponder <UIApplicationDelegate,UNUserNotificationCenterDelegate>

@end
```
###Register for push notification

<p align="justify">Before registration check the version of iOS and then based on versions do the code. For iOS 7 code was different, fro iOS 8 & 9 code was different and again for iOS 10 code is different.</p>

<p align="justify"><em>As per my opinion you have to set the deployment target to iOS 8 or iOS 9 and later. For this you can check the <a href="https://developer.apple.com/support/app-store/" target="_blank">adoption ratio of iOS</a> in the devices.</em></p>

<strong>Define constant for version check :</strong>

```objective-c
#define SYSTEM_VERSION_GRATERTHAN_OR_EQUALTO(v)  ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] != NSOrderedAscending)
```

<strong>Add code in your did finish launching</strong>

```objective-c
-(BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions{
    [self registerForRemoteNotifications];
    return YES;
}

- (void)registerForRemoteNotifications {
    if(SYSTEM_VERSION_GRATERTHAN_OR_EQUALTO(@"10.0")){
        UNUserNotificationCenter *center = [UNUserNotificationCenter currentNotificationCenter];
        center.delegate = self;
        [center requestAuthorizationWithOptions:(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge) completionHandler:^(BOOL granted, NSError * _Nullable error){
             if(!error){
                 [[UIApplication sharedApplication] registerForRemoteNotifications];
             }
         }];  
    }
    else {
        // Code for old versions
    }
}
```

###Handling delegate methods for UserNotifications

<p align="justify"><strong><em>You will be surprise that notification displayed when application in foreground too in iOS 10. As we know that in old versions we display alert or something else which will be look like notification comes in foreground.</em></strong></p>

There are two delegate methods need to be handled :

```objective-c

//Called when a notification is delivered to a foreground app.
-(void)userNotificationCenter:(UNUserNotificationCenter *)center willPresentNotification:(UNNotification *)notification withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler{
    NSLog(@"User Info : %@",notification.request.content.userInfo);
    completionHandler(UNAuthorizationOptionSound | UNAuthorizationOptionAlert | UNAuthorizationOptionBadge);
}

//Called to let your app know which action was selected by the user for a given notification.
-(void)userNotificationCenter:(UNUserNotificationCenter *)center didReceiveNotificationResponse:(UNNotificationResponse *)response withCompletionHandler:(void(^)())completionHandler{
    NSLog(@"User Info : %@",response.notification.request.content.userInfo);
    completionHandler();
}
```

###Add Push Notifications Entitlements

<p align="justify">Go to your project target's <strong>Capabilities</strong> tab and add Push Notifications Entitlements.</p>

<img src="http://ashishkakkad.com/wp-content/uploads/2016/09/Push-Notifications-Entitlements.png" alt="Push Notifications Entitlements" />

<p align="justify">If it's available in your certificates then it will enable directly else configure your profile with the certificates and you can enable this capability by that.</p>

<b>Blogpost on my website : <a href="http://ashishkakkad.com/2016/09/push-notifications-in-ios-10-objective-c/" target="_blank">Push Notifications in iOS 10 [Objective-C]</a><b>
