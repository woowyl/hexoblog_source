# 博客部署指南

1. 这个仓库是博客的源码，真正博客展示的内容在[woowyl.github.io](https://github.com/woowyl/woowyl.github.io) 

2. [woowyl.github.io](https://github.com/woowyl/woowyl.github.io) 内容是靠本仓库的代码通过hexo生成，并推送到以上仓库

3. 生成步骤
   1. 下载本项目到本地
   2. cd project_fold
   3. npm install
   4. 
      ``` js
      hexo new post POST_NAME  //也可在 /source/_posts/ 目录下直接新建

      hexo serve // run hexo in local environment
      
      hexo clean && hexo deploy // hexo will push the static files automatically into the specific branch(gh-pages) of your repo!
      ```
> Ported Theme of [Hux Blog](https://github.com/Huxpro/huxpro.github.io), Thank [Huxpro](https://github.com/Huxpro) for designing such a flawless theme. Thanks [kaijun](http://kaijun.rocks) for create this hexo project.
