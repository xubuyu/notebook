### 添加源地址

执行三条命令,添加php的源地址，更新，安装

```bash
sudo apt-get install software-properties-common
sudo add-apt-repository -y ppa:ondrej/php
sudo apt-get update
sudo apt-get install php7.4
```



#### 查看有没有php7的包

```bash
sudo apt list | grep php
```



### 安装PHP

**nginx**使用**php**的话要用到**php7.4-fpm**,所以要安装

```bash
sudo apt-get install php7.4-mysql php7.4-fpm php7.4-curl php7.4-xml php7.4-gd php7.4-mbstring php-memcached php7.4-zip
```



### 配置php-fpm

修改配置监听**9000**端口来处理**nginx**的请求(这种方法一般在windows上使用),

另一种方法linux下使用sock方法速度会更快，这个地方也可以不修改，真使用里面 **/run/php/php7.4-fpm.sock** 这样的路径，后面nginx也要设置成这种格式 **fastcgi_pass unix:/run/php/php7.4-fpm.sock;**

打开 /etc/php/7.4/fpm/pool.d/www.conf 文件找到如下位置注释第一行添加第二行

```php
;listen = /run/php/php7.4-fpm.sock
listen = 127.0.0.1:9000
```

修改权限

```bash
chmod 777 /run/php/php7.4-fpm.sock
```



打开nginx的配置文件 **/etc/nginx/sites-available/default** (也可以自己在其它地方添加配置文件,这个地方是默认的配置地方)

```bash
server {
    listen       80; #监听80端口，接收http请求
    server_name  www.example.com; #就是网站地址
    root /usr/local/etc/nginx/www/huxintong_admin; ### 准备存放代码工程的路径
    #路由到网站根目录www.example.com时候的处理
    location / {
        index index.php; #跳转到www.example.com/index.php
        autoindex on;
    }  
    #当请求网站下php文件的时候，反向代理到php-fpm
    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass 127.0.0.1:9000;#nginx fastcgi进程监听的IP地址和端口
        #fastcgi_pass unix:/run/php/php7.4-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }
}
```





### 启动php7.4-fpm

有时候安装完成后不知道安装到什么地方啦可以使用下面命令查找下

```bash
whereis php-fpm
```

https://www.zhaokeli.com/article/8496.html

#### 查看是否启动成功 

```bash
netstat -lnt | grep 9000
```

#### 重启

```bash
sudo service php7.4-fpm restart
```

### 参考文献

https://www.zhaokeli.com/article/8496.html