+++
title = "Debian 安装 Matomo (Piwik) 开源统计分析服务"
date = "2019-08-18"
description = "在 Debian VPS 上搭建 Matomo 进行网站访客分析"
tags = [
    "折腾",
    "网站相关",
]
image = "show.jpg"
+++

# 前言
之前我在[使用Github Pages和Hexo搭建个人博客(进阶篇)](https://viflythink.com/Use_GithubPages_and_Hexo_to_build_blog_advanced/#%E6%95%B0%E6%8D%AE%E7%BB%9F%E8%AE%A1%E4%B8%8E%E5%88%86%E6%9E%90)这一篇博文中已经提到了不考虑使用大型公司提供的网站统计分析服务了，只不过网站统计分析服务还是有必要的，至少能看到有多少人浏览过自己的网站。之所以不采用商业公司提供的分析服务是因为这等于助纣为虐，帮助这些公司建立更精准的用户画像，这些公司可以利用遍布于大半个互联网的自家的跟踪代码对读者进行浏览痕迹的跟踪，从而建立精准的用户画像，我无法接受这种侵犯用户隐私的行为，所以只能考虑自己搭建统计分析服务了。在 Google 上搜了一下后决定采用 Matomo（原名为 Piwik）这个开源的网站统计分析服务。本文主要参考了[在Debian 9上安装Matomo Analytics](https://my.oschina.net/u/3944788/blog/2874366)这一个教程，只不过很不巧的是目前 Debain 10 已经发布，这篇教程里的 php7.0 已经过时，但是没关系，下文中提供的安装 php 的指令并没有指定版本，所以对于 Debian 9/10 的用户都是可行的。

# 需求
1.基本的 Linux 终端操作经验   
2.一个安装了 Debian 9/10 的服务器（VPS），理论上来说 Ubuntu 18 也可以（并没有实测过）   
3.一个属于自己的域名，并且已经将其 DNS 解析指向自己的服务器

# 操作
先安装必须的库：     

    sudo apt install unzip apt-transport-https curl wget dirmngr php php-fpm php-curl php-gd php-cli php-mysql php-xml php-mbstring    

安装 MySQL 的替代品 MariaDB，这里必须提到的一点是，从 Debian9 开始，[软件包仓库中的 MySQL 实际上已经全被 MariaDB 取代了](https://mariadb.org/debian-9-released-mariadb-mysql-variant/)：     

    sudo apt install mariadb-server     

运行 mysql_secure_installation 脚本以改进 MariaDB 安装的安全性：    

    sudo mysql_secure_installation  

作为数据库 root 用户登录到 MariaDB（注意，必须使用 root 权限才可以作为数据库 root 用户登录到 MariaDB，数据库的 root 用户与系统中的 root 用户不是同一个东西）：  

    sudo mysql -u root -p   

假如没有一个用于 Matomo 的数据库用户的话，先执行以下指令新建数据库用户，localhost 意味这个用户只可以本地登录（*PS：记得将username和password替换为自己准备设置的用户名和密码，下同*）：     

    CREATE USER 'username'@'localhost' IDENTIFIED BY 'password';     

创建后请记住用户名和密码。  
创建一个新的 MariaDB 数据库并授权：   

    CREATE DATABASE db_name;     
    GRANT ALL ON db_name.* TO 'username' IDENTIFIED BY 'password';       
    FLUSH PRIVILEGES;        
    quit;     

安装 nginx：     

    sudo apt install -y nginx       
新建 Nginx 配置文件：     

    sudo nano /etc/nginx/sites-available/matomo     
在其中填入（将 your_domain 替换为你的域名，例如 stats.viflythink.com，fastcgi_pass 的内容请根据自己的版本进行填写，你可以通过 ls /run/php/ 看到对应的 sock 文件）：    

    server {
      listen 80;
      server_name your_domain;
      root /var/www/html/matomo;
      location / {
        try_files $uri /index.php$is_args$args;
      }
      location ~ \.php$ {
        try_files $uri =404;
        include fastcgi_params;
        fastcgi_pass unix:/run/php/php7.0-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME   $document_root$fastcgi_script_name;
      }
    }


通过建立软链接将刚写好的配置文件对应的网站设置为可访问的：  

    sudo ln -s /etc/nginx/sites-available/matomo /etc/nginx/sites-enabled/     
测试配置：  

    sudo nginx -t

创建 matomo 目录：  

    sudo mkdir -p /var/www/html/matomo  
下载和解压 matomo：  

    cd /var/www/html/matomo   
    wget https://builds.piwik.org/piwik.zip
    unzip piwik.zip
    rm piwik.zip
    mv piwik/* .
    rmdir piwik

更改该目录的所有权，确保访问者可以访问这些页面文件：    

    sudo chown -R www-data:www-data /var/www/html/matomo

重新加载 Nginx 以让配置生效： 

    sudo systemctl reload nginx.service

接下来使用浏览器打开 Nginx 配置文件中填写的域名，按照指引完成 matomo 的安装。
