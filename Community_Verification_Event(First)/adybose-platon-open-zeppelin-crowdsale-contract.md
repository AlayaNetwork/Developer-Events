# Issue 43 - Open Zappelin Crowdsale smart contract migration verification


[Project Repo](https://github.com/adybose/platon-open-zeppelin-crowdsale-contract)

[Issue #43](https://github.com/AlayaNetwork/Developer-Events/issues/43)

The issue encountered was unsuccessful compilation using `platon-truffle` with the Solidity version `0.5.17` as available from the `platon-truffle compile --list` command.

The error was an `InternalCompilerError` whose source sould not be traced, as a result of which the error could not be fixed.
Check the trace below that was encountered after running `platon-truffle compile`:
```
Compiling your contracts...
===========================
> Compiling ./contracts/Migrations.sol
> Compiling ./contracts/access/Roles.sol
> Compiling ./contracts/access/roles/CapperRole.sol
> Compiling ./contracts/access/roles/MinterRole.sol
> Compiling ./contracts/access/roles/PauserRole.sol
> Compiling ./contracts/access/roles/SignerRole.sol
> Compiling ./contracts/access/roles/WhitelistAdminRole.sol
> Compiling ./contracts/access/roles/WhitelistedRole.sol
> Compiling ./contracts/crowdsale/Crowdsale.sol
> Compiling ./contracts/crowdsale/distribution/FinalizableCrowdsale.sol
> Compiling ./contracts/crowdsale/distribution/PostDeliveryCrowdsale.sol
> Compiling ./contracts/crowdsale/distribution/RefundableCrowdsale.sol
> Compiling ./contracts/crowdsale/distribution/RefundablePostDeliveryCrowdsale.sol
> Compiling ./contracts/crowdsale/emission/AllowanceCrowdsale.sol
> Compiling ./contracts/crowdsale/emission/MintedCrowdsale.sol
> Compiling ./contracts/crowdsale/price/IncreasingPriceCrowdsale.sol
> Compiling ./contracts/crowdsale/validation/CappedCrowdsale.sol
> Compiling ./contracts/crowdsale/validation/IndividuallyCappedCrowdsale.sol
> Compiling ./contracts/crowdsale/validation/PausableCrowdsale.sol
> Compiling ./contracts/crowdsale/validation/TimedCrowdsale.sol
> Compiling ./contracts/crowdsale/validation/WhitelistCrowdsale.sol
> Compiling ./contracts/cryptography/ECDSA.sol
> Compiling ./contracts/cryptography/MerkleProof.sol
> Compiling ./contracts/lifecycle/Pausable.sol
> Compiling ./contracts/math/Math.sol
> Compiling ./contracts/math/SafeMath.sol
> Compiling ./contracts/ownership/Ownable.sol
> Compiling ./contracts/ownership/Secondary.sol
> Compiling ./contracts/payment/PaymentSplitter.sol
> Compiling ./contracts/payment/PullPayment.sol
> Compiling ./contracts/payment/escrow/ConditionalEscrow.sol
> Compiling ./contracts/payment/escrow/Escrow.sol
> Compiling ./contracts/payment/escrow/RefundEscrow.sol
> Compiling ./contracts/token/ERC20/ERC20.sol
> Compiling ./contracts/token/ERC20/ERC20Burnable.sol
> Compiling ./contracts/token/ERC20/ERC20Capped.sol
> Compiling ./contracts/token/ERC20/ERC20Detailed.sol
> Compiling ./contracts/token/ERC20/ERC20Mintable.sol
> Compiling ./contracts/token/ERC20/ERC20Pausable.sol
> Compiling ./contracts/token/ERC20/IERC20.sol
> Compiling ./contracts/token/ERC20/SafeERC20.sol
> Compiling ./contracts/token/ERC20/TokenTimelock.sol
> Compiling ./contracts/utils/Address.sol
> Compiling ./contracts/utils/Arrays.sol
> Compiling ./contracts/utils/ReentrancyGuard.sol

Trace: error:  { Error: InternalCompilerError: Conversion from rational numbers to address types is not supported
    at CompileError.ExtendableError (/home/adib/.nvm/versions/node/v10.12.0/lib/node_modules/platon-truffle/build/webpack:/packages/error/index.js:10:1)
    at new CompileError (/home/adib/.nvm/versions/node/v10.12.0/lib/node_modules/platon-truffle/build/webpack:/packages/compile-solidity/compileerror.js:13:1)
    at run (/home/adib/.nvm/versions/node/v10.12.0/lib/node_modules/platon-truffle/build/webpack:/packages/compile-solidity/run.js:56:1)
  message:
   'InternalCompilerError: Conversion from rational numbers to address types is not supported\n\n\u001b[31mCompilation failed. See above.\u001b[39m',
  stack:
   'Error: InternalCompilerError: Conversion from rational numbers to address types is not supported\n    at CompileError.ExtendableError (/home/adib/.nvm/versions/node/v10.12.0/lib/node_modules/platon-truffle/build/webpack:/packages/error/index.js:10:1)\n    at new CompileError (/home/adib/.nvm/versions/node/v10.12.0/lib/node_modules/platon-truffle/build/webpack:/packages/compile-solidity/compileerror.js:13:1)\n    at run (/home/adib/.nvm/versions/node/v10.12.0/lib/node_modules/platon-truffle/build/webpack:/packages/compile-solidity/run.js:56:1)',
  name: 'CompileError' }
    at Object.compile (/home/adib/.nvm/versions/node/v10.12.0/lib/node_modules/platon-truffle/build/webpack:/packages/workflow-compile/legacy/index.js:82:1)
Error: CompileError: InternalCompilerError: Conversion from rational numbers to address types is not supported

Compilation failed. See above.
    at Object.compile (/home/adib/.nvm/versions/node/v10.12.0/lib/node_modules/platon-truffle/build/webpack:/packages/workflow-compile/legacy/index.js:84:1)
Truffle v1.0.0 (core: 1.0.0)
Node v10.12.0
```

The solution that worked was compiling the contracts using `truffle compile`. It should compile successfully as below:
```
Compiling your contracts...
===========================
> Compiling ./contracts/AdyboseCrowdsaleERC20.sol
> Compiling ./contracts/Migrations.sol
> Compiling ./contracts/access/Roles.sol
> Compiling ./contracts/access/roles/CapperRole.sol
> Compiling ./contracts/access/roles/MinterRole.sol
> Compiling ./contracts/access/roles/PauserRole.sol
> Compiling ./contracts/access/roles/SignerRole.sol
> Compiling ./contracts/access/roles/WhitelistAdminRole.sol
> Compiling ./contracts/access/roles/WhitelistedRole.sol
> Compiling ./contracts/crowdsale/Crowdsale.sol
> Compiling ./contracts/crowdsale/distribution/FinalizableCrowdsale.sol
> Compiling ./contracts/crowdsale/distribution/PostDeliveryCrowdsale.sol
> Compiling ./contracts/crowdsale/distribution/RefundableCrowdsale.sol
> Compiling ./contracts/crowdsale/distribution/RefundablePostDeliveryCrowdsale.sol
> Compiling ./contracts/crowdsale/emission/AllowanceCrowdsale.sol
> Compiling ./contracts/crowdsale/emission/MintedCrowdsale.sol
> Compiling ./contracts/crowdsale/price/IncreasingPriceCrowdsale.sol
> Compiling ./contracts/crowdsale/validation/CappedCrowdsale.sol
> Compiling ./contracts/crowdsale/validation/IndividuallyCappedCrowdsale.sol
> Compiling ./contracts/crowdsale/validation/PausableCrowdsale.sol
> Compiling ./contracts/crowdsale/validation/TimedCrowdsale.sol
> Compiling ./contracts/crowdsale/validation/WhitelistCrowdsale.sol
> Compiling ./contracts/cryptography/ECDSA.sol
> Compiling ./contracts/cryptography/MerkleProof.sol
> Compiling ./contracts/lifecycle/Pausable.sol
> Compiling ./contracts/math/Math.sol
> Compiling ./contracts/math/SafeMath.sol
> Compiling ./contracts/ownership/Ownable.sol
> Compiling ./contracts/ownership/Secondary.sol
> Compiling ./contracts/payment/PaymentSplitter.sol
> Compiling ./contracts/payment/PullPayment.sol
> Compiling ./contracts/payment/escrow/ConditionalEscrow.sol
> Compiling ./contracts/payment/escrow/Escrow.sol
> Compiling ./contracts/payment/escrow/RefundEscrow.sol
> Compiling ./contracts/token/ERC20/ERC20.sol
> Compiling ./contracts/token/ERC20/ERC20Burnable.sol
> Compiling ./contracts/token/ERC20/ERC20Capped.sol
> Compiling ./contracts/token/ERC20/ERC20Detailed.sol
> Compiling ./contracts/token/ERC20/ERC20Mintable.sol
> Compiling ./contracts/token/ERC20/ERC20Pausable.sol
> Compiling ./contracts/token/ERC20/IERC20.sol
> Compiling ./contracts/token/ERC20/SafeERC20.sol
> Compiling ./contracts/token/ERC20/TokenTimelock.sol
> Compiling ./contracts/utils/Address.sol
> Compiling ./contracts/utils/Arrays.sol
> Compiling ./contracts/utils/ReentrancyGuard.sol
> Compilation warnings encountered:

    project:/contracts/AdyboseCrowdsaleERC20.sol:23:9: Warning: Unused function parameter. Remove or comment out the variable name to silence this warning.
        uint initial_supply
        ^-----------------^
,project:/contracts/crowdsale/Crowdsale.sol:138:5: Warning: Function state mutability can be restricted to pure
    function _preValidatePurchase(address beneficiary, uint256 vonAmount) internal view {
    ^ (Relevant source part starts here and spans across multiple lines).

> Artifacts written to /home/adib/code/repos/platon-open-zeppelin-crowdsale-contract/build/contracts
> Compiled successfully using:
   - solc: 0.5.17+commit.d19bba13.Emscripten.clang

```
After successful compilation, the contracts were migrated on the PlatON Dev Testnet using `platon-truffle migrate`.

The result is:
```
Compiling your contracts...
===========================
> Everything is up to date, there is nothing to compile.



Starting migrations...
======================
> Network name:    'development'
> Network id:      1
> Block gas limit: 0x8fcf88


2_initial_crowdsale.js
======================

   Deploying 'AdyboseCrowdsaleERC20Detailed'
   -----------------------------------------
   > transaction hash:    0xd3550af2082469a1cfc2739a6c094c410a168960b983de5c35b37870dddefb0c
   > Blocks: 0            Seconds: 0
   > contract address:    lat1cyj748c3uwkw2ftdrn0hlyezu8kvdtvk6wmuav
   > block number:        5866090
   > block timestamp:     1636981986330
   > account:             lat13u5xqwvdzfpzhdfl8daksy66ssss9tp0fknchz
   > balance:             199.88292594
   > gas used:            962572
   > gas price:           20 gvon
   > value sent:          0 LAT
   > total cost:          0.01925144 LAT

My Crowdsale token Adybose Token, symbol ABT at: lat1cyj748c3uwkw2ftdrn0hlyezu8kvdtvk6wmuav

   Deploying 'Crowdsale'
   ---------------------
   > transaction hash:    0xa1bb46d7c96f3eaaf94be6b12faaeaab958380c84da0370b5de54ba855b468ac
   > Blocks: 4            Seconds: 4
   > contract address:    lat13lcqj5uh707nu90qark7xd837pgcha3uym58k9
   > block number:        5866095
   > block timestamp:     1636981991848
   > account:             lat13u5xqwvdzfpzhdfl8daksy66ssss9tp0fknchz
   > balance:             199.87263828
   > gas used:            514383
   > gas price:           20 gvon
   > value sent:          0 LAT
   > total cost:          0.01028766 LAT

My Crowdsale contract at: lat13lcqj5uh707nu90qark7xd837pgcha3uym58k9

   > Saving migration to chain.
   > Saving artifacts
   -------------------------------------
   > Total cost:           0.0295391 LAT


Summary
=======
> Total deployments:   2
> Final cost:          0.0295391 LAT

```
The transaction hash and contract address are mentioned below:

Transaction hash: [0xa1bb46d7c96f3eaaf94be6b12faaeaab958380c84da0370b5de54ba855b468ac](https://devnetscan.platon.network/trade-detail?txHash=0xa1bb46d7c96f3eaaf94be6b12faaeaab958380c84da0370b5de54ba855b468ac)

Crowdsale contract address: [lat13lcqj5uh707nu90qark7xd837pgcha3uym58k9](https://devnetscan.platon.network/contract-detail?address=lat13lcqj5uh707nu90qark7xd837pgcha3uym58k9)
