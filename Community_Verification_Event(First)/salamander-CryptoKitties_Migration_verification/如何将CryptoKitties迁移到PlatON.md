这篇文章主要是讲如何将CryptoKitties迁移到PlatON上，中间会顺带讲一下代码，但是不会太深入，有兴趣的可以自己去看源码。

# 准备工作

**合约源码**

[https://etherscan.io/address/0x06012c8cf97bead5deae237070f9587f8e7a266d#code](https://etherscan.io/address/0x06012c8cf97bead5deae237070f9587f8e7a266d#code)

**迁移说明**

[https://devdocs.platon.network/docs/zh-CN/Solidity_Contract_Migrate](https://devdocs.platon.network/docs/zh-CN/Solidity_Contract_Migrate)

**系统**

ubuntu 18.04

**环境安装**

主要是安装platon-truffle，参考官方教程[https://devdocs.platon.network/docs/zh-CN/Solidity_Dev_Manual](https://devdocs.platon.network/docs/zh-CN/Solidity_Dev_Manual)

**测试账号**

准备三个开发网账号，分别记为owner，user1，user2，用于测试工作。

# 迁移流程

按照下面的步骤即可完成CryptoKitties的迁移，也可以在这个仓库https://github.com/salamandaaa/crypto-kitties完成迁移之后的项目代码。

## 创建工作目录

```plain
mkdir crypto-kitties && cd crypto-kitties
```

## 初始化工程

```plain
platon-truffle init
```
**这里可能会报错**
>✔ Preparing to download
>✖ Downloading
>RequestError: Error: connect ECONNREFUSED 0.0.0.0:443
>    at new RequestError (/usr/local/node-v10.12.0-linux-x64/lib/node_modules/platon-truffle/build/webpack:/node_modules/request-promise-core/lib/errors.js:14:1)

这个是网络原因，可以通过修改/etc/hosts解决。

## 整理代码

源码是打包放到一个文件中的，里面有很多的合约，为了使程序结构更加清晰，我们将合约拆分到不同的文件中，拆分之后的目录是这样的：

![](https://github.com/salamandaaa/crypto-kitties/blob/main/imgs/3.png)

其中Migration.sol是自动生成的，实际上文件数量应该是16个。

**在拆分文件的时候，需要完成以下三个操作**

**1.添加版本声明**

需要在每个合约开头都加上pragma solidity ^0.4.11。

**2.导入依赖**

由于将代码拆分到了不同的文件，因此需要在依赖其他合约的文件中添加import语句，如

import "./Ownable.sol"。

**3.代码兼容**

需要修改address(0)为address(uint160(0))。**新版本，即PlatON1.1.1及以上不需要这一步操作。**

## 代码说明

为了更好地理解代码逻辑，我们接下来讲一下关键的数据结构和接口。

以下是项目的UML图，我并没有把所有的内容都加进入，只加入了个人认为比较关键的点。

![](https://github.com/salamandaaa/crypto-kitties/blob/main/imgs/1.png)

![](https://github.com/salamandaaa/crypto-kitties/blob/main/imgs/2.png)

**KittyAccessControl**

权限管理相关的功能，总共分为了三个权限，分别是CEO、CFO、COO，其中CEO具有最高的权限，即CEO可以修改所有的权限。onlyCLevel是指这三个里面的任何一个都可以。

**KittyBase**

这个合约提供了Kitty相关的基本数据和方法，包括Kitty列表、Auction合约、转移Kitty和创建Kitty等。

**Kitty**

用于记录每只猫的信息，包括基因、代数、父母、生日等。

**KittyOwnerShip**

Kitty所有权相关的合约，主要是实现了ERC721接口。

**KittyBreeding**

提供Kitty的繁殖相关的功能。

**KittyAuction**

提供Kitty出售和sire with的相关方法。

**KittyMinting**

提供Kitty生成的相关方法，只能由COO调用，而且有数量限制。

**KittyCore**

CryptoKitties的主合约。

可以看出，**KittyCore**这个合约才是核心的合约，它承担了Kitty相关的所有业务逻辑。

因为Auction部分比较复杂，因此将其单独开发了合约，便于开发和维护。Auction部分的合约是完全独立于业务的，可以用于其他的NTF相关的拍卖。

**基因混合**

在UML中我们可以发现，Gene算法部分的代码是没有的，只有一个接口文件，因此我们需要自行将这部分代码补上。由于我们只是测试功能，因此只是用最简单的实现方式。我们将文件命名为GeneSimple.sol，放在contracts目录下，代码如下

```plain
pragma solidity ^0.4.11;
import "./GeneScienceInterface.sol";

/// @title SEKRETOOOO
contract GeneSimple is GeneScienceInterface {
    /// @dev simply a boolean to indicate this is the contract we expect to be
    function isGeneScience() public pure returns (bool) {
        return true;
    }

    /// @dev given genes of kitten 1 & 2, return a genetic combination - may have a random factor
    /// @param genes1 genes of mom
    /// @param genes2 genes of sire
    /// @return the genes that are supposed to be passed down the child
    function mixGenes(uint256 genes1, uint256 genes2, uint256 targetBlock) public returns (uint256) {
        bytes32 hash = keccak256(targetBlock);
        uint256 hash256 = uint256(hash);
        uint256 ret = (genes1 ^ genes2) & hash256;
        return ret;
    }
}
```

## 编译

从源码中可以看到版本声明是

```plain
pragma solidity ^0.4.11;
```
这表示仅支持0.4.11及以上版本编译器编译，而不允许0.5.0及以上版本编译器编译。PlatON是支持0.4.26的solidity的，因此我们需要修改配置文件truffle-config.js的编译器版本为0.4.26。
然后执行

```plain
platon-truffle compile
```

## 部署

为了运行CryptoKitties，我们需要部署KittyCore、GeneSimple、SaleClockAuction、SiringClockAuction这4个合约，ERC721Metadata这个合约其实是没什么用的，所以不用部署。

**创建脚本**

cd migrations && touch 2_initial_CryptoKitty.js

**添加代码**

```plain
const KittyCore = artifacts.require("KittyCore");
const GeneSimple = artifacts.require("GeneSimple");
const SaleClockAuction = artifacts.require("SaleClockAuction");
const SiringClockAuction = artifacts.require("SiringClockAuction");

const CUT = 5000;

module.exports = async function(deployer) {
      await deployer.deploy(KittyCore);
      console.log("KittyCore address: ", KittyCore.address);

      await deployer.deploy(GeneSimple);
      console.log("GeneSimple address: ", GeneSimple.address);

      await deployer.deploy(SaleClockAuction, KittyCore.address, CUT);
      console.log("SaleClockAuction address: ", SaleClockAuction.address);

      await deployer.deploy(SiringClockAuction, KittyCore.address, CUT);
      console.log("SiringClockAuction address: ", SiringClockAuction.address);
};
```

**修改配置**

修改文件truffle-config.js中的链信息

```plain
development: {
     host: "35.247.155.162",     // Localhost (default: none)
     port: 6789,            // Standard Ethereum port (default: none)
     network_id: "*",       // Any network (default: none)
     from: "lat1mph9ef447m8dd5cpa5792yzv3ldzexd9c5rcsz",        // Account to send txs from (default: accounts[0])
     gas: 9000000,
     gasPrice: 10000000000
    },
```
host: 节点地址，这里填的是PlatON官方提供的开发网地址。
from: 合约部署账号，填owner账号。

**解锁钱包账户**

进入platon-truffle控制台

```plain
platon-truffle console
```

导入私钥（如果之前已导入可以跳过此步骤）

```plain
web3.platon.personal.importRawKey("您的钱包私钥","您的钱包密码");
```
导入成功会输出私钥对应的地址
```plain
'lat1mph9ef447m8dd5cpa5792yzv3ldzexd9c5rcsz'
```

解锁钱包账户

```plain
web3.platon.personal.unlockAccount('您的钱包地址','您的钱包密码',999999);
```

**部署合约**

执行以下部署合约的命令

```plain
platon-truffle migrate
```

执行成功，得到合约地址如下所示

```plain
KittyCore address: lat1wgvxun6csmxkfsaauaduqp5mrw74ll4f94j3cy
GeneSimple address: lat17km9jwlan8avemk0d3jwp683y0k95y466tayq6
SaleClockAuction address: lat1x6nv5ga7l2pctt4tvlw9t4ekkge6z73gtwmfnv
SiringClockAuction address: lat164xrjncqn79ttcld27tdjq9wttcsz8t6txq72q
```

# 交互验证

前面部署的几个合约，GeneSimple是不包含权限管理的，其他三个的owner都是同一个，即前面设置的owner账号。

由于CryptoKitties核心逻辑在合约中，因此我们主要验证合约迁移是否成功就可以了。受限于时间和精力，我这里选择使用控制台客户端来进行交互验证，有兴趣的朋友可以开发图形界面来完整实现这个项目。控制台客户端是用js开发的，已经开源，有兴趣的朋友可以去看看。

[https://github.com/salamandaaa/crypto-kitties-client](https://github.com/salamandaaa/crypto-kitties-client)

接下来，我们将通过控制台来体验一下kitty的产生、繁殖和拍卖流程。除了owner之外，我们会用到另外两个账号user1和user2，用来模拟参与到Kitty游戏中的真实用户。

**设置合约**

这一步也可以放在部署脚本中，我们通过接口来设置，顺便对这是接口这个功能进行演示。

```plain
node index.js --set_sale_auction_address lat1x6nv5ga7l2pctt4tvlw9t4ekkge6z73gtwmfnv
node index.js --set_sire_auction_address lat164xrjncqn79ttcld27tdjq9wttcsz8t6txq72q
node index.js --set_gene_science_address lat17km9jwlan8avemk0d3jwp683y0k95y466tayq6
```
以上三条命令将SaleClockAuction、SiringClockAuction和GeneSimple三个合约注入到KittyCore中。
**启动**

程序默认是paused，必须将合约全部添加之后才能启动。

```plain
node index.js --unpause
```

**空投**

这里给user1空投一个kitty

```plain
node index.js --airdrop lat1kvprlyg44kuzgl3eqzu7fs0gsgg7903angx92g
```
从控制台的输出可以看到KittyId，这里KittyId是1。
**查询**

查询KittyId为1的owner。

```plain
node index.js --owner_of 1
```
输出结果
```plain
lat1kvprlyg44kuzgl3eqzu7fs0gsgg7903angx92g
```
与空投地址一样。
查询user1拥有的Kitty

```plain
node index.js --kitties_of_owner lat1kvprlyg44kuzgl3eqzu7fs0gsgg7903angx92g
```
输出结果
```plain
[ '1' ]
```
user1有一个id为1的Kitty。
**创建Kitty**

这个操作只能由合约owner来执行，而且创造产生的Kitty总量是有限的

```plain
node index.js --create_kitty
```
从控制台可以看到生成了一个id为2的Kitty。
**查询销售拍卖**

```plain
node index.js --query_sale_auction 2
```
输出结果
```plain
Result {
  '0': 'lat1wgvxun6csmxkfsaauaduqp5mrw74ll4f94j3cy',
  '1': '10000000000000000',
  '2': '0',
  '3': '86400',
  '4': '1636504092333',
  seller: 'lat1wgvxun6csmxkfsaauaduqp5mrw74ll4f94j3cy',
  startingPrice: '10000000000000000',
  endingPrice: '0',
  duration: '86400',
  startedAt: '1636504092333'
}
```
可以看到刚才创建的Kitty拍卖信息，seller是KittyCore合约的地址
**Kitty竞价**

默认起拍价是0.01LAT，只需要超过该价格即可竞拍成功，这里我们使用user2参与竞价

```plain
node index.js --bid_for_sale 2,1000000000000000000,lat1q8s2eekw3kc6f5tnvqs8l9lt4xdp7kxe6rlx7x
```

查询user2的Kitty

```plain
node index.js --kitties_of_owner lat1q8s2eekw3kc6f5tnvqs8l9lt4xdp7kxe6rlx7x
```
输出结果
```plain
[ '2' ]
```
说明已经竞拍成功。
**Kitty繁殖**

user1将id为1的Kitty作为sire拍卖

```plain
node index.js --sire_kitty 1,100000000000000000,10000000000000000,86400,lat1kvprlyg44kuzgl3eqzu7fs0gsgg7903angx92g
```

**查询繁殖拍卖**

查询id为1的Kitty的拍卖信息

```plain
node index.js --query_sire_auction 1
```
输出结果
```plain
Result {
  '0': 'lat1kvprlyg44kuzgl3eqzu7fs0gsgg7903angx92g',
  '1': '100000000000000000',
  '2': '10000000000000000',
  '3': '86400',
  '4': '1636510968566',
  seller: 'lat1kvprlyg44kuzgl3eqzu7fs0gsgg7903angx92g',
  startingPrice: '100000000000000000',
  endingPrice: '10000000000000000',
  duration: '86400',
  startedAt: '1636510968566'
}
```

**繁殖竞价**

user2用id为2的Kitty参与竞价

```plain
node index.js --bid_for_sire 1,2,1000000000000000000,lat1q8s2eekw3kc6f5tnvqs8l9lt4xdp7kxe6rlx7x
```

**出生**

可以由任何一个用户触发，这里我们使用user1

```plain
node index.js --give_birth 2,lat1kvprlyg44kuzgl3eqzu7fs0gsgg7903angx92g
```
从控制台可以看到产生了id为3的Kitty。
接下类我们查询一下这只Kitty的owner

```plain
node index.js --owner_of 3
```
输出结果
```plain
lat1q8s2eekw3kc6f5tnvqs8l9lt4xdp7kxe6rlx7x
```
是user2，也就是参与竞价的那个用户。
**出售**

用户user2对id为3的Kitty进行拍卖

```plain
node index.js --sell_kitty 3,100000000000000000,10000000000000000,86400,lat1q8s2eekw3kc6f5tnvqs8l9lt4xdp7kxe6rlx7x
```

用户user1竞价

```plain
node index.js --bid_for_sale 3,1000000000000000000,lat1kvprlyg44kuzgl3eqzu7fs0gsgg7903angx92g
```

查询id为3的Kitty的owner

```plain
node index.js --owner_of 3
```
输出结果
```plain
lat1kvprlyg44kuzgl3eqzu7fs0gsgg7903angx92g
```
是user1，说明竞价成功，已经获得了这只Kitty。
至此，我们的交互验证就完成了。合约中还有丰富的权限管理功能、合约升级功能等，有兴趣的朋友可以自己去研究。

# 参考资料

1.[https://medium.com/loom-network/how-to-code-your-own-cryptokitties-style-game-on-ethereum-7c8ac86a4eb3](https://medium.com/loom-network/how-to-code-your-own-cryptokitties-style-game-on-ethereum-7c8ac86a4eb3)

