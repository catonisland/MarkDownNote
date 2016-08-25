---
title: XAMPP下启用伪静态功能 
tags: XAMPP,本地环境,伪静态
grammar_cjkRuby: true
---

 - 第一步，创建伪静态规则文件.htaccess,并放置于需要伪静态程序的安装根目录，如..\htdocs\typecho\;
 - 第二步，修改..\xampp\apache\conf\httpd.conf ,首先找到“LoadModule rewrite_module modules/mod_rewrite.so” 将其前面的#号去掉；
 - 第三步：还是修改..\xampp\apache\conf\httpd.conf ,找到：

    > AllowOverride none 
    Require all denied

    将其改为：

    > Options FollowSymLinks 
    AllowOverride All

 - ps:第一步和第三步中的typecho及其路径需依据自己实际做对应修改。

 - pss:.htaccess文件的内容请根据使用web程序的rewrite规则做修改。
