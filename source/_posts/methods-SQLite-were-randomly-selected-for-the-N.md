title: SQLite随机取n行的方法
date: 2013-03-02
categories: Android
tags: [Android,SQLite]
---

百度SQLite 随机取n行的方法大多数如下：

```java
	SELECT key,value FROM dict ORDER BY RANDOM(key) LIMIT 1
```
那如何写入android的SQLite？LIMIT 1
我也懂了就是拿到一条数据，可能是key给我误导了，
我运行时一直报错，现在才调试好。
现在分享给大家，代码如下：

```java
	Cursor cursor = db.query("表名", new String[] { "*" }, null, null,null, null, "RANDOM()"); 
```
