##技巧8、使用Ken Burns effect切换特效

#####诉求：如何让照片切换时实现Ken Burns effect的效果？

#####技巧：

```
package com.elvizlai.test;

import android.app.Activity;
import android.os.Bundle;
import android.view.ViewGroup.LayoutParams;
import android.widget.FrameLayout;
import android.widget.ImageView;

import com.nineoldandroids.animation.Animator.AnimatorListener;
import com.nineoldandroids.animation.AnimatorSet;
import com.nineoldandroids.animation.ObjectAnimator;
import com.nineoldandroids.view.animation.AnimatorProxy;

import java.util.Random;

public class MyActivity extends Activity implements AnimatorListener {
	private AnimatorSet anim;
    private FrameLayout mContainer;
    private ImageView mView;
   	//用于随机选择切换动画
    private Random mRandom = new Random();
	//一共提供4种切换动画
    private static final int ANIM_COUNT = 4;
    //照片的索引
    private int mIndex = 0;
    private static final int[] PHOTOS = new int[]{R.drawable.photo1,
            R.drawable.photo2, R.drawable.photo3, R.drawable.photo4,
            R.drawable.photo5, R.drawable.photo6};
	private boolean isAnimStop = false;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        mContainer = new FrameLayout(this);
        mContainer.setLayoutParams(new LayoutParams(LayoutParams.MATCH_PARENT, LayoutParams.MATCH_PARENT));

        mView = createNewView();
        mContainer.addView(mView);

        setContentView(mContainer);


    }

	//创建一副ImageView
    private ImageView createNewView() {
        ImageView ret = new ImageView(this);
        ret.setLayoutParams(new LayoutParams(LayoutParams.MATCH_PARENT, LayoutParams.MATCH_PARENT));

        ret.setScaleType(ImageView.ScaleType.FIT_XY);
        ret.setImageResource(PHOTOS[mIndex]);

        //mIndex = (mIndex + 1 < PHOTOS.length) ? mIndex + 1 : 0;
        mIndex = ++mIndex % PHOTOS.length;

        return ret;
    }

	//用于最小化后，重新载入程序的恢复
    @Override
    protected void onResume() {
        super.onResume();
        isAnimStop = false;
        nextAnimation();
    }

	//activity不在最前时停止动画
    @Override
    protected void onPause() {
        super.onPause();
        anim.cancel();//会被onAnimationCancel()监听到
    }

    private void nextAnimation() {
        anim = new AnimatorSet();

        final int index = mRandom.nextInt(ANIM_COUNT);

        switch (index) {
            case 0:
                anim.playTogether(
                        ObjectAnimator.ofFloat(mView, "scaleX", 1.5f, 1f),
                        ObjectAnimator.ofFloat(mView, "scaleY", 1.5f, 1f));
                break;

            case 1:
                anim.playTogether(
                        ObjectAnimator.ofFloat(mView, "scaleX", 1, 1.5f),
                        ObjectAnimator.ofFloat(mView, "scaleY", 1, 1.5f));
                break;

            case 2:
                AnimatorProxy.wrap(mView).setScaleX(1.5f);
                AnimatorProxy.wrap(mView).setScaleY(1.5f);
                anim.playTogether(ObjectAnimator.ofFloat(mView,
                        "translationY", 80f, 0f));
                break;

            case 3:
            default:
                AnimatorProxy.wrap(mView).setScaleX(1.5f);
                AnimatorProxy.wrap(mView).setScaleY(1.5f);
                anim.playTogether(ObjectAnimator.ofFloat(mView,
                        "translationX", 0f, 40f));
                break;
        }

        anim.setDuration(3000);
        anim.addListener(this);
        anim.start();

    }

    @Override
    public void onAnimationStart(com.nineoldandroids.animation.Animator animation) {

    }

	//监听到动画结束，执行下面代码，移除旧图，添加新图，添加新的动画效果
    @Override
    public void onAnimationEnd(com.nineoldandroids.animation.Animator animation) {
        if (isAnimStop)
            return;
        mContainer.removeView(mView);
        mView = createNewView();
        mContainer.addView(mView);
        nextAnimation();
    }

	//用于监听到cancle，做相应的停止标记，根据API，该方法执行完后会执行onAnimationEnd()
    @Override
    public void onAnimationCancel(com.nineoldandroids.animation.Animator animation) {
		isAnimStop = true;
    }

    @Override
    public void onAnimationRepeat(com.nineoldandroids.animation.Animator animation) {

    }
}

```

效果如下：

<img src="/images/hack08/hack08_1.gif" width="480" height="360"/>


#####扩展阅读：  
http://en.wikipedia.org/wiki/Ken_Burns_effect  
https://github.com/JakeWharton/NineOldAndroids  
http://android-developers.blogspot.com.ar/2011/05/introducing-viewpropertyanimator.html  
http://android-developers.blogspot.com.ar/2011/02/animation-in-honeycomb.html  


