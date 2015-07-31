title: Android之SqliteSample
date: 2015-04-21
categories: [Android]
tags: [Android,Sqlite]
---
原文：[https://developer.android.com/training/basics/data-storage/databases.html](https://developer.android.com/training/basics/data-storage/databases.html)

* Define a Schema and Contract

```java
package com.xiaomolong.sqlitesample;

import android.provider.BaseColumns;

public final class FeedReaderContract {
	public FeedReaderContract() {
	}

	public static abstract class FeedEntry implements BaseColumns {
		public static final String TABLE_NAME = "entry";
		public static final String COLUMN_NAME_ENTRY_ID = "entryid";
		public static final String COLUMN_NAME_TITLE = "title";
		public static final String COLUMN_NAME_SUBTITLE = "subtitle";

	}
}

```
<!-- more -->
* FeedEntryDb

```java
package com.xiaomolong.sqlitesample;

public class FeedEntryDb {
	public String entryid;
	public String title;
	public String subtitle;

	public String getEntryid() {
		return entryid;
	}

	public void setEntryid(String entryid) {
		this.entryid = entryid;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public String getSubtitle() {
		return subtitle;
	}

	public void setSubtitle(String subtitle) {
		this.subtitle = subtitle;
	}
}

```


* Create a Database Using a SQL Helper

```java
package com.xiaomolong.sqlitesample;

import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteDatabase.CursorFactory;
import android.database.sqlite.SQLiteOpenHelper;

import com.xiaomolong.sqlitesample.FeedReaderContract.FeedEntry;

public class FeedReaderDbHelper extends SQLiteOpenHelper {
	public static final int DATABASE_VERSION = 1;
	public static final String DATABASE_NAME = "FeedReader.db";
	private static final String TEXT_TYPE = " TEXT";
	private static final String COMMA_SEP = ",";
	private static final String SQL_CREATE_ENTRIES = "CREATE TABLE "
			+ FeedEntry.TABLE_NAME + " (" + FeedEntry._ID
			+ " INTEGER PRIMARY KEY," + FeedEntry.COLUMN_NAME_ENTRY_ID
			+ TEXT_TYPE + COMMA_SEP + FeedEntry.COLUMN_NAME_TITLE + TEXT_TYPE
			+ COMMA_SEP + FeedEntry.COLUMN_NAME_SUBTITLE + TEXT_TYPE +
			// Any other options for the CREATE command
			" )";

	private static final String SQL_DELETE_ENTRIES = "DROP TABLE IF EXISTS "
			+ FeedEntry.TABLE_NAME;

	public FeedReaderDbHelper(Context context) {
		super(context, DATABASE_NAME, null, DATABASE_VERSION);
	}

	public FeedReaderDbHelper(Context context, String name,
			CursorFactory factory, int version) {
		super(context, name, factory, version);
	}

	@Override
	public void onCreate(SQLiteDatabase db) {
		db.execSQL(SQL_CREATE_ENTRIES);
	}

	@Override
	public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
		db.execSQL(SQL_DELETE_ENTRIES);
		onCreate(db);
	}

}

```

 * DatabaseManger，包括增删改查
 
 ```java
 package com.xiaomolong.sqlitesample;

import java.util.ArrayList;
import java.util.List;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.sqlite.SQLiteDatabase;

import com.xiaomolong.sqlitesample.FeedReaderContract.FeedEntry;

public class DatabaseManger {

	private static FeedReaderDbHelper mFeedReaderDbHelper = null;

	DatabaseManger(Context context) {
		getInstance(context);
	}

	public static void getInstance(Context context) {
		if (mFeedReaderDbHelper == null) {
			mFeedReaderDbHelper = new FeedReaderDbHelper(context);
		}
	}

	/**
	 * 插入
	 * 
	 * @return the row ID of the newly inserted row, or -1 if an error occurred
	 */
	public long insert(FeedEntryDb feedEntry) {

		// Gets the data repository in write mode
		SQLiteDatabase db = mFeedReaderDbHelper.getWritableDatabase();

		// Create a new map of values, where column names are the keys
		ContentValues values = new ContentValues();
		values.put(FeedEntry.COLUMN_NAME_ENTRY_ID, feedEntry.getEntryid());
		values.put(FeedEntry.COLUMN_NAME_TITLE, feedEntry.getTitle());
		values.put(FeedEntry.COLUMN_NAME_SUBTITLE, feedEntry.getSubtitle());

		// Insert the new row, returning the primary key value of the new row
		long newRowId;
		newRowId = db.insert(FeedEntry.TABLE_NAME, null, values);
		db.close();
		return newRowId;

	}

	/**
	 * 查询或通过条件查询
	 * 
	 * @param rowId
	 * @return
	 */
	public List<FeedEntryDb> query(int rowId) {
		SQLiteDatabase db = mFeedReaderDbHelper.getReadableDatabase();

		// Define a projection that specifies which columns from the database
		// you will actually use after this query.
		String[] projection = { FeedEntry._ID, FeedEntry.COLUMN_NAME_ENTRY_ID,
				FeedEntry.COLUMN_NAME_TITLE, FeedEntry.COLUMN_NAME_SUBTITLE };
		String selection = null;
		String[] selectionArgs = { String.valueOf(rowId) };
		if (rowId != -1) {// 通过条件查询
			selection = FeedEntry._ID + " = ?";
		} else {
			selectionArgs = null;
		}
		// How you want the results sorted in the resulting Cursor
		String sortOrder = FeedEntry.COLUMN_NAME_TITLE + " DESC";

		Cursor cursor = db.query(FeedEntry.TABLE_NAME, // The table to query
				projection, // The columns to return
				selection, // The columns for the WHERE clause
				selectionArgs, // The values for the WHERE clause
				null, // don't group the rows
				null, // don't filter by row groups
				sortOrder // The sort order
				);
		List<FeedEntryDb> feedEntryDbList = new ArrayList<FeedEntryDb>();
		while (cursor.moveToNext()) {
			FeedEntryDb feedEntryDb = new FeedEntryDb();
			long itemId = cursor.getLong(cursor
					.getColumnIndexOrThrow(FeedEntry._ID));
			feedEntryDb.setEntryid(cursor.getString(cursor
					.getColumnIndexOrThrow(FeedEntry.COLUMN_NAME_ENTRY_ID)));
			feedEntryDb.setTitle(cursor.getString(cursor
					.getColumnIndexOrThrow(FeedEntry.COLUMN_NAME_TITLE)));
			feedEntryDb.setSubtitle(cursor.getString(cursor
					.getColumnIndexOrThrow(FeedEntry.COLUMN_NAME_SUBTITLE)));
			feedEntryDbList.add(feedEntryDb);
		}
		cursor.close();
		db.close();
		return feedEntryDbList;

	}

	/**
	 * 更新
	 * 
	 * @param feedEntry
	 * @param rowId
	 * @return
	 */
	public int update(FeedEntryDb feedEntryDb, int rowId) {
		SQLiteDatabase db = mFeedReaderDbHelper.getReadableDatabase();
		// New value for one column
		ContentValues values = new ContentValues();
		values.put(FeedEntry.COLUMN_NAME_TITLE, feedEntryDb.getTitle()
				.toString());

		// Which row to update, based on the ID
		String selection = FeedEntry._ID + " = ?";
		String[] selectionArgs = { String.valueOf(rowId) };

		int count = db.update(FeedEntry.TABLE_NAME, values, selection,
				selectionArgs);
		db.close();
		return count;
	}

	/**
	 * 删除
	 * 
	 * @param rowId
	 * @return
	 */
	public int delete(int rowId) {
		SQLiteDatabase db = mFeedReaderDbHelper.getReadableDatabase();
		// Define 'where' part of query.
		String selection = FeedEntry._ID + " = ?";
		// Specify arguments in placeholder order.
		String[] selectionArgs = { String.valueOf(rowId) };
		// Issue SQL statement.
		int count = db.delete(FeedEntry.TABLE_NAME, selection, selectionArgs);
		db.close();
		return count;
	}
}

 ```
 
 * 方法调用
 
 ```java
 package com.xiaomolong.sqlitesample;

import java.util.List;

import android.app.Activity;
import android.os.Bundle;
import android.util.Log;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.TextView;

public class MainActivity extends Activity implements OnClickListener {
	DatabaseManger mDatabaseManger;
	TextView textView;

	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		textView = (TextView) findViewById(R.id.textView);
		findViewById(R.id.btnAdd).setOnClickListener(this);
		findViewById(R.id.btnQuery).setOnClickListener(this);
		findViewById(R.id.btnUpdate).setOnClickListener(this);
		findViewById(R.id.btnDelete).setOnClickListener(this);
		mDatabaseManger = new DatabaseManger(this);
	}

	@Override
	public void onClick(View v) {
		switch (v.getId()) {
		case R.id.btnAdd:
			for (int i = 0; i < 5; i++) {
				FeedEntryDb feedEntryDb = new FeedEntryDb();
				feedEntryDb.setEntryid(i + "");
				feedEntryDb.setTitle("title" + i + "");
				feedEntryDb.setSubtitle("subtitle" + i + "");
				mDatabaseManger.insert(feedEntryDb);
			}

			break;
		case R.id.btnQuery:
			List<FeedEntryDb> feedEntryDbList = mDatabaseManger.query(-1);
			String t = "";
			for (int i = 0; i < feedEntryDbList.size(); i++) {

				t = t + feedEntryDbList.get(i).getTitle() + "\n"
						+ feedEntryDbList.get(i).getSubtitle() + "\n\n";
			}
			textView.setText(t);
			break;
		case R.id.btnUpdate:

			FeedEntryDb feedEntryDb = new FeedEntryDb();
			feedEntryDb.setTitle("修改");
			Log.d("wxl", "count=" + mDatabaseManger.update(feedEntryDb, 1));

			break;
		case R.id.btnDelete:
			Log.d("wxl", "count=" + mDatabaseManger.delete(1));
			break;

		default:
			break;
		}

	}

	@Override
	public boolean onCreateOptionsMenu(Menu menu) {
		// Inflate the menu; this adds items to the action bar if it is present.
		getMenuInflater().inflate(R.menu.main, menu);
		return true;
	}

	@Override
	public boolean onOptionsItemSelected(MenuItem item) {
		// Handle action bar item clicks here. The action bar will
		// automatically handle clicks on the Home/Up button, so long
		// as you specify a parent activity in AndroidManifest.xml.
		int id = item.getItemId();
		if (id == R.id.action_settings) {
			return true;
		}
		return super.onOptionsItemSelected(item);
	}

}

 ```
