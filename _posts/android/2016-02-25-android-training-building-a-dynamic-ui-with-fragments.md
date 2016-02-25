---
layout: post
title: "Android Training: 使用Fragments构建动态UI"
date: 2016-02-25 10:26:00 +0800
categories: android-training
---

### 创建一个Fragment
* Fragment可以作为Activity中的一个模块化部分
* Fragment有自己的生命周期

#### 创建一个Fragment类
* extend Fragment
* override lifecycle methods
* use onCreateView() to define layout
* [Fragments developer guide](http://developer.android.com/guide/components/fragments.html)

~~~java
public class ArticleFragment extends Fragment {
  @Override
  public View onCreateView(LayoutInflater inflater, ViewGroup container,
    Bundle savedInstanceState) {
      // Inflate the layout for this Fragment
      return inflater.inflate(R.layout.article_view, container, false);
    }
}
~~~

#### 用XML将Fragment添加到Activity
* Fragment可重用
* Fragment必须和Activity关联
* 如果使用v7 appcompat library，需要extend AppCompatActivity

~~~xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
  android:orientation="horizontal"
  android:layout_width="fill_parent"
  android:layout_height="fill_parent">

  <fragment android:name="com.example.android.fragments.HeadlinesFragment"
    android:id="@+id/headlines_fragment"
    android:layout_weight="1"
    android:layout_width="0dp"
    android:layout_height="match_parent" />

  <fragment android:name="com.example.android.fragments.ArticleFragment"
    android:id="@+id/article_fragment"
    android:layout_weight="2"
    android:layout_width="0dp"
    android:layout_height="match_parent" />
</LinearLayout>
~~~

~~~java
public class MainActivity extends FragmentActivity {
  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.news_articles);
  }
}
~~~

### 构建灵活的UI
![fragments-screen-mock](/images/building-a-dynamic-ui-with-fragments/fragments-screen-mock.png)

#### 在Activity运行时添加Fragment
* 如果在Activity运行时替换fragment，必须动态添加fragment
* 通过FragmentManager创建FragmentTransaction
* 在onCreate方法中添加fragments

~~~java
public class MainActivity extends FragmentActivity {
  @Override
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.news_articles);

    if (findViewById(R.id.fragment_container) != null) {
      if (savedInstanceState != null) {
        return;
      }

      HeadlinesFragment firstFragment = new HeadlinesFragment();

      firstFragment.setArguments(getIntent().getExtras());

      getSupportFragmentManager().beginTransaction()
        .add(R.id.fragment_container, firstFragment).commit();
    }
  }
}
~~~

#### 替换fragment
* add 替换为 replace
* addToBackStack

~~~java
ArticleFragment newFragment = new ArticleFragment();
Bundle args = new Bundle();
args.putInt(ArticleFragment.ARG_POSITION, position);
newFragment.setArguments(args);

FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();
transaction.replace(R.id.fragment_container, newFragment);
transaction.addToBackStack(null);

transaction.commit();
~~~

#### Fragments间的交互
* 两个fragments之间不应该直接交互
* 通过关联的Activity交互

#### Fragment与Activity的交互
* 在Fragment中定义接口，在Activity中实现
* 传消息给Fragment: findFragmentById()

~~~java
public class HeadlinesFragment extends ListFragment {
  OnHeadlineSelectedListener mCallback;

  public interface OnHeadlineSelectedListener {
    public void onArticleSelected(int position);
  }

  @Override
  public void onAttach(Activity activity) {
    super.onAttach(activity);

    try {
      mCallback = (OnHeadlineSelectedListener) activity;
    } catch (ClassCastException e) {
      throw new ClassCastException(activity.toString)
        + " must implement OnHeadlineSelectedListener");
    }
  }

  @Override
  public void onListItemClick(ListView l, View v, int position, long id) {
    mCallback.onArticleSelected(position);
  }
}

public static class MainActivity implements HeadlinsFragment.OnHeadlineSelectedListener {
  public void onArticleSelected(int position) {
    ArticleFragment articleFrag = (ArticleFragment)
      getSupportFragmentManager().findFragmentById(R.id.article_fragment);

    if (articleFrag != null) {
      articleFrag.updateArticleView(position);
    } else {
      ArticleFragment newFragment = new ArticleFragment();
      Bundle args = new Bundle();
      args.putInt(ArticleFragment.ARG_POSITION, position);
      newFragment.setArguments(args);

      FragmentTransaction transaction = getSupportFragmentManager().beginTransaction();

      transaction.replace(R.id.fragment_container, newFragment);
      transaction.addToBackStack(null);
      transaction.commit();
    }
  }
}
~~~
