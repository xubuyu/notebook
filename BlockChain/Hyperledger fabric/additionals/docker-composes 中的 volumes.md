docker-compose 中有两种方式可以设置volumes，并且都是可持久化的。
1.使用具体路径直接挂载到本地，特点就是直观。

    volumes:
        - /var/run/:/host/var/run/
        - ../chaincode/:/opt/gopath/src/github.com/chaincode
        - ./crypto-config:/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/
        - ./scripts:/opt/gopath/src/github.com/hyperledger/fabric/peer/scripts/
        - ./channel-artifacts:/opt/gopath/src/github.com/hyperledger/fabric/peer/channel-artifacts
2.使用卷标的形式，特点就是简洁，但是不知道数据到底在本地的什么位置。需要通过卷标查看。

```
volumes:
  orderer.example.com:
  peer0.org1.example.com:
  peer1.org1.example.com:
  peer0.org2.example.com:
  peer1.org2.example.com:

networks:
  byfn:
```

以上均取自 fabric-samples 中的 first-network 中的 docker-compose-cli.yaml,在执行byfn.sh up的时候会创建数据卷：

```
Creating network "net_byfn" with the default driver
Creating volume "net_peer0.org2.example.com" with default driver
Creating volume "net_peer1.org2.example.com" with default driver
Creating volume "net_peer1.org1.example.com" with default driver
Creating volume "net_peer0.org1.example.com" with default driver
Creating volume "net_orderer.example.com" with default driver
```

查看所有卷标：

```bash
> docker volume ls
DRIVER              VOLUME NAME
local               net_orderer.example.com
local               net_peer0.org1.example.com
local               net_peer0.org2.example.com
local               net_peer1.org1.example.com
local               net_peer1.org2.example.com
```

查看卷对应的地址：

```bash
> docker volume inspect net_orderer.example.com
[
    {
        "CreatedAt": "2019-08-27T13:49:18+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/net_orderer.example.com/_data",
        "Name": "net_orderer.example.com",
        "Options": null,
        "Scope": "local"
    }
]
> tree /var/lib/docker/volumes/net_orderer.example.com/_data
.
├── chains
│   ├── byfn-sys-channel
│   │   └── blockfile_000000
│   └── mychannel
│       └── blockfile_000000
└── index
    ├── 000001.log
    ├── CURRENT
    ├── LOCK
    ├── LOG
    └── MANIFEST-000000

4 directories, 7 files
```

--volumes: 删除卷
--remove-orphans: 移除未在compose文件中定义的容器

```bash
docker-compose -f docker-compose-file.yaml down --volumes --remove-orphans
```



参考文献：https://blog.csdn.net/u010931295/article/details/100098205