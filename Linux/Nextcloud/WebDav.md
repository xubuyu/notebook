### 使用WebDAV映射网络驱动器至本地

由于NextCloud支持WebDAV服务，因此用户能通过WebDAV与其他产品（如WPS、PDF Expert）等连接，快速实现数据传递、数据存储，而不用再复制、粘贴文件到云端。

此外，利用NextCloud提供的WEBDAV服务，我们直接可以把NextCloud映射成本地硬盘驱动器，方便随时存储和查看文件。

```shell 
# 修改注册表让WIN7同时支持http和https
Cmd 输入 regedit
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WebClient\Parameters
修改 BasicAuthLevel 值为2

# 重启服务
net stop webclient
net start webclient
```

#### 2. 获取WebDAV

Nextcloud的WebDAV地址可以在登录Nextcloud后，左下角设置按钮中直接复制到

#### 3. 映射成本地硬盘驱动器

在我的电脑图标上点击右键，选择“映射网络驱动器”, 在界面中填入Nextcloud的webdav地址

### 参考文献

https://docs.nextcloud.com/server/18/user_manual/files/access_webdav.html