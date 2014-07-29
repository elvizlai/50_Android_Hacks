##技巧12、使用xml控制图片的属性

#####诉求：如何添加一个圆角的背景图片？

#####技巧：通过xml属性配置达到该目的。使用该方法还可以实现：按钮按下、松开自定义效果；选择按钮的选中、非选中等自定义效果。

首先在drawable目录下新建button_rounded_background.xml文件，内容如下：

```
<shape xmlns:android="http://schemas.android.com/apk/res/android"android:shape="rectangle">
<solid android:color="#AAAAAA"/>
<corners android:radius="15dp"/>
</shape>
```

然后在layout的xml文件中调用上述文件就可以了：

```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:gravity="center"
              android:orientation="vertical">

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="@drawable/button_rounded_background"
        android:padding="10dp"
        android:text="你好"
        android:textColor="#000000"/>

</LinearLayout>
```

效果如下：

<img src="/images/hack12/hack12_1.png" width="240" height="400"/>

扩展阅读：  
http://developer.android.com/guide/topics/resources/drawable-resource.html