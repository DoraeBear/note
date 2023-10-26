---
title: 使用vuepress搭建文档站点[转] 
tags: vue,vuepress,vdoing
category: /docs/web/vuepress
emoji: "🐻"
grammar_cjkRuby: true
---


##### 一、部署VuePress

1、安装nodejs、npm
yum install -y nodejs npm

2、升级npm(版本需要=> 9.8.1)
npm -v
npm install -g npm@9.8.1

3、清空哈希表缓存，使环境变量生效
hash -r

4、安装VuePress(网络超时可以多试几次)
npm install -D vuepress

5、创建项目文件夹
mkdir my-blog
cd my-blog

6、初始化项目，会在my-blog目录下生成package.json文件
npm init

vi package.json 修改如下：

```
"scripts": {
    "dev": "vuepress dev docs --debug",
    "build": "vuepress build docs"
  },
```

7、基础配置
7.1、Vuepress默认以docs作为根目录，以README.md或index.md为主页
新建docs文件夹，然后在该文件夹下新建README.md文件
mkdir docs
vi docs/README.md

```
---
home: true
heroImage: /images/logo.PNG
heroText: Hero 标题
tagline: Hero 副标题
actionText: 快速上手 →
actionLink: /zh/guide/
features:
- title: 简洁至上
  details: 以 Markdown 为中心的项目结构，以最少的配置帮助你专注于写作。
- title: Vue驱动
  details: 享受 Vue + webpack 的开发体验，在 Markdown 中使用 Vue 组件，同时可以使用 Vue 来开发自定义主题。
- title: 高性能
  details: VuePress 为每个页面预渲染生成静态的 HTML，同时在页面被加载的时候，将作为 SPA 运行。

---

::: slot footer
修改了底部
:::
```

7.2、
在文档目录下创建一个 .vuepress 目录，所有 VuePress 相关的文件都会被放在这里。
在 .vuepress 文件夹下添加 config.js，配置网站的标题和描述：
mkdir docs/.vuepress
vi docs/.vuepress/config.js

```
module.exports ={
    //这里面路径最开始的/是指向.vuepress/public/的
    base:'/',//部署站点的基础路径,默认/
    lang: 'zh-CN',//语言设置
    title: '你好， VuePress ！',//所有页面标题的后缀，并且在默认主题的导航栏中显示
    description: '这是我的第一个 VuePress 站点',//站点描述，它会被每个页面的 Frontmatter 中的 description 字段覆盖
    head: [['link', { rel: 'icon', href: '/images/logo.PNG' }]],//站点头部的icon
}
```

这时候执行npm run docs:dev浏览器打开 IP:8080 就可以看到这样的页面：

##### 二、导航栏配置

导航栏可能包含你的页面标题、搜索框、 导航栏链接、多语言切换、仓库链接，它们均取决于你的配置

config.js中新增themeConfig属性:

```
module.exports ={
    //这里面路径最开始的/是指向.vuepress/public/的
    base:'/',//部署站点的基础路径,默认/
    lang: 'zh-CN',//语言设置
    title: '你好， VuePress ！',//所有页面标题的后缀，并且在默认主题的导航栏中显示
    description: '这是我的第一个 VuePress 站点',//站点描述，它会被每个页面的 Frontmatter 中的 description 字段覆盖
    head: [['link', { rel: 'icon', href: '/images/logo.PNG' }]],//站点头部的icon
    themeConfig: {
        logo: '/images/logo.PNG',
        nav:[
            { text: "首页", link: "/" },
            {
                text: '测试知识库',
                link: '/test/'
            },
            {
                text: "相关链接",
                items: [
                    {
                    text: "测试百度的链接",
                    link: "https://www.baidu.com",
                    },
                ],
            },
        ]
      }
}
```

##### 三、侧边栏的配置

##### 手动配置侧边栏

想要使 侧边栏（Sidebar）生效，需要配置 themeConfig.sidebar，基本的配置，需要一个包含了多个链接的数组：

```
module.exports ={
    //这里面路径最开始的/是指向.vuepress/public/的
    base:'/',//部署站点的基础路径,默认/
    lang: 'zh-CN',//语言设置
    title: '你好， VuePress ！',//所有页面标题的后缀，并且在默认主题的导航栏中显示
    description: '这是我的第一个 VuePress 站点',//站点描述，它会被每个页面的 Frontmatter 中的 description 字段覆盖
    head: [['link', { rel: 'icon', href: '/images/logo.PNG' }]],//站点头部的icon
    themeConfig: {
        logo: '/images/logo.PNG',
        nav:[
            { text: "首页", link: "/" },
            {
                text: '测试知识库',
                link: '/test/first'
            },
            {
                text: '中文文件夹测试',
                link: '/中文测试/第一个'
            },
            {
                text: "相关链接",
                items: [
                    {
                    text: "测试百度的链接",
                    link: "https://www.baidu.com",
                    },
                ],
            },
        ],
        sidebar:  {
            '/test/':[
                {
                    title: '首页',
                    path: '/'
                },
                {
                  title: "基础学习",
                  path: '/test/first',
                  children: [
                    { title: "第一个", path: "/test/first" },
                    { title: "第二个", path: "/test/second" }
                  ],
                },
                
            ],
            '/中文测试/':[
                {
                    title: '首页',
                    path: '/'
                },
                {
                    title: "中文文件夹测试",
                    path: '/中文测试/第一个',
                    children: [
                      { title: "第一个", path: "/中文测试/第一个" }
                    ],
                }
            ],
        },
        sidebarDepth: 2//侧边栏自动提取文章的几层标题
    }
}
```

这时候，无论点击导航栏还是侧边栏。都会报404，这是因为生成路由了，但是没有对应的页面（vuepress下每个md文件就是一个页面）。所以需要新建页面。

如下图，新建test文件夹和“中文测试文件夹”，如下图创建文件：

##### 四、主题应用

到这一步，已经可以开始写markdown技术文档了，但是每次新增，都需要手动新增侧边栏的配置，这样一点也不机智，我希望它能够自动生成侧边栏。
为了简化开发成本，我们可以直接使用主题，这里我用的是：[vuepress theme vdoing](vuepress-theme-vdoing (xugaoyi.com))

```
npm install vuepress-theme-vdoing -D
```

值得注意的是，这个主题，为了结构化自动生成侧边栏，文章文件和文件夹命名需要满足它的规则：

```
1、无论是文件还是文件夹，请为其名称添加上正确的正整数序号和.，从00或01开始累计，如01.文件夹、02.文件.md，我们将会按照序号的顺序来决定其在侧边栏当中的顺序。
2、同一级别目录别内即使只有一个文件或文件夹也要为其加上序号。
```











[使用vuepress搭建文档站点]: https://blog.csdn.net/weixin_42349568/article/details/126995738



----------

