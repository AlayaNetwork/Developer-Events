# Overview

Chainlink is not just a contract, but also a work node, a management background and a client. This is true for all projects, with on-chain contracts, you also need a client developed using the SDK.

Since PlatON and Ethereum are both running EVM, contract migration is not a major issue. However, the non-contract part is very difficult to migrate due to the different SDK. The issues I encountered while migrating Chainlink included address incompatibilities, code inconsistencies, and code missing.

If the code of the project to be migrated needs to be changed, it will be very difficult to migrate because the Ethereum ecosystem project doesn't know much about PlatON's code, which can be detrimental to the introduction of the Ethereum ecosystem. If PlatON is to be fully compatible with Ethereum, then it should be in sync with Ethereum, at least in terms of node functionality and interfaces.

If this is done, the migration process for Ethereum projects will be very simple.

# Preparation

## Source Code

**Chainlink**

[https://github.com/smartcontractkit/chainlink](https://github.com/smartcontractkit/chainlink)

**truffle-starter-kit**

[https://github.com/smartcontractkit/truffle-starter-kit](https://github.com/smartcontractkit/truffle-starter-kit)

## Environment

**OS**

ubuntu 18.04

**Software**

truffle 5.4

node 12.20

go 1.17

metamask

platon devnetscan

**Chain**

platon dev network

## **Accounts**

Prepare a funded account for testing

# Migration

## Deploy Contracts

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
**Change Migration File**

Change 3_price_consumer_v3.js as below

```plain
const PriceConsumerV3 = artifacts.require('PriceConsumerV3')
const KOVAN_ETH_USD_PRICE_FEED = '0x9326BFA02ADD2366b30bacB125260Af641031331'

module.exports = async (deployer, network, [defaultAccount]) => {
    // Local (development) networks need their own deployment of the LINK
    // token and the Oracle contract

    // currently hardcoded for Kovan
    if (!network.startsWith('kovan')) {
        console.log("only for Kovan right now!")
    }
    else {
        let priceFeedAddress = KOVAN_ETH_USD_PRICE_FEED
        try {
            await deployer.deploy(PriceConsumerV3, KOVAN_ETH_USD_PRICE_FEED, { from: defaultAccount })
        } catch (err) {
            console.error(err)
        }
    }
}
```

**Change Configuration**

Configure truffle-config.js, add a network named platon

```plain
platon: {
      provider: () => {
        return new HDWalletProvider(mnemonic, url)
      },
      network_id: '1',
      skipDryRun: true
},
```

**Add Environment Variable**

Add a file named .env in the project root path

```plain
MNEMONIC="<PRIVATE_KEY>"
RPC_URL="http://35.247.155.162:6789"
```
<PRIVATE_KEY> is the private key of an funded account.
**Deploy Contract**

```plain
truffle migrate --network platon
```

Addresses of the contracts are

LinkToken

```plain
0xD42F9740853D5549f050a3E8A9A12ee353b43039
```
Oracle
```plain
0xF84b4D410A6CB732898aeefBc09b0839fBb2cc39
```
MyContract
```plain
0xf8fC3d4D59b3f0e38d6c5874Fe2594BC05E87eD9
```

You can see the address format is not PlatON's format. We can get PlatON format addresses on the PlatON Dev Scan.

The corresponding addresses are

LinkToken

```plain
lat16shewsy98425nuzs5052ngfwudfmgvpe9ygjav
```
Oracle
```plain
lat1lp956sg2djmn9zv2amaupxcg88am9npelyhqyw
```
MyContract
```plain
lat1lr7r6n2ek0cw8rtvtp60ufv5hsz7slke6nhq0k
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
platon.linkContractAddress = "0xD42F9740853D5549f050a3E8A9A12ee353b43039"
chainSpecificConfigDefaultSets[210309] = platon
```
Note that linkContractAddress is Ethereum format.
**Install And Run**

Refer [https://github.com/smartcontractkit/chainlink](https://github.com/smartcontractkit/chainlink).README.md

First, you must install go and postgres.

Then execute the command

```plain
make install
```

After chainlink is generated, configure the .env file

```plain
ETH_URL=ws://35.247.155.162:6790
DATABASE_URL=postgresql://postgres:postgres@127.0.0.1:5432/postgres?sslmode=disable
ETH_CHAIN_ID=210309
CHAINLINK_TLS_PORT=0
LOG_LEVEL=debug
SECURE_COOKIES=false
ALLOW_ORIGINS=*
```

Start chainlink in the same directory as .env file.

```plain
chainlink node start
```
Meanwhile, you are required to set node password and manager account, just follow the prompt.
# Fund

We can use metamask to finish the job.

## **Add Network**

We must first add PlatON dev network to metamask.

Click ***Add Network***, input data in these boxes.

![](https://github.com/hthuang996/platon-link-test/tree/main/imgs/1.png)



## Import Account

Import the private key of the prepared account

![](https://github.com/hthuang996/platon-link-test/tree/main/imgs/2.png)



## Add LINK Token

Click ***import tokens***, input the address of the LINK token contract

![](https://github.com/hthuang996/platon-link-test/tree/main/imgs/3.png)



You can see the LINK token like this

![](https://github.com/hthuang996/platon-link-test/tree/main/imgs/4.png)

## Fund MyContract Account

Transfer 100 LINK to Oracle account

![](https://github.com/hthuang996/platon-link-test/tree/main/imgs/5.png)


## Fund Node Address

Visit Chainlink Operator web, you may replace the IP with your service ip.

```plain
http://127.0.0.1:6688/
```
Node address is
```plain
0xf921958C4FBE420b927Bd979004d4be3B7e92EA9
```
PlatON format
```plain
lat1lysetrz0hepqhynmm9usqn2tuwm7jt4fq7gysv
```

Login, click setting button, you can find your node address here

![](https://github.com/hthuang996/platon-link-test/tree/main/imgs/8.png)

Transfer 10 LAT and 100 LINK to the address.

# Run

## Start Node

Execute the command in the same directory with .env

```plain
chainlink node start
```

## Create Job

Open Chainlink Operator, click ***Jobs*** -> ***New Job***

```plain
type = "directrequest"
schemaVersion = 1
name = "Get > Uint256"
contractAddress = "0xF84b4D410A6CB732898aeefBc09b0839fBb2cc39"
maxTaskDuration = "0s"
observationSource = """
    decode_log   [type="ethabidecodelog"
                  abi="OracleRequest(bytes32 indexed specId, address requester, bytes32 requestId, uint256 payment, address callbackAddr, bytes4 callbackFunctionId, uint256 cancelExpiration, uint256 dataVersion, bytes data)"
                  data="$(jobRun.logData)"
                  topics="$(jobRun.logTopics)"]

    decode_cbor  [type="cborparse" data="$(decode_log.data)"]
    fetch        [type="http" method=GET url="$(decode_cbor.url)"]
    parse        [type="jsonparse" path="$(decode_cbor.path)" data="$(fetch)"]
    multiply     [type="multiply" input="$(parse)" times="$(decode_cbor.times)"]
    encode_data  [type="ethabiencode" abi="(uint256 value)" data="{ \\"value\\": $(multiply) }"]
    encode_tx    [type="ethabiencode"
                  abi="fulfillOracleRequest(bytes32 requestId, uint256 payment, address callbackAddress, bytes4 callbackFunctionId, uint256 expiration, bytes32 data)"
                  data="{\\"requestId\\": $(decode_log.requestId), \\"payment\\": $(decode_log.payment), \\"callbackAddress\\": $(decode_log.callbackAddr), \\"callbackFunctionId\\": $(decode_log.callbackFunctionId), \\"expiration\\": $(decode_log.cancelExpiration), \\"data\\": $(encode_data)}"
                  ]
    submit_tx    [type="ethtx" to="0xF84b4D410A6CB732898aeefBc09b0839fBb2cc39" data="$(encode_tx)"]

    decode_log -> decode_cbor -> fetch -> parse -> multiply-> encode_data -> encode_tx -> submit_tx
"""

```
You should replace "0xF84b4D410A6CB732898aeefBc09b0839fBb2cc39" with the address of the Oracle contract you deployed.
Click Create ***Job*** -> ***Definition***, you can see that there is an extra field

```plain
externalJobID = "47be208d-ecb3-4a2a-a24c-9bfb2770aff7"
```
 This is the job ID.
## Set Permission

*From now on, we will use the PlatON SDK for validation, hence use the lat address.

After the node get the data you required, it will send a transaction to the Oracle contract, we must set permission for the node first.

```plain
async function authorize() {
    let nonce = web3.utils.numberToHex(await web3js.platon.getTransactionCount(FROM));
    let contract = new web3js.platon.Contract(JSON.parse(ORACLE_ABI), ORACLE_ADDRESS, null);
    let data = contract.methods["setFulfillmentPermission"](NODE_ADDRESS, true).encodeABI();
    let tx = {
        from: FROM,
        to: ORACLE_ADDRESS,
        chainId: 210309,
        data: data,
        gas: "1000000", 
        nonce: nonce,
    };
    // sign
    let signTx = await web3js.platon.accounts.signTransaction(tx, PK);
    // send
    let receipt = await web3js.platon.sendSignedTransaction(signTx.rawTransaction);
    console.log("sign tx data:\n", signTx.rawTransaction, receipt)
}
```
Call *authorize* function, if successful, the node is permitted to send transaction to Oracle contract.
## **Request**

```plain
async function request() {
    let nonce = web3.utils.numberToHex(await web3js.platon.getTransactionCount(FROM));
    let contract = new web3js.platon.Contract(JSON.parse(MYCONTRACT_ABI), MYCONTRACT_ADDRESS, null);
    let data = contract.methods["createRequestTo"].apply(contract.methods, [ORACLE_ADDRESS, JOBID, payment, url, path, times]).encodeABI();
    let tx = {
        from: FROM,
        to: MYCONTRACT_ADDRESS,
        chainId: 210309,
        data: data,
        gas: "1000000", 
        nonce: nonce,
    };
    // sign
    let signTx = await web3js.platon.accounts.signTransaction(tx, PK);
    // send
    let receipt = await web3js.platon.sendSignedTransaction(signTx.rawTransaction);
    console.log("sign tx data:\n", signTx.rawTransaction, receipt)
}
```
Call *request* function, and make sure you send transaction successfully.
## Result

**Node Log**

![](https://github.com/hthuang996/platon-link-test/tree/main/imgs/6.png)

You can see the node received a new task, and dealed with it.

**Contract Status**

Visit PlatON Dev Scan, query the Oracle contract address.

You can see a transaction sent by node.

![](https://github.com/hthuang996/platon-link-test/tree/main/imgs/7.png)

## 
**Get **Data

```plain
async function getFulfillData() {
    let contract = new web3js.platon.Contract(JSON.parse(MYCONTRACT_ABI), MYCONTRACT_ADDRESS, null);
    contract.methods.data().call(null, (error, result) => console.log(result));
}
```
Output is
```plain
4095
```

This means that the contract has been able to retrieve data from the outside, and our migration has been successful.

# Conclusion

As you can see from the migration process, when migrating Chainlink, we used Ethereum tools entirely, except for the block browser, and only modified the target network for deployment. For verification, we used the PlatON SDK and successfully verified that the deployed contracts and tools worked properly on PlatON.

This migration process I think is optimal because for the Ethereum ecosystem, they don't need to pay much attention to PlatON's technical details. For PlatON developers, they can just use the tools provided by PlatON and use the portability of the project. All they need is a platon-formatted address.This migration process is the least cost and most efficient for both parties.

# Appendix

**Chainlink**

[https://github.com/hthuang996/chainlink](https://github.com/hthuang996/chainlink)

**truffle-starter-kit**

[https://github.com/hthuang996/truffle-starter-kit](https://github.com/hthuang996/truffle-starter-kit)

**platon-link-test**

[https://github.com/hthuang996/platon-link-test](https://github.com/hthuang996/platon-link-test)

# Reference

1.[https://docs.chain.link/](https://docs.chain.link/)

2.[https://docs.chain.link/chainlink-nodes/](https://docs.chain.link/chainlink-nodes/)

3.[https://www.trufflesuite.com/docs/truffle/overview](https://www.trufflesuite.com/docs/truffle/overview)

4.[https://devdocs.platon.network/docs/en/Solidity_Dev_Manual](https://devdocs.platon.network/docs/en/Solidity_Dev_Manual)

5.[https://github.com/smartcontractkit/chainlink](https://github.com/smartcontractkit/chainlink)

6.[https://github.com/smartcontractkit/truffle-starter-kit](https://github.com/smartcontractkit/truffle-starter-kit)

7.[https://forum.latticex.foundation/t/topic/5831](https://forum.latticex.foundation/t/topic/5831)





