##技巧14、VideoView及屏幕方向的变化

#####诉求：当创建一个VideoView的时候，用户尝尝会把屏幕横过来获得更加良好的体验，那么如何判定当屏幕旋转时VideoView有相应的状态呢？

#####技巧：通过在xml文件中的配置监听方向变化时的监听，并在activity中捕捉来实现上述效果。

首先，在mainfest.xml文件中相应的acitivity下添加以下内容，来监听变化(有些手机还需要添加keyboardHidden|screenSize等属性来防止onCreate在config变化时执行)：

<img src="/images/hack14/hack14_1.png" width="286" height="62"/>

创建布局文件main.xml，其原理是获取右上角的位置与大小，并用mVideoView来遮挡住它：

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="fill_parent"
                android:layout_height="fill_parent">

    <LinearLayout
        android:id="@+id/main_portrait_content"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:orientation="vertical">

        <LinearLayout
            android:layout_width="fill_parent"
            android:layout_height="0dp"
            android:layout_weight="0.25"
            android:orientation="horizontal">

            <ScrollView
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1">

                <TextView
                    android:layout_width="fill_parent"
                    android:layout_height="fill_parent"
                    android:text="@string/par_01"/>
            </ScrollView>

            <View
                android:id="@+id/main_portrait_position"
                android:layout_width="0dp"
                android:layout_height="fill_parent"
                android:layout_margin="2dp"
                android:layout_weight="1"
                android:background="#ffffff"/>
        </LinearLayout>

        <View
            android:layout_width="fill_parent"
            android:layout_height="2dp"
            android:layout_marginBottom="5dp"
            android:layout_marginTop="5dp"
            android:background="#ffffff"/>

        <ScrollView
            android:layout_width="fill_parent"
            android:layout_height="0dp"
            android:layout_marginTop="2dp"
            android:layout_weight="0.75">

            <TextView
                android:layout_width="fill_parent"
                android:layout_height="fill_parent"
                android:text="@string/par_02"/>
        </ScrollView>
    </LinearLayout>

    <VideoView
        android:id="@+id/main_videoview"
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:visibility="gone"/>

</RelativeLayout>
```

在activity中做出相应的判断：

```
package com.elvizlai.test;

import android.app.Activity;
import android.content.res.Configuration;
import android.net.Uri;
import android.os.Bundle;
import android.view.View;
import android.view.ViewGroup.LayoutParams;
import android.widget.MediaController;
import android.widget.RelativeLayout;
import android.widget.VideoView;

public class MyActivity extends Activity {

    private VideoView mVideoView;
    private View mPortraitPosition;
    private View mPortraitContent;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        mPortraitPosition = findViewById(R.id.main_portrait_position);
        mPortraitContent = findViewById(R.id.main_portrait_content);
        mVideoView = (VideoView) findViewById(R.id.main_videoview);

        // We use a post to call initVideoView because
        // we need the width and height of mPortraitPosition
        mVideoView.post(new Runnable() {
            @Override
            public void run() {
                initVideoView();
            }
        });
    }

    private void initVideoView() {
        mVideoView.setMediaController(new MediaController(this));
        Uri uri = Uri.parse("android.resource://" + getPackageName() + "/"
                + R.raw.bigbuck);
        mVideoView.setVideoURI(uri);

        setVideoViewPosition();
        mVideoView.start();
    }

    private void setVideoViewPosition() {
        switch (getResources().getConfiguration().orientation) {
            case Configuration.ORIENTATION_LANDSCAPE: {
                mPortraitContent.setVisibility(View.GONE);
                mVideoView.setVisibility(View.VISIBLE);

                RelativeLayout.LayoutParams params = new RelativeLayout.LayoutParams(LayoutParams.FILL_PARENT, LayoutParams.FILL_PARENT);

                params.addRule(RelativeLayout.CENTER_IN_PARENT);
                mVideoView.setLayoutParams(params);
                break;
            }

            case Configuration.ORIENTATION_SQUARE:
            case Configuration.ORIENTATION_UNDEFINED:
            case Configuration.ORIENTATION_PORTRAIT:
            default: {
                mPortraitContent.setVisibility(View.VISIBLE);
                mVideoView.setVisibility(View.VISIBLE);

                //获取右上角mPortraitPosition所在的位置
                int[] locationArray = new int[2];
                mPortraitPosition.getLocationOnScreen(locationArray);

                //获取右上角mPortraitPosition的大小
                RelativeLayout.LayoutParams params = new RelativeLayout.LayoutParams(mPortraitPosition.getWidth(), mPortraitPosition.getHeight());

                params.leftMargin = locationArray[0];
                params.topMargin = 2;

                mVideoView.setLayoutParams(params);

                break;
            }
        }
    }

    @Override
    public void onConfigurationChanged(Configuration newConfig) {
        setVideoViewPosition();
        super.onConfigurationChanged(newConfig);
    }
}
```

效果如下：

<img src="/images/hack14/hack14_2.gif" width="360" height="240"/>

#####扩展阅读：  
http://developer.android.com/guide/topics/resources/runtime-changes.html