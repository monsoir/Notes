# 推送相关

## 本地推送

在 AppDelegate 中设置推送消息的动作

```swift
extension AppDelegate {
    func registerRichNotificationCategories() {
        // Notes: 可以在权限获取前执行
        registerNotificationCategory()
    }
    
    private func registerNotificationCategory() {
        // 动作
        let settings = UserNotificationUtils.ReminderNotificationSettings.self
        let actionDone = UNNotificationAction(identifier: settings.actionDone, title: settings.titleDone, options: .destructive)
        let actionLook = UNNotificationAction(identifier: settings.actionLook, title: settings.titleLook, options: .foreground)
        
        // 动作需要归类到一个 category
        // 同时，若需要自定义系统的取消按钮事件，需要在 options 中指明 UNNotificationCategoryOptions.customDismissAction
        let category = UNNotificationCategory(identifier: settings.categoryIdentifier, actions: [actionDone, actionLook], intentIdentifiers: [], options: [UNNotificationCategoryOptions.customDismissAction])
        
        // 最后将 category 添加到系统 
        UserNotificationUtils.UserNotificationCenter.setNotificationCategories([category])
    }
}
```

## 本地推送委托方法实现对象

本地推送的委托方法使用一个对象管理，更好管理

```swift
class NotificationDelegator: NSObject, UNUserNotificationCenterDelegate {
    
    override init() {
        super.init()
        UNUserNotificationCenter.current().delegate = self
    }
    
    // 当 app 正在运行时，必须实现此代理方法，才会收到消息推送
    func userNotificationCenter(_ center: UNUserNotificationCenter, willPresent notification: UNNotification, withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        completionHandler([.alert, .sound])
    }
    
    // 处理推送消息的按钮事件
    func userNotificationCenter(_ center: UNUserNotificationCenter, didReceive response: UNNotificationResponse, withCompletionHandler completionHandler: @escaping () -> Void) {
        let notificationIdentifier = response.notification.request.identifier
        
        switch response.actionIdentifier {
        case UNNotificationDismissActionIdentifier:
            // 系统清除按钮
            <#code#>
        case UNNotificationDefaultActionIdentifier:
            // 系统默认打开按钮
            <#code#>
        case "...":
            // 自定义的按钮
            <#code#>
        default:
            break
        }
        
        completionHandler()
    }
}
```


