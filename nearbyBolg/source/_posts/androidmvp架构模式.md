---
title: androidMVP架构模式
date: 2016/10/31 09:47:42
categories: android
tags: [android,mvp]
---
android MVP模式在Android项目中的使用

在开发当中我们会涉及到一些架构模式，对于初级程序员来说是很陌生，但是对于中级过渡高级的程序员来说是非常重要需要了解的模式。首先我们先了解有哪些架构模式：不管都前端，移动端，后端都会涉及到这三种：MVC、MVP、MVVM。
概念：什么是MVP
MVP是模型(Model)、视图(View)、主持人(Presenter)的缩写,分别代表项目中3个不同的模块。
模型(Model):负责处理数据的加载或者存储，比如从网络或者本地数据库获取数据等；
视图(View):负责界面数据的展示，与用户进行交互；
主持人(Presenter):相当于协调者，是模型与视图之间的桥梁，将模型与视图分离开来。

如下图所示，View与Model并不直接交互，而是使用Presenter作为View与Model之间的桥梁。其中Presenter中同时持有View层以及Model层的Interface的引用，而View层持有Presenter层Ineterface的引用。当View层某个界面需要展示展示某些数据的时候，首先会调用Presenter层的某个接口，然后Presneter层会调用Model层请求数据，当Model层数据加载成功之后会调用Presenter层的回调方法通知Presenter层数据加载完毕，最后Presneter层再调用View层的接口将加载后的数据展示给用户。这就是Mvp模式的整个核心过程。
![此处输入图片的描述][1]

这样分层的好处就是大大减少了Model与View层之间的耦合度。一方面可以使得View层和Model层单独开发与测试，互不依赖。另一方面Model层可以封装复用，可以极大的减少代码量。当然，Mvp还有其他的一些优点。

  [1]: http://images2015.cnblogs.com/blog/420264/201512/420264-20151223222959015-298327306.png
```  
  1.View层
    View层首页数据展示模块的组件是Activity，里面有一个RecycleView 布局如下：  

    <?xml version="1.0" encoding="utf-8"?>
     <android.support.v7.widget.RecyclerView
            xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:app="http://schemas.android.com/apk/res-auto"
            android:id="@+id/recycle_view"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:scrollbars="vertical"
            app:layout_behavior="@string/appbar_scrolling_view_behavior"
            android:paddingTop="@dimen/card_margin">
        </android.support.v7.widget.RecyclerView>
```
首页列表模块主要展示从网络获取的首页列表信息，view层的接口大概需要如下方法：
(1)请求数据的过程中需要提示‘正在加载’的反馈信息给用户
(2)加载数据成功后，将加载得到的数据填充到RecycleView展示给用户
(3)加载数据成功后，将提示‘正在加载’反馈信息取消掉
(4)若加载数据失败，如网络连接、则需要给用户提示信息。 

```
	//业务接口
    public interface HomeContract {
        interface View {
           void onLoading();
           void onError(String error);
           void updateUI(XXX xxx);
           void ToastMessage(String message);
        }
        interface Action extends IPresenter {
            void requestHome(XXX... xxx);
        }
    }

    //业务处理请求
    public class HomePresenter implements HomeContract.Action{
            HomeContract.view view;
            public HomePresenter(HomeContract.view view){
                    this.view =view;
            }
            @Override
            pubublic void requestHome(XXX... xxx){
                    //请求网络数据处理业务层 
                    view.onLoading();
                    view.onError();
                    view.updateUI();
                    view.ToastMessage();
            }
    }
```
首页列表Activity中实现上述接口：

```
public class HomeActivity extends AppCompatActivity implements HomeContract.view {
      HomeContract.Action action;
        @Override
        public void onLoading(){
                //提示加载信息
        }
        @Override
        public void onError(String error){
                //加载错误ui
        }
        @Override
        public void updateUI(XXX xxx){
                //更新UI数据
        }
        @Override
        public void ToastMessage(String message){
               //网络请求结束提示信息
        }
        @Override
        public void onCreate(@Nullable Bundle savedInstanceState){
            super.onCreate(savedInstanceState);
            action = new HomePresenter(this);
            action.requestHome(xx);
        }
    }
```
当用户打开首页界面时，就请求网络获取数据，初始化Presenter引用实例，调用Presenter层的requestHome接口，业务处理完毕后，调用相应的onLoading()、onError()、updateUI()、ToastMessage()方法处理不同逻辑。
    