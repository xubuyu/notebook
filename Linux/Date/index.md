update-alternatives --configupdate-alternatives --config执行下面命令，复制文件到 /etc/ 修改时区

```bash
sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```



### 1，查看系统时间

执行 **date** 命令可以查看当前系统的时间

### 2，手动修改系统时间

（1）执行如下命令可以设置一个新的系统时间：

```
date -s "20190712 18:30:50"
```


（2）设置完后还要执行如下命令保存一下设置：

```
hwclock --systohc
```


（3）当然我们也可以将上面两个操作合二为一：

```
date -s "20190712 18:30:50" && hwclock --systohc
```



### 3，通过网络同步时间

（1）首先安装 **ntpdate** 命令：

```
yum install -y ntpdate
```


（2）接着执行如下命令开始同步：

```
ntpdate 0.asia.pool.ntp.org
```

若上面的时间服务器不可用，也可以改用如下服务器进行同步：

- time.nist.gov
- time.nuri.net
- 0.asia.pool.ntp.org
- 1.asia.pool.ntp.org
- 2.asia.pool.ntp.org
- 3.asia.pool.ntp.org


（3）最后执行如下命令将系统时间同步到硬件，防止系统重启后时间被还原。

```
hwclock --systohc
```


原文出自：[www.hangge.com](https://www.hangge.com/) 转载请保留原文链接：https://www.hangge.com/blog/cache/detail_2499.html