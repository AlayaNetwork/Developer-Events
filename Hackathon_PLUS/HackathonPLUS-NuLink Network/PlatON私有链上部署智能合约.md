# PlatON私有链上部署智能合约

PlatON隐私AI网络是面向未来的全球隐私计算网络，是全数字经济时代的公共基础设施。区块链生态的建设主要建立在智能合约之上，PlatON隐私计算基础设施，能够解决区块链生态发展的技术瓶颈，使链上匮乏的算力能够革命性的提升，使被传统互联网漠视的隐私保护能够得到有效的尊重，使不可信环境中的参与者能够可信的协同，真正成为数字经济的底层动能。

PlatON支持EVM和WASM两种智能合约,本教程主要介绍如何在PlatON私有链上部署solidity智能合约。

## 环境
系统：ubuntu18.04

搭建PlatON私有链，这里我们使用PlatON的单节点模式。

1. 安装依赖
```
sudo apt update 
sudo apt install -y golang-go git cmake llvm g++ libgmp-dev libssl-dev
```
2. 获取源码
```
git clone -b master https://github.com/PlatONnetwork/PlatON-Go.git --recursive
```
3. 编译
```
cd PlatON-Go 
make all
```
4. 生成nodekey和blskey等相关文件
```
mkdir ./data && ./build/bin/platonkey genkeypair | tee >(grep "PrivateKey" | awk '{print $2}' > ./data/nodekey) >(grep "PublicKey" | awk '{print $3}' > ./data/nodeid) && ./build/bin/platonkey genblskeypair | tee >(grep "PrivateKey" | awk '{print $2}' > ./data/blskey) >(grep "PublicKey" | awk '{print $3}' > ./data/blspub)
```
```
说明：
nodeid：节点公钥(ID）文件，保存节点的公钥，用于标识节点身份
nodekey：节点私钥文件，保存节点的私钥，不能公开并且需要做好备份。
blspub：节点BLS公钥文件，保存节点的BLS公钥，用于共识协议中快速验证签名。
blskey：节点BLS私钥文件，保存节点的BLS私钥，不能公开并且需要做好备份。
```
5. 创建keystore文件
```
./build/bin/platon --datadir ./data account new
//address: lat15cc8dhgzz9qsy9jyut5uqkjuvjt2r25qj0xx25
```
6. 创世文件
```
{
    "config": {
        "chainId": 1277,
        "eip155Block": 0,
        "cbft": {
            "initialNodes": [
              {
                    "node":"enode://045067884a33de79b8d8d6ae0b3416e867239cd9bf8fd2908f48a9bf4a3bc58c7ff28c242a01951600d58bce88c6f846a8212eb8f824a314f55687bfb75cba07e5@127.0.0.1:16790",
                    "blsPubKey":"837ed63dffdafa96d7e5e28d78ff20a9a0afc0896ddc6e52f33fa612302e548f82b6145b49868e8a3d616b033211610065a04a0ff45d296758427d3c07e1f62cccb104ec150dba15180a99f179b2d55a97cd958324778c2882df4283fe578080"
              }
            ],
            "amount": 10,
            "period": 10000,
            "validatorMode": "ppos"
        },
        "genesisVersion": 3328
    },
    "economicModel":{
        "common":{
            "maxEpochMinutes":4,
            "maxConsensusVals":4,
            "additionalCycleTime":28
        },
        "staking":{
            "stakeThreshold": 1000000000000000000000000,
            "operatingThreshold": 10000000000000000000,
            "maxValidators": 30,
            "unStakeFreezeDuration": 2,
            "rewardPerMaxChangeRange": 500,
            "rewardPerChangeInterval": 10
        },
        "slashing":{
           "slashFractionDuplicateSign": 100,
           "duplicateSignReportReward": 50,
           "maxEvidenceAge":1,
           "slashBlocksReward":20,
           "zeroProduceCumulativeTime":3,
           "zeroProduceNumberThreshold":2,
           "zeroProduceFreezeDuration":1
        },
         "gov": {
            "versionProposalVoteDurationSeconds": 160,
            "versionProposalSupportRate": 6670,
            "textProposalVoteDurationSeconds": 160,
            "textProposalVoteRate": 5000,
            "textProposalSupportRate": 6670,          
            "cancelProposalVoteRate": 5000,
            "cancelProposalSupportRate": 6670,
            "paramProposalVoteDurationSeconds": 160,
            "paramProposalVoteRate": 5000,
            "paramProposalSupportRate": 6670      
        },
        "reward":{
            "newBlockRate": 50,
            "platonFoundationYear": 10,
            "increaseIssuanceRatio": 250
        },
        "innerAcc":{
            "platonFundAccount": "lat15cc8dhgzz9qsy9jyut5uqkjuvjt2r25qj0xx25",
            "platonFundBalance": 0,
            "cdfAccount": "lat15cc8dhgzz9qsy9jyut5uqkjuvjt2r25qj0xx25",
            "cdfBalance": 331811981000000000000000000
        }
    },
    "nonce": "0x0376e56dffd12ab53bb149bda4e0cbce2b6aabe4cccc0df0b5a39e12977a2fcd23",
    "timestamp": "0x61B874A5",
    "extraData": "",
    "gasLimit": "4712388",
    "alloc": {
        "lat15cc8dhgzz9qsy9jyut5uqkjuvjt2r25qj0xx25": {
            "balance": "200000000000000000000000000"
        }
    },
    "number": "0x0",
    "gasUsed": "0x0",
    "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```
7. 启动
配置好创世文件后，就可以开始启动节点了
```
初始化创世区块
./build/bin/platon --datadir ./data init platon.json
后台启动节点
 nohup ./build/bin/platon --identity "platon" --datadir ./data --port 6790 --rpcaddr 127.0.0.1 --rpcport 6789 --rpcapi "db,platon,net,web3,admin,personal" --rpc --nodiscover --nodekey ./data/nodekey --cbft.blskey ./data/blskey & > ./data/platon.log 2>&1 &
```

私有链启动后就可以开始编辑合约了，这里使用[alaya-truffle](https://platon-truffle.readthedocs.io/en/alaya/getting-started/installation.html)来编译，部署合约。

简单合约
```
pragma solidity ^0.5.17;

contract HelloWorld {
    
    string name;
    
    function setName(string memory _name) public returns(string memory){
        name = _name;
        return name;
    }
    
    function getName() public view returns(string memory){
        return name;
    }
}
```
初始化工程: 使用alaya-truffle初始化一个工程
```
mkdir HelloWorld && cd HelloWorld
alaya-truffle init
```
在操作完成之后，就有如下项目结构：
```
contracts/: Solidity合约目录
migrations/: 部署脚本文件目录
test/: 测试脚本目录
truffle-config.js: alaya-truffle 配置文件
```
将之前编写好的HelloWorld合约放至HelloWorld/contracts/目录下，并修改编译器版本
```
compilers: {
      solc: {
            version: "^0.5.17",    // 此版本号与HelloWorld.sol中声明的版本号保持一致
      }
}
```
编译合约
```
alaya-truffle compile
```
在操作完成之后，生成如下目录结构：
```
build/: Solidity合约编译后的目录
build/contracts/HelloWorld.json HelloWorld.sol对应的编译文件
```
增加部署脚本文件并修改配置
```
cd migrations/ && touch initial_helloworld.js
添加以下内容到initial_helloworld.js
const helloWorld = artifacts.require("HelloWorld"); 
    module.exports = function(deployer) {
       deployer.deploy(helloWorld); 
};
修改truffle-config.js中链的配置信息
networks: {
    development: {
       host: "127.0.0.1",     // 区块链所在服务器主机
       port: 6789 ,            // 链端口号
       network_id: "*",       // Any network (default: none)
       from: "lat15cc8dhgzz9qsy9jyut5uqkjuvjt2r25qj0xx25 ", //部署合约账号的钱包地址
       gas: 999999,
       gasPrice: 50000000004,
    },
}
```

到此为止，就可以部署合约了，由于部署合约需要签名交易，所以需要先解锁钱包.
```
解锁钱包
alaya-truffle console
 web3.platon.personal.unlockAccount('您的钱包地址','您的钱包密码',999999);
```
部署合约
```
alaya-truffle migrate
```
部署成功后，将看到类似如下信息：
```
initial_helloworld.js
======================

   Deploying 'HelloWorld'
   ----------------------
   > transaction hash:    0x33ec48cc467f9bc943fd096c57c8a7e7b7fa941380538d9e59797800c6c4356c
   > Blocks: 0            Seconds: 0
   > contract address:    atp1d2xxup4au4pqkgkm6a3p6hj3x0vvekdj52cc2a
   > block number:        3422
   > block timestamp:     1639678437
   > account:             lat15cc8dhgzz9qsy9jyut5uqkjuvjt2r25qj0xx25
   > balance:             190000000000000000000000000000
   > gas used:            145569
   > gas price:           1 gvon
   > value sent:          0 ATP
   > total cost:          0.000145569 ATP


   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:          0.000145569 ATP

Summary
=======
> Total deployments:   2
> Final cost:          0.000259892 ATP
```
到此，整个部署过程就完成了。

