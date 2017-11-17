---
title: vue 目录解析
date: 2017-11-08 16:03:12
categories: web
tags: [web]
---

# vue 目录解析

├── build/                      # webpack config files / webpack 配置文件
│   └── ...
├── config/
│   ├── index.js                # main project config / 项目主要配置
│   └── ...
├── src/                        # 主要的项目开发文件都在这个目录下：            
│   ├── main.js                 # app entry file / 应用入口文件
│   ├── App.vue                 # main app component / App 父组件
│   ├── components/             # ui components / 可复用的 ui 组件
│   │   └── ...
│   ├── assets/                 # module assets (processed by webpack) / 模块资源（经过 webpack 处理，如文件合并，图片压缩等）
│   │   └── ...
│   ├── page/                   ## 以页面为单位的 .vue 文件
│   │   ├── index.vue           ## 一级 router-view，顶部导航栏和左侧侧边导航栏
│   │   ├── 404.vue             ## 404 页面
│   │   ├── menu1/              ## 二级 router-view，导航切换后的页面内容
│   │   │   └── ...
│   │   └── menu2/              ## 按照菜单项创建文件夹对文件进行组织管理
│   │       └── ...
│   └── router/                 
│       └── index.js            # 路由配置文件
├── static/                     # pure static assets (directly copied) / 纯静态资源（直接拷贝使用，不经过 webpack 处理）
├── .babelrc                    # babel config
├── .eslintrc.js                # eslint config
├── .editorconfig               # editor config
├── index.html                  # index.html template
├── package.json                # build scripts and dependencies
└── ...


关闭Eslint：
build/webpack.base.conf.js
module: {
    rules: [
      // {
      //   test: /\.(js|vue)$/,
      //   loader: 'eslint-loader',
      //   enforce: "pre",
      //   include: [resolve('src'), resolve('test')],
      //   options: {
      //     formatter: require('eslint-friendly-formatter')
      //   }
      // },
      {
        test: /\.vue$/,
        loader: 'vue-loader',
        options: vueLoaderConfig
      },
      ...
    ]
  },

 