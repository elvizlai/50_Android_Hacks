##技巧6、ViewGroup子视图添加载入动画

#####诉求：当一个view载入时，不是立即呈现，而需要载入一定的动画

#####技巧：通过LayoutAnimationController来实现。

#####方案1：在xml中定义动画，在avtivity中载入：

在res下新建anim文件夹，并创建list_animation.xml文件，内容如下：  
```
<set xmlns:android="http://schemas.android.com/apk/res/android" >

    <!--duration为持续时间，为了演示效果，特意将时间变长-->

    <!--以下为透明度，fromAlpha为初始值，toAlpha为最终值-->
    <alpha
        android:duration="500"
        android:fromAlpha="0"
        android:toAlpha="1" />

    <!--以下为位置转移效果-->

    <!--整型值:-->
    <!--fromXDelta 属性为动画起始时 X坐标上的位置-->
    <!--toXDelta   属性为动画结束时 X坐标上的位置-->
    <!--fromYDelta 属性为动画起始时 Y坐标上的位置-->
    <!--toYDelta   属性为动画结束时 Y坐标上的位置-->
    <!--注意:-->
    <!--没有指定，-->
    <!--默认是以自己为相对参照物-->
    <!--长整型值：-->
    <!--duration  属性为动画持续时间-->
    <!--说明:   时间以毫秒为单位-->
    <!--在这些属性里面还可以加上%和p,例如:-->
    <!--android:toXDelta="100%",表示自身的100%,也就是从View自己的位置开始。-->
    <!--android:toXDelta="80%p",表示父层View的80%,是以它父层View为参照的。-->

    <translate
        android:duration="500"
        android:fromXDelta="-100%"
        android:toXDelta="0%"/>

    <translate
        android:duration="500"
        android:fromYDelta="-100%"
        android:toYDelta="0%" />
</set>
```

在activityz中调用：  
```
package com.elvizlai.test;

import android.app.Activity;
import android.os.Bundle;
import android.view.animation.AnimationUtils;
import android.view.animation.LayoutAnimationController;
import android.widget.ArrayAdapter;
import android.widget.ListView;


public class MyActivity extends Activity {

    String[] countries = new String[]{"Afghanistan", "Albania", "Algeria", "American Samoa", "Andorra",
            "Angola", "Anguilla", "Antarctica", "Antigua and Barbuda",
            "Argentina", "Armenia", "Aruba", "Australia", "Austria",
            "Azerbaijan", "Bahrain", "Bangladesh", "Barbados", "Belarus",
            "Belgium", "Belize", "Benin"};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my);
        ListView listView = (ListView) findViewById(R.id.mylist);
        listView.setLayoutAnimation(new LayoutAnimationController(AnimationUtils.loadAnimation(this, R.anim.list_animation), 0.5f));
        listView.setAdapter(new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, countries));
    }
}

```

#####方案2：直接在代码中定义动画  
```
package com.elvizlai.test;

import android.app.Activity;
import android.os.Bundle;
import android.view.animation.AlphaAnimation;
import android.view.animation.Animation;
import android.view.animation.AnimationSet;
import android.view.animation.AnimationUtils;
import android.view.animation.LayoutAnimationController;
import android.view.animation.TranslateAnimation;
import android.widget.ArrayAdapter;
import android.widget.ListView;


public class MyActivity extends Activity {

    String[] countries = new String[]{"Afghanistan", "Albania", "Algeria", "American Samoa", "Andorra",
            "Angola", "Anguilla", "Antarctica", "Antigua and Barbuda",
            "Argentina", "Armenia", "Aruba", "Australia", "Austria",
            "Azerbaijan", "Bahrain", "Bangladesh", "Barbados", "Belarus",
            "Belgium", "Belize", "Benin"};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_my);
        ListView listView = (ListView) findViewById(R.id.mylist);

        AnimationSet set = new AnimationSet(true);

        //透明度效果
        Animation animation = new AlphaAnimation(0.0f,1.0f);
        animation.setDuration(100);
        set.addAnimation(animation);

        //位置转移效果
        animation = new TranslateAnimation(Animation.RELATIVE_TO_SELF,0.0f,Animation.RELATIVE_TO_SELF,0.0f,Animation.RELATIVE_TO_SELF,-1.0f,Animation.RELATIVE_TO_SELF,0.0f);
        animation.setDuration(100);
        set.addAnimation(animation);

        LayoutAnimationController controller = new LayoutAnimationController(set,0.5f);
        listView.setLayoutAnimation(controller);
        
        listView.setAdapter(new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1, countries));
    }
}
```

效果如下：

<img src="/images/hack06/hack06_1.gif" width="240" height="400"/>

#####扩展阅读：  
http://developer.android.com/reference/android/view/animation/LayoutAnimationController.html
