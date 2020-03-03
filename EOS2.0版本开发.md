## EOS开发

## 概述

```
搭建以下环境均以ubuntu18.04为准
```

## 私链的搭建

### 1)下载EOSIO二进制文件

```
wget https://github.com/EOSIO/eos/releases/download/v2.0.0/eosio_2.0.0-1-ubuntu-18.04_amd64.deb
```

### 2)安装EOSIO二进制文件

```
sudo apt install ./eosio_2.0.0-1-ubuntu-18.04_amd64.deb
```

### 3)下载CDT二进制文件

```
wget https://github.com/EOSIO/eosio.cdt/releases/download/v1.6.3/eosio.cdt_1.6.3-1-ubuntu-18.04_amd64.deb
```

### 4) 安装CDT二进制文件

```
sudo apt install ./eosio.cdt_1.6.3-1-ubuntu-18.04_amd64.deb 
```

### 5)创建钱包

```
cleos wallet create --to-console
```

**PS:切记要记住钱包密码**

### 6)打开钱包

```
cleos wallet open
```

### 7)解锁钱包

```text
cleos wallet unlock
```

### 8)生成公私玥对

```
cleos create key --to-console 
```

**PS:私玥一定要记牢,公玥也需要记住**

### 9)导入EOSIO私玥

```
cleos wallet import --private-key 5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3
```

**PS:私链和测试网需要导入eosio系统私钥5KQwrPbwdL6PhXujxW37FSSQZ1JiwsST4cqQzDeyXtP79zkvFD3**

### 10)启动钱包节点

```
keosd &
```

**PS:如果报Failed to lock access to wallet directory; is another keosd running?**

**解决办法:**

````
cd eosio-wallet/
sudo rm -rf ./wallet.lock
cd ..
keosd &
````

### 11)启动nodeos节点

```
nodeos -e -p eosio \
--plugin eosio::producer_plugin \
--plugin eosio::producer_api_plugin \
--plugin eosio::chain_api_plugin \
--plugin eosio::http_plugin \
--plugin eosio::history_plugin \
--plugin eosio::history_api_plugin \
--filter-on="*" \
--access-control-allow-origin='*' \
--contracts-console \
--http-validate-host=false \
--verbose-http-errors >> nodeos.log 2>&1 &
```

### 12)检查nodeos节点是否产块

```
tail -f nodeos.log
```

### 13)检查钱包

```
cleos wallet list
```

**PS:请特别注意星号（*),这意味着钱包当前已解锁**

### 14)检查nodeos节点能否通信

```
sudo apt-get update
sudo apt install curl
curl http://localhost:8888/v1/chain/get_info
```

**PS:用于rpc api测试**

### 15)创建eos账户

```
cleos create account eosio bob EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
cleos create account eosio alice EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
```

### 16)获取账户公玥

```
cleos get account alice
```

### 17)创建合约目录

```
mkdir contracts
cd contracts
```

### 18)创建token合约

```
mkdir hello
cd hello
sudo apt-get update
sudo apt install git
git clone https://github.com/EOSIO/eosio.contracts --branch v1.7.0 --single-branch
```

### 19)创建部署合约的账户

```
cleos create account eosio eosio.token EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV
```

### 20)编译合约

```
cd eosio.contracts/contracts/eosio.token
eosio-cpp -I include -o eosio.token.wasm src/eosio.token.cpp --abigen
```

**PS:编译合约产生的文件:eosio.token.abi 和eosio.token.wasm**

**eosio.token.abi是一个json文件说明**

### 21)部署合约

```
cleos set contract eosio.token contracts/hello/eosio.contracts/contracts/eosio.token --abi eosio.token.abi -p eosio.token@active
```

### 22)创建token

```
cleos push action eosio.token create '[ "alice", "1000000000.0000 SYS"]' -p eosio.token@active
```

### 23)发行token

```
cleos push action eosio.token issue '[ "alice", "100.0000 SYS", "memo" ]' -p alice@active
```

### 24)实现token转账

```
cleos push action eosio.token transfer '[ "alice", "bob", "25.0000 SYS", "m" ]' -p alice@active
```

### 25)查询账户余额

```
cleos get currency balance eosio.token bob SYS
cleos get currency balance eosio.token alice SYS
```

