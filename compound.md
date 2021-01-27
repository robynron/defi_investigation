# compound

Compound是在Ethereum区块链上建立货币市场的协议。在传统金融中，货币市场是流动性强、期限短的金融工具的交易场所。货币市场主要是由大型机构和企业作为短期内借贷的一种手段。

在Compound金融协议的背景下，货币市场是由供需关系决定利率的 Token池，其利率是基于特定货币市场中Token的供求关系。Compound协议的货币市场还包含一个透明和公开的资产负债表，记录所有交易和历史利率。

用户可以存入稳定币来赚取利息，或者可以抵押代币来贷款。
## 目前支持的存款币种
WBTC、ETH、USDC、USDT、ZRX、BAT、COMP、DAI、REP、SAI、UNI

## compound存币
用户将代币存入compound合约，合约收集用户的的代币存入资金池，靠资金池来保证代币的流动性。对比p2p方式的优点是，用户可以随时将代币从资金池中取出。
### 存币流程
以usdc为例，用户将USDC存入compound合约，合约将铸造等额的cUSDC代币，返还给用户。当用户想赎回USDC代币的时候，将cUSDT传入compound合约进行销毁，compound合约也将吧用户存入的稳定代币及利息返还给用户
## compound借币
用户抵押的资产必须大于借款金额，借款的成本由不同市场的浮动价格决定
compound使用Price Oracle（价格预言机）跟踪借币户抵押的代币资产价值，如果用户提供的资产的总值除以其未偿还的借贷的值，降至抵押率以下，则会发生清算。
### 借币流程
以wBTC为例，用户将wBTC传入借款合约，合约将铸造cwBTC代币返回给借币方，同时提供稳定币给借币方。抵押的wBTC的价值必须大于借款的价值。当借币方偿还时，调用智能合约将借款的代币+利息存入compodund资金池，将cwBTC传入compound智能合约，compound合约将返还用户存入的wBTC代币（如果没有发生清算）。

## compound清算
如果用户提供的资产的总值除以其未偿还的借贷的值，降至抵押率以下，则该协议的其他用户将对该用户的帐户进行清算。如果发生清算，则清算人可以代表被清算的个人（也称为清算人）偿还部分或全部未偿还的借款。

这样的效果是使清算人的借贷余额重新回到抵押比率上。作为发起清算的回报，清算人获得了被称为“清算折扣”的折扣。此折扣代表用户启动清算时获得的百分比值。

## 借贷利率计算方法
compound将会根据供需关系决定利率的大小。需求低的时候借款利率就低，需求高的时候利率就高

利用率U的计算:
  ![](https://tva1.sinaimg.cn/large/008eGmZEly1gn2fxtjejij30eu01ujrk.jpg)
 
  a标识不同市场（我的理解是不同币种），cash是资金池中剩余的金额，borrow为借出金额

利息的计算：![](https://tva1.sinaimg.cn/large/008eGmZEly1gn2g0h1nc8j30ka02mwes.jpg)

## 技术实现
### cToken智能合约
    每个币种市场（上边提到的支持的）都通过ERC20构建了智能合约，用户的余额被标识为cToken的余额。用户可以想市场中提供token来兑换ctoken(存币)，也可以用ctoken兑换token。随着时间的推移cToken和基础资产的交换利率会增加，因为利息是由借款人产生的。
交换利率：![](https://tva1.sinaimg.cn/large/008eGmZEly1gn2gress6fj30m002e74q.jpg)

api:![](https://tva1.sinaimg.cn/large/008eGmZEly1gn2gxxoebaj31060sc462.jpg)

### 利率machine
compound货币市场根据供需关系定义一个利率，统一适用于所有借款人
每一个货币市场的每一个利率的历史都被一个利率指数记录下来，这个指数是在每次利率变化时计算出来的，这个利率变化是由用户铸造（存币）、赎回、借币、偿还或清算资产而产生的。
每次交易发生时，资产的利率指数都会从上一时期的利率指数更新为compound当前利率，计算该期间的利息，以r*t表示，计算每个block利率：![](https://tva1.sinaimg.cn/large/008eGmZEly1gn2hhrvcrnj30f601ymxg.jpg)

市场的未偿借款总额更新为包括自上一个指数以来的应计利息：![](https://tva1.sinaimg.cn/large/008eGmZEly1gn2hiui8hrj30og01ujrq.jpg)
应计利息的一部分作为准备金保留（预留），由

应计利息的一部分作为准备金保留（预留），由准备金系数确定，从0到1：![](https://tva1.sinaimg.cn/large/008eGmZEly1gn2hk88qwsj30tm020js0.jpg)
## 借款人数据结构
借款人的余额（包括应计利息）是当前利率指数除以上次检查用户余额时的利率指数的比率。
cToken中每个借款人地址的余额存储为checkpoint。checkpoint是一个solidity的二元组<uint256余额，uint256利息指数>。此元组描述上次对该帐户应用利息时的余额。
