---
title: Android知识点总结
date: 2019-06-20 21:47:29
tags: 面试
---

最近有作为面试官去面试，感觉有必要对Android的一些知识点进行总结，这里会参考各种资料和书籍，持续的去记录Android的一些知识点。这个过程有助于自己对于一些知识的理解整理，也可以作为后续自己出去面试或者自己面试的试题库来源。

### Android的线程和线程池

#### AsyncTask

首先关于AsyncTask的介绍和作用，引用谷歌文档说明

>  AsyncTask旨在成为Thread和Handler的辅助类，并不构成通用的线程框架。理想情况下，AsyncTasks应该用于短操作（最多几秒钟。）如果需要保持线程长时间运行，强烈建议您使用java.util.concurrent包提供的各种API，例如Executor，ThreadPoolExecutor和FutureTask

##### 介绍以及使用

AsyncTask是轻量级的异步任务类，在线程池中执行后台任务。

* 是抽象的泛型类，提供了Params、Progress、Result三个泛型参数。Params：参数的类型，Progress：后台任务执行进度的类型，Result：后台任务的返回结果的类型

  ```
  public abstract class AsyncTask<Params, Progress, Result>
  ```

* 提供了4个核心方法

  * onPreExecute(),在主线程中执行，在异步任务执行之前，此方法会调用，一般用于做一些准备工作。
  * doInBackground(Params… params) 在线程池中做耗时操作
  * onProgressUpdate(Progress… values) 主线程中执行，后台任务进度改变回调该方法
  * onPostExecute(Result result) 主线程执行，异步任务执行之后 result为doInBackground的返回值。

* 使用限制条件

  * AsyncTask的类必须在主线程中加载
  * AsyncTask的对象必须在主线程中创建 //源码中这么规定，内部创建handler用了主线程的Looper
  * execute方法必须在UI线程调用
  * 一个AsyncTask对象只能调用一次execute方法
  * AsyncTask的execute方法是串行执行任务，executeOnExecutor方法可以并行执行任务

##### 原理分析

execute方法中会调用executeOnExecutor方法，executeOnExecutor方法中会有以下的调用

```
mStatus = Status.RUNNING;
//这个准备的方法应该是在主线程执行的，所以execute方法必须在UI线程调用
onPreExecute();
mWorker.mParams = params;
exec.execute(mFuture);
```

内部静态变量 InternalHandler 在类初始化时就会创建，决定了AsyncTask的类必须在主线程中加载。

#### HandlerThread

HandlerThread继承了Thread，它是一种可以使用Handler的Thread。实现方式时在run方法中调用Looper.prepare()来创建消息队列，并Looper.loop()来开启消息循环。这样的目的就允许在HandlerThread中创建Handler。