---
title: hexo部署到服务器
date: 2022-05-20 13:38:05
tags: 服务器部署 hexo 
---

### hexo部署到服务器的过程记录

  重启了很久之前用的博客，并将其部署到自己的腾讯云服务器上。
1. 由于我们使用git部署，需要在服务器上将本地电脑下的`~/./ssh/id_ras.pub`复制到服务器上的`./ssh/authorized_keys`文件下。这样可以进行免密登录。
2. 服务器上在家目录`~/project`下新建俩文件夹`hexo`和`repo`，其中`repo`是git自动deploy的关键。
   1. 在`repo`下输入命令`git init --bare hexo.git`。
   2. 在`hexo.git/hooks`新建`post-update`，并输入以下。
   ```
   #!/bin/bash
   git --work-tree=/home/xyguo/project/hexo --git-dir=/home/xyguo/project/repo/hexo.git checkout -f    
   ```
3. nginx配置相应的文件。其中配置https文件放到了`/var/ssl/blog`目录下。
4. `service nginx restart`重启nginx。
   
#### 问题记录
  本地`hexo g -d`时候，记得使用`node12`14会报错，查了一下是hexo和node@14不兼容。
  参考地址：[将hexo部署到服务器](https://hjxlog.com/posts/20191130a1.html)
