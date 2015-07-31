title: Android字符串分割
date: 2015-04-27 
categories: [Android]
tags: [Android,Canvas]
---

```java
split("|")实现字符串分割
```

下面同样的效果

```java
String chartest = "abcdefghijk";
		char[] chs = chartest.toCharArray();// 注：这里一定要用char来定义。
		for (int i = 0; i < chs.length; i++) {
			// Log.d("wxl", "chs=========" + chs[i]);
		}
		// 输出: a b c d e f g h i j k
```

String[] strs = str.split("\\|\\|");

就可以了。|在正则表达式里表示或者的意思，转一下就好了。
