### 解决方案

在 `/etc/udev/rules.d` 目录中新建一个规则文件，如 `customize.rules`，加上以下配置：

```
KERNEL=="sd*[!0-9]", ATTR{removable}=="0", ENV{ID_BUS}=="usb", ENV{DEVTYPE}=="disk", ENV{UDISKS_DISABLE_POLLING}="1"
KERNEL=="sd*[!0-9]", ATTR{removable}=="0", ENV{ID_BUS}=="ata", ENV{DEVTYPE}=="disk", ENV{UDISKS_DISABLE_POLLING}="1"
KERNEL=="sd*[!0-9]", ATTR{removable}=="0", ENV{ID_BUS}=="scsi", ENV{DEVTYPE}=="disk", ENV{ID_VENDOR}=="ATA", ENV{UDISKS_DISABLE_POLLING}="1"
```



### 参考文献

https://ubuntuforums.org/showthread.php?t=2004775