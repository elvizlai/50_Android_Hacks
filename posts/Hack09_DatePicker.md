##技巧9、DatePicker的使用

#####诉求：在EditText中输入时间参数总需要判定其格式等问题

#####技巧：是用DatePicker来统一输入接口

```
package com.elvizlai.test;

import android.app.Activity;
import android.app.DatePickerDialog;
import android.app.Dialog;
import android.os.Bundle;
import android.view.View;
import android.widget.DatePicker;
import android.widget.Toast;

import java.util.Calendar;


public class MyActivity extends Activity {

    private static final int DATE_DIALOG_ID = 0;

    private int mYear;
    private int mMonth;
    private int mDay;
    private DatePickerDialog.OnDateSetListener mDateSetListener;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my);

        // 获取当前的日期
        final Calendar c = Calendar.getInstance();
        mYear = c.get(Calendar.YEAR);
        mMonth = c.get(Calendar.MONTH);
        mDay = c.get(Calendar.DAY_OF_MONTH);

		//选定时间点击确定按钮时回调该方法
        mDateSetListener = new DatePickerDialog.OnDateSetListener() {

            @Override
            public void onDateSet(DatePicker view, int year, int monthOfYear, int dayOfMonth) {
                //<string name="picked_date_format">日期： %1$d年 %2$d月 %3$d日</string>
                Toast.makeText(MyActivity.this, getString(R.string.picked_date_format, year, monthOfYear + 1, dayOfMonth), Toast.LENGTH_SHORT).show();
            }
        };

        findViewById(R.id.details_date).setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                showDialog(DATE_DIALOG_ID);
            }
        });
    }

    @Override
    protected Dialog onCreateDialog(int id) {
        switch (id) {
            case DATE_DIALOG_ID:
                return new DatePickerDialog(this, mDateSetListener, mYear,
                        mMonth, mDay);
        }
        return super.onCreateDialog(id);
    }
}
```

效果如下：

<img src="/images/hack09/hack09_1.gif" width="480" height="360"/>

#####扩展阅读：  
http://developer.android.com/reference/android/widget/DatePicker.html  
http://developer.android.com/reference/android/widget/EditText.html
