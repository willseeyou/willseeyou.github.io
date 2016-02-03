---
layout: post
title:  "Android Training: 管理Activity的生命周期"
date:   2016-02-02 15:40:00 +0800
categories: android training
---
### 启动与销毁Activity
![Basic LifeCycle of Activity](/images/manage-activity/basic-lifecycle.png)

#### 静态状态
* Resumed: Activity处在前台，用户可以交互
* Paused: Activity部分被另一个Activity遮盖；另外的半透明Activity来到前台，不覆盖整个屏幕
* Stopped: Activity完全隐藏，用户不可见。Activity实例及所有状态都会保留

#### 其他状态:暂态 transient
* Created
* Started

`onCreate()` -> `onStart()` -> `onResume()`

#### 指定程序首次启动Activity
```xml
<activity android:name=".MainActivity" android:label="@string/app_name">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <action android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```

#### 创建一个新的实例
* 系统会调用Activity中的onCreate()方法创建实例
* 在onCreate()方法中定义UI及实例化类成员变量
* onCreate()中尽量少做事情

```java
TextView mTextView; // 用于布局中的成员变量

@Override
public void onCreate(Bundle savedInstanceState) {
  super.onCreate(savedInstanceState);

  // 设置UI布局
  setContentView(R.layout.main_activity);

  // 初始化成员变量，通过id找到对应布局中的元素
  mTextView = (TextView) findViewById(R.id.text_message);

  // 确保运行在HoneyComb或者更高级别的API
  if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
    ActionBar actionBar = getActionBar();
    actionBar.setHomeButtonEnabled(false);
  }
}
```
![basic-lifecycle-create](/images/manage-activity/basic-lifecycle-create.png)

#### 销毁Activity
`如果Activity含有在onCreate调用时创建的后台线程，或者其他导致内存泄漏的资源，需要在onDestroy中进行资源清理，杀死后台线程`
```java
@Override
public void onDestroy() {
  super.onDestroy();

  // Stop method tracing that the activity started during onCreate()
  android.os.Debug.stopMethodTracing();
}
```

### 暂停与恢复Activity
![basic-lifecycle-paused](/images/manage-activity/basic-lifecycle-paused.png)

onPause应该做的事：
* 停止动画或者其他正在进行的操作
* 提交在用户离开时期待保存的内容（邮件草稿）
* 释放系统资源，如broadcast receiver, sensors，或其他影响电量的资源

```java
@Override
public void onPause() {
  super.onPause();

  // 释放Camera
  if (mCamera != null) {
    mCamera.release;
    mCamera = null;
  }
}
```

onPause不应该做的事：
* CPU-intensive:写数据到DB，需要放到onStop()中做
* 保存用户改变的数据到永久存储中

`Activity处于暂停状态时，Activity实例是驻留在内存中的，并且在activity恢复的时候重新调用，不需要重新初始化组件`

```java
@Override
public void onResume() {
  super.onResume();

  if (mCamera == null) {
    initializeCamera();
  }
}
```

### 停止与重启Activity
使用场合：
* 通过最近使用App菜单打开另外一个app，然后通过主界面启动图标或最近使用App回到我们的app，activity会重启
* 在app中启动一个新的Activity，在新的activity点击Back按钮，第一个activity会重启
* 使用app过程中接到来电

![basic-lifecycle-stopped](/images/manage-activity/basic-lifecycle-stopped.png)

`需要使用onStop()释放资源` `在onStop()中执行CPU intensive的操作，不要在onPuase()中`

```java
@Override
protected void onStop() {
  super.onStop();

  ContentValues values = new ContentValues();
  values.put(NotePad.Notes.COLUMN_NAME_NOTE, getCurrentNoteText());
  values.put(NotePad.Notes.COLUMN_NAME_TITLE, getCurrentNoteTitle());

  getContentResolver().update(
    mUri,
    values,
    null,
    null
  );
}
```

```java
@Override
protected void onStart() {
  super.onStart();

  LocationManager locationManager = (LocationManager) getSystemService(Context.LOCATION_SERVICE);
  boolean getEnabled = locationManager.isProviderEnabled(LocationManager.GPS_PROVIDER);

  if (!gpsEnabled) {

  }
}

@Override
protected void onRestart() {
  super.onRestart();
}
```

### 重新创建Activity
* 被系统用于恢复之前状态而保存的数据被叫做"instance state"
* 默认情况下，系统使用Bundle保存每一个View对象中的信息
* 跳转到其他Activity或是点击Home键会执行onSaveInstanceState
![basic-lifecycle-savestate](/images/manage-activity/basic-lifecycle-savestate.png)

#### 保存Activity状态
```java
static final String STATE_SCORE = "playerScore";
static final String STATE_LEVEL = "playerLevel";

@Override
public void onSaveInstanceState(Bundle savedInstanceState) {
  // Save the user's current game state
  savedInstanceState.putInt(STATE_SCORE, mCurrentScore);
  savedInstanceState.putInt(STATE_LEVEL, mCurrentLevel);

  super.onSaveInstanceState(savedInstanceState);
}
```
