title: github之PhotoView使用
date: 2015-09-14 11:12:47
tags: [Android,PhotoView,github]
category: Android
---
>PhotoView使用在ViewPager中,github地址：[https://github.com/chrisbanes/PhotoView](https://github.com/chrisbanes/PhotoView)
```js
 <com.android.xiaomolongstudio.danhuaer.view.HackyViewPager
        android:id="@+id/viewPager"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_above="@id/ad_layout" />
```
<!--more-->
HackyViewPager.java
```java
public class HackyViewPager extends ViewPager {

	private boolean isLocked;
	
    public HackyViewPager(Context context) {
        super(context);
        isLocked = false;
    }

    public HackyViewPager(Context context, AttributeSet attrs) {
        super(context, attrs);
        isLocked = false;
    }

    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
    	if (!isLocked) {
	        try {
	            return super.onInterceptTouchEvent(ev);
	        } catch (IllegalArgumentException e) {
	            e.printStackTrace();
	            return false;
	        }
    	}
    	return false;
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
        return !isLocked && super.onTouchEvent(event);
    }
    
	public void toggleLock() {
		isLocked = !isLocked;
	}

	public void setLocked(boolean isLocked) {
		this.isLocked = isLocked;
	}

	public boolean isLocked() {
		return isLocked;
	}
	
}
```
```java
 ViewPager mViewPager;
 mViewPager.setPageTransformer(true, new CardTransformer(0.8f));
        mViewPager.setOffscreenPageLimit(2);
        mViewPager.setAdapter(new ImageAdapter());
        mViewPager.setCurrentItem(position);
  
```
CardTransformer
```java
  /**
     * Awesome Launcher-inspired page transformer
     */
    private static class CardTransformer implements ViewPager.PageTransformer {

        private final float scaleAmount;

        public CardTransformer(float scalingStart) {
            scaleAmount = 1 - scalingStart;
        }

        @Override
        public void transformPage(View page, float position) {
            if (position >= 0f) {
                final int w = page.getWidth();
                float scaleFactor = 1 - scaleAmount * position;

                page.setAlpha(1f - position);
                page.setScaleX(scaleFactor);
                page.setScaleY(scaleFactor);
                page.setTranslationX(w * (1 - position) - w);
            }
        }

    }
```
 ImageAdapter
```java
 private class ImageAdapter extends PagerAdapter {

        @Override
        public Object instantiateItem(ViewGroup container, int position) {
            final String imageUrl = mHrefData.get(position);

            final View view = LayoutInflater.from(EmotionImageDetailActivity.this).inflate(R.layout.emotion_image_detail_item, container, false);

            final ProgressBar progressBar = (ProgressBar) view.findViewById(R.id.progressBar);
            final TextView introduction = (TextView) view.findViewById(R.id.introduction);
            introduction.setText(mIntroductionDataData.get(position));
            final PhotoView imageView = (PhotoView) view.findViewById(R.id.imageView);                
            Picasso.with(EmotionImageDetailActivity.this).load(imageUrl)
                    .into(imageView, new Callback() {
                        @Override
                        public void onSuccess() {
                            progressBar.setVisibility(View.GONE);
                        }

                        @Override
                        public void onError() {
                            progressBar.setVisibility(View.GONE);
                        }
                    });

            container.addView(view, ViewGroup.LayoutParams.MATCH_PARENT,
                    ViewGroup.LayoutParams.MATCH_PARENT);

            return view;
        }

        @Override
        public void destroyItem(ViewGroup container, int position, Object object) {
            container.removeView((View) object);
        }

        @Override
        public int getCount() {
            return mHrefData != null ? mHrefData.size() : 0;
        }

        @Override
        public boolean isViewFromObject(View view, Object object) {
            return view == object;
        }
    }
```
emotion_image_detail_item.xml
```js
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/black">


    <uk.co.senab.photoview.PhotoView
        android:id="@+id/imageView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_centerInParent="true" />

    <ScrollView
        android:layout_width="match_parent"
        android:layout_height="60dp"
        android:layout_alignParentBottom="true">

        <TextView
            android:id="@+id/introduction"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_gravity="top"
            android:padding="10dp"
            android:textColor="@color/white"
            android:textSize="@dimen/text_content" />
    </ScrollView>

    <ProgressBar
        android:id="@+id/progressBar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true" />
</RelativeLayout>

```
