##技巧7、Canvas的使用

#####诉求：是否有可以直接在屏幕中绘制图形的API？

#####技巧：使用Canvas

简单的介绍一下Canvas：

> Canvas作为一个接口，在你将要绘制的界面之上，所有的绘画调用指令都由它来处理。通过Canvas，绘制的图形展现在窗口中的潜在Bitmap之上。

首先创建一个类Rectangle来继承View，用于绘制矩形：

```
package com.elvizlai.test;

import android.content.Context;
import android.graphics.Canvas;
import android.graphics.Paint;
import android.graphics.RectF;
import android.view.View;

/**
 * Created by lyz on 14-7-27.
 */
public class Rectangle extends View {
    public static final int MAX_SIZE = 40;
    private static final int ALPHA = 255;

    private int mCoordX = 0;
    private int mCoordY = 0;
    private int mRealSize = 80;
    private int mSpeedX = 3;
    private int mSpeedY = 3;

    private boolean goRight = true;
    private boolean goDown = true;
    private DrawView mDrawView;

    private Paint mInnerPaint;
    private RectF mDrawRect;

    public Rectangle(Context context,DrawView drawView){
        super(context);
        mDrawView = drawView;
        mInnerPaint = new Paint();

        mDrawRect = new RectF();

        mInnerPaint.setARGB(ALPHA,255,0,0);
        mInnerPaint.setAntiAlias(true);
    }

    public void setARGB(int a,int r,int g,int b){
        mInnerPaint.setARGB(a,r,g,b);
    }

    public void move() {
        moveTo(mSpeedX, mSpeedY);
    }

    private void moveTo(int goX, int goY) {

        // check the borders, and set the direction if a border has reached
        if (mCoordX > (mDrawView.width - MAX_SIZE)) {
            goRight = false;
        }

        if (mCoordX < 0) {
            goRight = true;
        }

        if (mCoordY > (mDrawView.height - MAX_SIZE)) {
            goDown = false;
        }
        if (mCoordY < 0) {
            goDown = true;
        }

        // move the x and y
        if (goRight) {
            mCoordX += goX;
        } else {
            mCoordX -= goX;
        }
        if (goDown) {
            mCoordY += goY;
        } else {
            mCoordY -= goY;
        }
    }

    public void setSpeedX(int speedX) {
        mSpeedX = speedX;
    }

    public void setSpeedY(int speedY) {
        mSpeedY = speedY;
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);

        mDrawRect.set(mCoordX, mCoordY, mCoordX + mRealSize, mCoordY
                + mRealSize);
        canvas.drawRoundRect(mDrawRect, 0, 0, mInnerPaint);

    }

}

```

新建类DrawView继承View用于在画布中添加上面的矩形方块：

```
package com.elvizlai.test;

import android.content.Context;
import android.graphics.Canvas;
import android.view.View;

/**
 * Created by lyz on 14-7-27.
 */
public class DrawView extends View {
    private Rectangle mRectangle;
    public int width;
    public int height;

    public DrawView(Context context) {
        super(context);

        mRectangle = new Rectangle(context, this);
        mRectangle.setARGB(255, 255, 0, 0);
        mRectangle.setSpeedX(3);
        mRectangle.setSpeedY(3);
    }

    @Override
    protected void onDraw(Canvas canvas) {
        invalidate();

        mRectangle.move();
        mRectangle.draw(canvas);
    }
}

```

在Activity的onCreate中将上面的画布添加到其中：

```
package com.elvizlai.test;

import android.app.Activity;
import android.os.Bundle;
import android.view.Display;

public class MyActivity extends Activity {
    private DrawView mDrawView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Display display = getWindowManager().getDefaultDisplay();
        mDrawView = new DrawView(this);
        mDrawView.height = display.getHeight();
        mDrawView.width = display.getWidth();

        setContentView(mDrawView);
    }
}

```

效果如下：

<img src="/images/hack07/hack07_1.gif" width="480" height="360"/>


#####扩展阅读：  
http://developer.android.com/reference/android/graphics/Canvas.html  
http://developer.android.com/guide/topics/graphics/2d-graphics.html