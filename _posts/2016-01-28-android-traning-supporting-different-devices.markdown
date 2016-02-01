---
layout: post
title:  "Android Training: 支持不同的设备"
date:   2016-01-28 15:28:00 +0800
categories: android training
---

### 支持不同的语言
* Good practice: Extract UI strings from app code and keep them in an external file
* res/values/strings.xml
* a hyphen and the `ISO language code`
* [Providing Alternative Resources](http://developer.android.com/guide/topics/resources/providing-resources.html#AlternativeResources)

{% highlight ruby %}
MyProject/
    res/
       values/
           strings.xml
       values-es/
           strings.xml
       values-fr/
           strings.xml
{% endhighlight %}

### 支持不同的屏幕
* Two properties: `size` `density`
* Four generalized sizes: `small` `normal` `large` `xlarge`
* Four generalized densities: `low(ldpi)` `medium(mdpi)` `high(hdpi)` `extra high(xhdpi)`
* [Providing Resources](http://developer.android.com/guide/topics/resources/providing-resources.html#BestMatch)
* [Designing for Multiple Screens](http://developer.android.com/training/multiscreen/index.html)

{% highlight ruby %}
MyProject/
    res/
        layout/
            main.xml
        layout-large/
            main.xml
{% endhighlight %}

{% highlight ruby %}
MyProject/
    res/
        layout/              # default (portrait)
            main.xml
        layout-land/         # landscape
            main.xml
        layout-large/        # large (portrait)
            main.xml
        layout-large-land/   # large landscape
            main.xml
{% endhighlight %}

#### 创建不同的Bitmaps
`xhdpi:2.0` `hdpi:1.5` `mdpi:1.0` `ldpi:0.75`

[Iconography design guide](http://developer.android.com/design/style/iconography.html)

{% highlight ruby %}
MyProject/
    res/
        drawable-xhdpi/
            awesomeimage.png
        drawable-hdpi/
            awesomeimage.png
        drawable-mdpi/
            awesomeimage.png
        drawable-ldpi/
            awesomeimage.png
{% endhighlight %}

### 支持不同的平台版本
* Good practice: support about 90% of the active devices
* Use `Android Support Library`

{% highlight ruby %}
<manifest xmlns:android="http://schemas.android.com/apk/res/android" ... >
    <uses-sdk android:minSdkVersion="4" android:targetSdkVersion="15" />
    ...
</manifest>
{% endhighlight %}

#### 运行时检查系统版本

{% highlight java %}
private void setUpActionBar() {
    // Make sure we're running on Honeycomb or higher to use ActionBar APIs
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.HONEYCOMB) {
        ActionBar actionBar = getActionBar();
        actionBar.setDisplayHomeAsUpEnabled(true);
    }
}
{% endhighlight %}

#### 使用平台Styles和Themes
* [Styles and Themes](http://developer.android.com/guide/topics/ui/themes.html)
