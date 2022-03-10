---
title: "博客部署与博客源文件备份的几个问题"
subtitle: ""
date: 2022-03-10T19:18:42+08:00
draft: true
author: ""
authorLink: ""
description: ""
keywords: ""
license: ""
comment: false
weight: 0

tags:
- draft
categories:
- draft

hiddenFromHomePage: false
hiddenFromSearch: false

summary: ""
resources:
- name: featured-image
  src: featured-image.jpg
- name: featured-image-preview
  src: featured-image-preview.jpg

toc:
  enable: true
math:
  enable: false
lightgallery: false
seo:
  images: []

# See details front matter: /theme-documentation-content/#front-matter
---

<!--more-->

#### 1.博客源文件与静态部署文件的区别

- 博客源文件：

  - 本地位置
  ```shell
  (base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
  ╰─$pwd                                                         
  /Users/ucaslilong/Documents/myblog
  (base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
  ╰─$ tree -L 1
  .
  ├── archetypes
  ├── blogpirture
  ├── config.toml
  ├── content
  ├── data
  ├── layouts
  ├── public
  ├── resources
  ├── static
  └── themes

  9 directories, 1 file
  ```
  - Github位置
  [https://github.com/cuglilong/myblog](https://github.com/cuglilong/myblog)


- 博客静态网站文件:

    -  本地位置

    ```shell
  /Users/ucaslilong/Documents/myblog/public
    ```

    - Github位置
    [https://github.com/cuglilong/cuglilong.github.io](https://github.com/cuglilong/cuglilong.github.io)

#### 2.日常操作

- 写博客

```shell
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ ls
archetypes  config.toml data        public      static
blogpirture content     layouts     resources   themes
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ pwd                                                         
/Users/ucaslilong/Documents/myblog
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ hugo new posts/2022-03-10-example-blog.md                                
Content "/Users/ucaslilong/Documents/myblog/content/posts/2022-03-10-example-blog.md" created
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ code content/posts/2022-03-10-example-blog.md 
```

- 发布博客

```shell
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ pwd
/Users/ucaslilong/Documents/myblog
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ hugo --theme=FixIt --buildDrafts --baseUrl="https://cuglilong.github.io/"
Start building sites … 
hugo v0.93.2+extended darwin/amd64 BuildDate=unknown

                   | ZH-CN  
-------------------+--------
  Pages            |    43  
  Paginator pages  |     1  
  Non-page files   |     0  
  Static files     |    86  
  Processed images |     0  
  Aliases          |    12  
  Sitemaps         |     1  
  Cleaned          |     0  

Total in 201 ms
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ cd public                                    
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog/public ‹master●› 
╰─$ git remote rm origin                                     
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog/public ‹master●› 
╰─$ git remote add origin git@github.com:cuglilong/cuglilong.github.io.git
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog/public ‹master●› 
╰─$ git add .                                                             
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog/public ‹master●› 
╰─$ git commit -m "add example blog"  
[master ef712db] add example blog
 17 files changed, 496 insertions(+), 66 deletions(-)
 create mode 100644 posts/2022-03-10-about-blog/index.html
 create mode 100644 posts/2022-03-10-example-blog/index.html
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog/public ‹master› 
╰─$ git push -u origin master       
Enumerating objects: 51, done.
Counting objects: 100% (51/51), done.
Delta compression using up to 8 threads
Compressing objects: 100% (25/25), done.
Writing objects: 100% (29/29), 3.69 KiB | 1.23 MiB/s, done.
Total 29 (delta 20), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (20/20), completed with 16 local objects.
To github.com:cuglilong/cuglilong.github.io.git
   447653b..ef712db  master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

- 备份博客源文件
```shell
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog/public ‹master› 
╰─$ pwd      
/Users/ucaslilong/Documents/myblog/public
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog/public ‹master› 
╰─$ cd ..    
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ ls
archetypes  config.toml data        public      static
blogpirture content     layouts     resources   themes
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ git remote rm origin                                                  
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ git remote add origin git@github.com:cuglilong/myblog.git             
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ git add .                                                
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master●› 
╰─$ git commit -m "upload blog source"
[master 5f4305b] upload blog source
 2 files changed, 116 insertions(+), 2 deletions(-)
 create mode 100644 content/posts/2022-03-10-about-blog.md
 rename content/posts/{2022-03-10-learn-html.md => 2022-03-10-example-blog.md} (88%)
(base) ╭─ucaslilong@Long-Mac ~/Documents/myblog ‹master› 
╰─$ git push -u origin master         
Enumerating objects: 9, done.
Counting objects: 100% (9/9), done.
Delta compression using up to 8 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 1.79 KiB | 1.79 MiB/s, done.
Total 6 (delta 4), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (4/4), completed with 3 local objects.
To github.com:cuglilong/myblog.git
   fb63b88..5f4305b  master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```
