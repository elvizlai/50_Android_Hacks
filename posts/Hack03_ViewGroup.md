##技巧3、ViewGroup的使用

#####诉求：如何实现如下图所示的纸牌的堆叠方式并便于维护？
<img src="/images/hack03/hack03_1.png" width="220" height="240"/>

#####可行的方案：使用RelativeLayout 通过添加 magrins来实现效果。

```
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

    <View
        android:layout_width="100dp"
        android:layout_height="150dp"
        android:background="#FF0000"/>

    <View
        android:layout_width="100dp"
        android:layout_height="150dp"
        android:layout_marginLeft="30dp"
        android:layout_marginTop="20dp"
        android:background="#00FF00"/>

    <View
        android:layout_width="100dp"
        android:layout_height="150dp"
        android:layout_marginLeft="60dp"
        android:layout_marginTop="40dp"
        android:background="#0000FF"/>

</RelativeLayout>
```
效果如下：

<img src="/images/hack03/hack03_2.png" width="240" height="400"/>

----
#####技巧：使用ViewGroup来做到相同的效果，而且更加灵活。

这样做的好处有：

* 当需要在不同的activiy中调用时，更容易维护
* 可以通过指定属性的方式来调整ViewGroup中的子控件的位置
* xml更加简洁、更容易理解
* 当需要调整magrin的时候，不需要重新计算每一个子控件的margin

首先来了解一下Android中View的绘制过程：

> 绘制布局（layout）设计两个的过程：测量过程与布局过程。测量过程使用方法measure(int ,int)，自View树顶向下遍历，在循环过程中，每个View都讲其尺寸细节向下传递。测量过程完成后，每一个View都存储了自己的尺寸。第二个过程通过方法layout(int,int,int,int)来实现，且其也是自顶向下的遍历。这一过程中，每一个父View负责使用测量过程得到的尺寸来摆放其所有的子视图。

View树：

<img src="/images/hack03/hack03_3.png" width="300" height="200"/>

为了理解上述概念，简单分析下ViewGroup的绘制过程：首先使用onMeasure()方法测量宽度与高度。该方法中ViewGroup会自顶向下遍历得到每一个子View的尺寸。然后将最终的尺寸参数将传递给onLayout()方法，该方法中，ViewGroup会按传递过来的尺寸参数来对子View进行摆放。

理解了上述概念，那么就一步步的来实现ViewGroup吧

首先在res/values 文件夹下创建attrs.xml

```
<resources>
    <declare-styleable name="VGT">
        <attr name="horizontal_spacing" format="dimension" />
        <attr name="vertical_spacing" format="dimension" />
    </declare-styleable>
</resources>
```

然后在res/values/demins.xml文件中添加属性：

```
<dimen name="vgt_horizontal_spacing">10dp</dimen>
<dimen name="vgt_vertical_spacing">10dp</dimen>
```

接下来创建一个类来继承ViewGroup，需要实现构造函数及覆写

```
package com.elvizlai.test;

import android.content.Context;
import android.content.res.TypedArray;
import android.util.AttributeSet;
import android.view.View;
import android.view.ViewGroup;

/**
 * Created by elvizlai on 14-7-25.
 */
public class VGT extends ViewGroup {
    private int mHorizontalSpacing;
    private int mVerticalSpacing;

    /**
     * 构造方法
     */
    public VGT(Context context, AttributeSet attrs) {
        super(context, attrs);
        TypedArray a = context.obtainStyledAttributes(attrs, R.styleable.VGT);
        try {
            mHorizontalSpacing = a.getDimensionPixelSize(R.styleable.VGT_horizontal_spacing, getResources().getDimensionPixelSize(R.dimen.vgt_horizontal_spacing));
            mVerticalSpacing = a.getDimensionPixelSize(R.styleable.VGT_vertical_spacing, getResources().getDimensionPixelSize(R.dimen.vgt_vertical_spacing));
        } finally {
            a.recycle();
        }
    }

    /**
     * 布局参数设定内部类
     */
    public static class LayoutParams extends ViewGroup.LayoutParams {
        int x, y;
        //添加以下代码，用于实现子属性位置的自定义
        public int verticalSpacing;

        public LayoutParams(Context c, AttributeSet attrs) {
            super(c, attrs);

			//添加以下代码，用于实现子属性位置的自定义
            TypedArray a = c.obtainStyledAttributes(attrs,
                    R.styleable.VGT_LayoutParams);
            try {
                verticalSpacing = a.getDimensionPixelSize(R.styleable.VGT_LayoutParams_layout_vertical_spacing, -1);
            } finally {
                a.recycle();
            }
            
        }

        public LayoutParams(int w, int h) {
            super(w, h);
        }
    }

    /**
     * 测量方法
     */
    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        int width = getPaddingLeft();
        int height = getPaddingTop();
        //添加以下代码，用于实现子属性位置的自定义
        int verticalSpacing;

        final int count = getChildCount();
        for (int i = 0; i < count; i++) {
        	//添加以下代码，用于实现子属性位置的自定义
            verticalSpacing = mVerticalSpacing;

            View child = getChildAt(i);
            measureChild(child, widthMeasureSpec, heightMeasureSpec);
            LayoutParams lp = (LayoutParams) child.getLayoutParams();
            width = getPaddingLeft() + mHorizontalSpacing * i;

            lp.x = width;
            lp.y = height;

			//添加以下代码，用于实现子属性位置的自定义
            if (lp.verticalSpacing >= 0) {
                verticalSpacing = lp.verticalSpacing;
            }

            width += child.getMeasuredWidth();
            //修改以下代码，用于实现子属性位置的自定义
            height += verticalSpacing;
        }

        width += getPaddingRight();
        height += getChildAt(getChildCount() - 1).getMeasuredHeight() + getPaddingBottom();
        setMeasuredDimension(resolveSize(width, widthMeasureSpec), resolveSize(height, heightMeasureSpec));
    }

    /**
     * 用来实现布局的方法
     */
    @Override
    protected void onLayout(boolean changed, int l, int t, int r, int b) {
        final int count = getChildCount();
        for (int i = 0; i < count; i++) {
            View child = getChildAt(i);
            LayoutParams lp = (LayoutParams) child.getLayoutParams();
            child.layout(lp.x, lp.y, lp.x + child.getMeasuredWidth(), lp.y + child.getMeasuredHeight());
        }
    }

    /**
     * 以下4个方法必须覆写，否则会提示java.lang.ClassCastException
     */

    @Override
    protected boolean checkLayoutParams(ViewGroup.LayoutParams p) {
        return p instanceof LayoutParams;
    }

    @Override
    protected LayoutParams generateDefaultLayoutParams() {
        return new LayoutParams(LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT);
    }

    @Override
    protected LayoutParams generateLayoutParams(ViewGroup.LayoutParams p) {
        return new LayoutParams(p.width, p.height);
    }

    @Override
    public LayoutParams generateLayoutParams(AttributeSet attrs) {
        return new LayoutParams(getContext(), attrs);
    }

}


```

然后就可以在xml中使用啦
```
<!--本程序在Android Studio下书写，若使用eclipse，可能第二行要改为 
  xmlns:custom="http://schemas.android.com/apk/res/包名"-->

<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:custom="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <com.elvizlai.test.VGT
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        custom:horizontal_spacing="30dp"
        custom:vertical_spacing="20dp">

        <View
            android:layout_width="100dp"
            android:layout_height="150dp"
            android:background="#FF0000"/>

        <View
            android:layout_width="100dp"
            android:layout_height="150dp"
            android:background="#00FF00"/>

        <View
            android:layout_width="100dp"
            android:layout_height="150dp"
            android:background="#0000FF"/>

    </com.elvizlai.test.VGT>

</FrameLayout>
```
效果与前面通过RelativeLayout + margins的相同，不再上图。

再来看下如何给子View自定义属性

首先在attrs.xml文件中添加一项新的属性：
```
    <declare-styleable name="VGT_LayoutParams">
        <attr name="layout_vertical_spacing" format="dimension" />
    </declare-styleable>
```

按照上文的注释修改VGT,在View中可以添加属性

```
        <View
            android:layout_width="100dp"
            android:layout_height="150dp"
            android:background="#FF0000"
            custom:layout_vertical_spacing="90dp"/>
```

效果如下：

<img src="/images/hack03/hack03_4.png" width="240" height="400"/>

#####扩展阅读：  
http://developer.android.com/guide/topics/ui/how-android-draws.html   http://developer.android.com/reference/android/view/ViewGroup.html   http://developer.android.com/reference/android/view/ViewGroup.LayoutParams.html