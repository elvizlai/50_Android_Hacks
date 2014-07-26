##技巧5、TextSwitcher的使用

#####诉求：如何使Text或Image的切换不再变的那么生硬？诸如：

* 切换可以滑动的日期列表
* 通过时间选择器改变时间
* 翻页时钟
* 新闻标题

#####方案：使用TextSwitcher

原理：当调用setText()方法时，先执行通过setOutAnimation()设定的淡出动画移除原来的view，然后再执行通过setInAnimation()设定的载入动画载入新的view

代码：

```
package com.elvizlai.test;

import android.app.Activity;
import android.os.Bundle;
import android.view.Gravity;
import android.view.View;
import android.view.animation.Animation;
import android.view.animation.AnimationUtils;
import android.widget.Button;
import android.widget.TextSwitcher;
import android.widget.TextView;
import android.widget.ViewSwitcher;

import java.util.Date;


public class MyActivity extends Activity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my);

        Animation in = AnimationUtils.loadAnimation(this, android.R.anim.fade_in);
        Animation out = AnimationUtils.loadAnimation(this, android.R.anim.fade_out);

        final TextSwitcher mytest = (TextSwitcher) findViewById(R.id.mytext);

        mytest.setFactory(new ViewSwitcher.ViewFactory() {
            @Override
            public View makeView() {
                TextView t = new TextView(MyActivity.this);
                t.setGravity(Gravity.CENTER);
                return t;
            }
        });
        //设置动画
        mytest.setOutAnimation(out);
        mytest.setInAnimation(in);

        Button button = (Button) findViewById(R.id.mybutton);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                mytest.setText(new Date() + "");
            }
        });
    }
}

```

#####扩展阅读：  
http://developer.android.com/reference/android/widget/TextSwitcher.html  
http://developer.android.com/guide/topics/graphics/view-animation.html