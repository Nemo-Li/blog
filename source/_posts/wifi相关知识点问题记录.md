---
title: wifi相关知识点问题记录
date: 2019-06-21 10:44:14
tags: wifi
---

连接错误等问题需要log分析，log输出命令

```shell
adb logcat -v time process //可以打印出所有设备的log
adb logcat -help查看命令使用说明
```

wifi功能参考settings源码，用源码的方式去实现。需要源码编译提供jar包，这个由平台提供。

recyclerView列表局部刷新需要注意没有实现payload功能时不能调用notifyItemChanged(int position, @Nullable Object payload)方法，刷新不成功。

wifi扫描以及连接通过广播的形式接收结果，实现wifi功能通过WifiManager来实现

`SUPPLICANT_CONNECTION_CHANGE_ACTION `

```
Broadcast intent action indicating that a connection to the supplicant has been 
established or the connection to the supplicant has been lost. 
获取wifi连接状态信息,连接或者断开
```

通过EXTRA_SUPPLICANT_CONNECTED作为key值，从Intent中获取连接状态

`NETWORK_STATE_CHANGED_ACTION`

Broadcast intent action indicating that the state of Wi-Fi connectivity has changed

 `EXTRA_NETWORK_INFO` `EXTRA_BSSID` `EXTRA_WIFI_INFO` 分别可以获取networkInfo、bssid、wifiInfo信息。

`SUPPLICANT_STATE_CHANGED_ACTION`

```
Broadcast intent action indicating that the state of establishing a connection to an
access point has changed 连接wifi的状态发生改变
```

`EXTRA_NEW_STATE` `EXTRA_SUPPLICANT_ERROR` `EXTRA_SUPPLICANT_ERROR_REASON`

`WIFI_STATE_CHANGED_ACTION`

```Java
Broadcast intent action indicating that Wi-Fi has been enabled, disabled, enabling, 
disabling, or unknown.
```

`EXTRA_WIFI_STATE` `EXTRA_PREVIOUS_WIFI_STATE`

`SCAN_RESULTS_AVAILABLE_ACTION`

```java
An access point scan has completed, and results are available from the supplicant.
Call {@link #getScanResults()} to obtain the results. {@link #EXTRA_RESULTS_UPDATED}
indicates if the scan was completed successfully.
```
wifi功能实现主要通过WifiManager类，Android平台提供wifi功能[参考](https://developer.android.com/reference/android/net/wifi/package-summary)

介绍wifimanager的方法

disconnect 断开连接

```Java
//Disassociate from the currently active access point. This may result in the asynchronous delivery of state change events.
//取消与当前活动的接入点的关联。这可能导致状态更改事件的异步传递。
public boolean disconnect ()
```

forget忘记网络

```java
//Delete the network from the list of configured networks for the foreground user.
//This function is used instead of a sequence of removeNetwork() and saveConfiguration().
//这是一个忘掉当前网络的方法，默认是hide的，listener可以为null。这个方法与执行removeNetwork()和 
//saveConfiguration()两个方法效果一样
public void forget(int netId, ActionListener listener)
```

removeNetwork移除网络

```java
//Remove the specified network from the list of configured networks. This may result in the asynchronous delivery of state change events. Applications are not allowed to remove networks created by other applications.
//会有异步的状态改变事件，一个应用不能移除另一个应用创建的网络
public boolean removeNetwork (int netId)
```

saveConfiguration保存配置

```java
//Tell the device to persist the current list of configured networks.
//Note: It is possible for this method to change the network IDs of existing networks. You //should assume the network IDs can be different after calling this method.
//告诉设备保存当前网络配置列表，有可能会使网络ID发生改变，我们应该假设调用了该方法，网络ID会不可用
public boolean saveConfiguration ()
```

enableNetwork连接wifi

```java
//Allow a previously configured network to be associated with. If attemptConnect is true, //an attempt to connect to the selected network is initiated. This may result in the //asynchronous delivery of state change events.
//连接给定netId的wifi
public boolean enableNetwork (int netId, boolean attemptConnect)
```

其它关于WifiManager方法的API[文档](https://developer.android.com/reference/android/net/wifi/WifiManager)

