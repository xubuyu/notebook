### 安装

```shell 
docker run \
--name=mysql \
--restart=always \
-p 3306:3306 \
-v /opt/mysql/conf:/etc/mysql \
-v /opt/mysql/logs:/var/log \
-v /opt/mysql/data:/var/lib/mysql \
-v /etc/localtime:/etc/localtime:ro \
-e MYSQL_ROOT_PASSWORD=123456 \
-d mysql/mysql-server:latest \
--character-set-server=utf8mb4 --collation-server=utf8mb4_general_ci --lower_case_table_names=1
```

### 允许远程连接

进入 MySQL 容器

```shell
docker exec -it mysql mysql -uroot -p
```



```sql
# Mysql默认不允许远程登录，所以需要开启远程访问权限
USE mysql;
SELECT user,authentication_string,host FROM user;
UPDATE user SET host = '%' WHERE user = 'root';
FLUSH PRIVILEGES;
# navicat 连接 mysql 出现`Client does not support authentication protocol requested by server`
ALTER user 'root'@'%' identified with mysql_native_password by '密码';
```

