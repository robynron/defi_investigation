<center>StableSwap -稳定币流动性的有效机制</center>



# abstract

StableSwap提供了一种机制，以一种可以被称为“Uniswap with leverage”的方式创建稳定币的交叉市场。它是稳定币的完全自主做市商，价格波动非常小，同时也是流动性提供者的高效“法定储蓄账户”。



# Introduction

稳定币最近变得非常流行:托管USDC, USDT, BUSD，PAX, TrueUSD，以及去中心化的稳定币DAI。 然而，这些稳定币(特别是去中心化的银行)存在价格稳定和流动性的问题。这使得DeFi套利极其困难。例如，当MakerDAO将其费率降低到5.5%时，许多Compound的用户(当时利率为11%)更愿意留在那里，因为他们已经在DAI贷款，如若将DAI转换为USDC，费率不容小觑。

与此同时，许多DeFi用户愿意把他们的稳定币用于放贷，以赚取5%的年利率，因为这远远高于传统银行提供的利率。然而，他们也不会把钱给那些“承诺利润”的交易公司。

在这篇论文中，我介绍了StableSwap——稳定币的自动流动性提供者。在需求方面，它提供类似uniswap的自动交易所，价格滑移率很低(通常小100倍)。在供应方面,它提供了一个可存储多种稳定币的“储蓄账户”,根据仿真,倘若交易商从智能合约和交易所之间套利，根据以往的用户交易量与稳定币价格，这个账户可以为他们带来300%的年化率。而且这种模式不需要中间人负责交易，没有交易所，没有订单簿，没有人工造市商。



# How it works

## 方式一：资金池中货币总价值不变

如果资金池中有i种货币，则整个资金池中的货币价值会满足如下公式：[![ySNrmn.png](https://s3.ax1x.com/2021/01/28/ySNrmn.png)](https://imgchr.com/i/ySNrmn)

货币i的价格，可通过 −dxi/dxj 计算得到，在这个模型中，货币的价格恒定为1。但是，这种资金池模型不适用于货币价格震荡的市场，除非资金池中的货币价格能够不断更新调整。这可以通过价格预言来实现，但它有风险，而且不具备去中心化的特点。

## 方式二：资金池中货币数量乘积不变

像Uniswap, Bancor，Kyber，这类去中心化的交易所，需要适应震荡的市场行情。他们通过建立这样的一种资金池：向资金池中同时注入2种货币，且要满足其注入的货币数量符合如下公式：[![ySdUfK.png](https://s3.ax1x.com/2021/01/28/ySdUfK.png)](https://imgchr.com/i/ySdUfK)

此公式范化到任意多种种类的货币，根据每种货币在资金池中的数量占比，需满足如下公式：[![ySdjcF.png](https://s3.ax1x.com/2021/01/28/ySdjcF.png)](https://imgchr.com/i/ySdjcF)

上述的这两个公式，适用于像ETH这类的货币，对于稳定币则不适用。

而且这种模型，当某种货币价格下滑很大时，资金池中应有足够的资金储备，以保证资金池中货币的整体流动性。

上述模型同样不适用于稳定币，当向资金池注入DAI与USDC提供流动性时，收益甚微。

## 方式三：StableSwap

StableSwap提供了对与稳定币友好的数学模型。



## 数学模型对比

模型初始化：假设资金池中货币X与货币Y的理想数量为1。初始价格都为$5.0。

随着货币x，y的价格波动，其价格由曲线的切线与2坐标轴交点决定。

<center><a href="https://imgchr.com/i/yS0e2T"><img src="https://s3.ax1x.com/2021/01/28/yS0e2T.png" alt="yS0e2T.png" border="0" /></a></center>
<center>价格波动对比</center>

<center><a href="https://imgchr.com/i/yS05on"><img src="https://s3.ax1x.com/2021/01/28/yS05on.png" alt="yS05on.png" border="0" /></a></center>

<center>货币价格波动时，资金池中总价值对比</center>

## StableSwap不变量的构造

相比于数量乘积不变式与价值和不变式，我们需要寻找另一个在货币价格震荡不大时的不变式。这个不变式介于直线与双曲线之间。

资金池中货币价值和不变式：[![yS6Gid.png](https://s3.ax1x.com/2021/01/28/yS6Gid.png)](https://imgchr.com/i/yS6Gid)

资金池中货币数量乘积不变式：[![yS6yJs.png](https://s3.ax1x.com/2021/01/28/yS6yJs.png)](https://imgchr.com/i/yS6yJs)

当资金池中货币的价格相等时，常量D表示货币的总个数。

显然，对于稳定币而言，应具有一个小的曲率，一个较低的价格滑移。我们可以采用一个杠杆参数：χ。将价值不变式和乘积不变式组合得到如下不变式：

<center><a href="https://imgchr.com/i/yS4OdP"><img src="https://s3.ax1x.com/2021/01/28/yS4OdP.png" alt="yS4OdP.png" border="0" /></a></center>

当χ=0，则模型就为乘积不变式；当χ=∞，此模型就为价值和不变式；

上述模型并不支持当资金池中的货币总价值远远偏离于理想值的情况。

因此，我们把χ设置成一个动态可变量。当资金池动态平衡时，χ就是一个常数A。当资金池中出现巨大滑点时，χ就会下降为0。如下：[![yS4gq1.png](https://s3.ax1x.com/2021/01/28/yS4gq1.png)](https://imgchr.com/i/yS4gq1)

因此我们就得到了StableSwap不变式：

<center><a href="https://imgchr.com/i/yS4OdP"><img src="https://s3.ax1x.com/2021/01/28/yS4OdP.png" alt="yS4OdP.png" border="0" /></a></center>

当在资金池中充入货币后，我们都应该计算D，并在任何交易后，总保持此等式成立。



# 仿真与性能

假设提供3种稳定币(DAI、USDC和USTD)的流动性，并获取价格信息，对算法的性能进行评估和优化。

数据来源：Coinbase Pro (DAI/USDC)、Binance (USDC/USDT)和HitBtc (USDT/DAI)为期6个月(2019年5月至10月)。

模拟假设合约中的总流动价值为$30000。只有价格有巨大的变化时，交易才会进行。结果如下：

* 最佳的A（放大系数）=85；
* 每次交易最佳手续费：0.06%;
* 流动性提供商在最佳参数下的利润：312%年化率；



# 实现

Vyper实现了多稳定币合约。其只使用整数算法，在智能合约本身内迭代地得到了使用stableswap不变量的方程的解。



# 其他应用

除了稳定币的流动性，同样的方法也可以用于向有息资产(cDAI)提供流动性。

将这种方法应用到稳定币上可以提高去中心化(非托管)稳定币的可用性。