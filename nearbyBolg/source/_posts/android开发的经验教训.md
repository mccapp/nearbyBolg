---
title: android开发的经验教训
date: 2016-3-11 10:19:45
categories: android
tags: [android]
---

 1. 在添加任何第三方party之前，请三思：这真的是一个成熟的项目吗？

 2. 如果一个东西用户看不到，[就不要绘制它！][1]

 3. 除非真的需要，否则别使用数据库；

 4. 达到65k方法数限制来的非常快，真的，非常快！不过 [multidexing 可以拯救你][2];

 5. [RxJava][3] 是 [AsyncTasks][4] 以及其它杂碎的最佳替代者；

 6. [Retrofit][5] 可能是现在最佳的网络请求库；

 7. 使用 [Retrolambda][6]来简化你的代码；

 8. 把 [RxJava 和 Retrofit 以及 Retrolambda][7] 结合起来会让你酷炸天！

 9. 我使用 [EventBus][8]而且它也很好，但是我并不会用太多，因为这样代码库会变得凌乱；

 10. [包名按照功能来，而不是按分层来][9];

 11. 把所有东西都移到 application thread之外；

 12. [lint][10] 可以帮助你优化布局与布局树，这样你就能识别出那些可能已经被移除的冗余的View；

 13. 如果你在使用gradle，使用一切[可能][11]的方式去加速它；

 14. 对builds做[profile reports][12]，以便知道什么占据了编译时间；

 15. 使用一个 [有名的][13] 架构；

 16. [测试时比较花时间，但是一旦你找到诀窍它可以比不使用测试更快、更健壮][14];

 17. 使用 [依赖注入][15] 让你的app更模块化，从而更易于测试；

 18. 收听 [fragmented podcast][16] 将会让你受益；

 19. [千万别用私人邮箱作为你安卓市场的发布者账号][17];

 20. 总是使用[恰当的][18]输入类型；

 21. Use analytics to find usage patterns and isolate bugs;

 22. 关注最新的 [libraries][19]（使用[dryrun][20] 来快速查看它们的的效果）

 23. 你的service应该做你所需的事情，并且尽快死去；

 24. 使用 [Account Manager][21] 来提示登录用户名和邮箱地址；

 25. 使用CI（持续集成）来构建和发布你的beta和产品apk；

 26. 不要运行自己的CIserver，维护server是费时的事情，因为磁盘空间，安全问题，更新server以防止SSL攻击等等原因。使用circleci, travis 或者 shippable, 它们便宜，而且你也可以少担心一些事情；

 27. Automate your deployments to the playstore;

 28. 如果一个library很大而你只需要使用其中的一部分功能，你应该寻找一个更小的来替代（比如依靠[proguard][22] ）；

 29. 不要使用多于实际需要的module。从零开始编译一个module或者仅仅是检查上个module是否最新所需要的时间几乎是加载 binary .jar/.aar依赖的4倍以上；
 30. 开始[考虑用SVG来替代PNG][23] ；

 31. Make library abstraction classes, it’ll be way easier to switch to a new library if you only need to switch in one place (e.g.AppLogger.d(“message”) can contain Log.d(TAG, message) and later realise that Timber.d(message) is a better option);

 32. 监控你的连接情况以及连接类型（wifi之下更新更多数据？）；

 33. 监控你的电源和电量（在充电的时候更新更多的数据？电量低的时候暂停更新？）；

 34. 一个用户界面就如一个笑话那样，如果你需要解释，那么证明它并不够好；

 35. [Tests are great for performance: Write slow (but correct) implementation then verify optimizations don’t break anything with tests.][24]

原文：[Building Android Apps — 30 things that experience made me learn the hard way。][25] 


  [1]: http://riggaroo.co.za/optimizing-layouts-in-android-reducing-overdraw/
  [2]: https://medium.com/@rotxed/dex-skys-the-limit-no-65k-methods-is-28e6cb40cf71
  [3]: https://github.com/ReactiveX/RxJava
  [4]: https://medium.com/swlh/party-tricks-with-rxjava-rxandroid-retrolambda-1b06ed7cd29c
  [5]: http://square.github.io/retrofit/
  [6]: https://medium.com/android-news/retrolambda-on-android-191cc8151f85
  [7]: https://medium.com/swlh/party-tricks-with-rxjava-rxandroid-retrolambda-1b06ed7cd29c
  [8]: https://github.com/greenrobot/EventBus
  [9]: https://medium.com/the-engineering-team/package-by-features-not-layers-2d076df1964d
  [10]: http://developer.android.com/tools/help/layoutopt.html
  [11]: https://medium.com/the-engineering-team/speeding-up-gradle-builds-619c442113cb
  [12]: https://medium.com/the-engineering-team/speeding-up-gradle-builds-619c442113cb
  [13]: http://fernandocejas.com/2015/07/18/architecting-android-the-evolution/
  [14]: http://stackoverflow.com/a/67500/794485
  [15]: http://fernandocejas.com/2015/04/11/tasting-dagger-2-on-android/
  [16]: http://fragmentedpodcast.com/
  [17]: https://www.reddit.com/r/Android/comments/2hywu9/google_play_only_one_strike_is_needed_to_ruin_you/
  [18]: http://developer.android.com/training/keyboard-input/style.html
  [19]: http://android-arsenal.com/
  [20]: https://github.com/cesarferreira/dryrun
  [21]: http://developer.android.com/reference/android/accounts/AccountManager.html
  [22]: http://developer.android.com/tools/help/proguard.html
  [23]: http://developer.android.com/tools/help/vector-asset-studio.html
  [24]: https://twitter.com/danlew42/status/677151453476032512
  [25]: https://medium.com/@cesarmcferreira/building-android-apps-30-things-that-experience-made-me-learn-the-hard-way-313680430bf9#.6d0cbhkxa