### 查询分区 UUID

```shell 
blkid
```

### 编辑 ```/etc/fstab```

添加配置并重启查看是否生效

```shell 
UUID=<分区UUID>    /mnt/udisk0/    ext4    defaults    0    2
```

### 配置说明

第一列：Device：磁盘设备文件或者该设备的Label或者UUID

第二列：Mount point：设备的挂载点，就是你要挂载到哪个目录下

第三列：filesystem：磁盘文件系统的格式，包括 ext2, ext3, reiserfs, nfs, vfat 等

第四列：parameters：文件系统的参数
| 值 | 描述 |
| ----------- | ------------------------------------------------------------ |
| Async/sync  | 设置是否为同步方式运行，默认为async                         |
| auto/noauto | 当下载mount -a 的命令时，此文件系统是否被主动挂载。默认为auto |
| rw/ro       | 是否以以只读或者读写模式挂载                                 |
| exec/noexec | 限制此文件系统内是否能够进行"执行"的操作                     |
| user/nouser | 是否允许用户使用mount命令挂载                                |
| suid/nosuid | 是否允许SUID的存在                                           |
| Usrquota    | 启动文件系统支持磁盘配额模式                                 |
| Grpquota    | 启动文件系统对群组磁盘配额模式的支持                         |
| Defaults    | 同事具有rw,suid,dev,exec,auto,nouser,async等默认参数的设置   |

第五列：能否被dump备份命令作用：dump是一个用来作为备份的命令。通常这个参数的值为 0 或者 1

| 值   | 描述                     |
|  ---- | ---------------------------- |
| 0    | 代表不要做dump备份         |
| 1    | 代表要每天进行dump的操作   |
| 2    | 代表不定日期的进行dump操作 |

第六列：是否检验扇区：开机的过程中，系统默认会以 fsck 检验系统是否完整（clean）

| 值   | 描述                     |
|  ---- | ---------------------------- |
| 0    | 不要检验                     |
| 1    | 最早检验（一般根目录会选择） |
| 2    | 1级别检验完成之后进行检验    |

### 参考文献

https://blog.csdn.net/wh8_2011/article/details/82767032

