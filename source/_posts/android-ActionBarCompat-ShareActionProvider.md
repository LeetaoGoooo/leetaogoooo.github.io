---
title: android-ActionBarCompat-ShareActionProvider
tags: [Android]

date: 2016-09-25
published: true
hideInList: false
feature: 
isTop: false
---







一个上下文相关的分享提供者
## layout
### ViewPager
ViewPage是Android拓展包v4包中的类,可以让用户左右切换当前的View,官方api的描述如下:
> Layout manager that allows the user to flip left and right through pages of data.  You supply an implementation of a  PagerAdapter to generate the pages that the view shows.
ViewPager is most often used in conjunction with Fragment, which is a convenient way to supply and manage the lifecycle of each page. There are standard adapters implemented for using fragments with the ViewPager, which cover the most common use cases. These are FragmentPagerAdapter and FragmentStatePagerAdapter; each of these classes have simple code showing how to build a full user interface with them.

从这个描述中我们可以知道以下几点:

1. ViewPager绝大多数情况下和Frament结合使用,并且提供了FragmentPagerAdapter 和 FragmentStatePagerAdapter
2. ViewPager需要配合PagerAdaper使用,PagerAdapter的作用是产生展示view的页面

### TextView
```xml
android:textAppearance="?android:textAppearanceMedium"
```
这是设置文字的外观,引用系统自带的一个外观, **?** 表示系统是否有这种外观,否则使用默认的外观<p>
```xml
android:lineSpacingMultiplier="1.1"
```
设置行间距的倍数
```xml
android:lineSpacingExtra = "7dp"
```
设置行间距
### ImageView
```xml
android:scaleType="fitCenter" //使用 CENTER 方式缩放图像
android:scaleType="center" //在视图中使图像居中，不执行缩放
android:scaleType="centerCrop" //均衡的缩放图像（保持图像原始比例），使图片的两个坐标（宽、高）都大于等于 相应的视图坐标（负的内边距）.图像则位于视图的中央
android:scaleType="centerInside" //均衡的缩放图像（保持图像原始比例），使图片的两个坐标（宽、高）都小于等于 相应的视图坐标（负的内边距）
android:scaleType="fitEnd" //使用 END 方式缩放图像
android:scaleType="fitStart" //使用 START 方式缩放图像
android:scaleType="fitXY" //使用 FILL 方式缩放图像
android:scaleType="matrix" //绘制时，使用图像矩阵方式缩放
```
这是将图片边界缩放,以适应视图边界时的可选项.可参阅
[ImageView.ScaleType代码演示](http://blog.sina.com.cn/s/blog_407abb0d0100mao1.html)

## MainActivity
### setOnPageChangeListener
第68行:ViewPager的**setOnPageChangeListener**弃用
将 **setOnPageChangeListener** 改为 **addOnpageChangeListener**
具体源码如下:
```java
    /**
     * Set a listener that will be invoked whenever the page changes or is incrementally
     * scrolled. See  OnPageChangeListener.
     *
     * @param listener Listener to set
     *
     * @deprecated Use addOnPageChangeListener(OnPageChangeListener)
     * and removeOnPageChangeListener(OnPageChangeListener) instead.
     */
    @Deprecated
    public void setOnPageChangeListener(OnPageChangeListener listener) {
        mOnPageChangeListener = listener;
    }
```
其含义就是设置一个监听器用来监控页面改变或者递增的滚动.

### OnPageChangeListener
OnPageChangeListener是一个接口,需要去自行实现
具体源码如下:
```java
    /**
     * Callback interface for responding to changing state of the selected page.
     */
    public interface OnPageChangeListener {

        /**
         * This method will be invoked when the current page is scrolled, either as part
         * of a programmatically initiated smooth scroll or a user initiated touch scroll.
         *
         * @param position Position index of the first page currently being displayed.
         *                 Page position+1 will be visible if positionOffset is nonzero.
         * @param positionOffset Value from [0, 1) indicating the offset from the page at position.
         * @param positionOffsetPixels Value in pixels indicating the offset from position.
         */
        public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels);

        /**
         * This method will be invoked when a new page becomes selected. Animation is not
         * necessarily complete.
         *
         * @param position Position index of the new selected page.
         */
        public void onPageSelected(int position);

        /**
         * Called when the scroll state changes. Useful for discovering when the user
         * begins dragging, when the pager is automatically settling to the current page,
         * or when it is fully stopped/idle.
         *
         * @param state The new scroll state.
         * @see ViewPager#SCROLL_STATE_IDLE
         * @see ViewPager#SCROLL_STATE_DRAGGING
         * @see ViewPager#SCROLL_STATE_SETTLING
         */
        public void onPageScrollStateChanged(int state);
    }
```
从源码可以看出该接口是当选中页面状态改变时做出响应的,需要实现依次三个方法,三个方法分别对应着*onPageScrolled(int position, float positionOffset, int positionOffsetPixels)*(当页面滚动的时候),*onPageSelected(int position)*(当页面被选的时候),*onPageScrollStateChanged(int state)*(当页面滚动状态改变的时候)

### PagerAdapter
官方api的说明:
>Base class providing the adapter to populate pages inside of a ViewPager.You will most likely want to use a more
specific implementation of this, such as  android.support.v4.app.FragmentPagerAdapter or
android.support.v4.app.FragmentStatePagerAdapter.

大致意思就是提供一个填充ViewPager中的页面内容的适配器.然后可能使用FragmentPagerAdapter和FragmentStatePagerAdapter可能性更高.

当继承PageAdapter的是否,需要重写以下的方法:

1. instantiateItem(ViewGroup, int)
2. destroyItem(ViewGroup, int, Object) 
3. getCount() 
4. isViewFromObject(View, Object)

具体源码如下:
```java
    /**
     * Create the page for the given position.  The adapter is responsible
     * for adding the view to the container given here, although it only
     * must ensure this is done by the time it returns from finishUpdate(ViewGroup).
     *
     * @param container The containing View in which the page will be shown.
     * @param position The page position to be instantiated.
     * @return Returns an Object representing the new page.  This does not
     * need to be a View, but can be some other container of the page.
     *
     * @deprecated Use instantiateItem(ViewGroup, int)
     */
    @Deprecated
    public Object instantiateItem(View container, int position) {
        throw new UnsupportedOperationException(
                "Required method instantiateItem was not overridden");
    }
    
    /**
     * Remove a page for the given position.  The adapter is responsible
     * for removing the view from its container, although it only must ensure
     * this is done by the time it returns from finishUpdate(View).
     *
     * @param container The containing View from which the page will be removed.
     * @param position The page position to be removed.
     * @param object The same object that was returned by
     * instantiateItem(View, int).
     *
     * @deprecated Use destroyItem(ViewGroup, int, Object)
     */
    @Deprecated
    public void destroyItem(View container, int position, Object object) {
        throw new UnsupportedOperationException("Required method destroyItem was not overridden");
    }
    
    /**
     * Return the number of views available.
     */
    public abstract int getCount();
    
    /**
     * Determines whether a page View is associated with a specific key object
     * as returned by instantiateItem(ViewGroup, int). This method is
     * required for a PagerAdapter to function properly.
     *
     * @param view Page View to check for association with object
     * @param object Object to check for association with view
     * @return true if view is associated with the key object object
     */
    public abstract boolean isViewFromObject(View view, Object object);
```
根据源码说的注释说明,可以得知:

1. **instantiateItem(View container, int position)**该方法是在指定位置创建相应的页面,其中*参数container*是展示页面中的view,*参数position*页面被实例化的位置,不过该方法现在已经被弃用,改用为instantiateItem(ViewGroup container, int position)
2. **destroyItem(View container, int position, Object object)**该方法是移除指定位置的页面,其中*参数container*是将要被移走页面的view,*参数position*是将要被移除页面的位置,*参数object*是由instantiateItem(View, int)返回的对象,该方法也被弃用,改用为destroyItem(ViewGroup, int, Object)
3. **getCount()**该方法这是返回可以获取到的view数量
4. **isViewFromObject(View view, Object object)** 判断当前view跟由instantiateItem(ViewGroup, int)返回的对象是否有关联,如果有则返回true,*参数view*需要校验的view,*参数object*需要校验的对象

### LayoutInflater
layoutInflater其作用类似findViewById(),不同点则是LayoutInflater是用来找res/layout/下的xml布局文件，并且实例化；而findViewById()是找xml布局文件下的具体widget控件(如Button、TextView等).
具体作用:

1. 对于一个没有被载入或者想要动态载入的界面，都需要使用LayoutInflater.inflate()来载入
2. 对于一个已经载入的界面，就可以使用Activiyt.findViewById()方法来获得其中的界面元素

LayoutInflater 是一个抽象类，在文档中如下声明:
```java
public abstract class LayoutInflater extends Object
```
获得 LayoutInflater 实例的三种方式:

1. LayoutInflater inflater = getLayoutInflater(); //调用Activity的getLayoutInflater() 
2. LayoutInflater inflater = LayoutInflater.from(context);  
3. LayoutInflater inflater = (LayoutInflater)context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
<p>
**LayoutInflater的inflate方法**
```java
    public View inflate(int resource, ViewGroup root) {
        throw new RuntimeException("Stub!");
    }

    public View inflate(XmlPullParser parser, ViewGroup root) {
        throw new RuntimeException("Stub!");
    }

    public View inflate(int resource, ViewGroup root, boolean attachToRoot) {
        throw new RuntimeException("Stub!");
    }

    public View inflate(XmlPullParser parser, ViewGroup root, boolean attachToRoot) {
        throw new RuntimeException("Stub!");
    }
```
其返回值均为view,但是值得注意的是:**inflater 是用来找 res/layout 下的 xml 布局文件,并且实例化;findViewById() 是找具体 xml 布局文件中的具体 widget 控件(如:Button、TextView 等**

### ShareActionProvider
官网api说明:
> Provides a share action, which is suitable for an activity's app bar. Creates views that enable data sharing. If the provider appears in the overflow menu, it creates a submenu with the appropriate sharing actions.

从上述描述我们可以ShareActionProvider其实会提供一个分享的按钮.如果分享按钮出现在溢出菜单,它会创建一个合适的子菜单

### onCreateOptionsMenu
```java
    public boolean onCreateOptionsMenu(Menu menu) {
        throw new RuntimeException("Stub!");
    }
```
此方法用于初始化菜单，其中menu参数就是即将要显示的Menu实例.返回true则显示该menu,false 则不显示,只会在第一次初始化菜单时调用.<p>
**Activity菜单机制**
Activity有一套机制来实现对菜单的管理，方法如下:

1. public boolean onCreateOptionsMenu(Menu menu)
2. public boolean onPrepareOptionsMenu(Menu menu) 在onCreateOptionsMenu执行后,菜单被显示前调用;如果菜单已经被创建,则在菜单显示前被调用.同样的,返回true则显示该menu,false 则不显示;(可以通过此方法动态的改变菜单的状态,比如加载不同的菜单等)
3. public void onOptionsMenuClosed(Menu menu) 每次菜单被关闭时调用.(菜单被关闭有三种情形,menu按钮被再次点击,back按钮被点击或者用户选择了某一个菜单项)
4. public boolean onOptionsItemSelected(MenuItem item) 菜单项被点击时调用，也就是菜单项的监听方法


