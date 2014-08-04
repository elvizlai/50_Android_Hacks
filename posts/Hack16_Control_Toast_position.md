##技巧16、控制Toast的位置

#####诉求：使用Toast通知的时候，每次都是出现在屏幕中下方，感觉很无味，如何设置其出现的位置呢？

#####技巧：使用setGravity来控制位置

先回顾下Toast的常规用法：

```
Toast.makeText(this,"Hi!",Toast.LENGTH_SHORT).show();
```

然后其就会在屏幕的种下方弹出提示消息

但是使用方法setGravity(int gravitt,int xOffset,int yOffset)来控制其出现的位置，比如右下方可以这样：

```
Toast toast = Toast.makeText(this,"Hi!",Toast.LENGTH_SHORT);

toast.setGravity(Gravity.BOTTOM | Gravity.RIGHT, 0, 0);

toast.show();
```

这样，Toast提示就会出现在右下角了。

#####扩展阅读：  
http://developer.android.com/guide/topics/ui/notifiers/toasts.html
