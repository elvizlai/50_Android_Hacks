##技巧15、优化Activity的启动时间

#####诉求：Activity跳转时效率很低，视图载入的速度不理想。

#####技巧：使用Android SDK自带的hierarchyviewer工具查看程序中的视图概况，并对其做出合并与简化。

首先打开工具

然后手机连接数据线后打开需要查看的应用，hierarchyviewer中会呈现如下视图：

<img src="/images/hack15/hack15_1.png" width="121" height="102"/>

<img src="/images/hack15/hack15_2.png" width="1333" height="792"/>

<img src="/images/hack15/hack15_5.png" width="1281" height="799"/>

#####扩展阅读：  
http://developer.android.com/tools/debugging/debugging-ui.html  
http://stackoverflow.com/questions/6499004/androidwindowbackground-null-to-improve-app-speed

