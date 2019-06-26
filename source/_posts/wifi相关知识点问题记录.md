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

* `SUPPLICANT_CONNECTION_CHANGE_ACTION `

  ```
  Broadcast intent action indicating that a connection to the supplicant has been 
  established or the connection to the supplicant has been lost. 
  获取wifi连接状态信息,连接或者断开
  ```

  通过EXTRA_SUPPLICANT_CONNECTED作为key值，从Intent中获取连接状态

* `NETWORK_STATE_CHANGED_ACTION`

  ```
  Broadcast intent action indicating that the state of Wi-Fi connectivity has changed
  ```

   `EXTRA_NETWORK_INFO` `EXTRA_BSSID` `EXTRA_WIFI_INFO` 分别可以获取networkInfo、bssid、wifiInfo信息。

* `SUPPLICANT_STATE_CHANGED_ACTION`

  ```
  Broadcast intent action indicating that the state of establishing a connection to an
  access point has changed 连接wifi的状态发生改变
  ```

  `EXTRA_NEW_STATE` `EXTRA_SUPPLICANT_ERROR` `EXTRA_SUPPLICANT_ERROR_REASON`

* `WIFI_STATE_CHANGED_ACTION`

  ```Java
  Broadcast intent action indicating that Wi-Fi has been enabled, disabled, enabling, 
  disabling, or unknown.
  ```

  `EXTRA_WIFI_STATE` `EXTRA_PREVIOUS_WIFI_STATE`

* `SCAN_RESULTS_AVAILABLE_ACTION`

  ```java
  An access point scan has completed, and results are available from the supplicant.
  Call {@link #getScanResults()} to obtain the results. {@link #EXTRA_RESULTS_UPDATED}
  indicates if the scan was completed successfully.
  ```