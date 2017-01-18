---
title: Android之Lru算法
date: 2016-06-18 20:45:21
categories: android
tags: [android,glide]
---
Lru：

   LRU是Least Recently Used 的缩写，翻译过来就是“最近最少使用”，LRU缓存就是使用这种原理实现，简单的说就是缓存一定量的数据，当超过设定的阈值时就把一些过期的数据删除掉，比如我们缓存10000条数据，当数据小于10000时可以随意添加，当超过10000时就需要把新的数据添加进来，同时要把过期数据删除，以确保我们最大缓存10000条，那怎么确定删除哪条过期数据呢，采用LRU算法实现的话就是将最老的数据删掉。

 1.）初始化MemoryCache

  这里内存缓存的是Drawable 而不是Bitmap 理由是Drawable相对Bitmap来说有很大的内存优势
  int maxMemory = (int) Runtime.getRuntime().maxMemory();//获取系统分配给应用的总内存大小
  int mCacheSize = maxMemory / 8;//设置图片内存缓存占用八分之一
  mMemoryCache = new LruCache<String, Drawable>(mCacheSize) {
  //必须重写此方法，来测量Bitmap的大小
  @Override
  protected int sizeOf(String key, Drawable value) {
  　　if (value instanceof BitmapDrawable) {
  　　　　Bitmap bitmap = ((BitmapDrawable) value).getBitmap();
  　　　　return bitmap == null ? 0 : bitmap.getByteCount();
　　　}
　　　return super.sizeOf(key, value);
            }
        };

2.）添加一个Drawable到内存缓存
	/**
	 * 添加Drawable到内存缓存
     *
     * @param key
     * @param drawable
     */
    private void addDrawableToMemoryCache(String key, Drawable drawable) {
        if (getDrawableFromMemCache(key) == null && drawable != null) {
            mMemoryCache.put(key, drawable);
        }
    }
 
3.）从内存缓存中获取一个Drawable

 
    /**
     * 从内存缓存中获取一个Drawable
     *
     * @param key
     * @return
     */
    public Drawable getDrawableFromMemCache(String key) {
        return mMemoryCache.get(key);
    }
 
4.）从内存缓存中移除一个Drawable

 
   /**
     * 从内存缓存中移除
     *
     * @param key
     */
    public void removeCacheFromMemory(String key) {
        mMemoryCache.remove(key);
    }
 
5.）清空内存缓存

 
    /**
     * 清理内存缓存
     */
    public void cleanMemoryCCache() {
        mMemoryCache.evictAll();
    }
 
其实Lru缓存机制本质上就是存储在一个LinkedHashMap存储，为了保障插入的数据顺序，方便清理。

基于DiskLruCache实现磁盘缓存：

   DiskLruCache类并不是谷歌官方实现，需要自行下载，下载地址：https://github.com/JakeWharton/DiskLruCache

  1.）初始化DiskLruCache

 
       File cacheDir = context.getCacheDir();//指定的是数据的缓存地址
        long diskCacheSize = 1024 * 1024 * 30;//最多可以缓存多少字节的数据
        int appVersion = DiskLruUtils.getAppVersion(context);//指定当前应用程序的版本号
        int valueCount = 1;//指定同一个key可以对应多少个缓存文件
        try {
            mDiskCache = DiskLruCache.open(cacheDir, appVersion, valueCount, diskCacheSize);
        } catch (Exception ex) {
        }
 
2.）写入一个文件到磁盘缓存

 
    /**
     * 添加Bitmap到磁盘缓存
     *
     * @param key
     * @param value
     */
    private void addBitmapToDiskCache(String key, byte[] value) {
        OutputStream out = null;
        try {
            DiskLruCache.Editor editor = mDiskCache.edit(key);
            if (editor != null) {
                out = editor.newOutputStream(0);
                if (value != null && value.length > 0) {
                    out.write(value);
                    out.flush();
                    editor.commit();
                } else {
                    editor.abort();
                }
            }
            mDiskCache.flush();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            DiskLruUtils.closeQuietly(out);
        }
    }
 
3.）从磁盘缓存中读取Drawable

 
    /**
     * 从磁盘缓存中获取一个Drawable
     *
     * @param key
     * @return
     */
    public Drawable getDrawableFromDiskCache(String key) {
        try {
            DiskLruCache.Snapshot snapShot = mDiskCache.get(key);
            if (snapShot != null) {
                InputStream is = snapShot.getInputStream(0);
                Bitmap bitmap = BitmapFactory.decodeStream(is);
                Drawable drawable = DiskLruUtils.bitmap2Drawable(bitmap);
                //从磁盘中读取到之后 加入内存缓存
                addDrawableToMemoryCache(key, drawable);
                return drawable;
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
 
4.）从磁盘缓存中移除

 
    /**
     * 从磁盘缓存中移除
     *
     * @param key
     */
    public void removeCacheFromDisk(String key) {
        try {
            mDiskCache.remove(key);
        } catch (Exception e) {
        }
    }
 
5.）清空磁盘缓存

 
    /**
     * 清理磁盘缓存
     */
    public void cleanDiskCache() {
        try {
            mDiskCache.delete();
        } catch (Exception e) {
        }
    }
 
图片下载过程：

   接下来实例中用到了一点RxJava的知识有不了解RxJava的请自行了解一下。

  1.）采用异步方式操作磁盘缓存和网络下载， 内存缓存可以在主线程中操作

 
   public void disPlay(final ImageView imageView, String imageUrl) {
        //生成唯一key
        final String key = DiskLruUtils.hashKeyForDisk(imageUrl);
        //先从内存中读取
        Drawable drawableFromMemCache = getDrawableFromMemCache(key);
        if (drawableFromMemCache != null) {
            imageView.setImageDrawable(drawableFromMemCache);
            return;
        }
        Observable.just(imageUrl)
                .map(new Func1<String, Drawable>() {
                    @Override
                    public Drawable call(String imageUrl) { // 参数类型 String
                        //从磁盘中读取
                        Drawable drawableFromDiskCache = getDrawableFromDiskCache(key);
                        if (drawableFromDiskCache != null) {
                            return drawableFromDiskCache;
                        }
                        //网络下载
                        return download(imageUrl); // 返回类型 Drawable
                    }
                })
                .subscribeOn(Schedulers.io()) // 指定 subscribe() 发生在 IO 线程
                .observeOn(AndroidSchedulers.mainThread()) // 指定 Subscriber 的回调发生在主线程
                .subscribe(new Action1<Drawable>() {
                    @Override
                    public void call(Drawable drawable) { // 参数类型 Drawable
                        imageView.setImageDrawable(drawable);
                    }
                });
    }
 
2.）下载图片过程以及处理

 
 private Drawable download(String imageUrl) {
        HttpURLConnection urlConnection = null;
        ByteArrayOutputStream bos = null;
        InputStream ins = null;
        try {
            final URL url = new URL(imageUrl);
            urlConnection = (HttpURLConnection) url.openConnection();
            ins = urlConnection.getInputStream();
            bos = new ByteArrayOutputStream();
            int b;
            while ((b = ins.read()) != -1) {
                bos.write(b);
            }
            bos.flush();
            byte[] bytes = bos.toByteArray();
            Bitmap bitmap = DiskLruUtils.bytes2Bitmap(bytes);
            String key = DiskLruUtils.hashKeyForDisk(imageUrl);
            Drawable drawable = DiskLruUtils.bitmap2Drawable(bitmap);
            //加入内存缓存
            addDrawableToMemoryCache(key, drawable);
            //加入磁盘缓存
            addBitmapToDiskCache(key, bytes);
            return drawable;
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (urlConnection != null) {
                urlConnection.disconnect();
            }
            DiskLruUtils.closeQuietly(bos);
            DiskLruUtils.closeQuietly(ins);
        }
        return null;
    }
  