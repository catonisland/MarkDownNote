
## XAMPP实现本地搭建多个网站.md ##


> xampp 是一个非常方便的本地 apache + php + mysql 的调试环境，在本地安装测试 WordPress 等各种博客、论坛程序非常方便。
今天我们来给大家介绍一下，**如何使用XAMPP在本地进行安装多个网站。**

一般情况下，我们只需要网站程序放到 xampp/htdoc/ 目录下，然后在浏览器里输入 IP 地址 http://127.0.0.1/ 或者输入域名 http://localhost/ 就可以了。

但是这样我们只能使用一个程序，建立一个网站。如果我们想要测试测试不同的程序，比如一个Typecho的网站，一个Carbon Forum 的论坛，那就比较麻烦了。
我们这篇文章，就给大家介绍一下，如何在 XAMPP 中添加多站点支持，让你可以很方便地安装多个网站。

## 需求 ##
**现在假定你想实现以下效果**

|程序名|路径|访问网址
|---|---|---
|Typecho| /xampp/htdocs/a|t.com
|CF|/xampp/htdocs/b|cf.com


## 实现方式 ##
 1. **在 hosts 文件中设置域名解析**
 
    和正常的域名一样，这两个域名并不存在，我们也需要进行解析，而因为我们搭建在本地且仅本地访问，我们可以通过修改本地 hosts 文件来解决这个问题。关于解析的具体介绍参考本站其他文章。

    - **找到文件位置**
    
        Hosts 文件保存在 C:\Windows\System32\drivers\etc下，无扩展名，使用文本编辑器（记事本等）打开编辑；
        **Windows 7 及以上系统，需要首先使用管理权权限打开记事本。**
        
    - **修改内容**
    
        在本例子中，我们应该这样修改,讲以下内容添加至文件最后面

        ```
        127.0.0.1 t.com typecho
        127.0.0.1 cf.com Carbon Forum
        ```
        
 2. **在 htdocs 目录下建立文件夹**
 
    - 我们在 xampp/htdocs 目录下建立两个文件夹，分别命名为 a 和 b 。
    这里文件目录的名字仅仅是为了便于区分，并非强制要求；只要目录名和后面的设置一致即可。

    - 在a、b目录下加入一个 index.html 文件，内容如下：

        ```
        <html><H1>t.com已经可以访问了</H1></html>
        ```

 3. **在 apache 中添加多域名支持**
  
    - **失效httpd.conf**
 
    打开xampp\apache\conf\httpd.conf文件，搜索 " Include conf/extra/httpd-vhosts.conf "，确保前面没有 # 注释符，也就确保引入了 vhosts 虚拟主机配置文件。 **开启了httpd-vhosts.conf，默认的httpd.conf默认配置就失效**

    - **修改https-vhosts.conf**
    
    使用文件编辑器打开文件:xampp/apache/conf/extra/httpd-vhosts.conf 。
    首先找到 NameVirtualHost *:80 ；去掉前面的注释符号 #。如果没有这一行，就自己添加一行。
    同时添加以下代码：

    ```
    <VirtualHost *:80>
        ServerAdmin postmaster@t.com
        DocumentRoot "/xampp/htdocs/a"
        ServerName t.com
        ErrorLog "logs/t.com-error.log"
        CustomLog "logs/t.com-access.log" common
    </VirtualHost> 
    
    <VirtualHost *:80>
        ServerAdmin postmaster@cf.com
        DocumentRoot "/xampp/htdocs/b"
        ServerName cf.com
        ErrorLog "logs/cf.com-error.log"
        CustomLog "logs/cf.com-access.log" common
    </VirtualHost>
    ```

    这样，访问本机IP 127.0.0.1 或者说 localhost 的请求将全部指向 vhosts.conf 中的第一个虚拟主机，即/a目录
    而通过域名cf.com访问的请求将指向/b目录
    如需默认访问路径改为/,则照葫芦画瓢即可。

4. **重启 Apache 并验证多域名**

    完成之后，重新启动 apache 服务，重新打开浏览器。
    在浏览器中分别输入 http://t.com 和 http://cf.com，可以分别看到我们前面输入的不同文字，则代表已经成功了。


>如有问题，欢迎留言一起交流~

