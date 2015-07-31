title: Fragment里更改Activity菜单
date: 2015-06-10
categories: [Android]
tags: [Android,Fragment]
---

```java
  @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //来指出fragment愿意添加item到选项菜单(否则, fragment将接收不到对 onCreateOptionsMenu()的调用)
        setHasOptionsMenu(true);
    }
    
 @Override
    public void onCreateOptionsMenu(Menu menu, MenuInflater inflater) {
        getActivity().getMenuInflater().inflate(R.menu.menu_yuqing_search, menu);
    }
```
