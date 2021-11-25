# Preparation

## Source Code

**Chainlink**

[https://github.com/smartcontractkit/chainlink](https://github.com/smartcontractkit/chainlink)

**truffle-starter-kit**

[https://github.com/smartcontractkit/truffle-starter-kit](https://github.com/smartcontractkit/truffle-starter-kit)

**LinkToken**

[https://github.com/smartcontractkit/LinkToken](https://github.com/smartcontractkit/LinkToken)

## Environment

**OS**

ubuntu 18.04

**Software**

platon-truffle

node 12.18

**Chain**

platon dev network

## **Accounts**

Prepare a funded account for testing

# Migration

## Deploy LinkToken

**Create Project**

Create a new platon-truffle project

```plain
mkdir linktoken
cd linktoken
platon-truffle init
```

**Create Contract**

Copy LinkToken.sol from [https://github.com/smartcontractkit/LinkToken/blob/master/contracts-flat/v0.6/LinkToken.sol](https://github.com/smartcontractkit/LinkToken/blob/master/contracts-flat/v0.6/LinkToken.sol) into contracts directory

*If you use platon-truffle of version 1.0.0, you must change address(0) to address(uint160(0)), or you will get compile errors.

**Change Configuration**

Configure the truffle-config.js file, because link contracts are almost ^0.6.6, and platon-truffle support compiler version 0.6.12, so we change compiler version to 0.6.12.

```plain
compilers: {
    solc: {
      version: '0.6.12',
    },
  },
```
Don't forget to configure networks, you can to go [https://devdocs.platon.network/docs/en/Solidity_Dev_Manual](https://devdocs.platon.network/docs/en/Solidity_Dev_Manual) for help.
**Deploy contract**

```plain
platon-truffle migrate
```
Address of the contract is
```plain
lat1nsfwaseyy2f6hgycsdj33myhzadctuz0459ztl
```
*Repo:[https://github.com/hthuang996/PlatONLink](https://github.com/hthuang996/PlatONLink)
## Deploy MyOracle

**Clone Repo**

Clone truffle-starter-kit repository into your workspace

```plain
git clone https://github.com/smartcontractkit/truffle-starter-kit.git
```

**Install**

```plain
npm install
```
*You probably encounter a network error when mirroring a github repository, you can use an offshore server.
Remove dependency truffle

```plain
rm -rf node_modules/@truffle
rm -rf node_modules/@trufflesuite
```

Delete all files in migrations directory except for 1_initial_migration.js.

**Create Contract**

Create a new contract named MyOracle.sol

```plain
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.6;
import "@chainlink/contracts/src/v0.6/Oracle.sol";

contract MyOracle is Oracle {
    constructor(address _link) Oracle(_link)
    public
  {
    
  }
}
```
*If you use platon-truffle of version 1.0.0, you must change address(0) to address(uint160(0)) of *.sol in contracts, @chainlink/contracts/src/v0.6 and @openzeppelin
**Add Migration File**

Create a file named 2_oracle.js

```plain
const Oracle = artifacts.require('MyOracle')

module.exports = deployer => {
  deployer.deploy(Oracle, '<TOKEN_ADDRESS>')
}
```
You should replace <TOKEN_ADDRESS> with the deployed token address.
**Change Configuration**

Configure truffle-config.js, add a network named platon, and delete other networks, change solc version to 0.6.12

```plain
module.exports = {
  networks: {
    development: {
      host: '35.247.155.162',
      port: 6789,
      from: 'lat127rmxxmql7vhy6pjtlz4wga7qukx6g78wcwdge',
      network_id: '*'
    },
  },
  compilers: {
    solc: {
      version: '0.6.12',
    },
  },
}
```

**Deploy Contract**

```plain
platon-truffle migrate --network development
```
Address of the contract is
```plain
lat1qj0xtgxp7frjyv52ss2lk6xvxsjuhkcft429cv
```

## **Deploy MyContract**

**Add Migration File**

Add a file named 3_mycontract_migration.js in migrations directory

```plain
const MyContract = artifacts.require('MyContract')

module.exports = async (deployer, network, [defaultAccount]) => {
  deployer.deploy(MyContract, '<ORACLE_ADDRESS>')
}
```
You should replace <ORACLE_ADDRESS> with deployed MyOracle address.
**Deploy Contract**

```plain
platon-truffle migrate --network development
```
Address of the contract is
```plain
lat176tlwz75ykuv03yf4jp0cawqn2ulkw8kfdpe7z
```

## Off-Chain Node

**Clone Repository**

```plain
git clone https://github.com/smartcontractkit/chainlink
```

**Change Code**

core/chains/evm/config/chain_specific_config.go

```plain
platon := mainnet
platon.linkContractAddress = "<LINK_TOKEN_ADDRESS>"
chainSpecificConfigDefaultSets[210309] = platon
```
core/services/keystore/keys/ethkey/address.go
```plain
// address := common.HexToAddress(s)
// if s != address.Hex() {
//      return EIP55Address(""), fmt.Errorf(`"%s" is not a valid EIP55 formatted address`, s)
// }
```

**Install And Run**

Refer [https://github.com/smartcontractkit/chainlink](https://github.com/smartcontractkit/chainlink).README.md

# Run

Code can be found at [https://github.com/hthuang996/platon-link-test](https://github.com/hthuang996/platon-link-test)

## Fund

Before using the contract, you must fund the contract with LINK. Here, we transfer 100LINK to MyContract address.

```plain
let web3js = new web3('http://35.247.155.162:6789');
const FROM = 'lat127rmxxmql7vhy6pjtlz4wga7qukx6g78wcwdge'
const PK = '<PRIVATE_KEY_OF_FROM>'

async function fund() {
    // abi of LINK token
    let ABI = '[{"inputs":[],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"owner","type":"address"},{"indexed":true,"internalType":"address","name":"spender","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"from","type":"address"},{"indexed":true,"internalType":"address","name":"to","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"},{"indexed":false,"internalType":"bytes","name":"data","type":"bytes"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"from","type":"address"},{"indexed":true,"internalType":"address","name":"to","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"inputs":[{"internalType":"address","name":"owner","type":"address"},{"internalType":"address","name":"spender","type":"address"}],"name":"allowance","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"approve","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"account","type":"address"}],"name":"balanceOf","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[],"name":"decimals","outputs":[{"internalType":"uint8","name":"","type":"uint8"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"subtractedValue","type":"uint256"}],"name":"decreaseAllowance","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"subtractedValue","type":"uint256"}],"name":"decreaseApproval","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"addedValue","type":"uint256"}],"name":"increaseAllowance","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"addedValue","type":"uint256"}],"name":"increaseApproval","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"name","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[],"name":"symbol","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[],"name":"totalSupply","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[{"internalType":"address","name":"recipient","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transfer","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"to","type":"address"},{"internalType":"uint256","name":"value","type":"uint256"},{"internalType":"bytes","name":"data","type":"bytes"}],"name":"transferAndCall","outputs":[{"internalType":"bool","name":"success","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"sender","type":"address"},{"internalType":"address","name":"recipient","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transferFrom","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"typeAndVersion","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"pure","type":"function","constant":true}]';
    // address of LINK token
    let ADDRESS = 'lat1nsfwaseyy2f6hgycsdj33myhzadctuz0459ztl'

    let nonce = web3.utils.numberToHex(await web3js.platon.getTransactionCount(FROM));
    let contract = new web3js.platon.Contract(JSON.parse(ABI), ADDRESS, null);
    let data = contract.methods["transfer"]('<MY_CONTRACT_ADDRESS>', '100000000000000000000').encodeABI();
    let tx = {
        from: FROM,
        to: ADDRESS,
        chainId: 210309,
        data: data,
        gas: "1000000", 
        nonce: nonce,
    };
    // sign
    let signTx = await web3js.platon.accounts.signTransaction(tx, PK);
    // send
    let receipt = await web3js.platon.sendSignedTransaction(signTx.rawTransaction);
    console.log("sign tx data:\n", signTx.rawTransaction, receipt)
}
```
Replace <MY_CONTRACT_ADDRESS> with the deployed MyContract address.
## Query

Query if <MY_CONTRACT_ADDRESS> has LINK.

```plain
async function getBalance(addr) {
    let ABI = '[{"inputs":[],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"owner","type":"address"},{"indexed":true,"internalType":"address","name":"spender","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Approval","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"from","type":"address"},{"indexed":true,"internalType":"address","name":"to","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"},{"indexed":false,"internalType":"bytes","name":"data","type":"bytes"}],"name":"Transfer","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"from","type":"address"},{"indexed":true,"internalType":"address","name":"to","type":"address"},{"indexed":false,"internalType":"uint256","name":"value","type":"uint256"}],"name":"Transfer","type":"event"},{"inputs":[{"internalType":"address","name":"owner","type":"address"},{"internalType":"address","name":"spender","type":"address"}],"name":"allowance","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"approve","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"account","type":"address"}],"name":"balanceOf","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[],"name":"decimals","outputs":[{"internalType":"uint8","name":"","type":"uint8"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"subtractedValue","type":"uint256"}],"name":"decreaseAllowance","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"subtractedValue","type":"uint256"}],"name":"decreaseApproval","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"addedValue","type":"uint256"}],"name":"increaseAllowance","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"spender","type":"address"},{"internalType":"uint256","name":"addedValue","type":"uint256"}],"name":"increaseApproval","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"name","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[],"name":"symbol","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[],"name":"totalSupply","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[{"internalType":"address","name":"recipient","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transfer","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"to","type":"address"},{"internalType":"uint256","name":"value","type":"uint256"},{"internalType":"bytes","name":"data","type":"bytes"}],"name":"transferAndCall","outputs":[{"internalType":"bool","name":"success","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"sender","type":"address"},{"internalType":"address","name":"recipient","type":"address"},{"internalType":"uint256","name":"amount","type":"uint256"}],"name":"transferFrom","outputs":[{"internalType":"bool","name":"","type":"bool"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"typeAndVersion","outputs":[{"internalType":"string","name":"","type":"string"}],"stateMutability":"pure","type":"function","constant":true}]';
    let ADDRESS = 'lat1nsfwaseyy2f6hgycsdj33myhzadctuz0459ztl'
    
    let contract = new web3js.platon.Contract(JSON.parse(ABI), ADDRESS, null);
    contract.methods["balanceOf"](addr).call(null, (error, result) => console.log(result));
}

getBalance('<MY_CONTRACT_ADDRESS>')
```
Output is
```plain
100000000000000000000
```
## **Request**

```plain
async function request() {
    const ABI = '[{"inputs":[{"internalType":"address","name":"_link","type":"address"}],"stateMutability":"nonpayable","type":"constructor"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"bytes32","name":"id","type":"bytes32"}],"name":"ChainlinkCancelled","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"bytes32","name":"id","type":"bytes32"}],"name":"ChainlinkFulfilled","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"bytes32","name":"id","type":"bytes32"}],"name":"ChainlinkRequested","type":"event"},{"anonymous":false,"inputs":[{"indexed":true,"internalType":"address","name":"previousOwner","type":"address"},{"indexed":true,"internalType":"address","name":"newOwner","type":"address"}],"name":"OwnershipTransferred","type":"event"},{"inputs":[],"name":"data","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[],"name":"owner","outputs":[{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[],"name":"renounceOwnership","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"address","name":"newOwner","type":"address"}],"name":"transferOwnership","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"getChainlinkToken","outputs":[{"internalType":"address","name":"","type":"address"}],"stateMutability":"view","type":"function","constant":true},{"inputs":[{"internalType":"address","name":"_oracle","type":"address"},{"internalType":"bytes32","name":"_jobId","type":"bytes32"},{"internalType":"uint256","name":"_payment","type":"uint256"},{"internalType":"string","name":"_url","type":"string"},{"internalType":"string","name":"_path","type":"string"},{"internalType":"int256","name":"_times","type":"int256"}],"name":"createRequestTo","outputs":[{"internalType":"bytes32","name":"requestId","type":"bytes32"}],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"bytes32","name":"_requestId","type":"bytes32"},{"internalType":"uint256","name":"_data","type":"uint256"}],"name":"fulfill","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[],"name":"withdrawLink","outputs":[],"stateMutability":"nonpayable","type":"function"},{"inputs":[{"internalType":"bytes32","name":"_requestId","type":"bytes32"},{"internalType":"uint256","name":"_payment","type":"uint256"},{"internalType":"bytes4","name":"_callbackFunctionId","type":"bytes4"},{"internalType":"uint256","name":"_expiration","type":"uint256"}],"name":"cancelRequest","outputs":[],"stateMutability":"nonpayable","type":"function"}]';
    const ADDRESS = 'lat176tlwz75ykuv03yf4jp0cawqn2ulkw8kfdpe7z';
    const ORACLE_ADDRESS = 'lat1qj0xtgxp7frjyv52ss2lk6xvxsjuhkcft429cv';
    const JOBID = web3.utils.toHex('145a9cce137642689456396d09ac8cbc');
    const url =
    'https://min-api.cryptocompare.com/data/price?fsym=ETH&tsyms=USD,EUR,JPY'
    const path = 'USD'
    const times = 1;
    const payment = web3.utils.toVon('1')
    
    let nonce = web3.utils.numberToHex(await web3js.platon.getTransactionCount(FROM));
    let contract = new web3js.platon.Contract(JSON.parse(ABI), ADDRESS, null);
    let data = contract.methods["createRequestTo"].apply(contract.methods, [ORACLE_ADDRESS, JOBID, payment, url, path, times]).encodeABI();
    let tx = {
        from: FROM,
        to: ADDRESS,
        chainId: 210309,
        data: data,
        gas: "1000000", 
        nonce: nonce,
    };
    // sign
    let signTx = await web3js.platon.accounts.signTransaction(tx, PK);
    // send
    let receipt = await web3js.platon.sendSignedTransaction(signTx.rawTransaction);
    console.log("sign tx data:\n", signTx.rawTransaction, receipt)
}

request()
```

## Result

```plain
async function getFulfillData() {
    let contract = new web3js.platon.Contract(JSON.parse(ABI), ADDRESS, null);
    contract.methods.data().call(null, (error, result) => console.log(result));
}

getFulfillData()
```
Output is
```plain
0
```

Program works well, so we can say the migration of contracts is successfully. But the result is not what we expected, that's a problem.

After debugging and checking the code, I found that the function of capturing logs did not work. That is most likely the problem with go-ethereum.

Later, I will modify the node program to make it work on PlatON network, It's not an easy thing to do, but I think it's worth doing.

# Reference

1.[https://docs.chain.link/](https://docs.chain.link/)

2.[https://docs.chain.link/chainlink-nodes/](https://docs.chain.link/chainlink-nodes/)

3.[https://www.trufflesuite.com/docs/truffle/overview](https://www.trufflesuite.com/docs/truffle/overview)

4.[https://devdocs.platon.network/docs/en/Solidity_Dev_Manual](https://devdocs.platon.network/docs/en/Solidity_Dev_Manual)

5.[https://github.com/smartcontractkit/chainlink](https://github.com/smartcontractkit/chainlink)

6.[https://github.com/smartcontractkit/truffle-starter-kit](https://github.com/smartcontractkit/truffle-starter-kit)







