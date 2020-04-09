### 安装依赖

```shell 
sudo apt-get install bluez bluez-tools
```

### 连接设备

```shell 
bluetoothctl
[bluetooth]#
[bluetooth]# scan on
[bluetooth]# pair xx:xx:xx:xx:xx:xx
[bluetooth]# connect xx:xx:xx:xx:xx:xx
```

### 找不到蓝牙 Bug 修复

找不到设备的情况

```shell 
$ sudo hcitool dev
Devices:
$ sudo bluetoothctl
Agent registered
[bluetooth]# list
[bluetooth]# show
No default controller available
[bluetooth]#
```

修复

```shell 
sudo ln -s /lib/firmware /etc/firmware
sudo hciattach /dev/ttyAMA0 bcm43xx 921600 -
```

参考文献：[StackExchange](https://askubuntu.com/questions/1156466/cannot-find-bluetooth-device-ubuntu-core-on-raspberry-pi-3-b)