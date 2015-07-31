title: Android之LoadMoreListView
date: 2015-04-28 
categories: [Android]
tags: [Android,ListView]
---

java

```java
/**
 * Android load more ListView when scroll down.
 * 
 * @author Charon Chui
 */
public class LoadMoreListView extends ListView {
	protected static final String TAG = "LoadMoreListView";
	private View mFooterView;
	private OnScrollListener mOnScrollListener;
	private OnLoadMoreListener mOnLoadMoreListener;

	/**
	 * If is loading now.
	 */
	private boolean mIsLoading;

	private int mCurrentScrollState;

	public LoadMoreListView(Context context, AttributeSet attrs, int defStyle) {
		super(context, attrs, defStyle);
		init(context);
	}

	public LoadMoreListView(Context context, AttributeSet attrs) {
		super(context, attrs);
		init(context);
	}

	public LoadMoreListView(Context context) {
		super(context);
		init(context);
	}

	private void init(Context context) {
		mFooterView = View.inflate(context, R.layout.load_more_footer, null);
		addFooterView(mFooterView);
		hideFooterView();
		/*
		 * Must use super.setOnScrollListener() here to avoid override when call
		 * this view's setOnScrollListener method
		 */
		super.setOnScrollListener(superOnScrollListener);
	}

	/**
	 * Hide the load more view(footer view)
	 */
	private void hideFooterView() {
		mFooterView.setVisibility(View.GONE);
	}

	/**
	 * Show load more view
	 */
	private void showFooterView() {
		mFooterView.setVisibility(View.VISIBLE);
	}

	@Override
	public void setOnScrollListener(OnScrollListener l) {
		mOnScrollListener = l;
	}

	/**
	 * Set load more listener, usually you should get more data here.
	 * 
	 * @param listener
	 *            OnLoadMoreListener
	 * @see OnLoadMoreListener
	 */
	public void setOnLoadMoreListener(OnLoadMoreListener listener) {
		mOnLoadMoreListener = listener;
	}

	/**
	 * When complete load more data, you must use this method to hide the footer
	 * view, if not the footer view will be shown all the time.
	 */
	public void onLoadMoreComplete() {
		mIsLoading = false;
		hideFooterView();
	}

	private OnScrollListener superOnScrollListener = new OnScrollListener() {

		@Override
		public void onScrollStateChanged(AbsListView view, int scrollState) {
			mCurrentScrollState = scrollState;
			// Avoid override when use setOnScrollListener
			if (mOnScrollListener != null) {
				mOnScrollListener.onScrollStateChanged(view, scrollState);
			}
		}

		@Override
		public void onScroll(AbsListView view, int firstVisibleItem,
				int visibleItemCount, int totalItemCount) {
			if (mOnScrollListener != null) {
				mOnScrollListener.onScroll(view, firstVisibleItem,
						visibleItemCount, totalItemCount);
			}
			// The count of footer view will be add to visibleItemCount also are
			// added to totalItemCount
			if (visibleItemCount == totalItemCount) {
				// If all the item can not fill screen, we should make the
				// footer view invisible.
				hideFooterView();
			} else if (!mIsLoading
					&& (firstVisibleItem + visibleItemCount >= totalItemCount)
					&& mCurrentScrollState != SCROLL_STATE_IDLE) {
				showFooterView();
				mIsLoading = true;
				if (mOnLoadMoreListener != null) {
					mOnLoadMoreListener.onLoadMore();
				}
			}
		}
	};


	/**
	 * Interface for load more
	 */
	public interface OnLoadMoreListener {
		/**
		 * Load more data.
		 */
		void onLoadMore();
	}

````
<!-- more -->
load_more_footer.xml

```js
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:gravity="center"
    android:orientation="horizontal" >

    <ProgressBar
        android:id="@+id/load_more_pb"
        style="@android:style/Widget.ProgressBar.Small"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginRight="10dp" />

    <TextView
        android:id="@+id/load_more_tv"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingBottom="5dp"
        android:paddingTop="5dp"
        android:text="正在加载..."
        tools:ignore="HardcodedText" />

</LinearLayout>
```

调用

```java
mListView.setOnLoadMoreListener(new OnLoadMoreListener() {

			@Override
			public void onLoadMore() {
				
			}
		});
```
