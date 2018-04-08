Trading Systems
A new approach to system development and
portfolio optimisation


# Part I: 交易系统开发&评估实践指导

## Chapter 1: 什么是交易系统?

交易系统就是一个自动定义入场点和出场点的, 不包含任何人为介入因素的规则集合.
因为交易规则并没有定义交易时间和交易地点, 这样使得交易系统是静态可测试的.
通过历史数据, 我们可以在一定程度上计算交易系统的可信度.
如果我们向已有规则中添加资金管理规则和投资组合规则, 我们就拥有了一个交易策略, 或者换句话说, 就是对市场进行完全自动化模拟.

我们提到的资金管理并不是我们通常认为的风险管理, 而是如何去止损和止盈.

### 1.1 简易交易系统示例

* 入场规则
买入2份最近20天内价格创最高的合约;
卖空2份最近20天内价格创新低的合约;
* 离场规则
如果 marketposition = 1, 那么在最接近 avgtruerange(14) 的价位卖出平仓;
如果 marketposition = -1, 那么在最接近 avgtruerange(14) 的价位买入平仓.

quantitative traders 量化交易者

### 1.2 为什么你需要交易系统

统计结果毫无疑问地表明只有百分之几的交易者能够年复一年地打败市场.大部分是私人和机构交易者迟早会破产.如果你不属于幸运的随意交易者, 那么为了生存, 唯一的选择就是使用交易系统.如果你购买了这本书, 你很可能并不是一个成功的自由交易者.按照我的经验, 成功的自主交易者拥有天生的直觉, 能够依靠本能预测市场动向.与之对应的是, 有许多成功的基金经理, 机构和私募交易者利用预设的交易策略和投资方法获利.如果有人认为交易系统可以很容易地战胜所有的交易中的阻碍, 那么这是对交易系统的一种误解.一方面交易系统可以帮助交易者击败市场, 另一方面它也会引入一些新的问题.

首先, 如果交易者在勇气方面有些问题, 面对机会时没有足够的魄力及时行事.这种情况下交易系统并非一个完美的解决方案.正如 Larry Williams 所说, "trading systems work, system traders do not”.对应一个有系统的交易者而言, 没有什么比忽略交易信号更大的恶行, Bill Eckhardt 写到：

*如果你做了一笔坏交易, 你还有资金管理, 你还有一大堆东西能够帮助你, 做一笔坏交易真的没有你想的这么严重.但是, 如果你错过了一笔好的交易, 不要找任何理由.如果你遇到了一笔注定盈利的交易, 而你却错过了它, 你注定会在这个游戏中完蛋.*

其次, 为了信任交易系统, 特别是在利润回撤抹去交易者对交易系统能力的信心时, 一定要做大量的研究和统计工作, 这并不是每个人都能够做到的.开发, 实施, 测试和评估一个交易系统并不是一个简单的事情.

最后, 影响自由交易的许多因素同样影响着系统性交易, 例如 缺乏足够的启动资金, 投资组合多样化的可能性, 全职, 24小时, 敬业.

更重要的是, 我们可以说做交易并不像在一个理性的企业工作, 你做出计划, 得出结论, 一切可以用逻辑的方式来解释. 恐惧和贪婪用一种大脑无法掌控的方式操纵价格. 当然有一些幸运交易者可以凭直觉地打败市场, 但在这些情况下, 他们并不能清晰的解释他们为什么买或卖. 如果这一切都是真的, 意味着你需要一个不理智和不合逻辑的工具来进入和退出市场, 这东西不能让你完全理解, 而且违反直觉. 通常情况下, 那些你认为是不合逻辑的, 或者只会导致失败的信号会让你成为赢家.

使用机械的交易系统意味着你需要放弃自己持有的关于金融的信念, 放弃所有让你"感觉良好”的交易方式：通常每个人买入下跌中的合约会感觉良好, 买入价格创新高的合约则会感觉不舒服, 但有很大概率是后者的方法更好. 测试交易系统, 意味着数字的野蛮力量将会强加于你, 让你感觉倍受约束. 成为一名完全机械的交易者意味着与自己搏斗. 这是赢利的唯一途径, 除非你是一个幸运的劫匪, 日复一日地赚钱, 但却不知道如何赚钱.

### 1.3 交易系统的相关知识

主观技术分析(subjective technical analysis)和客观技术分析(objective technical analysis)之间有很大的区别.

客观的技术分析方法是定义清晰的, 可重复的, 能够产生明确交易信号的过程.这使得它们可以用计算机程序来实现, 并且可以使用历史数据做回测. 同时, 回测产生的结果可以通过严格的评估来做定量分析.主观技术分析方法没有明确的分析过程.由于其天生的模糊性, 必须依赖分析师的私人解释.这种特性妨碍了电脑化, 回测和客观的性能评估.换句话说, 我们没有方法能确认或否定主观方法的功效.因为这个原因, 他们很难被证实是有效的.

因为其模糊性和缺乏科学的方法, 主观技术分析在学术圈和严肃的市场从业人员中并没有获得良好的声誉. 要成为一个图表或技术分析师, 而不是投资组合经理, 有一套个人的技术分析绝活可能是金融业中最好的跳板.关于为什么人们会相信奇怪的东西, 如教条, 信仰, 神话和轶事, 已经有很多的社会学和心理学的文献来解释, 同样这也是使用科学方法推导反而更容易错过好的科学技术分析的原因. 当然, 我并不是要在这里讲授关于科学哲学, 我只是想简要提醒读者什么是科学知识.科学知识是经验的, 是基于对现实的观察：

*技术分析的实质是统计推断.它试图从历史数据中归纳出模式, 规则等形式并进行概括, 然后推断他们的未来.*

因此, 技术分析利用统计推断工具, 从单个样本开始观察, 目的是评估整个人群的统计学属性. 通过这种方式, 技术分析是可以量化的, 就像统计学一样.此外, 技术分析和科学一样, 试图通过变量之间的函数关系来预测未来. 如果一个变量变了, 那么因变量会随之变化.技术分析中的规则对应统计学中的函数, 这里函数是有一定概率的.科学技术分析和统计学之间没有任何障碍, 那些不喜欢主观技术分析的人提出的疑惑突然消失了.定量技术分析借用了应用科学中的典型分析方法：由牛顿发起并由波普尔推广的假设 - 演绎方法.该假设演绎法有五个阶段：
1.观察.系统开发人员, 通过持续观察金融市场上的日内波动及日间波动, 设计变量之间的关系, 例如当天交易量和收盘价之间的关联, 或者指数和第二天开盘价之间的关联.
2.假设.假设来自系统开发人员的创新思维 - 一个智力火花, 其中的起源无法得知.系统开发人员必须清晰的知道他的假设不是来源于分析样本的特殊性, 而是从全部数据中推演出来, 对其中的大部分数据都是适用的.
3.预测.如果这种关系是真的, 那么可以依据假设来构建一个条件命题或一个预测, "如果这个假设确实是真实的, 预测将准确的告诉我们可以在新的集合中观察到什么”.
4.验证.系统开发人员验证预测在新集合中是否成立.
5.结论.系统开发人员, 通过使用统计推断工具(例如信任区间和假设测试), 将通过衡量实际观察结果与预测是否一致, 确认假设是否成立.
这个过程与化学或生物学等应用科学中用到的科学评估方法没有任何区别.

## Chapter 2: 设计, 测试, 优化和评估一个交易系统 

### 2.1 设计

交易系统从一个像创业愿景一样的想法开始. 创新是介于创意和幻想之间的一类东西, 但是想法能否成为现实则取决于你为它奉献了多少时间. 有些系统交易员说一个关于交易系统的好主意是在你最不期待的时候出现的, 但是与优秀交易者聊天很好会有些帮助, 强烈建议你参加研讨会, 大会和专业交易者之间的聚会.即使能够亲眼看到自由交易如何交易也是有用的.不幸的是, 创新并没有确定的道路, 但下面是
提示可以促使你走出想象的边界.

#### 开始

书单：
1.Traders www.traders-mag.co.uk
2.Active Trader www.activetradermag.com
3.Futures www.futuresmag.com
4.The Technical Analyst www.technicalanalyst.co.uk
5.Technical Analysis of Stocks & Commodities www.traders.com

TradeStation 论坛
"System Traders and Development Club” (STAD)

#### 编程目标

一个系统由一个入场公式, 一个离场公式和一个资金管理公式组成. 离场公式与 "风险管理” 有关, 即初始止损, 追踪止损, 目标退出, 通俗一点来讲就是我们承担多少风险以及在每一笔交易如何承担风险. "资金管理”关心的是我们在每笔交易中投资多少, 就是我们买或卖多少股票或期货合约. 系统交易的初学者不愿意相信的是, 只要积极地使用杠杆和资金管理技术, 回报就会是惊人的, 换句话来说, 如果没有合适的资金和风险管理工具, 即使交易系统优秀的令人屏息, 也不会成为可行的投资工具.

#### 交易时机

期货价格系列没有任何问题, 股票要考虑回购, 分红, 增发

### 2.2 测试

#### 市场数据的重要性

#### 回测时间长度

文献指出, 一个交易系统为了确保健壮性和一致性, 必须在多时段多市场测试(multi-period multi-market test)中达到一定的成功率.
一些机械交易者(mechanical traders)声称, 支持多市场的交易规则是不存在的, 因为交易系统有自己的个性, 交易系统只能适合部分而非全部市场.如果多市场测试是指在许多不同的市场 (债券, 股票, 商品, 货币, 股票, 等等) 中表现的都一样好, 从这个角度来讲, 能够通过多市场测试的系统确实很少.我们与这些交易者看法一致 - 不过, 经过20年的不断尝试, 今天的我们称得上是屈指可数的真正多市场交易系统之一.

反过来说, 正因为多周期测试是令人厌恶的, 机械交易者才认为过去只能是过去(意思是对未来并没有指导意义)：市场是随着经济结构和社会的变化而不断变化的.为什么会认为市场是一成不变呢？很多使用日内交易系统的系统交易者的绝不会用12个月之前的数据做回测, 甚至有些只用3个月内的数据.

我们认为, 回测周期的长度由长期交易中形成的智慧决定.举个例子, 一支银行股的价格是波动的, 在2000年的泡沫期间, 这家银行突然与一家网上银行合并.那么在2001年, 使用之前的交易系统进行测试是不合适的, 因为股票的走势已经因为合并而改变了.所以严肃的交易者会去掉非正常环境下的数据, 比如说 1999-2000 的股市泡沫, 或者是2008年的原油危机.每个系统在波动巨大的时候都是有效的(意思是单向趋势), 但是只有一个稳健的系统才能在正常情况下始终保持稳定工作.不正常的情况时有发生, 我们知道将会面对它们, 然而市场在80％的时间中都是正常的, 当它变得疯狂导致风险太高时, 一个好的系统能够感知到.

#### 规则复杂度和自由度

多市场测试的首要目标是检查一个系统是否按照预期的方式执行(如果手动测试, 系统发出的信号与程序员想要的信号位于同一位置), 并且当它们应用到市场上是否有利可图.我们不应该期望系统在我们测试的每个市场上都能盈利, 但能够盈利市场越多, 系统就越优秀.

测试可以对系统在统计学上的有效性进行初步验证, 而优化则是关注市场的特定行为特征, 并以此为依据对系统进行微调.尽管这只是一个不完全的定义, 但它有助于澄清为什么优化是在测试后进行 - 此时我们已经确认系统是可运行的.

通常的结果是, 一个系统在相似的合约上都是有利可图的; 换句话说, 例如, 在所有的能源期货上, 它们的表现都是一样的, 但在完全不同的债券市场上表现会差一些, 而在货币市场上则表现平平.

测试系统时最重要的是测试窗口大小的选择; 也就是说, 我们需要将系统应用到什么样的价格序列.这个决定并不遵循明确的时间表或经验法则, 而是需要遵守两个统计要求：价格系列必须足够长才能囊括不同的市场走势, 并随之产生大量的交易信号.

变量的数量和它们消耗的数据, 一般被认为也和整个数据样本相关, 这种相关关系我们称之为 "自由度"  - 也就是说, 变量和条件的数量和他们使用的数据不应超过整个数据样本的 10%. 避免一件事情是至关重要的, 那就是在我们拥有 500 个交易日数据的情况下, 为交易系统选取 500 个不同的条件.可能每个条件都不同于其余的499个, 而且只适合于那个特定的交易日, 这样每一天都有一个能赚到最多的钱的最优条件, 但是却对未来的市场没有任何预测能力(详见第5章).

对于那些没有数学思维的人, 规则的复杂性和自由度是一个难题.即使很多数学家也不能说清楚什么是自由度.在解释自由度时(通常表示为df), 也许最恰当和容易理解的解释就是那个关于已婚男人的笑话: "我只有一个妻子, 我的自由度是零.我应该通过观察其他女性来增加我的"样本量"."

严格来讲, 从统计到数学, 几何, 物理, 和力学, 自由度这个概念有很多定义. 在互联网上有一篇免费的论文尝试简单解释这个概念, 当然这是个困难的任务. 自由度的第一个定义(Larry Toothaker, 1986)可能是 "独立组件的数量减去估计参数的数量(the number of independent components minus the number of estimated parameters)”. 这个定义源于Walker(1940)的定义："观察的次数减去这些观察之间必要关系的数量(The number of observations minus the number of necessary relations among these observations)". 但是如果从实践出发, 最好的解释是 Robert Schulle 博士(俄克拉荷马大学)的一个说法:


在只有一个数据点的散点图(scatter plot)中, 你不可能对回归线(regression line)做任何估计. 这条线可以走向任何方向...此时你没有自由度(n-1 = 0, 其中 n = 1)用于估计(这可能会让你想起那个关于已婚男人的笑话). 为了绘制回归线, 你必须至少有两个数据点(一个妻子和一个情妇). 在这种情况下, 你有一个自由度用于估计 (n-1 = 1, 其中 n = 2). 换句话说, 自由度告诉你用于估计的有效数据数量. 但是, 如果只有两个数据点, 则可以将它们连成一条直线回归线, 并得到完美的相关性 (确定指数 = 1.00). 因此自由度越低, 评估效果越差.

所以我们从直觉上就能得出结论, 样本规模越大, 变量的数量越少, 评估效果越好. 罗伯特·帕尔多是当前唯一能在文献中把这事讲清楚的作者, 他在自己的书中给出了精辟的描述：

自由度的计算 = 整个数据样本 - 规则和条件 - 规则和条件消耗的数据

一般来说, 剩余自由度少于 90% 都被认为是太少了. 除了帕尔多的公式, 从实践的角度来看, 如果要在一个有 20 个变量的系统上进行适当的优化, 不能在仅有 6 个月日线数据的数据集上进行, 这一点非常重要. 变量的数量以及交易系统的条件集合, 与测试周期的长度密切相关. 换句话说, 一些度量需要更多的信息. 度量所需自由度的值, 是基于数据集中信息独立片段的数量. 信息越多, 度量越准确, 自由度越高.

至少剩下 90% 自由度的理念, 也可以反过来应用, 这就是表示用于系统计算的数据与测试窗口长度之间关系的 10 倍法则. 如果你使用 30 天移动平均收盘价格, 测试时至少需要 300天 (30 x 10) 的数据.

让我们举个例子：我们现在有一个数据集, 包括三年内每个交易日的最高价格, 最低价格, 开盘价格和收盘价格, 这样共有(每年260个交易日)260 x 3 x 4 = 3120 个数据点. 我们假设有一个交易策略使用基于最高价格的 20 日平均线和基于最低价格的 60 日平均线.第一个均线使用 21 个自由度：20 个最高价格加上另外 1 个作为一条规则, 第二个均线使用 61 个自由度：60 个最低价格加上 1 个作为一条规则. 合起来总共是 82 个自由度. 以百分比计, 结果是82/3120 = 2.6%, 剩下97.4% 的自由度.

Data points used twice in calculations are counted once so that if you are using a 5-day
moving average of the closes and a 10-day moving average of the closes you will have
for the latter condition 10 data + 1 rule while for the first condition you will have just 1
rule.The total is 12 data consumed.It is obvious that since the 5-day moving average is
included into the longer one only the latter will be relevant for the degrees of freedom
calculations.

在计算中使用两次的数据点仅被计为一次, 如果你同时使用收盘价 5 日均线和 10 日均线, 对于后一个条件来说你将有 11 个自由度(10数据 + 1规则), 而对于第一个条件, 你将只有 1 个自由度(1规则). 总共消耗了 12 个数据. 很显然, 因为 5 日均线包括在 10 日均线中, 只有后者才会参与自由度的度量.

A test is significant if it produces a number of trades that will
allow the risk of being wrong to be kept at the lowest level.The test window’s length
should take care of this.Let’s say that the obvious standard error should be added to or
subtracted from all the trading system’s report parameters according to the trade sample.
Standard error is:
Standard Error = square root of n + 1
Where n = number of the trades

衡量系统是否可信需要的交易数量也与测试窗口的长度有关. 如果一个测试在产生多笔交易的过程中, 始终让错误的风险保持在最低水平, 这样的测试结果对于评估系统是否可信是非常重要的. 测试窗口的长度应该考虑到这个因素. 标准错误应该依据交易样本从交易系统的报告范围中添加或减去.
标准误差是：
标准误差 = n + 1 的平方根
其中 n = 交易的数量

The higher the number of trades, the lower the possible error in the trading system’s
metrics.In other words if we have few trades, the risk that these trades are profitable by
accident is high.If you shoot once and you hit the bull’s-eye it is possible either that you
are a good marksman or simply that you are lucky.Conversely if you shoot 100 times
and you hit the mark every time the probabilities that you are a good marksman are higher.

交易笔数越多, 交易系统度量时可能出现的错误就越低. 换句话说, 如果我们很少交易, 那些看上去交易有利可图的交易带来的风险越高. 如果你一次就射中了靶心, 要么你是一个神射手, 要么说你纯粹就是运气好. 相反, 如果你 100 次都射中靶心, 你就有很大概率是一名神射手.

一个系统要被认为是值得信赖的, 至少需要交易 100 笔, 这样其标准误差将是 100 + 1 的平方根, +10.04% 或者 -10.04% .

所有的交易系统指标都会在 +10% 和 -10% 的范围内变化.也就是说, 如果净利润是100美元, 那么实际净利润可能分布在从 90 美元到 110 美元的区间内.

### 2.3 交易系统的预测能力

#### 优化

优化在很多交易者中名声并不好. 甚至可以是对系统交易者的一种冒犯(系统交易者总是认为自己的系统已经足够好). 优化系统意味着在系统中找到那些能使利润最大化的系统变量, 或者能让交易者趋向主要目标的约束条件(例如, 系统更倾向于减少利润回吐, 而不是最大化利润). 让我们举一个例子：你有一个移动平均线交叉系统; 即系统在短期移动平均线穿过长期均线时买入. 针对这个系统的优化问题是短期均线是多短, 长期均线又是多长. 优化意味着"适合”一个系统; 也就是说让系统适应我们打算交易的市场[4].

但是优化是一把双刃剑：从波动性, 初始风险, 回报等角度, 让系统来适应市场是一回事.但是, 寻找那些可能让你在过去赚得最多, 却对未来没有预测能力的输入则是另外一回事. 让我们假设有一个系统, 每天都会以最低的价格购买并在最高的价格出售, 系统具有精确的入场和出场价格, 使其能够达成利润最大的目标. 这是一种错误的优化, 因为我们寻找一个每天都在变化, 而只能在每天收市后才能确认的最好价格. 这个系统没有任何预测能力(泛化, 规则必须有普遍意义).

在交易系统的开发过程中避免优化是绝对不可能的. 只要想想每个交易者目前正在做什么, 你就会明白, 优化是我们不得不面对的东西. 有交易者拒绝投入优化过程, 因为根据他们的观点, 一个系统可以在相同输入的情况下永远工作. 然后他们决定在一批系统选中某个交易系统, 就因为这个系统在过去比其他系统赚了更多的钱. 这难道不是一种优化吗？又一次他们改变了原系统的代码, 在原系统上添加约束和条件, 依据市场价格行为对系统进行调整, 然后选择在历史数据中表现最好的系统调整：这不也是一种优化吗？ 如果你现在不是特别倾向于优化, 请回顾下你的立场, 并考虑下你有多少次不经意地使用了优化.

优化在系统交易中是有价值的, 我们需要区分正常的优化过程及过度优化, 称为曲线拟合或过度优化. 例如：我们交易债券期货的日间系统会受到货币政策的影响. 货币政策不会每天都变, 它必须与经济扩张-衰退周期匹配, 所以我们谈论的实际上是持续多年的事情.在这种情况下, 很明显我们需要将优化窗口设置为6,12或18个月 - 这是依据市场和货币政策来调整系统的合理时间长度.

如果系统产生了大量的交易, 我们将在系统启用后的前两年测试系统, 然后我们微调系统, 上线后则在每6,12或18个月重新优化一次. 这种方法针对的是真正的交易, 而不是对系统的理论评估. 当然, 为了尽快确认系统是否可行, 系统必须用我们现有的最长价格序列进行测试和优化. 但这个过程并不能帮助我们找出适当的参数来进行下一笔交易. 这只是一个评估过程, 帮助我们确定系统是否适合该特定市场; 那就是如果净值曲线正在增长(净值可能不会像我们希望的那样平滑增长, 但至少应该果断向上).

换句话说, 通过应用已有的最长价格序列进行测试, 可以帮助我们检查系统是否能够捕捉指定市场的动向, 而在优化过程中我们通过改变输入, 观察系统是否仍有改进的空间. 然后, 我们在6到12个月的周期上持续优化, 对系统进行微调, 从输入的角度, 考虑特定市场的特点, 使系统与市场保持同步变化.

对于日内系统来说, 所有的测试, 优化和重新优化周期都会比日间或周间系统要短.

#### 走势分析(Walk forward analysis)

总之, 为了同步系统与市场, 我们可以说, 就数据窗口而言, 优化是可变的. 在电脑计算在大多数市场参与者中普及之前, 优化后"样本外周期(out-of-sample period)"对于所有交易系统开发人员都是值得推荐的. "样本外周期" 是一个与优化过程无关的数据窗口(通常是整个优化数据窗口的 10% 到 20%), 它会被应用到优化后的交易系统上, 以便在未知的数据上验证系统的预测能力. 如果系统在 "样本外数据" 上的表现与在训练集上表现一致, 这意味着该系统是稳健的, 能够进行可信的交易.
 
**到目前为止, 我们讨论的关于交易系统的想法, 在任何流通的书籍中都可以找到. 但是这些关于优化的观点都过时了, 它们来自那些计算机没有被广泛使用的年代. 今天的优化已经发展成为一种更有效和更适当的测试方法, 可以让一个系统与长期价格序列匹配. 这个方法以 "前向分析(walk forward analysis)" 或 "前向测试(walk forward testing)" 的名义不断发展.**

前向测试是一种多轮的, 连续的样本外数据测试, 它不停的用样本外数据测试同一个数据序列. 让我们举个例子: 先用数据集中前两年的数据对系统进行优化, 然后用随后6个月的数据进行验证. 此时再将优化窗口前移6个月, 对系统进行新的优化, 再用未来6个月的数据进行验证, 就这样继续下去. 这种优化是一种 "滚动" 的前向分析, 因为每次我们重新优化时, 优化窗口总是前移6个月. 如果开始时间不变, 随着时间的推移增加优化窗口长度, 这种方式称为 "锚定" 前向分析. "滚动" 前向分析更适合于盘中交易系统, 因为盘中交易系统面对的是不断变化的市场条件.
<p align="left"><font color=#fd0209 size=4 ><b>注: 因为短期趋势不断变化, 不可捉摸, 所以减小分析的时间跨度, 保持对趋势的跟踪, 提升灵敏度.</b></font></p>

```
Rolling walk forward: out-of-sample (OOS) = 20%:
Run #1 |--------- In-sample 80% -------------- | OOS 20% |
Run #2                 |---------- In-sample 80% ------------ | OOS 20% |
Run #3                       |---------- In-sample 80% ---------------------| OOS 20% |
Anchored walk forward: out-of-sample (OOS) = 20%:

Run #1 |--------------In-sample 80% --------------- | OOS 20% |
Run #2 |----------------------------- In-sample 80% ---------------| OOS 20% |
Run #3 |-------------------------------------------- In-sample 80% --------------- | OOS 20% |
```
<p align="center"><font size=2>Figure 2.1: A graphical description of a “rolling” and “anchored” walk forward analysis</font></p>

前向测试中产生的净值线是交易系统开发过程中最接近真实的地方, 因为这就是真正的交易将带给我们的. 显而易见的是, 同基于整个价值序列测试或优化交易系统生成的净值线相比, 这种前向分析生成的净值线将完全不同. <font color=#fd0209 size=4 ><b>注: 一个关注长期趋势, 一个追踪短期波动</b></font> 所以交易员在决定是否放弃一个交易系统时往往会欺骗自己, 依赖交易系统在整个价格序列上生成的净值线, 实际上这样的净值线压根就没有反映经过定期重优化后的真实交易情况<font color=#fd0209 size=4 ><b>注: 定期重优化拟合的是最近的波动, 对于全周期未必是适用的</b></font>.

一种被广泛接受的衡量系统预测能力及其一致性的方法, 是计算前向测试年化净利润与优化期间年化净利润的比率. 这就是前向有效率(walk forward efficiency ratio). 如果该比率高于 100%, 那么系统是高效的, 在实际交易中保持预测能力的可能性也比较高. 如果交易者决定使用前向有效率为 50% 的系统进行交易(许多交易者认为这个水平已经是最低了), 他们应该期望该系统的实际表现至少是优化测试结果的一半. 统计学证据还指出, 一些优化的不够好的系统, 也可能在前向测试的一两步中幸运的有良好表现. 为了规避这个陷阱, 应该执行尽可能多的执行前向测试, 或者至少让测试窗口(即我们在优化后的交易系统上应用的数据窗口)前进 10 步, 并且测试窗口涉及的数据至少占整个优化价格序列的 10% 至 20%.

讨论 "静态的基于数据集中部分数据的 "样本外" 测试" 或 "如何优化交易系统" 都已经过时, 因为大多数专业交易系统开发软件都集成了前向分析功能. 这并不意味着交易者不用熟悉常见的测试和优化过程. 我们建议在使用 WFA 之前, 你应该做关于优化的作业, 从而获得系统及其性能的完整视图. 要运行完整的前向分析耗时巨大, 为了提升速度, 我们可以通过先进行一轮测试, 再进行一轮优化来检查系统的稳健性. 无论如何, 为了简单起见, 我们将总结一些好的优化提示.

如果我们有许多输入需要优化, 最好的方法是每轮测试一到两个输入, 与此同时其他输入都保持不变.<font color=#fd0209 size=4 ><b>注: 数学思维</b></font> 这样能将过度优化的风险降到最低, 因为当所有的输入不在同一轮优化时, 不可能简单的找到一组输入能让我们给方程的约束最大化.(since it is impossible to find the batch of inputs that will maximise the constraint we gave to the equation simply because the inputs will not be optimised together in the same run.)

#### 健壮性

But can we deduce from the post-optimisation window if the system is robust or whether
it is the product of over-optimisation? We do not need to trust the area of the best
performing inputs as a sure way to victory.If enough darts are thrown at the board, a
high-scoring grouping will occur or, put in another manner, if a monkey is put in front of
a piano and enough time is allotted, it will eventually compose a sonata.This joke
suggests that, at very least, the average of the results should be profitable if we want to
trust the most performing inputs.If just 1 to 5% of the results are profitable this could
have happened by accident: if the system’s variables are given wide enough input ranges
eventually the system will make a fortune over the past data.A robust system will show
post-optimisation positive performances not only in 5% of all the tests but on the average
of the tests.In other words, if the average results are positive then we can assume that
the trading system is a robust one.If you are more statistically inclined you can also
subtract the standard deviation (or a multiple of it) from the average net profit and check
if the average net profit remains positive in this case.

如果系统是健壮的, 我们可以从后优化窗口中推断出来, 是否
它是过度优化的产物?
但是, 如果系统是健壮的或者是否可以从后优化窗口推导出来
它是过度优化的产物? 我们不需要相信最好的地区
执行投入是一个肯定的胜利方式. 如果足够的飞镖投掷在板上, a
高分组会发生, 或者换句话说, 如果猴子被放在前面
一架钢琴和足够的时间分配, 它将最终构成一首奏鸣曲. 这个笑话
这表明至少, 如果我们愿意, 结果的平均值应该是有利可图的
相信性能最好的输入. 如果只有 1% 到 5% 的结果是有利可图的, 这可以
都是偶然发生的: 如果系统的变量有足够宽的输入范围
这个系统最终会为过去的数据发财. 一个强大的系统将显示出来
优化后的积极表现不仅在所有测试的 5% 中, 而且在平均水平上
的测试. 换句话说, 如果平均结果是肯定的, 那么我们可以假设
交易系统是一个强大的. 如果你更统计倾向, 你也可以
从平均净利润和支票中减去标准偏差(或其倍数)
如果在这种情况下平均净利润保持正值.

So the number of inputs, conditions and variables must be kept under control and reduced
to its minimum term.But how many inputs, conditions and variables are too many? This
is a controversial area where the unique hallmark is the number of degrees of freedom
that must always respect the numerical condition we depicted in the previous paragraph.
Before taking an input into consideration it is obviously important to check with a rapid
and cursory optimisation if the input varies or if it does not have any change under
optimisation.If not, keep it constant in order to increase the degrees of freedom.

因此, 投入, 条件和变量的数量必须控制和减少
到它的最小期限. 但有多少输入, 条件和变量太多? 这个
是一个有争议的领域, 其中独特的标志是自由度的数量
必须始终尊重我们在前面段落中描述的数字条件.
在考虑意见之前, 快速检查显然很重要
粗略优化如果输入变化或者它没有任何改变
优化. 如果不是, 保持它不变以增加自由度.

Another point to be considered is what scan range to choose for each input.An example
will give a clearer picture of this problem: if you want to test a moving average crossover
system with a short-term moving average and a long-term moving average on daily data,
you cannot test the short moving average from 1 to 20 (this is what is considered the
short term with daily data) and the long moving average from 20 to 200 (the latter is the
interval that is usually considered long term with daily data).Indeed a step from 1 to 2 is
a 100% change and a step from 19 to 20 is a 5% change.But a step change from 199 to
200 is just a 0.5 % change.You need to put the step scan range in an almost parallel
relationship so that the scan from 1 to 20 will be performed with a step of 2 and the scan
from 20 to 200 will be performed with a step of 20.

要考虑的另一点是每个输入的扫描范围. 例如
将给这个问题更清晰的描述: 如果你想测试移动平均交叉
系统在日常数据上具有短期移动平均线和长期移动平均线, 
你不能测试从 1 到 20 的短移动平均线(这就是所认为的
短期与日常数据)和长期均线从 20 至 200(后者是
间隔通常被认为是长时间的日常数据). 从 1 到 2 的步骤是
一个 100% 的变化, 从19到20的步骤是 5% 的变化, 但从 199 步骤变为 200 只是 0.5% 的变化. 您需要将步进扫描范围几乎平行放置
关系, 从1到20的扫描将以2步和扫描进行
从20到200将以20的步长执行.

After optimisation is done a critical decision should be taken: which inputs’ batch should
we choose? First of all what we need to do is create a function chart that puts the variable’s
inputs scan range in relation to the net profits (or whichever else criteria was chosen for
optimisation).

优化完成后, 应该做出关键的决定: 批次应该输入哪些输入
我们选择? 首先我们需要做的是创建一个放置变量的函数图表
输入与净利润相关的扫描范围(或其他选择的标准)优化).

What we are looking for is a line that ideally would be as close as possible to a horizontal
line, so that the net profit is not dependent on the input values.Reality is much different
from theory so that we should be content with a line that grows lightly, then tops for a
while and then decreases.The topping level is what we are looking for, that is an area
where, even when changing the inputs, the net profits stay almost constant.This is the
area where the robust input values are.This is diametrically opposite to a profit spike,
that is a point in the line where net profit is high but it decreases deeply in the surrounding
values.In other words we need to find an area where even after changing the input values
net profit stays stable.

我们正在寻找的是理想情况下尽可能接近水平的线
因此, 净利润不依赖于投入价值. 现实情况差别很大
从理论上讲, 我们应该满足于一条轻微增长的路线, 然后才能达到一条
然后降低. 顶级水平是我们正在寻找的, 这是一个领域
即使在改变投入时, 净利润也几乎保持不变. 这是
输入值强大的区域. 这与利润上涨截然相反, 
这是净利润高的行中的一个点, 但在周围深度下降
换句话说, 我们需要在改变输入值之后找到一个区域
净利润保持稳定.

In summary we can state that there should be a logical path into the inputs’ results so that
something coherent in terms of inputs’ batch should arise.When there is not a linear
relationship with inputs and net profits, or drawdown, or whichever constraint you are
putting as a primary rule of the optimisation, the whole set of results must be regarded as
suspicious.

总之, 我们可以说, 应该有一个逻辑的路径来输入结果
在输入批次方面应该出现一致性. 当不存在线性时
与投入和净利润之间的关系, 或者缩减, 或者无论你是什么限制
作为优化的主要规则, 整套结果必须被视为可疑.

### 2.4 交易系统的评估

Evaluating a trading system can look easier than it is in reality. In the end what a prudent
trader must do is something which is counterintuitive: at first glance we indeed would
say that the higher the net profit the better the system. Unfortunately nothing is further
from reality than this impression. We will put forward some general methodological
criteria not based on net profit and absolute numbers in order to weed out this deceptive
approach. Then we will introduce the indicator RINA index, which was elaborated by
TradeStation. RINA index is more and more common among system traders and we
believe that it comes closer to a good analysis than any other tool.

评估一个交易系统看起来比现实要容易. 到底什么是审慎
交易者必须做的是违反直觉的东西: 乍一看我们确实会这样做
说净利越高系统越好. 不幸的是没有进一步的
来自现实比这个印象. 我们将提出一些一般的方法论
标准不是基于净利润和绝对数量来消除这种欺骗性
做法. 接下来我们将介绍 RINA 指标, 该指标已经被详细阐述
TradeStation. RINA 指数在系统交易者和我们之间越来越普遍
相信它比任何其他工具都更接近良好的分析.

#### What to look for in an indicator

#### Average trade

#### Percentage of profitable trades

#### Profit factor

#### Drawdown

#### Time averages

#### RINA Index

###2.5 结论

# Part II: 交易系统开发&演进真实案例

## Chapter 3: How to develop a trading system step-by-step – using the example of the British pound/US dollar pair

* **Introduction**

The currency markets are attractive to all types of traders including individual day traders,
trading companies, financial and non-financial companies, banks and governments. They
trade 24 hours a day from Monday morning in New Zealand until Friday night in
America. Markets with strong movements like the British pound offer you all the
possibilities to develop any type of trading system from any different idea on any time
scale.

In this chapter we will not present the very best trading system, which promises the
highest profits. Instead our goal is to show you a trading system which is based on a
sound idea and improved for a high robustness. As an aid to understanding our concept
of trading system development you will find in the following pages a step-by-step
explanation of how a new trading system is developed and tested for stability.

As an example we choose a trend-following system with a breakout component. We take
this system and show how you can improve it up to become a profitable trading system
in the following steps.

3.1 The entry logic and code. How to improve a normal moving average crossover
system with a breakout filter.

3.2 Evaluation of the trading system without parameter optimisation and exits – the
importance of commissions and slippage.

3.3 Variation of the input parameters: Optimisation and stability graphs.

3.4 Inserting an intraday time filter: the importance of time for short-term trading.

3.5 Determination of appropriate exits for your system by checking the development of
all the system’s trades. How John Sweeney’s Maximum Adverse and Maximum

Favourable Excursion can support you.

Let’s start with the description of the logic of the system.

### 3.1 The birth of a trading system

#### The free LUXOR system code

#### The entry logic

### 3.2 First evaluation of the trading system

#### Calculation without slippage and commissions

#### Calculation after adding slippage and commissions

### 3.3 Variation of the input parameters: optimisation and stability diagrams

#### What does stability of a system’s input parameter mean? A short theoretical excursion

#### Dependency of main system figures on the two moving averages

#### Result with optimised input values

### 3.4 Inserting an intraday time filter

#### Finding the best entry time

#### Result with added time filter

### 3.5 Determination of appropriate exits – risk management

#### The concept of Maximum Adverse Excursion (MAE)

#### Inserting a risk stop loss

#### Adding a trailing stop

#### Looking for profit targets: Maximum Favourable Excursion(MFE)

#### Summary: Result of the entry logic with the three added exits

#### How exits are affected by money management

### 3.6 Summary: Step-by-step development of a trading system

## Chapter 4: Two methods for evaluating the system’s predictive power

## Chapter 5: The factors around your system

## Chapter 6: Periodic re-optimisation and walk forward analysis

## Chapter 7: Position sizing example, using the LUXOR system

# Part II: Systematic Portfolio Trading

## Chapter 8: Dynamic portfolio construction

### 8.1 Introduction to portfolio construction

### 8.2 Correlation among equity lines

### 8.3 A dynamic approach: equity line crossover

### 8.4 Dynamic portfolio composition: the walk forward analysis activator

### 8.5 Largest losing trade/largest losing streak/largest drawdown

