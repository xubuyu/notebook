### 搭建fabric网络的步骤

1. 生成证书,相当于账号
   	组织,节点,用户
      	yaml配置文件

2. 创建生成创始区块和通道的文件
   	运行在docker中

3. 启动节点
   	orderer,peer,客户端
      	运行在docker中
      	为了方便管理,编写docker-compose配置文件,批量启动节点

4. 通过当前组织的客户端,依次连接到当前组织的peer节点上
   	创建通道-创建一次
      	加入到通道中
      		每个节点都需要加入
      		安装链码,每个节点都需要安装
      		对链码初始化,只需要做一次,在任意节点上做
      			初始化完成,才可以调用
      		测试->链码调用(客户端)
      			读数据
      			写数据

### 搭建实例

现在来搭建一个fabric网络，网络结构如下:

1. 排序节点1个
   	hostname:orderer

2. 组织个数2个
   	aaa组织		
      		-peer节点2个
      		-用户3个
      	bbb组织	
      		-peer节点2个
      		-用户3个

现在我们来通过cyptogen根据配置文件来生成用户账号
我们先通过showtemplate来生成一个模板文件
cryptogen showtemplate > crypto-config.yaml

然后我们vi crypto-config.yaml
来修改一下这个配置文件
```yaml
OrdererOrgs:
      - Name: Orderer
	Domain: abc.com
	Specs:
		- Hostname: orderer
PeerOrgs:
      - Name: aaa
	Domain: aaa.abc.com
	EnableNoeOUs: true 

Template:
	Count: 2
Users:
	Count: 3
 
  - Name: bbb
Domain: bbb.abc.com
EnableNoeOUs: true 
 
Template:
	Count: 2
Users:
	Count: 3
```

 


修改完成,看一下配置文件

```yaml
OrdererOrgs:
	- Name: Orderer
	Domain: abc.com
	Specs:
	- Hostname: orderer
PeerOrgs:
	- Name: aaa
	Domain: aaa.abc.com
	EnableNodeOUs: true
	Template:
		Count: 2
	Users:
		Count: 3
	- Name: bbb
	Domain: bbb.abc.com
	EnableNodeOUs: true
	Template:
		Count: 2
	Users:
		Count: 3

```

 

现在配置文件改好了
我们来根据配置文件来生成证书

```shell
crypto generate --config=crypto-config.yaml
```

执行成功,输出了
aaa.abc.com
bbb.abc.com

ls一下,
crypto-config  crypto-config.yaml
有配置文件和生成了一个crypto-config文件夹

我们来看一下目录

```shell
/test$ tree -L 3
.
├── crypto-config
│   ├── ordererOrganizations
│   │   └── abc.com
│   └── peerOrganizations
│       ├── aaa.abc.com
│       └── bbb.abc.com
└── crypto-config.yaml
6 directories, 1 file
```

里面有orderer组织和peer组织
然后下面是
abc.com
aaa.abc.com
bbb.abc.com

生成了很多账号
组织的orderer组织,n个peer组织
每个orderer节点,每个peer节点,都有一个账号.

### MSP

MSP 即 Membership Service provider, 成员服务提供者
是一个用来提供成员操作体系结构抽象的组件
MSP抽象出发布和验证证书以及用户身份验证背后的所有加密机制和协议
MSP可以定义他们自己的身份概念
以及管理这些身份(身份验证)和身份验证(签名生成和验证)的规则

简单来说
比如在生成证书的时候,在证书目录中有msp子目录
之后在设置配置文件的时候,也会有msp目录的设置

我们可以把msp理解为账号
账号包括
1.证书
2.秘钥

### 锚节点

然后我们来说一下锚节点
锚节点是一个peer节点
那么锚节点和普通peer节点的区别就是

在一个网络中,有多个组织,每个组织有多个peer节点
组织和组织之间需要通信
那么每个组织中选取一个peer节点作为代表 
这个peer节点的代表就是锚节点
锚节点的职责就是代表当前这个组织
和其他的组织进行通信

我们可以在配置文件中指定锚节点
任意的peer节点都有资格成为锚节点
一个组织最多有一个锚节点

### 参考文献

https://blog.csdn.net/qq_33781658/article/details/88760179