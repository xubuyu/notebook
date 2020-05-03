ubuntu 系统默认已安装ufw.

### 安装

```bash
sudo apt-get install ufw
```

### 启用

```bash
sudo ufw enable
sudo ufw default deny
```

运行以上两条命令后，开启了防火墙，并在系统启动时自动开启。关闭所有外部对本机的访问，但本机访问外部正常。

### 开启/禁用

```bash
sudo ufw allow|deny [service]
```

打开或关闭某个端口，例如：

```bash
sudo ufw allow smtp　允许所有的外部IP访问本机的25/tcp (smtp)端口
sudo ufw allow 22/tcp 允许所有的外部IP访问本机的22/tcp (ssh)端口
sudo ufw allow 53 允许外部访问53端口(tcp/udp)
sudo ufw allow from 192.168.1.100 允许此IP访问所有的本机端口
sudo ufw allow proto udp 192.168.0.1 port 53 to 192.168.0.2 port 53
sudo ufw deny smtp 禁止外部访问smtp服务
sudo ufw delete allow smtp 删除上面建立的某条规则
```

### 查看防火墙状态

```bash
sudo ufw status
```

一般用户，只需如下设置：

```bash
sudo apt-get install ufw
sudo ufw enable
sudo ufw default deny
```

以上三条命令已经足够安全了，如果你需要开放某些服务，再使用sudo ufw allow开启。

开启/关闭防火墙 (默认设置是’disable’)

```bash
sudo ufw enable|disable
```

转换日志状态

```bash
sudo ufw logging on|off
```

设置默认策略 (比如 “mostly open” vs “mostly closed”)

```bash
sudo ufw default allow|deny
```

许 可或者屏蔽端口 (可以在“status” 中查看到服务列表)。可以用“协议：端口”的方式指定一个存在于/etc/services中的服务名称，也可以通过包的meta-data。 ‘allow’ 参数将把条目加入 /etc/ufw/maps ，而 ‘deny’ 则相反。基本语法如下：

```bash
sudo ufw allow|deny [service]
```

显示防火墙和端口的侦听状态，参见 /var/lib/ufw/maps。括号中的数字将不会被显示出来。

```bash
sudo ufw status
```

### UFW 使用范例：

允许 53 端口

```bash
sudo ufw allow 53
```

禁用 53 端口

```bash
 sudo ufw delete allow 53
```

允许 80 端口

```bash
sudo ufw allow 80/tcp
```

禁用 80 端口

```bash
sudo ufw delete allow 80/tcp
```

允许 smtp 端口

```bash
sudo ufw allow smtp
```

删除 smtp 端口的许可

```bash
sudo ufw delete allow smtp
```

允许某特定 IP

```bash
sudo ufw allow from 192.168.254.254
```

删除上面的规则

```bash
sudo ufw delete allow from 192.168.254.254
```

Linux 2.4内核以后提供了一个非常优秀的防火墙工具：netfilter/iptables,他免费且功能强大，可以对流入、流出的信息进行细化控制，它可以 实现防火墙、NAT（网络地址翻译）和数据包的分割等功能。netfilter工作在内核内部，而 iptables 则是让用户定义规则集的表结构。

但是iptables的规则稍微有些“复杂”，因此 ubuntu 提供了 ufw 这个设定工具，以简化 iptables 的某些设定，其后台仍然是 iptables。ufw 即 uncomplicated firewall 的简称，一些复杂的设定还是要去 iptables。

ufw相关的文件和文件夹有：

/etc /ufw/：里面是一些 ufw 的环境设定文件，如 before.rules、after.rules、sysctl.conf、ufw.conf，及 for ip6 的 before6.rule 及 after6.rules。这些文件一般按照默认的设置进行就 ok。

若开启 ufw 之后，/etc/ufw/sysctl.conf会覆盖默认的 /etc/sysctl.conf 文件，若你原来的/etc/sysctl.conf做了修 改，启动ufw后，若 /etc/ufw/sysctl.conf 中有新赋值，则会覆盖 /etc/sysctl.conf 的，否则还以 /etc /sysctl.conf 为准。当然你可以通过修改 /etc/default/ufw 中的 “IPT_SYSCTL=” 条目来设置使用哪个 sysctrl.conf.

/var/lib/ufw/user.rules 这个文件中是我们设置的一些防火墙规则，打开大概就能看明白，有时我们可以直接修改这个文件，不用使用命令来设定。修改后记得 ufw reload 重启 ufw 使得新规则生效。

下面是ufw命令行的一些示例：

```bash
ufw enable/disable:打开/关闭ufw
ufw status：查看已经定义的ufw规则
ufw default allow/deny:外来访问默认允许/拒绝
ufw allow/deny 20：允许/拒绝 访问20端口,20后可跟/tcp或/udp，表示tcp或udp封包。
ufw allow/deny servicename:ufw从/etc/services中找到对应service的端口，进行过滤。
ufw allow proto tcp from 10.0.1.0/10 to 本机ip port 25:允许自10.0.1.0/10的tcp封包访问本机的25端口。
ufw delete allow/deny 20:删除以前定义的"允许/拒绝访问20端口"的规则
```

### 参考文献

https://blog.csdn.net/Manipula/article/details/91491699