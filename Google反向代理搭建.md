## Google反向代理搭建.md ##

>**方法概述：使用VPS建立Nginx反代，创建Google镜像，并使用SSL加密**

一般来说，vps性能会有过剩，那么除了建站，也可以同时创建google镜像，用于搜索。
该文在centos 6 64位下成功使用。

> 服务器为[CloudAtCost][1]的35美元永久小鸡，经常打折低至3.5美元 如注册请在推荐人处填写 **xxfxxx@foxmail.com**，则我能得到一点好处，不能便宜奸商对不~

1. **登录SSH**

   - 推荐工具：[Xshell5][2]
   
   - 工具使用教程参考[这里][3]

2. **安装LNMP**

   - 建议安装 Nginx、Mysql 5.5、PHP 5.6、Zend Opcache，其他按需安装，这几个组件完全可以用于建站。
   
   - 对于本次，仅需要Nginx

   - LNMP的源也可自己改，建议从[军哥网站][4]获取最新版本 

    ```
    wget http://down.zhujiwiki.com/lnmp.tar.gz && tar xzf lnmp.tar.gz && cd lnmp && ./install.sh
    ```
    
    或
    
    ```
    screen -S lnmp
    wget -c http://soft.vpser.net/lnmp/lnmp1.3-full.tar.gz && tar zxf lnmp1.3-full.tar.gz && cd lnmp1.3-full && ./install.sh lnmp
    ```
    
3. **创建站点**

    - 安装完lnmp后，创建站点：

    ```
    ./vhost.sh
    ```
    
    - 按说明即可。（不需要ssl站点，之后会有说明）
    
    - 域名请自备,此处以a.com为例

4. **创建SSL证书**

    - 使用免费的Let's encrypt 证书。
    - 建议使用独立的文件夹存放SSL证书，如/root/ssl/
    - 详细步骤参考：[快速获取/更新 Let's encrypt 证书][5]

5. **使用Nginx反代google**

    - 修改站点的配置文件，一般在/usr/local/nginx/conf/vhost目录下，以域名开头。打开，全部替换为：

    ```
    server
    {
        listen 443;
        server_name a.com;
    
        ssl on;
        ssl_certificate /root/ssl/a.com.crt;
        ssl_certificate_key /root/ssl/a.com.key;
        ssl_protocols SSLv3 TLSv1;
        ssl_ciphers ALL:-ADH:+HIGH:+MEDIUM:-LOW:-SSLv2:-EXP;
        
        location / {
            proxy_redirect off;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto https;
            proxy_pass https://www.google.com;
            }
    }
    
    server
    {
        listen 80;
        server_name a.com;
        rewrite ^(.*) https://a.com$1 permanent;
    }

    server
    {
        listen 80;
        server_name a.com;
        rewrite ^(.*) https://a.com$1 permanent;
    }
    ```
    
    把其中的a.com全部替换为你的域名。
    
    把ssl_certificate、ssl_certificate_key替换为你的域名证书和存放目录。

    修改nginx.conf文件，一般在/usr/local/nginx/conf目录下，在http区域添加：
    
    ```
    proxy_connect_timeout 5;
    proxy_read_timeout 60;
    proxy_send_timeout 5;
    proxy_buffer_size 16k;
    proxy_buffers 4 64k;
    proxy_busy_buffers_size 128k;
    proxy_temp_file_write_size 128k;
    proxy_temp_path /data/wwwroot/a.com/cache/temp;
    proxy_cache_path /data/wwwroot/a.com/cache/pathlevels=1:2 keys_zone=cache_one:50m inactive=7d max_size=1g;
    ```
    
    其中proxy_temp_path和proxy_cache_path修改为你的缓存存放目录。
    
    把其中的a.com全部替换为你的域名。

6. **重启nginx**：

    ```
    service nginx restart
    ```

    如无报错，就表明成功。


  [1]: http://cloudatcost.com/
  [2]: http://xiazai.xshellcn.com/wm/Xshell_setup_wm.exe
  [3]: http://www.cnblogs.com/perseverancevictory/p/4910145.html
  [4]: http://lnmp.org/install.html
  [5]: http://zhujiwiki.com/9127.html