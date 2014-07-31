##技巧13、在onCreate()方法中获取View的宽高属性

#####诉求：如何在onCreate()中获取view的宽度与高度？为什么直接调用view.getWidth()方法得到的确是0？在onResume()方法中调用也是如此。

#####技巧：使用view.post(Runnable)或使用View的测量方法。

首先来介绍下布局绘制的过程：

> 这部分在[Hack03](https://github.com/ElvizLai/50_Android_Hacks/blob/master/posts/Hack03_ViewGroup.md)已经介绍过了，请直接参考，不再赘述。

当onCreate()方法完全结束后，布局开始进行，此时View才能得到相应的高度与宽度。

使用post方案，直接看代码：

```
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.measurement);
        mTextView = (TextView) findViewById(R.id.measure);

		//视图加载完成后才会执行
        mTextView.post(new Runnable() {
            @Override
            public void run() {
                System.out.println("H:"+mTextView.getHeight() + " W:" + mTextView.getWidth());
            }
        });
    }
```

使用测量方案：

```
        int  width =View.MeasureSpec.makeMeasureSpec(0, View.MeasureSpec.UNSPECIFIED);
        int height =View.MeasureSpec.makeMeasureSpec(0,View.MeasureSpec.UNSPECIFIED);

        mTextView.measure(width,height);

        height = mTextView.getMeasuredHeight();
        width = mTextView.getMeasuredWidth();
```