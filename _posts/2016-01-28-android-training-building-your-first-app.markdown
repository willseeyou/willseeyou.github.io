---
layout: post
title:  "Android Training: 创建第一个APP"
date:   2016-01-28 10:23:00 +0800
categories: android training
---

### 搭建环境
1. 下载 [Android Studio](http://developer.android.com/sdk/index.html)
2. 下载 SDK tools [SDK Manager](http://developer.android.com/tools/help/sdk-manager.html)

### 创建应用
![create-new-project-step1](/images/build-your-first-app/create-new-project-step1.png)

![create-new-project-step2](/images/build-your-first-app/create-new-project-step2.png)

![create-new-project-step3](/images/build-your-first-app/create-new-project-step3.png)

![create-new-project-step4](/images/build-your-first-app/create-new-project-step4.png)

### app/src/main/res/layout/activity_my.xml
`布局文件` `Text View` `App bar` `Floating action button` `Material Design`

![activity_my_layout.png](/images/build-your-first-app/activity_my_layout.png)

### app/src/main/java/com.example.myfirstapp/MyActivity.java
`start activity` `load layout file`

### app/src/main/AndroidManifest.xml
`fundamental characteristics of the app` `define components`

### app/build.gradle
* 使用Gradle编译和构建App
* compiledSdkVersion: 编译App的平台版本
* applicationId
* minSdkVersion
* targetSdkVersion
* [Building Your Project with Gradle](http://developer.android.com/sdk/installing/studio-build.html)

### /res包含应用的resources
* drawable-density:  [drawable resources](http://developer.android.com/guide/topics/resources/drawable-resource.html)
* layout: user interface
* menu
* mipmap: Launcher icons
* values

### 在真机上运行
* Enable USB debugging

### 通过command line运行
`gradlew.bat assembleDebug`<br>
`adb install app/build/outputs/apk/app-debug.apk`

### 创建简单User Interface
* View: UI widgets such as buttions or text fields
* ViewGroup: invisible view containers
* Define UI in XML using a hierarchy of UI elements
* Layouts are subclasses of the ViewGroup

### 创建线性布局 [Linear Layout](http://developer.android.com/intl/zh-cn/guide/topics/ui/declaring-layout.html)
* 修改content_my.xml为：

{% highlight ruby %}
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal"
    app:layout_behavior="@string/appbar_scrolling_view_behavior"
    tools:showIn="@layout/activity_my">
{% endhighlight %}

### 添加 Text Field

{% highlight ruby %}
<EditText android:id="@+id/edit_message"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:hint="@string/edit_message" />
{% endhighlight %}

### 添加 Button

{% highlight ruby %}
<Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/button_send"/>
{% endhighlight %}

![Button](/images/build-your-first-app/first-activity-button.png)

### Make the Input Box Fill in the Screen Width

{% highlight ruby %}
android:layout_weight="1"
android:layout_width="0dp"
{% endhighlight %}

![Input Box](/images/build-your-first-app/first-activity-inputbox.png)

### 启动另一个Activity
* 为Button添加android:onClick属性

{% highlight ruby %}
<Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/button_send"
        android:onClick="sendMessage" />
{% endhighlight %}

* 在MyActivity.java中添加sendMessage方法

{% highlight java %}
public void sendMessage(View view) {
  // Do something in response to button
}
{% endhighlight %}

`public` `void` `参数唯一（View类型，代表被点击的视图）`

### 创建Intent

{% highlight java %}
Intent intent = new Intent(this, DisplayMessageActivity.class);
EditText editText = (EditText) findViewById(R.id.edit_message);
String message = editText.getText().toString();
intent.putExtra(EXTRA_MESSAGE, message);
startActivity(intent);
{% endhighlight %}


### 创建DisplayMessageActivity
![Create Second Activity](/images/build-your-first-app/create-second-activity.png)

### 接收Intent中的消息

{% highlight java %}
Intent intent = getIntent();
String message = intent.getStringExtra(MyActivity.EXTRA_MESSAGE);
{% endhighlight %}
