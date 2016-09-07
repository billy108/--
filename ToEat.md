**9/5/2016 10:56:26 PM** 

如果要用RecyclerView控件，要先compile 'com.android.support:recyclerview-v7:24.0.0'

**9/6/2016 9:12:30 PM** 

viewPager设置OnPageChangeListener监控滑动。

	/**
     * 功能：Fragment页面改变事件
     */
    public class TabOnPageChangeListener implements ViewPager.OnPageChangeListener {

        //当滑动状态改变时调用
        public void onPageScrollStateChanged(int state) {

        }

        //当前页面被滑动时调用
        public void onPageScrolled(int position, float positionOffset, int positionOffsetPixels){

        }

        //当新的页面被选中时调用
        public void onPageSelected(int position) {
            
        }
    }

