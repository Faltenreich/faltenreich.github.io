---
layout: post
title: Reading Android notifications with custom views using reflection
tags: android notifications reflection
github: home-assistant-camaps-fx-adapter
---

Notifications on Android have seen countless changes through the years. What began as little more than a set of texts and icons, has been expanded with media controls, direct reply actions, notification groups/channels and more. However, one thing has been there from the very beginning: RemoteViews as the API for custom views. 

This blogpost shows how to read Android notifications with such custom views, how to analyze internal APIs using reflection and which stumbling blocks to watch out for. You can find a showcase at the end of this page in which all the mechanisms used are explained.

### Observing notifications from other apps

Any incoming notification can be read by any app, as of Android 15, as long as the user gives permission. Therefore notifications should not contain sensitive data and instead be treated as public domain. This also affects content that may be displayed in a notification later on, like mails or SMS, but we will not delve deeper into this privacy concern at this point.

Notifications can be observed via [NotificationListenerService](https://developer.android.com/reference/android/service/notification/NotificationListenerService). This service has to be registered in the AndroidManifest and will be started and called by the operating system.

```kotlin
<manifest>
    <uses-permission android:name="android.permission.BIND_NOTIFICATION_LISTENER_SERVICE"/>
    <application>
        <service
            android:name="MyService"
            android:permission="android.permission.BIND_NOTIFICATION_LISTENER_SERVICE">
            <intent-filter>
                <action android:name="android.service.notification.NotificationListenerService"/>
            </intent-filter>
        </service>
    </application>
</manifest>
```

The user has to grant the `enabled_notification_listeners` permission for the NotificationListenerService to work. So we check the permission and, if not granted, redirect the user to the settings.

```kotlin
fun hasNotificationListenerPermission(): Boolean {
    val componentName = ComponentName(context, MyService::class.java)
    val enabledListeners = Settings.Secure.getString(
        context.contentResolver,
        "enabled_notification_listeners",
    )
    return enabledListeners.contains(componentName.flattenToString())
}

fun openNotificationListenerSettings() {
    val intent = Intent(Settings.ACTION_NOTIFICATION_LISTENER_SETTINGS)
    activity.startActivityForResult(intent, 0)
}
```

Every time a new notification is being posted or updated in the status bar, the NotificationListenerService's `onNotificationPosted` will be invoked.

```kotlin
class MyService : NotificationListenerService() {
    override fun onNotificationPosted(statusBarNotification: StatusBarNotification?) {
        // Do something with the statusBarNotification
    }
}
```

> ⚠️ NotificationListenerService may become unreliable during development until it stops completely. This issue has yet to be fixed by Google (see [issuetracker.google](https://issuetracker.google.com/issues/75414169)) and can be workaround by restarting the device (see [stackoverflow](https://stackoverflow.com/a/37081128/3269827)).

### Reading notification contents

`onNotificationPosted` receives a [StatusBarNotification](https://developer.android.com/reference/android/service/notification/StatusBarNotification) whose [Notification](https://developer.android.com/reference/android/app/Notification) exposes everything the user can see or do. Basic notifications contain pre-determined properties like title, summary or icon. More complex notifications use [RemoteViews](https://developer.android.com/reference/android/widget/RemoteViews) to render custom views.

The notification in my example is being posted by the app "CamAPS FX". This app displays medical data in RemoteViews of a local notification. There is no broadcast nor any other interface to access this data without some third-party cloud-based service.

<img src="/assets/images/posts/2025-01-notification.png" width="420"/>
*Sample notification from "CamAPS FX", with "172" being the value we are trying to read*

### Reading RemoteViews

RemoteViews do not expose their contents, so we use [reflection](https://kotlinlang.org/docs/reflection.html) to read their internals. The debugger helps us identifying the properties we need to read data from the custom view. We find out that `mActions` lists every callable method and its current value.

<img src="/assets/images/posts/2025-01-remoteviews.jpg" width="420"/>
*RemoteViews with list of actions containing method name and value*

We access `mActions` by retrieving RemoteViews' members via `KClass.memberProperties`, searching for the one with the right name, making it accessible and casting the result to a list of wildcards.

```kotlin
fun getRemoteViewActions(remoteViews: RemoteViews): List<Pair<String, Any?>> {
    val actionsProperty = RemoteViews::class.memberProperties
        .firstOrNull { it.name == "mActions" }
        ?: return emptyList()
    actionsProperty.isAccessible = true
    val actions = actionsProperty.get(remoteViews) as? ArrayList<*> 
        ?: return emptyList()
    return actions.mapNotNull(::getRemoteViewAction)
}
```

We then iterate over this list of actions and access every entry's method name (`mMethodName`) and value (`mValue`) via reflection like before.

```kotlin
fun getRemoteViewAction(action: Any): Pair<String, Any?>? {
    val memberProperties = action::class.memberProperties

    val methodNameProperty = memberProperties
        .firstOrNull { it.name == "mMethodName" } 
        ?: return null
    methodNameProperty.isAccessible = true
    val methodName = methodNameProperty.getter.call(action) as? String 
        ?: return null

    val valueProperty = memberProperties
        .firstOrNull { it.name == "mValue" } 
        ?: return null
    valueProperty.isAccessible = true
    val value = valueProperty.getter.call(action)

    return methodName to value
}
```

Now we have access to every piece of data the notification contains. Next we can filter the method names and values for those of interest.

In our example we are looking for some sort of text view that displays a number ("172" in the screenshot above). Therefore we search the actions for one that sets a text (`methodName == "setText"`) to a number (`(value as? String)?.toFloatOrNull()`).

```
val remoteViewActions: List<Pair<String, Any?>> = getRemoteViewActions(remoteViews)
val setTextActions = remoteViewActions.filter { it.first == "setText" }
val number = setTextActions.mapNotNull { (it.second as? String)?.toFloatOrNull() }.firstOrNull()
```

Now we have access to the number in the notification's custom view.

### Beware breaking changes

As neat as this approach is, it also creates a moving target for changes outside of our reach. Updates to the notification-producing app may change the structure of its RemoteViews. Updates to the operating system may change the RemoteViews' internals. Both would break our mapping logic.

This happened during the writing of this blogpost and its showcase. After updating to Android 15 no data from RemoteViews could be read anymore. The reason for this was the renaming of RemoteViews' internal properties which now use the hungarian notation and previously did not. `actions` became `mActions` and thus the filtering logic for that property stopped working.

### Conclusion

Reflection is a powerful tool that allows us to read data we are not supposed to. It can be a feasible approach to overcome dead ends. However, this comes at a cost and may be too fragile for long-living software with a broad target audience.

This worked well in my example, in which I wanted to transfer data from an Android app and its notifications to my local Home Assistance instance. I do not plan to publish this solution to any store and am fine with the service not running for a while in case of a breaking change. You can find the code for this example in the GitHub project below.