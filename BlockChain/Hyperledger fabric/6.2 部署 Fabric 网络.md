### 进入工作路径

```bash
cd $GOPATH/src/github.com/hyperledger/fabric/fabric-samples/romengine/network
```

### 设置工作路径

```bash
export FABRIC_CFG_PATH=$GOPATH/src/github.com/hyperledger/fabric/fabric-samples/romengine/network/configtx
```

### 环境清理

```bash
rm -Rf organizations/peerOrganizations && rm -Rf organizations/ordererOrganizations
```

### 生成证书文件

```bash
cryptogen generate --config=./organizations/cryptogen/crypto-config-org1.yaml --output="organizations"
cryptogen generate --config=./organizations/cryptogen/crypto-config-org2.yaml --output="organizations"
cryptogen generate --config=./organizations/cryptogen/crypto-config-orderer.yaml --output="organizations"
```

### 生成创世区块

```bash
configtxgen -profile TwoOrgsOrdererGenesis -channelID system-channel -outputBlock ./system-genesis-block/genesis.block
```

### 生成通道的创世交易

```bash
configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/tenant.tx -channelID tenant
```

### 生成组织锚节点（主节点）交易

```bash
configtxgen -profile TwoOrgsChannel -outputAnchorPeersUpdate ./channel-artifacts/<组织MSP>anchors.tx -channelID tenant -asOrg <组织MSP>
```

### 启动网络

```bash
cd ./docker
docker-compose up -d
```



---



执行以下命令进入 cli ：

```bash
docker exec -it cli bash
```

↓↓↓↓↓↓

### 创建通道

```bash
peer channel create -o orderer.romengine.com:7050 -c tenant -f /opt/gopath/src/github.com/hyperledger/fabric/channel-artifacts/tenant.tx --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/romengine.com/orderers/orderer.romengine.com/msp/tlscacerts/tlsca.romengine.com-cert.pem --outputBlock /opt/gopath/src/github.com/hyperledger/fabric/channel-artifacts/tenant.block
```

> `-o` 选项指定与哪个 orderer 进行通信
>
> `-c` 通道名称
>
> `-f` 指定创世交易
>
> `--outputBlock` 产生通道创世区块

### 加入通道

```bash
peer channel join -b /opt/gopath/src/github.com/hyperledger/fabric/channel-artifacts/tenant.block
```

> `-b` 指定通道创世区块，创世区块在创建通道时生成

查看加入的通道

```bash
peer channel list
```



### 设置主节点

进入 cli1 操作

```bash
peer channel update -o orderer.romengine.com:7050 -c tenant -f /opt/gopath/src/github.com/hyperledger/fabric/channel-artifacts/Org1MSPanchors.tx --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/romengine.com/orderers/orderer.romengine.com/msp/tlscacerts/tlsca.romengine.com-cert.pem
```

进入 cli2 操作

```bash
peer channel update -o orderer.romengine.com:7050 -c tenant -f /opt/gopath/src/github.com/hyperledger/fabric/channel-artifacts/Org2MSPanchors.tx --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/romengine.com/orderers/orderer.romengine.com/msp/tlscacerts/tlsca.romengine.com-cert.pem
```

> 即设置某组织于某通道的主节点。

### 链码实例化

Fabric 2.0 的智能合约需要打包成一个 `.tar.gz` 压缩包。
打开控制台，执行 `docker exec -it cli` 进入 cli 容器

由于没接触过新的智能合约命令，我们先输入 `help`，获取下帮助文档，控制台输入：

```bash
peer lifecycle chaincode --help
```

```bash
Perform chaincode operations: package|install|queryinstalled|getinstalledpackage|approveformyorg|checkcommitreadiness|commit|querycommitted

Usage:
  peer lifecycle chaincode [command]

Available Commands:
  approveformyorg      # 同意智能合约定义
  checkcommitreadiness # 检查合约是否在通道上已经定义
  commit               # 提交合约定义到指定通道
  getinstalledpackage  # 从节点中获取已经安装的链码包
  install              # 部署链码
  package              # 打包链码
  querycommitted       # 查询节点上已提交的链码定义
  queryinstalled       # 查询节点已经安装的链码
```

#### 链码打包

由于要打包合约此处我们使用 `peer lifecycle chaincode package`， 在容器内部执行命令：

```bash
peer lifecycle chaincode package tenantinfo.tar.gz --path /opt/gopath/src/github.com/hyperledger/fabric/application/tenant-info/contract --lang node --label tenantinfo_1.0.0
```

> `tenantinfo.tar.gz` ：打包合约包文件名
> `--path` ：智能合约代码路径
> `--lang` ：智能合约语言 支持 golang、node、Java
> `--label` ：智能合约标签，描述作用

执行完成后，查看当前目录，出现 `tenantinfo.tar.gz` 查看打包后的文件内容，主要包含：

（1）.chaincode-Package-Metadata.json 文件，主要是定义合约的代码路径、合约语言以及合约标签

```json
{ "path" : "/opt/gopath/src/github.com/hyperledger/fabric/application/tenant-info/contract",
"type":"node",
"label":"tenantinfo_1.0.0"}
```

（2）压缩的合约代码文件 code.tar.gz,包含合约代码以及依赖包

#### 部署合约到节点

继续在 cli 执行以下命令：

```bash
peer lifecycle chaincode install tenantinfo.tar.gz
```

日志最后输出：

```
Chaincode code package identifier: tenantinfo_1.0.0:088b66af218c2fd793ed1ac83d5322c2f7f88afd8341e3543f06fdec3c3dc60f
```

验证合约安装是否安装到节点：

```bash
peer lifecycle chaincode queryinstalled
```

输出：

```
Installed chaincodes on peer:
Package ID: tenantinfo_1.0.0:088b66af218c2fd793ed1ac83d5322c2f7f88afd8341e3543f06fdec3c3dc60f, Label: tenantinfo_1.0.0
```

在宿主机执行 `docker ps` 发现目前合约容器还未启动。

#### 当前组织同意合约定义

Fabric 2.0 智能合约由合约定义控制。当通道成员批准合约定义时，该批准作为组织对其接受的合约参数的投票。这些经过批准的组织定义允许通道成员在链码在通道上使用之前对链码达成一致。链码定义包括以下参数，这些参数需要在组织之间保持一致：

**名称：** 智能合约名称
**版本：** 与给定的合约包相关联的版本号或值。如果您升级了合约二进制文件，那么您也需要更改合约版本。
**序列：** 定义链码的次数。此值为整数，用于跟踪合约升级。例如，当您第一次安装并批准一个 chaincode 定义时，序列号将是 1。下次升级合约时，序列号将增加到 2。
**背书策略：** 哪些组织需要执行和验证事务输出。背书策略可以表示为传递给 CLI 或 SDK 的字符串，也可以在通道配置中引用策略。默认情况下，背书策略被设置为通道/应用程序/背书，这默认要求通道中的大多数组织对交易进行背书。
**集合配置：** 指向与您的 chaincode 关联的私有数据集合定义文件的路径。有关私有数据收集的更多信息，请参见私有数据体系结构参考。
**初始化：** 所有的链码需要包含一个初始化函数来初始化链码。默认情况下，这个函数永远不会执行。但是，您可以使用 chaincode 定义来请求 Init 函数是可调用的。如果请求执行 Init，则 fabric 将确保在调用任何其他函数之前调用 Init，并且只调用一次。
**ESCC/VSCC 插件**: 自定义认证或验证插件的名称。

cli 容器输入以下命令：

```bash
peer lifecycle chaincode approveformyorg -o orderer.romengine.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/romengine.com/orderers/orderer.romengine.com/msp/tlscacerts/tlsca.romengine.com-cert.pem --channelID tenant --name tenantinfo --version 1 --init-required --package-id tenantinfo_1.0.0:088b66af218c2fd793ed1ac83d5322c2f7f88afd8341e3543f06fdec3c3dc60f --sequence 1 --waitForEvent
```

> `--tls` ：是否启动 tls
> `--ca` ：ca 证书路径
> `--channelID` ：智能合约安装通道
> `--name` ：合约名
> `--version` ：合约版本
> `--package-id queryinstalled` ：查询的合约 ID
> `--sequence` ：序列号
> `--waitForEvent` ：等待 peer 提交交易返回
> `--init-required` ：合约是否必须执行 init

控制台日志：

```
txid [86dd573a3af5e9e59c4b1a57a9af84652ed8061a8c22f8ba3fa7241c7a6998d5] committed with status (VALID) at
```

能看到通过 orderer.romengine.com 产生了一个交易。

#### 检查合约定义是否满足策略

Fabric 2.0 的智能合约的部署必须满足一定合约定义策略，策略的定义在通道配置中具体定义
控制台输入以下命令：

```bash
peer lifecycle chaincode checkcommitreadiness --channelID tenant --name tenantinfo --version 1 --sequence 1 --output json --init-required
```

#### 提交合约

```bash
peer lifecycle chaincode commit -o orderer.romengine.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/romengine.com/orderers/orderer.romengine.com/msp/tlscacerts/tlsca.romengine.com-cert.pem --channelID tenant --name tenantinfo --peerAddresses peer0.org1.romengine.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.romengine.com/peers/peer0.org1.romengine.com/tls/ca.crt --peerAddresses peer0.org2.romengine.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.romengine.com/peers/peer0.org2.romengine.com/tls/ca.crt --version 1 --sequence 1 --init-required
```

> `--tls` ：是否启动 tls
> `--ca` ：ca 证书路径
> `--channelID` ：智能合约安装通道
> `--name` ：合约名
> `--version` ：合约版本
> `--package-id queryinstalled` ：查询的合约 ID
> `--sequence` ：序列号
> `--waitForEvent` ：等待 peer 提交交易返回
> `--init-required` ：合约是否必须执行 init
> `--peerAddresses` ：节点路径
> `--tlsRootCertFiles` ：节点 ca 根证书路径(--peerAddresses --tlsRootCertFiles 连用，可多个节点，即将合约部署到对应节点集合上)

日志输出：

```
2020-04-30 02:29:15.572 UTC [chaincodeCmd] ClientWait -> INFO 001 txid [2b9369335527f1148584a8a1766189ee6c9f72e0ca3f9fd375dac3f5a8f8a1ee] committed with status (VALID) at peer0.org1.romengine.com:7051
2020-04-30 02:29:15.573 UTC [chaincodeCmd] ClientWait -> INFO 002 txid [2b9369335527f1148584a8a1766189ee6c9f72e0ca3f9fd375dac3f5a8f8a1ee] committed with status (VALID) at peer0.org2.romengine.com:9051
```

此时可以看到启动了两个链码容器。

#### 查看节点已提交合约

```bash
peer lifecycle chaincode querycommitted --channelID tenant --name tenantinfo
```

日志输出：

```
Committed chaincode definition for chaincode 'tenantinfo' on channel 'tenant':
Version: 1, Sequence: 1, Endorsement Plugin: escc, Validation Plugin: vscc, Approvals: [Org1MSP: true, Org2MSP: true]
```

#### 链码交互

```bash
peer chaincode invoke -o orderer.romengine.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/romengine.com/orderers/orderer.romengine.com/msp/tlscacerts/tlsca.romengine.com-cert.pem --peerAddresses peer0.org1.romengine.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.romengine.com/peers/peer0.org1.romengine.com/tls/ca.crt --peerAddresses peer0.org2.romengine.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.romengine.com/peers/peer0.org2.romengine.com/tls/ca.crt -C tenant -n tenantinfo --isInit -c '{"Args":["initLedger"]}'
```

```
peer chaincode query -C tenant -n tenantinfo -c '{"Args":["queryAllCars","1","1"]}'
```



### 链码升级

#### 查看需要升级的智能合约信息

`docker exec -it cli bash` 进入 cli 的控制台，默认 cli 的环境变量节点为 `peer0.org1.romengine.com`。目前已部署一个智能合约到 `peer0.org1.romengine.com`，cli 控制台输入命令：

```bash
peer lifecycle chaincode queryinstalled
```

查看 `tenantinfo` 合约的 package_id 如下：

```
Installed chaincodes on peer:
Package ID: tenantinfo_1.0.0:088b66af218c2fd793ed1ac83d5322c2f7f88afd8341e3543f06fdec3c3dc60f, Label: tenantinfo_1.0.0
```

查看 `tenantinfo` 合约在 tenant 通道的合约定义，cli 控制台输入命令：

```bash
peer lifecycle chaincode querycommitted -C tenant
```

可以看到 tenantinfo 合约具体信息：

```bash
Committed chaincode definitions on channel 'tenant':
Name: tenantinfo, Version: 1, Sequence: 1, Endorsement Plugin: escc, Validation Plugin: vscc
```

| 参数               | 中文描述                    | 值         |
| :----------------- | :-------------------------- | :--------- |
| Name               | 合约名称                    | tenantinfo |
| Version            | 合约版本                    | 1          |
| Sequence           | 合约序列号(升级 1 次，加 1) | 1          |
| Endorsement Plugin | 背书插件                    | escc       |
| Validation Plugin  | 校验插件                    | vscc       |

#### 修改合约代码

假如本地不存在该合约代码，2.0 提供从节点上获取代码包的操作
控制台输入以下命令：

```bash
peer lifecycle chaincode getinstalledpackage --package-id  tenantinfo_1.0.0:088b66af218c2fd793ed1ac83d5322c2f7f88afd8341e3543f06fdec3c3dc60f
```

#### 重新打包

```bash
peer lifecycle chaincode package tenantinfo.tar.gz --path /opt/gopath/src/github.com/hyperledger/fabric/application/tenant-info/contract --lang node --label tenantinfo_1.0.1
```

#### 重新安装合约

```bash
peer lifecycle chaincode install tenantinfo.tar.gz
```

日志输出：

```
2020-04-30 06:41:23.255 UTC [cli.lifecycle.chaincode] submitInstallProposal -> INFO 001 Installed remotely: response:<status:200 payload:"\nQtenantinfo_1.0.1:3f49cfeb350d4d317c452aa7332f5c767a5f7b4032da544a6e857b1718f5d220\022\020tenantinfo_1.0.1" > 
2020-04-30 06:41:23.255 UTC [cli.lifecycle.chaincode] submitInstallProposal -> INFO 002 Chaincode code package identifier: tenantinfo_1.0.1:3f49cfeb350d4d317c452aa7332f5c767a5f7b4032da544a6e857b1718f5d220
```

此时我们再查询一下节点安装合约信息，控制台输入：

```bash
peer lifecycle chaincode queryinstalled
```

日志输出：

```
Installed chaincodes on peer:
Package ID: tenantinfo_1.0.0:088b66af218c2fd793ed1ac83d5322c2f7f88afd8341e3543f06fdec3c3dc60f, Label: tenantinfo_1.0.0
Package ID: tenantinfo_1.0.1:3f49cfeb350d4d317c452aa7332f5c767a5f7b4032da544a6e857b1718f5d220, Label: tenantinfo_1.0.1
```

可以看到新增了一个 package_id 不一样的 `tenantinfo` 合约，原来的还在。

#### 修改合约定义

```bash
peer lifecycle chaincode approveformyorg -o orderer.romengine.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/romengine.com/orderers/orderer.romengine.com/msp/tlscacerts/tlsca.romengine.com-cert.pem --channelID tenant --name tenantinfo --version 1 --init-required --package-id tenantinfo_1.0.1:3f49cfeb350d4d317c452aa7332f5c767a5f7b4032da544a6e857b1718f5d220 --sequence 2 --waitForEvent
```

检查 `approve` 状态，控制台输入：

```bash
peer lifecycle chaincode checkcommitreadiness --channelID tenant --name tenantinfo --version 1 --sequence 2 --output json --init-required
```

> sequence 要改成 2，因为 install 了 2 次，否则报错 Error: query failed with status: 500 - failed to invoke backing implementation of 'CheckCommitReadiness': requested sequence is 1, but new definition must be sequence 2

切换节点进行同样操作

#### 重新提交合约定义

```bash
peer lifecycle chaincode commit -o orderer.romengine.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/romengine.com/orderers/orderer.romengine.com/msp/tlscacerts/tlsca.romengine.com-cert.pem --channelID tenant --name tenantinfo --peerAddresses peer0.org1.romengine.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.romengine.com/peers/peer0.org1.romengine.com/tls/ca.crt --peerAddresses peer0.org2.romengine.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.romengine.com/peers/peer0.org2.romengine.com/tls/ca.crt --version 1 --sequence 2 --init-required
```

```bash
peer chaincode invoke -o orderer.romengine.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/romengine.com/orderers/orderer.romengine.com/msp/tlscacerts/tlsca.romengine.com-cert.pem -C tenant -n tenantinfo --peerAddresses peer0.org1.romengine.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.romengine.com/peers/peer0.org1.romengine.com/tls/ca.crt --peerAddresses peer0.org2.romengine.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.romengine.com/peers/peer0.org2.romengine.com/tls/ca.crt  -c  '{"Args":["add","3", "1", "赵四儿"]}'  

```

[https://learnblockchain.cn/article/592#4--%E6%80%BB%E7%BB%93](https://learnblockchain.cn/article/592#4--总结)