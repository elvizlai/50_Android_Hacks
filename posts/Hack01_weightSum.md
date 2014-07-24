##技巧1、在视图中使用weights属性

#####诉求：在xml中，如何实现按钮在屏幕的中间且其宽度为父窗体的50%？效果如下图

<img src="https://github.com/ElvizLai/50_Android_Hacks/images/hack01/hack01_1.png" />
<img src="https://github.com/ElvizLai/50_Android_Hacks/blob/master/images/hack01/hack01_2.png" />

######竖屏与横屏下按钮宽度都占屏幕宽度的50%。

#####技巧：使用weightSum与layout_weight属性
先上代码：
```
<LinearLayout
    android:id="@+id/"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="horizontal"
    android:weightSum="1">

    <Button
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="0.5"
        android:text="Hack01"/>

</LinearLayout>
```
#####weightSum属性表示该控件在主方向（上述代码中horizontal为主方向）的总比重。layout_weight则用来表示子控件在主方向所占的比重。上例中，总长为1，子长为0.5，所以子控件达到了占总控件一半的诉求。

#####如果weightSum不做定义，那么其值为所有子控件layout_weight之和。如果只有一个子控件有layout_weight属性，则其会尽量填充满其父控件的主方向。如下所示：
```
<LinearLayout
    android:id="@+id/"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="horizontal">

    <Button
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="2"
        android:text="Hack01"/>

    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hack02"/>

</LinearLayout>
```
<img src="/images/hack01/hack01_2.png" />
