---
layout: post
title: "Android Training: 保存数据"
date: 2016-02-25 14:18:00 +0800
categories: android-training
---
### 保存到key-value
* SharedPreferences
* 命名：com.example.myapp.PREFERENCE_FILE_KEY
* getSharedPreferences
* getPreferences

~~~java
// getSharedPreferences
Context context = getActivity();
SharedPreferences sharedPref = context.getSharedPreferences(getString(R.string.preference_file_key),
  Context.MODE_PRIVATE);

// getPreferences
SharedPreferences sharedPref = getActivity().getPreferences(cointext.MODE_PRIVATE);
~~~

#### 写入Shared Preference

~~~java
SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
SharedPreferences.Editor editor = sharedPref.edit();
editor.putInt(getString(R.string_saved_high_score), newHighScore);
editor.commit();
~~~

#### 读取Shared Preference

~~~java
SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
int defaultValue = getResources().getInteger(R.string.saved_high_score_default);
long highScore = sharedPref.getInt(getString(R.string.saved_high_score_default), defaultValue);
~~~

### 保存到文件
* internal storage: built-in non-volatile memory
* external storage: removable storage medium such as a micro SD card
* android:installLocation

#### internal storage
* 总是可用的
* 存储在这里的文件默认只能被相关的app访问
* 当卸载app时，系统会把app相关的所有文件都从internal storage中删除
* 不被用户与其他app访问的最佳存储区域

#### external storage
* 并不总是可用的
* World-readable
* 卸载app时，系统仅仅删除getExternalFilesDir()下的相关文件
* 当不需要访问控制并且想要将文件共享给其他app的最佳存储区域

~~~xml
// WRITE_EXTERNAL_STORAGE 默认会有READ_EXTERNAL_STORAGE权限
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
~~~

#### 保存到internal storage
* getFilesDir()
* getCacheDir()

~~~java
File file = new File(context.getFilesDir(), filename);

////
String filename = "myfile";
String string = "Hello world!";
FileOutputStream outputStream;

try {
  outputStream = openFileOutput(filename, Context.MODE_PRIVATE);
  outputStream.write(string.getBytes());
  outputStream.close();
} catch (Exception e) {
  e.printStackTrace();
}

////
public File getTempFile(Context context, String url) {
  File file;
  try {
    String fileName = Uri.parse(url).getLastPathSegment();
    file = File.createTempFile(fileName, null, context.getCacheDir());
  } catch (IOException e) {
    // Error while creating file
  }
  return file;
}
~~~

#### 保存到external storage
* 使用exnernal storage之前先检测其可用性
* getExternalStorageState(): MEDIA_MOUNTED

~~~java
/* Checks if external storage is available for read and write */
public boolean isExternalStorageWritable() {
  String state = Environment.getExternalStorageState();
  if (Environment.MEDIA_MOUNTED.equals(state)) {
    return true;
  }
  return false;
}

/* Checks if external storage is available to at least read */
public boolean isExternalStorageReadable() {
  String state = Environment.getExternalStorageState();
  if (Environment.MEDIA_MOUNTED.equals(state) ||
    Environment.MEDIA_MOUNTED_READ_ONLY.equals(state)) {
      return true;
    }
  return false;
}
~~~

* Public Files: 对用户或其他app是public的（图片或下载的文件）
* Private Files: app私有的，卸载app时应该删除
* 使用api定义的常量: DIRECTORY_PICTURES etc.
* getFreeSpace() getTotalSpace()

~~~java
// Public files
public File getAlbumStorageDir(String albumName) {
  File file = new File(Environment.getExternalStoragePublicDirectory(
    Environment.DIRECTORY_PICTURES), albumName);
  if (!file.mkdirs()) {
    Log.e(LOG_TAG, "Directory not created");
  }
  return file;
}

// Private files
public File getAlbumStorageDir(Context context, String albumName) {
    File file = new File(context.getExternalFilesDir(
      Environment.DIRECTORY_PICTURES), albumName);
    if (!file.mkdirs()) {
      Log.e(LOG_TAG, "Directory not created");
    }
    return file;
}
~~~

#### 删除文件
* 当app被卸载时，系统会删除下面的文件:
* 所有保存在internal storage中的文件
* 所有使用getExternalFilesDir()方法保存到external storage中的文件
* 定期手动删除所有通过getCacheDir()方法创建的缓存文件

~~~java
myFile.delete();

myContext.deleteFile(fileName);
~~~

### 保存到SQL数据库

* schema: a formal declaration of how the database is organized
* contract: explicitly specifies the layout of your schema in a systematic and self-documenting way
* Contract is a container for constants that define names for URIs, tables, and columns

`Best Practice: Put definitions that are global to your whole database in the root level of the class. Then create an inner class for each table that enumerates its columns.`

~~~java
public final class FeedReaderContract {
  public FeedReaderContract() {}

  public static abstract class FeedEntry implements BaseColumns {
    public static final String TABLE_NAME = "entry";
    public static final String COLUMN_NAME_ENTRY_ID = "entryid";
    public static final String COLUMN_NAME_TITLE = "title";
    public static final String COLUMN_NAME_SUBTITLE = "subtitle";
    ...
  }
}
~~~

#### 使用SQL Helper创建数据库
* 数据库存储在internal storage

~~~java
private static final String TEXT_TYPE = " TEXT";
private static final String COMMA_SEP = ",";
private static final String SQL_CREATE_ENTRIES =
  "CREATE TABLE " + FeedEntry.TABLE_NAME + " ()" +
  FeedEntry._ID + " INTEGER PRIMARY KEY," +
  FeedEntry.COLUMN_NAME_ENTRY_ID + TEXT_TYPE + COMMA_SEP +
  FeedEntry.COLUMN_NAME_TITLE + TEXT_TYPE + COMMA_SEP +
  ... // Any other options for the CREATE command
  " )";

private static final String SQL_DELETE_ENTRIES =
  "DROP TABLE IF EXISTS " + FeedEntry.TABLE_NAME;
~~~

~~~java
public class FeedReaderDbHelper extends SQLiteOpenHelper {
  public static final int DATABASE_VERSION = 1;
  public static final String DATABASE_NAME = "FeedReader.db";

  public FeedReaderDbHelper(Context context) {
    super(context, DATABSE_NAME, null, DATABASE_VERSION);
  }

  public void onCreate(SQLiteDatabase db) {
    db.execSQL(SQL_CREATE_ENTRIES);
  }

  public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
    db.execSQL(SQL_DELETE_ENTRIES);
    onCreate(db);
  }

  public void onDowngrade(SQLiteDatabase db, int oldVersion, int newVersion) {
    onUpgrade(db, oldVersion, newVersion);
  }
}
~~~

#### 向数据库中插入数据

~~~java
// Gets the data repository in write mode
SQLiteDatabase db = mDbHelper.getWritableDatabase();

// Create a new map of values, where column names are the keys
ContentValues values = new ContentValues();
values.put(FeedEntry.COLUMN_NAME_ENTRY_ID, id);
values.put(FeedEntry.COLUMN_NAME_TITLE, title);
values.put(FeedEntry.COLUMN_NAME_CONTENT, content);

// Insert the new row, returning the primary key value of the new row
long newRowId;
newRowId = db.insert(FeedEntry.TABLE_NAME, FeedEntry.COLUMN_NAME_NULLABLE, values);
~~~

#### 从数据库中读取数据

~~~java
SQLiteDatabase db = mDbHelper.getReadableDatabase();

// Define a projection that specifies which columns from the database
// you will actually use after this query
String[] projection = {
  FeedEntry._ID,
  FeedEntry.COLUMN_NAME_TITLE,
  FeedEntry.COLUMN_NAME_UPDATED,
  ...
};

// How you want the results sorted in the resulting Cursor
String sortOrder = FeedEntry.COLUMN_NAME_UPDATED + " DESC";

Cursor c = db.query(
  FeedEntry.TABLE_NAME,          // The table to query
  projection,                    // The columns to return
  selection,                     // The columns for the WHERE clause
  selectionArgs,                 // The values for the WHERE clause
  null,                          // don't group the rows
  null,                          // don't filter by row groups
  sortOrder                      // The sort order
);

////
cursor.moveToFirst();
long itemId = cursor.getLong(cursor.getColumnIndexOrThrow(FeedEntry._ID));
~~~

#### 从数据库中删除数据

~~~java
// Define 'where' part of query
String selection = FeedEntry.COLUMN_NAME_ENTRY_ID + " LIKE ?";
// Specify arguments in placeholder order
String[] selectionArgs = { String.valueOf(rowId) };
// Issue SQL statement
db.delete(table_name, selection, selectionArgs);
~~~

#### 更新数据库

~~~java
SQLiteDatabase db = mDbHelper.getReadableDatabase();

// New value for one column
ContentValues values = new ContentValues();
values.put(FeedEntry.COLUMN_NAME_TITLE, title);

// Which row to update, based on the ID
String selection = FeedEntry.COLUMN_NAME_ENTRY_ID + " LIKE ?";
String[] selectionArgs = { String.valueOf(rowId) };

int count = db.update(
  FeedReaderDbHelper.FeedEntry.TABLE_NAME,
  values,
  selection,
  selectionArgs);
~~~
