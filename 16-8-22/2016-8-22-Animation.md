##1 背景 ##
Android最主要的划分可以分为如下几类：

View Animation： 视图动画在古老的Android版本系统中就已经提供了，只能被用来设置View的动画。

Drawable Animation： 这种动画（也叫Frame动画、帧动画）其实可以划分到视图动画的类别，专门用来一个一个的显示Drawable的resources，就像放幻灯片一样。

Property Animation： 属性动画只对Android 3.0（API 11）以上版本的Android系统才有效，这种动画可以设置给任何Object，包括那些还没有渲染到屏幕上的对象。这种动画是可扩展的，可以让你自定义任何类型和属性的动画。

##2 View Animation（视图动画）使用详解 ##
**2-1 视图动画概述**

视图动画，也叫Tween（补间）动画可以在一个视图容器内执行一系列简单变换（位置、大小、旋转、透明度）。譬如，如果你有一个TextView对象，您可以移动、旋转、缩放、透明度设置其文本，当然，如果它有一个背景图像，背景图像会随着文本变化。

补间动画通过XML或Android代码定义，建议使用XML文件定义，因为它更具可读性、可重用性。

如下是视图动画相关的类继承关系
![](http://img.blog.csdn.net/20150613182703583)

![](http://i.imgur.com/5mtqn93.png)

**2-2 视图动画详细说明**

可以看出来Animation抽象类是所有补间动画类的基类，所以基类会提供一些通用的动画属性方法，如下我们就来详细看看这些属性
![](http://i.imgur.com/jVIlkTk.png)

**2-2-2 Alpha属性详解**
![](http://i.imgur.com/jkIbLlq.png)
**2-2-3 Rotate属性详解**
![](http://i.imgur.com/E63Su10.png)
**2-2-4 Scale属性详解**
![](http://i.imgur.com/U6vqiRZ.png)
**2-2-5 Translate属性详解**
![](http://i.imgur.com/AgTe8F9.png)
**2-2-6 AnimationSet详解**

AnimationSet继承自Animation，是上面四种的组合容器管理类，没有自己特有的属性，他的属性继承自Animation，所以特别注意，当我们对set标签使用Animation的属性时会对该标签下的所有子控件都产生影响。

**2-3 视图动画使用方法**

通过上面对于动画的属性介绍之后我们来看看在Android中这些动画如何使用（PS：这里直接演示xml方式，至于Java方式太简单了就不说了），如下：
    
    <?xml version="1.0" encoding="utf-8"?>
    <set xmlns:android="http://schemas.android.com/apk/res/android"
    android:interpolator="@[package:]anim/interpolator_resource"
    android:shareInterpolator=["true" | "false"] >
    <alpha
        android:fromAlpha="float"
        android:toAlpha="float" />
    <scale
        android:fromXScale="float"
        android:toXScale="float"
        android:fromYScale="float"
        android:toYScale="float"
        android:pivotX="float"
        android:pivotY="float" />
    <translate
        android:fromXDelta="float"
        android:toXDelta="float"
        android:fromYDelta="float"
        android:toYDelta="float" />
    <rotate
        android:fromDegrees="float"
        android:toDegrees="float"
        android:pivotX="float"
        android:pivotY="float" />
    <set>
        ...
    </set>
    </set>
 

    ImageView spaceshipImage = (ImageView) findViewById(R.id.spaceshipImage);
    Animation hyperspaceJumpAnimation = AnimationUtils.loadAnimation(this, R.anim.hyperspace_jump);
    spaceshipImage.startAnimation(hyperspaceJumpAnimation);

至于补间动画的使用，Animation还有如下一些比较实用的方法介绍：

![](http://i.imgur.com/GwNgesp.png)

既然补间动画只能给View使用，那就来看看View中和动画相关的几个常用方法吧，如下：

![](http://i.imgur.com/OVReWdL.png)
**2-4 视图动画注意事项**

特别特别注意：补间动画执行之后并未改变View的真实布局属性值。切记这一点，譬如我们在Activity中有一个 Button在屏幕上方，我们设置了平移动画移动到屏幕下方然后保持动画最后执行状态呆在屏幕下方，这时如果点击屏幕下方动画执行之后的Button是没 有任何反应的，而点击原来屏幕上方没有Button的地方却响应的是点击Button的事件。

**2-5 视图动画Interpolator插值器详解**

**2-5-1 插值器简介**

系统提供了许多已经实现OK的插值器，具体如下：
![](http://i.imgur.com/RqXPqTP.png)

**2-5-2 插值器使用方法**

插值器的使用比较简答，如下：

    <set android:interpolator="@android:anim/accelerate_interpolator">
    ...
    </set>

**2-5-3 插值器的自定义**

先看看XML自定义插值器的步骤：

在res/anim/目录下创建filename.xml文件。
修改你准备自定义的插值器如下：    

    <?xml version="1.0" encoding="utf-8"?>
    <InterpolatorName xmlns:android="http://schemas.android.com/apk/res/android"
    android:attribute_name="value"
    />
 

在你的补间动画文件中引用该文件即可。

可以看见上面第二步修改的是现有插值器的一些属性，但是有些插值器却不具备修改属性，具体如下：

> accelerateDecelerateInterpolator
> 
> 无可自定义的attribute。
> 
> accelerateInterpolator
> 
> android:factor 浮点值，加速速率（默认值为1）。
> 
> anticipateInterploator
> 
> android:tension 浮点值，起始点后拉的张力数（默认值为2）。
> 
> anticipateOvershootInterpolator
> 
> android:tension 浮点值，起始点后拉的张力数（默认值为2）。 
> 
> android:extraTension 浮点值，拉力的倍数（默认值为1.5）。
> 
> bounceInterpolator
> 
> 无可自定义的attribute。
> 
> cycleInterplolator
> 
> android:cycles 整形，循环的个数（默认为1）。
> 
> decelerateInterpolator
> 
> android:factor 浮点值，减速的速率（默认为1）。
> 
> linearInterpolator
> 
> 无可自定义的attribute。
> 
> overshootInterpolator
> 
> android:tension 浮点值，超出终点后的张力（默认为2）。

再来看看Java自定义插值器的（Java自定义插值器其实是xml自定义的升级，也就是说如果我们修改xml的属性还不能满足需求，那就可以选择通过Java来实现）方式。

可以看见上面所有的Interpolator都实现了Interpolator接口，而Interpolator接口又继承自 TimeInterpolator，TimeInterpolator接口定义了一个float getInterpolation(float input);方法，这个方法是由系统调用的，其中的参数input代表动画的时间，在0和1之间，也就是开始和结束之间。

如下就是一个动画始末速率较慢、中间加速的AccelerateDecelerateInterpolator插值器：
    
    public class AccelerateDecelerateInterpolator extends BaseInterpolator
        implements NativeInterpolatorFactory {
    ......
    public float getInterpolation(float input) {
        return (float)(Math.cos((input + 1) * Math.PI) / 2.0f) + 0.5f;
    }
    ......
    }

## 3 Drawable Animation（Drawable动画）使用详解 ##
**3-1 Drawable动画概述**

Drawable动画其实就是Frame动画（帧动画），它允许你实现像播放幻灯片一样的效果，这种动画的实质其实是Drawable，所以这种动画的XML定义方式文件一般放在res/drawable/目录下。

**3-2 Drawable动画详细说明**

我们依旧可以使用xml或者java方式实现帧动画。但是依旧推荐使用xml，具体如下：

> <animation-list>必须是根节点，包含一个或者多个<item>元素，属性有：
> 
> 1 android:oneshot true代表只执行一次，false循环执行。
> 
> 2 <item>类似一帧的动画资源。
> 
> <itemanimation-list>的子项，包含属性如下：
> 
> 1 android:drawable 一个frame的Drawable资源。
> 
> 2 android:duration 一个frame显示多长时间。

**3-3 Drawable动画实例演示**
    
    <!-- 注意：rocket.xml文件位于res/drawable/目录下 -->
    <?xml version="1.0" encoding="utf-8"?>
    <animation-list xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot=["true" | "false"] >
    <item
        android:drawable="@[package:]drawable/drawable_resource_name"
        android:duration="integer" />
    </animation-list>
 

    ImageView rocketImage = (ImageView) findViewById(R.id.rocket_image);
    rocketImage.setBackgroundResource(R.drawable.rocket_thrust);
    
    rocketAnimation = (AnimationDrawable) rocketImage.getBackground();
    rocketAnimation.start();

**特别注意，AnimationDrawable的start()方法不能在Activity的onCreate方法中调运，因为AnimationDrawable还未完全附着到window上，所以最好的调运时机是onWindowFocusChanged()方法中。**
## 4 Property Animation（属性动画）使用详解 ##
在使用属性动画之前先来看几个常用的View属性成员：

> translationX，translationY：控制View的位置，值是相对于View容器左上角坐标的偏移。
> 
> rotationX，rotationY：控制相对于轴心旋转。
> 
> x，y：控制View在容器中的位置，即左上角坐标加上translationX和translationY的值。
> 
> alpha：控制View对象的alpha透明度值

**4-1 属性动画概述**

属性动画实现原理就是修改控件的属性值实现的动画

![](http://i.imgur.com/faqvKGp.png)
所以可以看见，我们平时使用属性动画的重点就在于AnimatorSet、ObjectAnimator、TimeAnimator、ValueAnimator。所以接下来我们就来依次说说如何使用。

**4-2 属性动画详细说明**

**4-2-1 属性动画计算原理**

Duration：动画的持续时间；
> 1 TimeInterpolation：定义动画变化速率的接口，所有插值器都必须实现此接口，如线性、非线性插值器；
> 
> 2 TypeEvaluator：用于定义属性值计算方式的接口，有int、float、color类型，根据属性的起始、结束值和插值一起计算出当前时间的属性值；
> 
> 3 Animation sets：动画集合，即可以同时对一个对象应用多个动画，这些动画可以同时播放也可以对不同动画设置不同的延迟；
> 
> 4 Frame refreash delay：多少时间刷新一次，即每隔多少时间计算一次属性值，默认为10ms，最终刷新时间还受系统进程调度与硬件的影响；
> 
> 5 Repeat Country and behavoir：重复次数与方式，如播放3次、5次、无限循环，可以让此动画一直重复，或播放完时向反向播放；


**4-2-2 XML方式属性动画**

在xml中可直接用的属性动画节点有ValueAnimator、ObjectAnimator、AnimatorSet。如下是官方的一个例子和解释:
    
    <set
      android:ordering=["together" | "sequentially"]>

    <objectAnimator
        android:propertyName="string"
        android:duration="int"
        android:valueFrom="float | int | color"
        android:valueTo="float | int | color"
        android:startOffset="int"
        android:repeatCount="int"
        android:repeatMode=["repeat" | "reverse"]
        android:valueType=["intType" | "floatType"]/>

    <animator
        android:duration="int"
        android:valueFrom="float | int | color"
        android:valueTo="float | int | color"
        android:startOffset="int"
        android:repeatCount="int"
        android:repeatMode=["repeat" | "reverse"]
        android:valueType=["intType" | "floatType"]/>

    <set>
        ...
    </set>
    </set>

set属性解释：
![](http://i.imgur.com/jkwESxy.png)
objectAnimator属性解释：
![](http://i.imgur.com/7GNsN82.png)
![](http://i.imgur.com/CwNZyy4.png)
objectAnimator属性解释： 
同上<objectAnimator>属性，不多介绍。

XML属性动画使用方法：

    AnimatorSet set = (AnimatorSet) AnimatorInflater.loadAnimator(myContext,
    R.animtor.property_animator);
    set.setTarget(myObject);
    set.start();

**4-2-3 Java方式属性动画**

> **1、ObjectAnimator：**继承自ValueAnimator，允许你指定要进行动画的对象以及该对象 的一个属性。该类会根据计算得到的新值自动更新属性。大多数的情况使用ObjectAnimator就足够了，因为它使得目标对象动画值的处理过程变得足 够简单，不用像ValueAnimator那样自己写动画更新的逻辑，但是ObjectAnimator有一定的限制，比如它需要目标对象的属性提供指定 的处理方法（譬如提供getXXX，setXXX方法），这时候你就需要根据自己的需求在ObjectAnimator和ValueAnimator中看 哪种实现更方便了。
> 
> ObjectAnimator类提供了ofInt、ofFloat、ofObject这个三个常用的方法，这些方法都是设置动画作用的元素、属性、开始、结束等任意属性值。当属性值（上面方法的参数）只设置一个时就把通过getXXX反射获取的值作为起点，设置的值作为终点；如果设置两个（参数），那么一个是开始、另一个是结束。
> 
> 特别注意：ObjectAnimator的动画原理是不停的调用setXXX方法更新属性值，所有使用ObjectAnimator更新属性时的前提是Object必须声明有getXXX和setXXX方法。
> 我们通常使用ObjectAnimator设置View已知的属性来生成动画，而一般View已知属性变化时都会主动触发重绘图操作，所以动画会自 动实现；但是也有特殊情况，譬如作用Object不是View，或者作用的属性没有触发重绘，或者我们在重绘时需要做自己的操作，那都可以通过如下方法手 动设置：

    ObjectAnimator mObjectAnimator= ObjectAnimator.ofInt(view, "customerDefineAnyThingName", 0,  1).setDuration(2000);
    mObjectAnimator.addUpdateListener(new AnimatorUpdateListener()
        {
            @Override
            public void onAnimationUpdate(ValueAnimator animation)
            {
                //int value = animation.getAnimatedValue();  可以获取当前属性值
                //view.postInvalidate();  可以主动刷新
                //view.setXXX(value);
                //view.setXXX(value);
                //......可以批量修改属性
            }
        });
 

如下是一个我在项目中的Y轴3D旋转动画实现实例：

    ObjectAnimator.ofFloat(view, "rotationY", 0.0f, 360.0f).setDuration(1000).start();
 

**2、PropertyValuesHolder：**多属性动画同时工作管理类。有时候我们需要同时修改多个属性，那就可以用到此类，具体如下：

    PropertyValuesHolder a1 = PropertyValuesHolder.ofFloat("alpha", 0f, 1f);  
    PropertyValuesHolder a2 = PropertyValuesHolder.ofFloat("translationY", 0, viewWidth);  
    ......
    ObjectAnimator.ofPropertyValuesHolder(view, a1, a2, ......).setDuration(1000).start();
 

如上代码就可以实现同时修改多个属性的动画啦。

**3、ValueAnimator：**属性动画中的时间驱动，管理着动画时间的开始、结束属性值，相应时间属性值计算方法等。包含所有计算动画值的核心函数以及每一个动画时间节点上的信息、一个动画是否重复、是否监听更新事件等，并且还可以设置自定义的计算类型。

特别注意：ValueAnimator只是动画计算管理驱动，设置了作用目标，但没有设置属性，需要通过updateListener里设置属性才会生效。   
 
    ValueAnimator animator = ValueAnimator.ofFloat(0, mContentHeight);  //定义动画
    animator.setTarget(view);   //设置作用目标
    animator.setDuration(5000).start();
    animator.addUpdateListener(new AnimatorUpdateListener() {
    @Override
    public void onAnimationUpdate(ValueAnimator animation){
        float value = (float) animation.getAnimatedValue();
        view.setXXX(value);  //必须通过这里设置属性值才有效
        view.mXXX = value;  //不需要setXXX属性方法
    }
    });
 

大眼看上去可以发现和ObjectAnimator没啥区别，实际上正是由于ValueAnimator不直接操作属性值，所以要操作对象的属性可以不需要setXXX与getXXX方法，你完全可以通过当前动画的计算去修改任何属性。
**
4、AnimationSet：**动画集合，提供把多个动画组合成一个组合的机制，并可设置动画的时序关系，如同时播放、顺序播放或延迟播放。具体使用方法比较简单，如下：

    ObjectAnimator a1 = ObjectAnimator.ofFloat(view, "alpha", 1.0f, 0f);  
    ObjectAnimator a2 = ObjectAnimator.ofFloat(view, "translationY", 0f, viewWidth);  
    ......
    AnimatorSet animSet = new AnimatorSet();  
    animSet.setDuration(5000);  
    animSet.setInterpolator(new LinearInterpolator());   
    //animSet.playTogether(a1, a2, ...); //两个动画同时执行  
    animSet.play(a1).after(a2); //先后执行
    ......//其他组合方式
    animSet.start();  
**
5、Evaluators相关类解释：** Evaluators就是属性动画系统如何去计算一个属性值。它们通过Animator提供的动画的起始和结束值去计算一个动画的属性值。

IntEvaluator：整数属性值。

FloatEvaluator：浮点数属性值。

ArgbEvaluator：十六进制color属性值。

TypeEvaluator：用户自定义属性值接口，譬如对象属性值类型不是int、float、color类型，你必须实现这个接口去定义自己的数据类型。

既然说到这了，那就来个例子吧，譬如我们需要实现一个自定义属性类型和计算规则的属性动画，如下类型float[]：

    ValueAnimator valueAnimator = new ValueAnimator();
    valueAnimator.setDuration(5000);
    valueAnimator.setObjectValues(new float[2]); //设置属性值类型
    valueAnimator.setInterpolator(new LinearInterpolator());
    valueAnimator.setEvaluator(new TypeEvaluator<float[]>()
    {
    @Override
    public float[] evaluate(float fraction, float[] startValue,
                            float[] endValue)
    {
        //实现自定义规则计算的float[]类型的属性值
        float[] temp = new float[2];
        temp[0] = fraction * 2;
        temp[1] = (float)Math.random() * 10 * fraction;
        return temp;
    }
    });

    valueAnimator.start();
    valueAnimator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener()
    {
        @Override
    public void onAnimationUpdate(ValueAnimator animation)
    {
        float[] xyPos = (float[]) animation.getAnimatedValue();
        view.setHeight(xyPos[0]);   //通过属性值设置View属性动画
        view.setWidth(xyPos[1]);    //通过属性值设置View属性动画
    }
    });

**6、Interpolators相关类解释：**

AccelerateDecelerateInterolator：先加速后减速。

AccelerateInterpolator：加速。

DecelerateInterpolator：减速。

AnticipateInterpolator：先向相反方向改变一段再加速播放。

AnticipateOvershootInterpolator：先向相反方向改变，再加速播放，会超出目标值然后缓慢移动至目标值，类似于弹簧回弹。

BounceInterpolator：快到目标值时值会跳跃。

CycleIinterpolator：动画循环一定次数，值的改变为一正弦函数：Math.sin(2 * mCycles * Math.PI * input)。

LinearInterpolator：线性均匀改变。

OvershottInterpolator：最后超出目标值然后缓慢改变到目标值。

TimeInterpolator：一个允许自定义Interpolator的接口，以上都实现了该接口。

举个例子，就像系统提供的标准API一样，如下就是加速插值器的实现代码，我们自定义时也可以类似实现：
    
    //开始很慢然后不断加速的插值器。
    public class AccelerateInterpolator implements Interpolator {
    private final float mFactor;
    private final double mDoubleFactor;

    public AccelerateInterpolator() {
        mFactor = 1.0f;
        mDoubleFactor = 2.0;
    }

    ......

    //input  0到1.0。表示动画当前点的值，0表示开头，1表示结尾。
    //return  插值。值可以大于1超出目标值，也可以小于0突破低值。
    @Override
    public float getInterpolation(float input) {
        //实现核心代码块
        if (mFactor == 1.0f) {
            return input * input;
        } else {
            return (float)Math.pow(input, mDoubleFactor);
        }
    }
    }

综上可以发现，我们可以使用现有系统提供标准的东东实现属性动画，也可以通过自定义继承相关接口实现自己的动画，只要实现上面提到的那些主要方法即可。

**4-2-4 Java属性动画拓展之ViewPropertyAnimator动画**
在Android API 12时，View中添加了animate方法，具体如下：
    
    public class View implements Drawable.Callback, KeyEvent.Callback,
        AccessibilityEventSource {
     ......
     /**
     * This method returns a ViewPropertyAnimator object, which can be used to animate
     * specific properties on this View.
     *
     * @return ViewPropertyAnimator The ViewPropertyAnimator associated with this View.
     */
    public ViewPropertyAnimator animate() {
        if (mAnimator == null) {
            mAnimator = new ViewPropertyAnimator(this);
        }
        return mAnimator;
    }
    ......
    }

可以看见通过View的animate()方法可以得到一个ViewPropertyAnimator的属性动画（有人说他没有继承Animator类，是的，他是成员关系，不是之前那种继承关系）。

ViewPropertyAnimator提供了一种非常方便的方法为View的部分属性设置动画（切记，是部分属性），它可以直接使用一个 Animator对象设置多个属性的动画；在多属性设置动画时，它比 上面的ObjectAnimator更加牛逼、高效，因为他会管理多个属性的invalidate方法统一调运触发，而不像上面分别调用，所以还会有一些 性能优化。如下就是一个例子：

    myView.animate().x(0f).y(100f).start(); 
 

**4-2-5 Java属性动画拓展之LayoutAnimator容器布局动画**
Property动画系统还提供了对ViewGroup中View添加时的动画功能，我们可以用LayoutTransition对 ViewGroup中的View进行动画设置显示。LayoutTransition的动画效果都是设置给ViewGroup，然后当被设置动画的 ViewGroup中添加删除View时体现出来。该类用于当前布局容器中有View添加、删除、隐藏、显示等时候定义布局容器自身的动画和View的动 画，也就是说当在一个LinerLayout中隐藏一个View的时候，我们可以自定义 整个由于LinerLayout隐藏View而改变的动画，同时还可以自定义被隐藏的View自己消失时候的动画等。

我们可以发现LayoutTransition类中主要有五种容器转换动画类型，具体如下：

LayoutTransition.APPEARING：当View出现或者添加的时候View出现的动画。

LayoutTransition.CHANGE_APPEARING：当添加View导致布局容器改变的时候整个布局容器的动画。

LayoutTransition.DISAPPEARING：当View消失或者隐藏的时候View消失的动画。

LayoutTransition.CHANGE_DISAPPEARING：当删除或者隐藏View导致布局容器改变的时候整个布局容器的动画。

LayoutTransition.CHANGE：当不是由于View出现或消失造成对其他View位置造成改变的时候整个布局容器的动画。

XML方式使用系统提供的默认LayoutTransition动画：

我们可以通过如下方式使用系统提供的默认ViewGroup的LayoutTransition动画：

    android:animateLayoutChanges=”true”

在ViewGroup添加如上xml属性默认是没有任何动画效果的，因为前面说了，该动画针对于ViewGroup内部东东发生改变时才有效，所以当我们设置如上属性然后调运ViewGroup的addView、removeView方法时就能看见系统默认的动画效果了。

还有一种就是通过如下方式设置：

    android:layoutAnimation=”@anim/customer_anim”
 

通过这种方式就能实现很多吊炸天的动画。

Java方式使用系统提供的默认LayoutTransition动画：

在使用LayoutTransition时，你可以自定义这几种事件类型的动画，也可以使用默认的动画，总之最终都是通过 setLayoutTransition(LayoutTransition lt)方法把这些动画以一个LayoutTransition对象设置给一个ViewGroup。

譬如实现如上Xml方式的默认系统LayoutTransition动画如下：
    
    mTransitioner = new LayoutTransition();
    mViewGroup.setLayoutTransition(mTransitioner);

稍微再高端一点吧，我们来自定义这几类事件的动画，分别实现他们，那么你可以像下面这么处理：

    mTransitioner = new LayoutTransition();
    ......
    ObjectAnimator anim = ObjectAnimator.ofFloat(this, "scaleX", 0, 1);
    ......//设置更多动画
    mTransition.setAnimator(LayoutTransition.APPEARING, anim);
    ......//设置更多类型的动画mViewGroup.setLayoutTransition(mTransitioner);

到此通过LayoutTransition你就能实现类似小米手机计算器切换普通型和科学型的炫酷动画了。

## 5 Android动画总结 ##
到此Android动画基本已经描述OK了，也就这么三大类，尤其是属性动画更加一筹。但是特别说一句，上面基本都没有提及到各种动画的 Listener接口，原因是这个玩意太简单，所以不提了，相信你会监听View的onClickListener就一定会触类旁通动画的 Listener方法的。有了这些基础相信无论是自定义控件时还是自定义动画时都会起到直接的指导参考作用。其实对于Android的动画实现远远不止现 在提到的这些，但是这些又是基础，所以后面还会写文章说说Android提供的其他动画参考工具类的。

**View动画：**

View动画只能够为View添加动画，如果想为非View对象添加动画须自己实现；且View动画支持的种类很少；尤其是他改变的是View的绘制效果，View的属性没有改变，其位置与大小都不变； View动画代码量少，使用简单方便。

**Property动画：**

弥补了View动画的缺陷，你可以为一个对象的任意属性添加动画，对象自己的属性会被真的改变；当对象的属性变化的时候，属性动画会自动刷新屏幕；属性动画改变的是对象的真实属性，而且属性动画不止用于View，还可以用于任何对象。
