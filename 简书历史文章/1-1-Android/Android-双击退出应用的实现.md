## 1 需求效果
为了防止用户点击返回键就直接退出APP，通常会加入一个双击退出的要求。

如果用户在两秒之内重复点击了返回键，则执行退出操作；如果用户点击了一次返回键之后，超过两秒未再次点击，则不响应退出事件。效果图如下：

![BottomNavigationView.gif](http://upload-images.jianshu.io/upload_images/2551993-c18922ea06023b21.gif?imageMogr2/auto-orient/strip)

## 2 需求分析
实现这种需求的整体思路就是监测返回键，然后监测是否在指定时间之内再次点击了返回键。

###（1）监测返回键有三种方式，分别如下：
>* onBackPressed( ) 
>* onKeyDown( )
>* onKeyUp( ) 

###（2）控制指定时间有两种方式：
>* 使用 Timer（）计时器，超出指定时间之后销毁第一次的点击事件
>* 手动记录点击的时间-- System.currenTimeMillis()，判断两次点击的时间是否超过指定时间

## 3 代码实现
综上：整体的实现方式就有六种，分别如下：
###(1)第一种办法
响应Activity的 onKeyUp事件,两次点击时间大于2秒钟就不响应，小于2秒钟就退出程序 
代码：

```
    //记录用户首次点击返回键的时间
    private long firstTime = 0;

     /**
     * 第一种解决办法 通过监听keyUp
     * @param keyCode
     * @param event
     * @return
     */
    @Override
    public boolean onKeyUp(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK && event.getAction() == KeyEvent.ACTION_UP) {
            long secondTime = System.currentTimeMillis();
            if (secondTime - firstTime > 2000) {
                Toast.makeText(MainActivity.this, "再按一次退出程序", Toast.LENGTH_SHORT).show();
                firstTime = secondTime;
                return true;
            } else {
               finish();
            }
        }

        return super.onKeyUp(keyCode, event);
    }
```

###(2)第二种办法
和第一种办法一样，这不过这次响应onKeyDown事件 
代码：
```
 /**
     * 第二种办法
     * @param keyCode
     * @param event
     * @return
     */
    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK && event.getAction() == KeyEvent.ACTION_DOWN) {
            long secondTime = System.currentTimeMillis();
            if (secondTime - firstTime > 2000) {
                Toast.makeText(MainActivity.this, "再按一次退出程序", Toast.LENGTH_SHORT).show();
                firstTime = secondTime;
                return true;
            } else{
                finish();
            }
        }
        return super.onKeyDown(keyCode, event);
    }
```

###(3)第三种办法
直接重写onBackPressed()方法 
代码：
```
 /**
     * 第三种方法
     */
    @Override
    public void onBackPressed() {
        long secondTime = System.currentTimeMillis();
        if (secondTime - firstTime > 2000) {
            Toast.makeText(MainActivity.this, "再按一次退出程序", Toast.LENGTH_SHORT).show();
            firstTime = secondTime;
        } else{
            finish();
        }
}
```

###(4)第四种办法

通过线程延时的办法实现，借助Timer 
代码：
```
/**
     * 第四种方法
     */
    @Override
    public void onBackPressed() {
        if (!mBackKeyPressed) {
            Toast.makeText(this, "再按一次退出程序", Toast.LENGTH_SHORT).show();
            mBackKeyPressed = true;
            new Timer().schedule(new TimerTask() {
                @Override
                public void run() {
                    mBackKeyPressed = false;
                }
            }, 2000);
        } else {
          finish();
        }
    }
```

###(5)第五种办法 
在onKeyUp()方法内使用Timer
```
     /**
     * 第五种办法
     * @param keyCode
     * @param event
     * @return
     */
    @Override
    public boolean onKeyUp(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK && event.getAction() == KeyEvent.ACTION_UP) {
            if (!mBackKeyPressed) {
                Toast.makeText(this, "再按一次退出程序", Toast.LENGTH_SHORT).show();
                mBackKeyPressed = true;
                new Timer().schedule(new TimerTask() {
                    @Override
                    public void run() {
                        mBackKeyPressed = false;
                    }
                }, 2000);
                return true;
            } else {
               finish();
            }
        }
        return super.onKeyUp(keyCode, event);
    }
```

###(6)第六种办法 
在onKeyDown()方法内使用Timer
```
 /**
     * 第六种办法
     * @param keyCode
     * @param event
     * @return
     */
    @Override
    public boolean onKeyDown(int keyCode, KeyEvent event) {
        if (keyCode == KeyEvent.KEYCODE_BACK && event.getAction() == KeyEvent.ACTION_DOWN) {
            if (!mBackKeyPressed) {
                Toast.makeText(this, "再按一次退出程序", Toast.LENGTH_SHORT).show();
                mBackKeyPressed = true;
                new Timer().schedule(new TimerTask() {
                    @Override
                    public void run() {
                        mBackKeyPressed = false;
                    }
                }, 2000);
                return true;
            } else {
                finish();
            }
        }
        return super.onKeyDown(keyCode, event);
    }
```


