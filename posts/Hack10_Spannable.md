##技巧10、使用Spannable扩展TextView的样式

#####诉求：当需要在TextView中展示混合样式时如何实现？

#####技巧：使用Html格式的文字，用方法Html.formHtml()或者使用Spannable来加强其显示内容

代码：

```
package com.elvizlai.test;

import android.app.Activity;
import android.graphics.Color;
import android.os.Bundle;
import android.text.Html;
import android.text.Spannable;
import android.text.SpannableString;
import android.text.method.LinkMovementMethod;
import android.text.style.BackgroundColorSpan;
import android.text.style.ForegroundColorSpan;
import android.widget.TextView;


public class MyActivity extends Activity {

    private TextView mTextView1, mTextView2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my);
        mTextView1 = (TextView) findViewById(R.id.my_text_view_html);
        String text = "Visit <a href=\"http://1024coder.com/\"> ElvizLai's home page</a>";
        mTextView1.setText(Html.fromHtml(text));
        mTextView1.setMovementMethod(LinkMovementMethod.getInstance());


        mTextView2 = (TextView) findViewById(R.id.my_text_view_spannable);
        Spannable sText = new SpannableString(mTextView2.getText());
        //mTextView2.getText()得到的文字为：ElvizLais

        //第1个字符以后（从第2个算起）到第4个字符，背景色为红色
        sText.setSpan(new BackgroundColorSpan(Color.RED), 1, 4, 0);
        //第5个字符以后（从第6个算起）到第9个字符，字体颜色为蓝色
        sText.setSpan(new ForegroundColorSpan(Color.BLUE), 5, 9, 0);
        mTextView2.setText(sText);
    }
}
```

效果如下：

<img src="/images/hack10/hack10_1.png" width="537" height="95"/>

#####扩展阅读：  
http://developer.android.com/reference/android/text/Spannable.html