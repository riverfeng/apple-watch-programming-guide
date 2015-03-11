通知要点

  如果你的IOS应用程序支持本地或者远程通知，Apple Watch会在合适的时候显示这些通知。当一个应用程序的本地或者远程通知被用户的Iphone收到后，IOS会决定是否将该通知显示在Iphone或者Apple Watch上。对于发送到Apple Watch的通知，系统会隐约的告诉用户有一个通知了。如果用户选择查看通知，系统会先显示该通知的一个简短版本，之后才是一个更详细的版本。用户可以选择忽略该通知，也可以启动你的WatchKit程序，或者通过通知上的有效活动按钮来触发其他操作。

  应用程序不需要做任何事情来支持通知。系统提供了一个默认的通知显示界面来显示提醒信息，不过，应用程序也能自定义通知界面，包括自定义图形，内容和品牌。

  只有在IOS支持它们的情况下，Apple Watch才能显示本地或者远程通知，有关如何支持本地和远程通知你的OS应用程序信息，请查看“Local and Remote Notification Programming Guide.[https://developer.apple.com/library/prerelease/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Introduction.html#//apple_ref/doc/uid/TP40008194
]
”

Short-Look界面

当用户第一次看到通知的时候，系统会显示一个short-look界面，图15-1展示的就是样子示例。short-look界面是非自定义并且不能滚动的。系统会使用一个模板来显示应用程序的名字，图标以及存储在本地通知或者远程通知中得标题字符。如果用户继续查看通知，系统将会快速的从short-look界面转换到long-look界面。
【https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Art/shortlook_calendar_2x.png
】

  在short-look使用的标题字符串提供了该通知的一个简短的意图指示。对于本地通知，使用UILocalNotification对象的alertTitle属性指定该字符串。对于远程通知，则在负载数据中的dictionary（字典）里添加一个title的key。获取更多关于添加一个标题字符串到你的通知中，请查看“Local and Remote Notification Programming Guide https://developer.apple.com/library/prerelease/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Introduction.html#//apple_ref/doc/uid/TP40008194
]
”
Long-Look界面

  Long-look界面是一个能滚动并且显示通知内容以及相关活动按钮的屏幕。如果你没有提供一个自定义的通知界面，Apple Watch会显示一个默认的界面，包含你应用程序的图标，通知的标题以及提示消息。如果你提供了自定义的通知界面，Apple Watch则会显示你自定义的界面。

  long-look通知界面由三块区域组成
sash（腰带）区域是一个包含了应用程序图标和名称的浮层。sash区域在默认情况下是透明的，但是你也能在你自定义的通知界面修改它的颜色。 

内容区域包含了通知的具体信息。对于自定义的界面，你可以配置该区域位于sash开始位置或者在它的下方。获取更多关于如何自定义内容区域，请查看Custom Notification Interface[https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/CustomzingthePushNotificationInterface.html#//apple_ref/doc/uid/TP40014969-CH6-SW1 ]

按钮区域包含了“忽略”按钮以及其他IOS程序注册的活动按钮。

图15-2展示了一个包含了两个活动按钮的long-look通知示例。

图片链接【https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Art/longlook_calendar_2x.png】

 点击应用程序图标启动WatchKit程序。点击程序自定义的活动按钮则会将选择的活动发送到IOS应用程序或者你得WatchKit应用程序。前台活动被发送到你的WatchKit程序和拓展，后台活动则发送到你的IOS程序。点击“忽略”按钮则啥都不做，直接关闭当前通知界面。点击其他地方则什么效果也没有。
  更多关于如何为你的应用提供自定义long-look界面，请见Custom Notification Interfaces【https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/CustomzingthePushNotificationInterface.html#//apple_ref/doc/uid/TP40014969-CH6-SW1
】

为通知添加活动按钮

  活动按钮通过为通知提供封装响应为用户节省时间。Apple Watch借用IOS程序注册的交互通知来显示活动按钮，在IOS8以及更高的版本中，应用都需要使用UIUserNotificationSetting[https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIUserNotificationSettings_class/index.html#//apple_ref/occ/cl/UIUserNotificationSettings ]对象来注册通知的类型。当注册信息的时候，应用程序同样可以注册一系列自定义通知目录，Apple Watch使用这些目录信息将对应的活动按钮添加到long-look界面上。
代码15-1片段展示了如何为一个示例IOS程序注册设置和目录。这个方法是被包含IOS程序实现的，而不是WatchKit拓展，并且是在IOS程序启动时委托调用的。这个方法的是使用swift语言写的，还展示了“invitation”目录的创建和注册，“ invitation”目录包含了接受和拒绝会议邀请的活动动作。

代码段：【

func registerSettingsAndCategories() {
var categories = NSMutableSet()
var acceptAction = UIMutableUserNotificationAction()
acceptAction.title = NSLocalizedString("Accept", comment: "Accept invitation")
acceptAction.identifier = "accept"
acceptAction.activationMode = UIUserNotificationActivationMode.Background
acceptAction.authenticationRequired = false
var declineAction = UIMutableUserNotificationAction()
declineAction.title = NSLocalizedString("Decline", comment: "Decline invitation")
declineAction.identifier = "decline"
declineAction.activationMode = UIUserNotificationActivationMode.Background
declineAction.authenticationRequired = false
var inviteCategory = UIMutableUserNotificationCategory()
inviteCategory.setActions([acceptAction, declineAction],
forContext: UIUserNotificationActionContext.Default)
inviteCategory.identifier = "invitation"
categories.addObject(inviteCategory)
// Configure other actions and categories and add them to the set...
var settings = UIUserNotificationSettings(forTypes: (.Alert | .Badge | .Sound),
categories: categories)
UIApplication.sharedApplication().registerUserNotificationSettings(settings)
}
】

更多关于如何在你的IOS应用程序中配置目录和活动，请见[Local and Remote Notification Programming Guide“https://developer.apple.com/library/prerelease/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Introduction.html#//apple_ref/doc/uid/TP40008194”]



响应活动按钮点击

  当用户点击活动按钮时，系统使用注册UIUserNotification【https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIUserNotificationAction_class/index.html#//apple_ref/occ/cl/UIUserNotificationAction】对象中的消息来决定如何处理这个动作。动作能在前端和后台经过或者不经过验证被执行。不管怎样，前端和后台动作的处理是不同的：

  前端动作会启动你的WatchKit程序并且将你点击的按钮ID发送到你主控制接口中的handleActionWithIdentifier:forRemoteNotification【https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/instm/WKInterfaceController/handleActionWithIdentifier:forRemoteNotification:】或者handleActionWithIdentifier:forLocalNotification【https://developer.apple.com/library/prerelease/ios/documentation/WatchKit/Reference/WKInterfaceController_class/index.html#//apple_ref/occ/instm/WKInterfaceController/handleActionWithIdentifier:forLocalNotification:】方法。

  后台动作则会在后台启动能处理该动作的IOS程序，对应动作的信息将被发送到程序代理的application:handleActionWithIdentifier:forRemoteNotification:completionHander或者application:handleActionWithIdentifier:forLocalNotification:completionHandler方法。

对于前台按钮动作来说，WKUserNotificationInterfaceController子类并不处理这个按钮动作。选中一个前台按钮动作将启动你的应用，并为你应用的主入口点载入界面控制器。该初始界面控制器负责处理所有按钮动作。这个界面控制器必须实现handleActionWithIderntifier:forRemoteNotification和handleActionWithIdentifier:forLocalNotification方法（如果适用的话）来处理按钮动作
