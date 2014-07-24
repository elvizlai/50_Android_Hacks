##技巧2、视图的复用与ViewStub

#####诉求1：同一个子视图出现在多个主视图中，如何快速修改与维护？
#####技巧1：在视图中使用include标签，*上代码*

```
<LinearLayout
    android:id="@+id/"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="horizontal">

    <include layout="@layout/hack02"/>

    <include
        layout="@layout/hack02"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"/>

</LinearLayout>
```
<img src="/images/hack02/hack02_1.png" width="240" height="400"/>

#####这样，对于重复出现的子视图就可以在一个xml中统一维护了，而且还可以设置属性来控制其位置与大小。


- - -

#####诉求2：需要在主视图中添加一个占用资源较多的隐藏子视图，需要的时候才让其显示。
#####技巧2：使用ViewStub

*直接上代码：*
```
<LinearLayout
    android:id="@+id/"
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:gravity="center"
    android:orientation="horizontal">

    <ViewStub
        android:id="@+id/hack02"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:inflatedId="@+id/hack_02"
        android:layout="@layout/hack02"/>

</LinearLayout>
```
#####当需要时，在activity中调用以下代码将其设为可见：
```
ViewStub viewStub = (ViewStub) findViewById(R.id.hack02);
viewStub.setVisibility(View.VISIBLE);
```

#####当设为可见后，可通过控制属性中的inflatedId来操控载入的视图。

#####例如，上代码中hack02所表示的layout为一个Button，当将其设为可见后，可通过`Button button = (Button) findViewById(R.id.hack_02);`来获取该按钮，并做出相应的操作。（*注意，必须是已经设置后可见后才能进行此操作*）