## Foundry

**Foundry is a blazing fast, portable and modular toolkit for Ethereum application development written in Rust.**

Foundry consists of:

- **Forge**: Ethereum testing framework (like Truffle, Hardhat and DappTools).
- **Cast**: Swiss army knife for interacting with EVM smart contracts, sending transactions and getting chain data.
- **Anvil**: Local Ethereum node, akin to Ganache, Hardhat Network.
- **Chisel**: Fast, utilitarian, and verbose solidity REPL.

## Documentation

https://book.getfoundry.sh/

## Usage

### Build

```shell
$ forge build
```

### Test

```shell
$ forge test
```

### Format

```shell
$ forge fmt
```

### Gas Snapshots

```shell
$ forge snapshot
```

### Anvil

```shell
$ anvil
```

### Deploy

```shell
$ forge script script/Counter.s.sol:CounterScript --rpc-url <your_rpc_url> --private-key <your_private_key>
```

### Cast

```shell
$ cast <subcommand>
```

### Help

```shell
$ forge --help
$ anvil --help
$ cast --help
```

# Proveably Random Raffle Contracts

## About

This code is create a proveably random smart contract lottery.

## What we want it to do?

1. Users can enter by paying for a ticket
   1. The ticket fees are going to go to the winner during the draw
2. After X period of time, the lottery will automatically draw a winner
   1.And this will be done programatically.
3. Using Chainlink VRF & Chainlink Automation
   1. Chainlink VRF -> Randomness
   2. Chainlink Automation -> Time based trigger

下载 VRFCoordinatorV2Interface 合约文件
forge install smartcontractkit/chainlink-brownie-contracts@0.6.1 --no-commit

## Tests!

1. Write some deploy scripts
2. Write our tests
   1. Work on a local chain
   2. Forked Testnet
   3. Forked Mainnet

安装 solmate 模块
forge install transmissions11/solmate --no-commit

测试 chainlink vrf subscription fund
forge script script/Interfactions.s.sol:FundSubscription --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY --broadcast

安装 foundry devops 获取最近部署的合约
forge install ChainAccelOrg/foundry-devops --no-commit

离成功又近了一点，离失败也近了一点

forge test 执行测试脚本

# TODO 需要查看一下怎么运行指定的测试脚本

将代码覆盖报告导入到名为 coverage.txt 的文件中
forge coverage --report debug > coverage.txt

两种测试模式：
1.forge test 它在 foundry 内置的 Anvil 区块链上进行测试（本地测试）
2.forge test --fork-url $SEPOLIA_RPC_URL 在测试网区块链上进行测试。

ERROR:
[FAIL. Reason: Error != expected error: 0x584327aa000000000000000000000000000000000000000000000000008e1bc9bf04000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000 != ]
解决方法：
之前，这个（）里面是空的，没有传 deployerKey,vm.startBroadcast();
下面是传了之后，报错解决。
vm.startBroadcast(deployerKey);
Raffle raffle = new Raffle(
entranceFee,
interval,
vrfCoordinator,
gaslane,
subscriptionId,
callbackGasLimit
);
vm.stopBroadcast();
===============================
Error: Compiler error (C:\Users\circleci\project\libsolidity\codegen\CompilerUtils.cpp:1434):Stack too deep. Try compiling with `--via-ir` (cli) or the equivalent `viaIR: true` (standard JSON) while enabling the optimizer. Otherwise, try removing local variables.
解决方法：
RaffleTest.t.sol 中的
(
entranceFee,
interval,
vrfCoordinator,
/_gasLane_/
/_subscriptionId_/
/_callbackGasLimit_/
/_link_/
/_deployerKey_/
,
,
,
,

        ) = helperConfig.activeNetworkConfig();

去掉了一些没有用到的参数，报错解决。我认为之后还是需要避免使用无用的参数，这样可以避免报错。

在合约代码中写的 console.log（测试代码块）要删除，避免 gas 浪费，最好每个文件中都不要有多余的 console.log

forge test --debug testRaffleRecordPlayerWhenTheyEnter -vv

git Submit Setup:
1.git status
2.git add . 将所有在 git status 中显示的文件夹或文件都添加进来
