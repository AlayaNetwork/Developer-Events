# Ethereum智能合约迁移到PlatON教程
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Ethereum生态的不断壮大，导致交易数量急剧增加，用户不得不在交易速度和手续费之间做出艰难的选择。随着DeFi和NFT项目持续火爆，产生了高额利润催生了大量的“套利”交易，导致用户对区块链数据隐私的需求不断增加。PlatON结合区块链、人工智能和隐私计算技术，建立了一个去中心化的协作式隐私人工智能区块链网络。相较于以太坊在交易速度、交易成本和数据隐私方面有巨大的优势。为提高开发效率，PlatON 1.1.1版本和Alaya0.16.1版本将开始全面兼容Ethereum生态工具。本文将以ENS为例，讲解如何将Ethereum智能合约迁移到PlatON中。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ENS（Ethereum Name Service）是一个基于Ethereum的分布式、开放和可扩展的命名系统。ENS是一个层次结构的域名系统，层次的名称叫做域，不同域之间以“点“作为分隔符，一个域的所有者能够完全控制其子域。ENS主要由注册表和解析器组成。其中，注册表用于维护所有域名和子域名列表，解析器负责将域名转换为地址。通过分析ENS智能合约，`ENSRegistry.sol`合约实现了注册表功能；`ensdomains/resolver/PublicResolver`合约实现了解析器功能。为实现子域名自动化注册，需要部署`FIFSRegistrar.sol`合约。此外，启用ENS反向解析，需部署`ReverseRegistrar.sol`合约。

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;在Ethereum生态中，用于智能合约部署的工具主要有Truffle和Remix两种，首先介绍如何使用Truffle部署合约。
## 一、使用Truffle迁移ENS合约

### 1.1安装Truffle

首先需要利用高于v8.9.4的nodejs安装Truffle
```bash
npm install -g truffle
```
[Truffle使用文档](https://www.trufflesuite.com/docs/truffle/overview)
### 1.2 下载编译ENS合约

**Step1** 下载ENS合约
```bash
git clone https://github.com/ensdomains/ens.git && cd ens
```
**Step2** 使用truffle初始化一个工程
```bash
truffle init
```
在操作完成之后，就有如下项目结构：
- contracts/: Solidity合约目录
- migrations/: 部署脚本文件目录
- test/: 测试脚本目录
- truffle-config.js: platon-truffle 配置文件
**Step3** 修改platon-truffle 配置文件truffle-config.js
PlatON测试网RPC信息：
- chainid为`210309`
- RPC为`http://35.247.155.162:6789、"https://devnetopenapi.platon.network/rpc"  以及 ws://35.247.155.162:6790`
前往[PlatON测试网水龙头](https://faucet.platon.network/faucet/?id=e5d32df10aee11ec911142010a667c03)，在水龙头中输入的地址可以是LAT或0x格式的地址.
```bash
vim truffle-config.js
```
truffle-config.js 修改部分内容如下：
```bash
development: {
      host: "35.247.155.162",     // RPC(default: none)
      port: 6789,            // Standard PlatON port (default: none)
      network_id: "*",       // Any network (default: none)
      from: "地址",        // Account to send txs from (default: accounts[0]) 这个地址格式可以是0x或LAT,建议使用0x格式
},

...

compilers: {
      solc: {
            version: "^0.5.17",    // 此版本号与合约声明的版本号保持一致
      }
}
```
**Step4**  编译合约
```bash
truffle compile
```
在操作完成之后，生成如下目录结构：
- build/: Solidity合约编译后的目录
- build/contracts/: 对应的编译文件
### 1.3 部署ENS合约

**Step1** 新增合约部署脚本文件
```bash
cd migrations/ && vim 2_initial_ens.js
```
部署脚本文件名建议使用合约名称便于后面维护,如ENS合约对应的部署脚本文件为2_initial_ens.js，内容如下所示：
```bash
const ENS = artifacts.require("@ensdomains/ens/ENSRegistry");
const FIFSRegistrar = artifacts.require("@ensdomains/ens/FIFSRegistrar");
const ReverseRegistrar = artifacts.require("@ensdomains/ens/ReverseRegistrar");
const PublicResolver = artifacts.require("@ensdomains/resolver/PublicResolver");

const utils = require('web3-utils');
const namehash = require('eth-ens-namehash');

const tld = "test";

module.exports = function(deployer, network, accounts) {
  let ens;
  let resolver;
  let registrar;

  // Registry
  deployer.deploy(ENS)
  // Resolver
  .then(function(ensInstance) {
    ens = ensInstance;
    return deployer.deploy(PublicResolver, ens.address);
  })
  .then(function(resolverInstance) {
    resolver = resolverInstance;
    return setupResolver(ens, resolver, accounts);
  })
  // Registrar
  .then(function() {
    return deployer.deploy(FIFSRegistrar, ens.address, namehash.hash(tld));
  })
  .then(function(registrarInstance) {
    registrar = registrarInstance;
    return setupRegistrar(ens, registrar);
  })
  // Reverse Registrar
  .then(function() {
    return deployer.deploy(ReverseRegistrar, ens.address, resolver.address);
  })
  .then(function(reverseRegistrarInstance) {
    return setupReverseRegistrar(ens, resolver, reverseRegistrarInstance, accounts);
  })
};

async function setupResolver(ens, resolver, accounts) {
  const resolverNode = namehash.hash("resolver");
  const resolverLabel = utils.sha3("resolver");

  await ens.setSubnodeOwner("0x0000000000000000000000000000000000000000", resolverLabel, accounts[0]);
  await ens.setResolver(resolverNode, resolver.address);
  await resolver.setAddr(resolverNode, resolver.address);
}

async function setupRegistrar(ens, registrar) {
  await ens.setSubnodeOwner("0x0000000000000000000000000000000000000000", utils.sha3(tld), registrar.address);
}

async function setupReverseRegistrar(ens, resolver, reverseRegistrar, accounts) {
  await ens.setSubnodeOwner("0x0000000000000000000000000000000000000000", utils.sha3("reverse"), accounts[0]);
  await ens.setSubnodeOwner(namehash.hash("reverse"), utils.sha3("addr"), reverseRegistrar.address);
}
```
**Step2** 解锁账户
进入Truffle控制台
```bash
truffle console
```
导入账户
```bash
web3.eth.personal.importRawKey('私钥','密码')   	// 私钥没有0x前缀
```

解锁账户
```bash
web3.eth.personal.unlockAccount('地址', '密码', 999999)
```
**Step3** 合约部署
```bash
truffle migrate
```
当看到以下内容代表合约已经部署成功。Gas实际上使用LAT结算，因此用于部署合约的地址中需要有一定的LAT。
```bash
Replacing 'ENSRegistry'
   -----------------------
   > transaction hash:    0x7f166ce0d1287202e0583c739cacb77d0166fb8c5c459b9aecd2491e44bfc5ba
   > Blocks: 4            Seconds: 4
   > contract address:    0x5215c9F58E1Bf2c4BF6c7B5F80AD612CE0623AA9
   > block number:        6157441
   > block timestamp:     1637306960253
   > account:             0x54AFd12E9e5678F3Fc6f528120e1a13cb9c3c2AF
   > balance:             198.00101596
   > gas used:            584642 (0x8ebc2)
   > gas price:           500 gwei
   > value sent:          0 ETH
   > total cost:          0.292321 ETH


   Replacing 'PublicResolver'
   --------------------------
   > transaction hash:    0x78b33dc7ca8df8f72c0da17fd8d125207bc7903039c2987578500a150e32499d
   > Blocks: 2            Seconds: 4
   > contract address:    0xA447FDd6c8BF9Ce3Ce80f52b7075d5651D301369
   > block number:        6157448
   > block timestamp:     1637306967968
   > account:             0x54AFd12E9e5678F3Fc6f528120e1a13cb9c3c2AF
   > balance:             196.30776246
   > gas used:            3386507 (0x33ac8b)
   > gas price:           500 gwei
   > value sent:          0 ETH
   > total cost:          1.6932535 ETH


   Replacing 'FIFSRegistrar'
   -------------------------
   > transaction hash:    0xcc61e6a21a9ec5b1f4678becbde438846396fbee59616f58898d05d5c8faa9e8
   > Blocks: 2            Seconds: 4
   > contract address:    0xd72eF5Af584bc13799361505207B3508bc317D93
   > block number:        6157470
   > block timestamp:     1637306992188
   > account:             0x54AFd12E9e5678F3Fc6f528120e1a13cb9c3c2AF
   > balance:             196.13476646
   > gas used:            202670 (0x317ae)
   > gas price:           500 gwei
   > value sent:          0 ETH
   > total cost:          0.101335 ETH


   Replacing 'ReverseRegistrar'
   ----------------------------
   > transaction hash:    0x80a6bb520f60849a385691e84600518a98308bd6e1cc2ecfbca66d79a1c36b3b
   > Blocks: 3            Seconds: 4
   > contract address:    0x7431eB4F2f8B55c76017b8e26fc0B7205709F08F
   > block number:        6157481
   > block timestamp:     1637307004348
   > account:             0x54AFd12E9e5678F3Fc6f528120e1a13cb9c3c2AF
   > balance:             195.85967096
   > gas used:            505078 (0x7b4f6)
   > gas price:           500 gwei
   > value sent:          0 ETH
   > total cost:          0.252539 ETH

   > Saving artifacts
   -------------------------------------
   > Total cost:           2.3394485 ETH


Summary
=======
> Total deployments:   4
> Final cost:          2.3394485 ETH
```

## 二、使用Remix迁移ENS合约

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;使用Truffle迁移智能合约并非是最佳选择，主要原因包括三点：一是，需部署额外的`Migrations.sol`合约，增加了部署成本；二是，若部署合约所需时间较长，无法及时根据网络拥堵情况，灵活调整Gas；三是，部署过程中需全程保证网络链接，一旦失去网络链接需从头开始部署合约。而使用Remix可以较好的解决上述问题。

### 2.1 将合约导入Remix

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;通过分析可知，迁移ENS需要部署四个智能合约。这四个合约分别位于`https://github.com/ensdomains/ens/tree/master/contracts`和`https://github.com/ensdomains/resolvers`内。首先进入`https://remix.ethereum.org/`将所有合约导入Remix中，如图1所示。

![image](https://github.com/MetaPort-DID/Developer-Events/blob/main/Hackathon_PLUS/HackathonPLUS-MetaPort/IMG/1ENS%E6%89%80%E6%B6%89%E5%8F%8A%E5%88%B0%E7%9A%84%E5%90%88%E7%BA%A6.jpeg)

<center>图1 ENS所涉及到的合约</center>

### 2.2 部署合约

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;根据合约依赖关系，需要第一个部署的是注册表合约，即`ENSRegistry.sol`。首先，选择所需部署的合约；其次，根据合约声明选择solidity版本；最后，点击`Compile ContractName`编译合约，如图2、3所示。

![image](https://github.com/MetaPort-DID/Developer-Events/blob/main/Hackathon_PLUS/HackathonPLUS-MetaPort/IMG/2%E9%80%89%E6%8B%A9%E6%89%80%E9%9C%80%E9%83%A8%E7%BD%B2%E7%9A%84%E5%90%88%E7%BA%A6.jpeg)
<center>图2 选择所需部署的合约</center>

![image](https://github.com/MetaPort-DID/Developer-Events/blob/main/Hackathon_PLUS/HackathonPLUS-MetaPort/IMG/3%E7%BC%96%E8%AF%91%E6%89%80%E9%9C%80%E9%83%A8%E7%BD%B2%E7%9A%84%E5%90%88%E7%BA%A6.jpeg)
<center>图3 编译所需部署的合约</center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;若编译成功，则进入Remix的部署界面，完成ENSRegistry合约的部署，如图4所示。部署时，首先将PlatON测试网RPC添加到MateMask中，通过私钥将LAT地址导入到MateMask中；其次，在Remix的deploy界面中将ENVIRONMENT设置为Injected Web3 ，并将CONTRACT设置为所需部署的合约；然后，点击Deploy可以设置该笔交易的Gas Price，如图5所示；最后，在MateMask中点击确认，便成功部署该合约。

![image](https://github.com/MetaPort-DID/Developer-Events/blob/main/Hackathon_PLUS/HackathonPLUS-MetaPort/IMG/4%E5%90%88%E7%BA%A6%E9%83%A8%E7%BD%B2%E7%95%8C%E9%9D%A2.jpeg)
<center>图4 合约部署界面</center>


![image](https://github.com/MetaPort-DID/Developer-Events/blob/main/Hackathon_PLUS/HackathonPLUS-MetaPort/IMG/5%E8%B0%83%E6%95%B4%E4%BA%A4%E6%98%93Gas%20Price%E4%BB%B7%E6%A0%BC.jpeg)
<center>图5 调整交易Gas Price价格</center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;同理，使用Remix部署其余三个合约与部署`ENSRegistry.sol`方法一致。需额外注意的有三点。一是，如果合约通过`import "@PATH/contractName.sol"`导入其他合约，需要统一修改为`import "https://github.com/PATH/contractName.sol"`；二是，若导入合约所使用solidity版本与项目版本不一致，需修改solidity版本和因此导致的编译错误；三是，构造函数需要传参时，需在Deploy后面传入参数。入参大于一时，使用“,”分割，如图6所示。


![image](https://github.com/MetaPort-DID/Developer-Events/blob/main/Hackathon_PLUS/HackathonPLUS-MetaPort/IMG/6%E6%9E%84%E9%80%A0%E5%87%BD%E6%95%B0%E4%BC%A0%E5%8F%82.jpeg)
<center>图6 构造函数传参</center>

## 三、合约测试

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果使用Truffle部署合约，可以使用Web3对部署的合约进行调试。首先，通过`npm install web3`安装web3包。由于项目需要调试的内容比较多，本文只介绍测试程序的主要框架。
```bash
const Web3 = require('web3')
const HttpProvider = ${RPC}
const web3 = new Web3(new Web3.providers.HttpProvider(HttpProvider))

const fs = require("fs");
const ABIString= fs.readFileSync("./build/contracts/fileName.json", "utf-8");
const ABIJSON= JSON.parse(ABIString);

const privateKey = '私钥'
web3.eth.accounts.wallet.add(privateKey)
const myAddr = web3.eth.accounts.wallet[0].address
var ContractName = new web3.eth.Contract(ABIJSON.abi,'ContractAddr');

async function test() {
    // 修改合约状态的方法
    var renturnsContent = await ContractName.methods.methodsName(param).send({
        from:myAddr,
        gas:2000000,
        gasPrice:"999999"
    },function(error,txHash){
        console.log("error:",error);
        console.log("txHash:",txHash);
    })
    console.log("renturnsContent:",renturnsContent);
    
    // 查询合约状态
    var renturnsContent= await ContractName.methods.methodsName(param).call()
    console.log("renturnsContent:",renturnsContent);
}
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;如果使用Remix部署合约，合约部署成功之后在Deployed Contract下面会显示合约名称，合约地址和合约的对外方法，如图7所示。


![image](https://github.com/MetaPort-DID/Developer-Events/blob/main/Hackathon_PLUS/HackathonPLUS-MetaPort/IMG/7Remix%E5%90%88%E7%BA%A6%E8%B0%83%E8%AF%95%E7%95%8C%E9%9D%A2.jpeg)
<center>图7 Remix合约调试界面</center>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;至此，将ENS迁移到PlatON的整个流程介绍完毕。整个流程与在Ethereum上部署合约的体验一致，说明PlatON在兼容Ethereum生态方面已经足够成熟，将一步降低了Ethereum开发者在PlatON上的开发成本。期待，PlatON借助自身的隐私计算、AI方面的优势以及Ethereum强悍的生态，打造一更加强悍的生态，更好的服务于实体经济的发展。



