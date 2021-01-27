> [视频地址](https://www.bilibili.com/video/av372447227)
>
> [视频地址1](https://www.bilibili.com/video/BV1ja4y1a7FQ)



# 传统中心化交易所

* 原理: 基于订单簿

[![szJAnx.png](https://s3.ax1x.com/2021/01/27/szJAnx.png)](https://imgchr.com/i/szJAnx)

* 做市商:(频繁调整挂单价格)![szJn4e.png](https://s3.ax1x.com/2021/01/27/szJn4e.png)

> 做市商这么做的目的: 自己赚钱, 提供货币流动性;
>
> 做市商的技术要求: 保证买单与卖单都被吃掉, 如果仅仅被吃掉一方, 则会亏钱;



# 早期去中心化交易所

* 基于订单薄
* 失败原因:
  * 以太坊交易又慢又贵;



# 近期DeFi项目

## uniswap商户做市

1. 向资金池充入代币;
2. 代币价格由系统计算得出;
3. 做市商利润: 系统向交易者收取的手续费;

## uniswap用户进行交易

[![szJYE8.png](https://s3.ax1x.com/2021/01/27/szJYE8.png)](https://imgchr.com/i/szJYE8)

* 机理:
  * 卖ETH越多, ETH数量会越多, ETH会越便宜;
  * 买ETH越多, ETH数量越少, ETH会越贵;
* 问题: 当单次交易的ETH数额较大, 获取另一种代币的数目就会有较大偏差, 较多次交易获取的另一种代币数目偏少;
* 滑点:
  * 定义: 
    * 初始usdt与ETH的兑换比率, 与交易兑换时的兑换比率不一致;
  * 单次交易数额越大, 滑点越严重; 
  * 资金池的大小;
  * 区块确认速度: 区块时间内净交易量越小, 滑点越小;
  * [![szJaCQ.png](https://s3.ax1x.com/2021/01/27/szJaCQ.png)](https://imgchr.com/i/szJaCQ)
* 手续费计算:
  * [![szJDuq.png](https://s3.ax1x.com/2021/01/27/szJDuq.png)](https://imgchr.com/i/szJDuq)
  * 采用固定手续费: 0.3%
  * 用户的实际代币数 = 用户的初始代币数 * (1 - 手续费)
  * DeFi获得的手续费仍然放在资金池中, 供做市商分;
  * 计算兑换另一种代币数目时, 不加入上述的手续费; (仍要满足)
* 优势:
  * 做市商不用提交手续费;
  * 资金池模式计算简单, 节省算力;
  * 无法挂限价单;