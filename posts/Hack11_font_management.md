##技巧11、载入自定义字体

#####诉求：如何实现电子表的计时特效？

#####技巧：使用RelativeLayout布局及新建类LedTextView来继承TextView，通过在其中载入设置字体的方式来完成这一特效。

首先在main目录下新建文件夹assets，其中再新建一个文件夹fonts，并将字体[digital-7.ttf](/images/hack11/fonts)拷贝到该文件夹下。如下图所示：

<img src="/images/hack11/hack11_1.png" width="246" height="496"/>

先创建一个类LedTextView使其继承TextView

```
package com.elvizlai.test;

import android.content.Context;
import android.content.res.AssetManager;
import android.graphics.Typeface;
import android.util.AttributeSet;
import android.widget.TextView;

/**
 * Created by ElvizLai on 14-7-29.
 */
public class LedTextView extends TextView {

    //字体路径
    private static final String FONT_DIGITAL_7 = "fonts/digital-7.ttf";

    public LedTextView(Context context) {
        super(context);
        init(context);
    }

    public LedTextView(Context context, AttributeSet attrs) {
        super(context, attrs);
        init(context);
    }

    public LedTextView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        init(context);
    }

	//该发放的作用是设置TextView的字体
    private void init(Context context) {
        AssetManager assets = context.getAssets();
        final Typeface font = Typeface.createFromAsset(assets, FONT_DIGITAL_7);
        setTypeface(font);
    }
}
```

然后在xml文件中调用字体：

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:background="@android:color/black"
                android:orientation="vertical">

    <com.elvizlai.test.LedTextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="@string/default_time"
        android:textColor="#3300ff00"
        android:textSize="80sp"/>

    <com.elvizlai.test.LedTextView
        android:id="@+id/main_clock_time"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:shadowColor="#00ff00"
        android:shadowDx="0"
        android:shadowDy="0"
        android:shadowRadius="10"
        android:textColor="#00ff00"
        android:textSize="80sp"/>

</RelativeLayout>
```

在Activity中使用Handler的方式定时更新TextView的时间：

```
package com.elvizlai.test;

import android.app.Activity;
import android.os.Bundle;
import android.os.Handler;
import android.widget.TextView;

import java.util.Date;


public class MyActivity extends Activity {
    private TextView mTextView;
    private static final String DATE_FORMAT = "%02d:%02d:%02d";
    private static final int REFRESH_DELAY = 1000;
    //
    private final Handler mHandler = new Handler();
    //刷新事件
    private final Runnable mTimeRefresher = new Runnable() {
        @Override
        public void run() {
            final Date d = new Date();
            mTextView.setText(String.format(DATE_FORMAT, d.getHours(), d.getMinutes(), d.getSeconds()));
            //每隔1秒该方法就会被触发一次
            mHandler.postDelayed(this, REFRESH_DELAY);
        }
    };


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my);
        mTextView = (TextView) findViewById(R.id.main_clock_time);
    }

    //初始化及恢复应用焦点后时间开始走动
    @Override
    protected void onResume() {
        super.onResume();
        mHandler.post(mTimeRefresher);
    }

    //最小化后时间停止走动
    @Override
    protected void onStop() {
        super.onStop();
        mHandler.removeCallbacks(mTimeRefresher);
    }
}
```

效果如下：

<img src="/images/hack11/hack11_2.gif" width="360" height="240"/>

#####扩展阅读：  
http://www.styleseven.com  
http://developer.android.com/reference/android/os/Handler.html  
https://developer.android.com/training/multiple-threads/define-runnable.html