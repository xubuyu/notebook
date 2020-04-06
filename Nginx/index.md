### 安装

1. 安装依赖包

   ```shell
   [yum | apt-get] -y install gcc pcre-devel zlib-devel openssl openssl-devel
   ```

2. 下载并解压

   ```shell 
   wget https://nginx.org/download/nginx-xxx.tar.gz
   tar -zxvf nginx-xxx.tar.gz
   ```

3. 配置并编译

   ```shell 
   cd nginx-xxx.tar.gz
   ./configure [配置选项]
   make && make install
   ```

### 配置开机启动

#### Debian, Ubuntu

1. 创建 ```/etc/init.d/nginx```

    ```shell 
    #! /bin/sh
    
    ### BEGIN INIT INFO
    # Provides:          nginx
    # Required-Start:    $all
    # Required-Stop:     $all
    # Default-Start:     2 3 4 5
    # Default-Stop:      0 1 6
    # Short-Description: starts the nginx web server
    # Description:       starts nginx using start-stop-daemon
    ### END INIT INFO
    
    
    PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
    DAEMON=/usr/local/nginx/sbin/nginx # nginx 命令路径
    NAME=nginx
    DESC=nginx
    
    test -x $DAEMON || exit 0
    
    # Include nginx defaults if available
    if [ -f /etc/default/nginx ] ; then
            . /etc/default/nginx
    fi
    
    set -e
    
    case "$1" in
      start)
            echo -n "Starting $DESC: "
            start-stop-daemon --start --quiet --pidfile /usr/local/nginx/logs/$NAME.pid \
                    --exec $DAEMON -- $DAEMON_OPTS
            echo "$NAME."
            ;;
      stop)
            echo -n "Stopping $DESC: "
            start-stop-daemon --stop --quiet --pidfile /usr/local/nginx/logs/$NAME.pid \
                    --exec $DAEMON
            echo "$NAME."
            ;;
      restart|force-reload)
            echo -n "Restarting $DESC: "
            start-stop-daemon --stop --quiet --pidfile \
                    /usr/local/nginx/logs/$NAME.pid --exec $DAEMON
            sleep 1
            start-stop-daemon --start --quiet --pidfile \
                    /usr/local/nginx/logs/$NAME.pid --exec $DAEMON -- $DAEMON_OPTS
            echo "$NAME."
            ;;
      reload)
          echo -n "Reloading $DESC configuration: "
          start-stop-daemon --stop --signal HUP --quiet --pidfile /usr/local/nginx/logs/$NAME.pid \
              --exec $DAEMON
          echo "$NAME."
          ;;
      *)
            N=/etc/init.d/$NAME
            echo "Usage: $N {start|stop|restart|force-reload}" >&2
            exit 1
            ;;
    esac
    
    exit 0
    ```
	> 注意：``` DAEMON=/usr/local/nginx/sbin/nginx``` 应自己的 nginx 命令路径

2. 设置文件的访问权限

   ```shell 
   sudo chmod a+x /etc/init.d/nginx  # a+x ==> all user can execute 所有用户可执行
   ```

3. 添加至启动项

   ```shell 
   sudo /usr/sbin/update-rc.d -f nginx defaults 
   ```

4. 测试

   ```shell 
   sudo /etc/init.d/nginx start
   sudo /etc/init.d/nginx stop
   sudo /etc/init.d/nginx restart
   sudo service nginx start
sudo service nginx stop
   sudo service nginx restart
   ```
   
   

#### RHEL, Fedora, CentOS

1. 创建 ```/etc/init.d/nginx```

   ```shell 
   #!/bin/sh
   #
   # nginx - this script starts and stops the nginx daemon
   #
   # chkconfig:   - 85 15
   # description:  NGINX is an HTTP(S) server, HTTP(S) reverse \
   #               proxy and IMAP/POP3 proxy server
   # processname: nginx
   # config:      /etc/nginx/nginx.conf
   # config:      /etc/sysconfig/nginx
   # pidfile:     /var/run/nginx.pid
   
   # Source function library.
   . /etc/rc.d/init.d/functions
   
   # Source networking configuration.
   . /etc/sysconfig/network
   
   # Check that networking is up.
   [ "$NETWORKING" = "no" ] && exit 0
   
   nginx="/usr/sbin/nginx" # nginx 命令路径
   prog=$(basename $nginx)
   
   NGINX_CONF_FILE="/etc/nginx/nginx.conf"
   
   [ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx
   
   lockfile=/var/lock/subsys/nginx
   
   make_dirs() {
      # make required directories
      user=`$nginx -V 2>&1 | grep "configure arguments:.*--user=" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
      if [ -n "$user" ]; then
         if [ -z "`grep $user /etc/passwd`" ]; then
            useradd -M -s /bin/nologin $user
         fi
         options=`$nginx -V 2>&1 | grep 'configure arguments:'`
         for opt in $options; do
             if [ `echo $opt | grep '.*-temp-path'` ]; then
                 value=`echo $opt | cut -d "=" -f 2`
                 if [ ! -d "$value" ]; then
                     # echo "creating" $value
                     mkdir -p $value && chown -R $user $value
                 fi
             fi
          done
       fi
   }
   
   start() {
       [ -x $nginx ] || exit 5
       [ -f $NGINX_CONF_FILE ] || exit 6
       make_dirs
       echo -n $"Starting $prog: "
       daemon $nginx -c $NGINX_CONF_FILE
       retval=$?
       echo
       [ $retval -eq 0 ] && touch $lockfile
       return $retval
   }
   
   stop() {
       echo -n $"Stopping $prog: "
       killproc $prog -QUIT
       retval=$?
       echo
       [ $retval -eq 0 ] && rm -f $lockfile
       return $retval
   }
   
   restart() {
       configtest || return $?
       stop
       sleep 1
       start
   }
   
   reload() {
       configtest || return $?
       echo -n $"Reloading $prog: "
       killproc $prog -HUP
       retval=$?
       echo
   }
   
   force_reload() {
       restart
   }
   
   configtest() {
     $nginx -t -c $NGINX_CONF_FILE
   }
   
   rh_status() {
       status $prog
   }
   
   rh_status_q() {
       rh_status >/dev/null 2>&1
   }
   
   case "$1" in
       start)
           rh_status_q && exit 0
           $1
           ;;
       stop)
           rh_status_q || exit 0
           $1
           ;;
       restart|configtest)
           $1
           ;;
       reload)
           rh_status_q || exit 7
           $1
           ;;
       force-reload)
           force_reload
           ;;
       status)
           rh_status
           ;;
       condrestart|try-restart)
           rh_status_q || exit 0
               ;;
       *)
           echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
           exit 2
   esac
   ```

   

2. 设置文件的访问权限

   ```shell 
   sudo chmod a+x /etc/init.d/nginx  # a+x ==> all user can execute 所有用户可执行
   ```

3. 测试

   ```shell 
   /etc/init.d/nginx start
   /etc/init.d/nginx stop
   ```
   
4. 将 nginx 服务加入 chkconfig 管理列表

   ```shell 
   sudo chkconfig --add /etc/init.d/nginx
   ```

5.  使用service对nginx进行启动，重启等操作

   ```shell 
   sudo service nginx start
   sudo service nginx stop
   sudo service nginx restart
   ```

6. 设置开机自启动

   ```shell 
   chkconfig nginx on
   ```

   