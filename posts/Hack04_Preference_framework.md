##技巧4、Preferences框架

#####诉求：如何快速构建一个配置界面？

#####技巧：使用Preferences框架来快速构建

首先在res文件夹下新建xml文件夹，并在其中新建prefs.xml文件

```
<!--android:key用来在activity中使用findPreference来取得该控件-->
<PreferenceScreen xmlns:android="http://schemas.android.com/apk/res/android"
                  android:key="pref_first_preferencescreen_key"
                  android:title="Preferences">

    <PreferenceCategory
        android:title="User">

        <EditTextPreference
            android:key="pref_username"
            android:summary="Username"
            android:title="Username"/>

    </PreferenceCategory>

    <PreferenceCategory
        android:title="Application">
        <Preference
            android:key="pref_rate"
            android:summary="Rate the app in the store!"
            android:title="Rate the app"/>

        <Preference
            android:key="pref_share"
            android:summary="Share the app with your friends"
            android:title="Share it"/>

        <com.elvizlai.test.EmailDialog
            android:dialogIcon="@drawable/ic_launcher"
            android:dialogMessage="Do you want to send an email with feedback?"
            android:dialogTitle="Feedback"
            android:key="pref_sendemail_key"
            android:negativeButtonText="Cancel"
            android:positiveButtonText="OK"
            android:summary="Send your feedback by e-mail"
            android:title="Send Feedback"/>

        <com.elvizlai.test.AboutDialog
            android:dialogIcon="@drawable/ic_launcher"
            android:dialogTitle="About"
            android:key="pref_about_key"
            android:negativeButtonText="@null"
            android:title="About"/>

    </PreferenceCategory>

</PreferenceScreen>
```

新建一个类Pref，让其继承PreferenceActivity并实现OnSharedPreferenceChangeListener接口：

```
package com.elvizlai.test;

import android.content.Intent;
import android.content.SharedPreferences;
import android.net.Uri;
import android.os.Bundle;
import android.preference.EditTextPreference;
import android.preference.Preference;
import android.preference.PreferenceActivity;

/**
 * Created by ElvizLai on 14-7-25.
 */
public class Pref extends PreferenceActivity implements SharedPreferences.OnSharedPreferenceChangeListener {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        //载入Preference， 相当于setContentView()
        addPreferencesFromResource(R.xml.prefs);

        //获取到key为pref_rate的Preference
        Preference ratePref = findPreference("pref_rate");
        Uri uri = Uri.parse("market://details?id=" + getPackageName());
        Intent goToMarket = new Intent(Intent.ACTION_VIEW, uri);
        ratePref.setIntent(goToMarket);
    }

    @Override
    protected void onResume() {
        super.onResume();
        getPreferenceScreen().getSharedPreferences().registerOnSharedPreferenceChangeListener(this);
    }

    @Override
    protected void onPause() {
        super.onPause();
        getPreferenceScreen().getSharedPreferences().unregisterOnSharedPreferenceChangeListener(this);
    }

    //当Preference变化，且对应的key为pref_username时，执行updateUserText()方法
    @Override
    public void onSharedPreferenceChanged(SharedPreferences sharedPreferences, String key) {
        if (key.equals("pref_username")) {
            updateUserText();
        }
    }

    private void updateUserText() {
        EditTextPreference pref;
        pref = (EditTextPreference) findPreference("pref_username");
        String user = pref.getText();

        if (user == null) {
            user = "?";
        }
        pref.setSummary(String.format("Username: %s", user));
    }
}
```

新建类LaunchEmailUtil用于实现书写邮件的跳转：

```
package com.elvizlai.test;

import android.content.Context;
import android.content.Intent;

/**
 * Created by ElvizLai on 14-7-25.
 */
public class LaunchEmailUtil {
    public static void launchEmailToIntent(Context context) {
        Intent intent = new Intent(Intent.ACTION_SEND);
        intent.setType("message/rfc822");
        intent.putExtra(Intent.EXTRA_EMAIL, new String[]{"sdrzlyz@126.com"});
        intent.putExtra(Intent.EXTRA_SUBJECT, "[50AH] Feedback");
        intent.putExtra(Intent.EXTRA_TEXT, "Feedback:\n");
        context.startActivity(Intent.createChooser(intent, "Send feedback"));
    }
}
```

新建类EmailDialog继承DialogPreference：

```
package com.elvizlai.test;

import android.content.Context;
import android.content.DialogInterface;
import android.preference.DialogPreference;
import android.util.AttributeSet;

/**
 * Created by ElvizLai on 14-7-25.
 */
public class EmailDialog extends DialogPreference {
    Context mContext;

    public EmailDialog(Context context) {
        this(context, null);
    }

    public EmailDialog(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public EmailDialog(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        mContext = context;
    }

    @Override
    public void onClick(DialogInterface dialog, int which) {
        super.onClick(dialog, which);

		//用于判断点击OK键跳转
        if (DialogInterface.BUTTON_POSITIVE == which) {
            LaunchEmailUtil.launchEmailToIntent(mContext);
        }
    }
}
```

新建一个AboutDialog类继承DialogPreference用于展示关于里面的内容：

```
package com.elvizlai.test;

import android.content.Context;
import android.content.pm.PackageManager;
import android.preference.DialogPreference;
import android.text.Html;
import android.text.method.LinkMovementMethod;
import android.util.AttributeSet;
import android.view.View;
import android.widget.LinearLayout;
import android.widget.TextView;

/**
 * Created by ElvizLai on 14-7-25.
 */
public class AboutDialog extends DialogPreference {
    private Context mContext;
    private String mVersionNumber;

    public AboutDialog(Context context) {
        this(context, null);
    }

    public AboutDialog(Context context, AttributeSet attrs) {
        this(context, attrs, 0);

    }

    public AboutDialog(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);
        mContext = context;
    }

    @Override
    protected View onCreateDialogView() {
        //弹出的对话框为LinearLayout+TextView
        LinearLayout layout = new LinearLayout(mContext);
        layout.setOrientation(LinearLayout.VERTICAL);
        TextView splashText = new TextView(mContext);
        String fmt = " Version %s<br />" + "<a href=\"http://1024coder.com\"> MoreInfo</a>";
        try {
            String pkg = mContext.getPackageName();
            mVersionNumber = mContext.getPackageManager().getPackageInfo(pkg, 0).versionName;
        } catch (PackageManager.NameNotFoundException e) {
            e.printStackTrace();
        }

        if (mVersionNumber != null) {
            String aboutMsg = String.format(fmt, mVersionNumber);
            splashText.setText(Html.fromHtml(aboutMsg));
            splashText.setMovementMethod(LinkMovementMethod.getInstance());
        }

        layout.addView(splashText);
        return layout;
    }
}
```

最终效果如下：

<img src="/images/hack04/hack04_1.png" width="240" height="400"/>

#####扩展阅读：  
http://developer.android.com/reference/android/preference/PreferenceActivity.html