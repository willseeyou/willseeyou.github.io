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
SharedPreferences ahredPref = context.getSharedPreferences(getString(R.string.preference_file_key),
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
