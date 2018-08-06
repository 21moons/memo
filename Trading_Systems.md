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

首先, 如果交易者在勇气方面有些问题, 面对机会时没有足够的魄力及时行事.这种情况下交易系统并非一个完美的解决方案.正如 Larry Williams 所说, "trading systems work, system traders do not".对应一个有系统的交易者而言, 没有什么比忽略交易信号更大的恶行, Bill Eckhardt 写到：

*如果你做了一笔坏交易, 你还有资金管理, 你还有一大堆东西能够帮助你, 做一笔坏交易真的没有你想的这么严重.但是, 如果你错过了一笔好的交易, 不要找任何理由.如果你遇到了一笔注定盈利的交易, 而你却错过了它, 你注定会在这个游戏中完蛋.*

其次, 为了信任交易系统, 特别是在利润回撤抹去交易者对交易系统能力的信心时, 一定要做大量的研究和统计工作, 这并不是每个人都能够做到的.开发, 实施, 测试和评估一个交易系统并不是一个简单的事情.

最后, 影响自由交易的许多因素同样影响着系统性交易, 例如 缺乏足够的启动资金, 投资组合多样化的可能性, 全职, 24小时, 敬业.

更重要的是, 我们可以说做交易并不像在一个理性的企业工作, 你做出计划, 得出结论, 一切可以用逻辑的方式来解释. 恐惧和贪婪用一种大脑无法掌控的方式操纵价格. 当然有一些幸运交易者可以凭直觉地打败市场, 但在这些情况下, 他们并不能清晰的解释他们为什么买或卖. 如果这一切都是真的, 意味着你需要一个不理智和不合逻辑的工具来进入和退出市场, 这东西不能让你完全理解, 而且违反直觉. 通常情况下, 那些你认为是不合逻辑的, 或者只会导致失败的信号会让你成为赢家.

使用机械的交易系统意味着你需要放弃自己持有的关于金融的信念, 放弃所有让你"感觉良好"的交易方式：通常每个人买入下跌中的合约会感觉良好, 买入价格创新高的合约则会感觉不舒服, 但有很大概率是后者的方法更好. 测试交易系统, 意味着数字的野蛮力量将会强加于你, 让你感觉倍受约束. 成为一名完全机械的交易者意味着与自己搏斗. 这是赢利的唯一途径, 除非你是一个幸运的劫匪, 日复一日地赚钱, 但却不知道如何赚钱.

### 1.3 交易系统的相关知识

主观技术分析(subjective technical analysis)和客观技术分析(objective technical analysis)之间有很大的区别.

客观的技术分析方法是定义清晰的, 可重复的, 能够产生明确交易信号的过程.这使得它们可以用计算机程序来实现, 并且可以使用历史数据做回测. 同时, 回测产生的结果可以通过严格的评估来做定量分析.主观技术分析方法没有明确的分析过程.由于其天生的模糊性, 必须依赖分析师的私人解释.这种特性妨碍了电脑化, 回测和客观的性能评估.换句话说, 我们没有方法能确认或否定主观方法的功效.因为这个原因, 他们很难被证实是有效的.

因为其模糊性和缺乏科学的方法, 主观技术分析在学术圈和严肃的市场从业人员中并没有获得良好的声誉. 要成为一个图表或技术分析师, 而不是投资组合经理, 有一套个人的技术分析绝活可能是金融业中最好的跳板.关于为什么人们会相信奇怪的东西, 如教条, 信仰, 神话和轶事, 已经有很多的社会学和心理学的文献来解释, 同样这也是使用科学方法推导反而更容易错过好的科学技术分析的原因. 当然, 我并不是要在这里讲授关于科学哲学, 我只是想简要提醒读者什么是科学知识.科学知识是经验的, 是基于对现实的观察：

*技术分析的实质是统计推断.它试图从历史数据中归纳出模式, 规则等形式并进行概括, 然后推断他们的未来.*

因此, 技术分析利用统计推断工具, 从单个样本开始观察, 目的是评估整个人群的统计学属性. 通过这种方式, 技术分析是可以量化的, 就像统计学一样.此外, 技术分析和科学一样, 试图通过变量之间的函数关系来预测未来. 如果一个变量变了, 那么因变量会随之变化.技术分析中的规则对应统计学中的函数, 这里函数是有一定概率的.科学技术分析和统计学之间没有任何障碍, 那些不喜欢主观技术分析的人提出的疑惑突然消失了.定量技术分析借用了应用科学中的典型分析方法：由牛顿发起并由波普尔推广的假设 - 演绎方法.该假设演绎法有五个阶段：
1.观察.系统开发人员, 通过持续观察金融市场上的日内波动及日间波动, 设计变量之间的关系, 例如当天交易量和收盘价之间的关联, 或者指数和第二天开盘价之间的关联.
2.假设.假设来自系统开发人员的创新思维 - 一个智力火花, 其中的起源无法得知.系统开发人员必须清晰的知道他的假设不是来源于分析样本的特殊性, 而是从全部数据中推演出来, 对其中的大部分数据都是适用的.
3.预测.如果这种关系是真的, 那么可以依据假设来构建一个条件命题或一个预测, "如果这个假设确实是真实的, 预测将准确的告诉我们可以在新的集合中观察到什么".
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
"System Traders and Development Club" (STAD)

#### 编程目标

一个系统由一个入场公式, 一个离场公式和一个资金管理公式组成. 离场公式与 "风险管理" 有关, 即初始止损, 追踪止损, 目标退出, 通俗一点来讲就是我们承担多少风险以及在每一笔交易如何承担风险. "资金管理"关心的是我们在每笔交易中投资多少, 就是我们买或卖多少股票或期货合约. 系统交易的初学者不愿意相信的是, 只要积极地使用杠杆和资金管理技术, 回报就会是惊人的, 换句话来说, 如果没有合适的资金和风险管理工具, 即使交易系统优秀的令人屏息, 也不会成为可行的投资工具.

#### 交易时机

期货价格系列没有任何问题, 股票要考虑回购, 分红, 增发

### 2.2 测试

#### 市场数据的重要性

#### 回测时间长度

文献指出, 一个交易系统为了确保健壮性和一致性, 必须在多时段多市场测试(multi-period multi-market test)中达到一定的成功率.
一些机械交易者(mechanical traders)声称, 支持多市场的交易规则是不存在的, 因为交易系统有自己的个性, 交易系统只能适合部分而非全部市场.如果多市场测试是指在许多不同的市场 (债券, 股票, 商品, 货币, 股票, 等等) 中表现的都一样好, 从这个角度来讲, 能够通过多市场测试的系统确实很少.我们与这些交易者看法一致 - 不过, 经过20年的不断尝试, 今天的我们称得上是屈指可数的真正多市场交易系统之一.

反过来说, 正因为多周期测试是令人厌恶的, 机械交易者才认为过去只能是过去(意思是对未来并没有指导意义)：市场是随着经济结构和社会的变化而不断变化的.为什么会认为市场是一成不变呢？很多使用日内交易系统的系统交易者的绝不会用12个月之前的数据做回测, 甚至有些只用3个月内的数据.

我们认为, 回测周期的长度由长期交易中形成的智慧决定.举个例子, 一支银行股的价格是波动的, 在2000年的泡沫期间, 这家银行突然与一家网上银行合并.那么在2001年, 使用之前的交易系统进行测试是不合适的, 因为股票的走势已经因为合并而改变了.所以严肃的交易者会去掉非正常环境下的数据, 比如说 1999-2000 的股市泡沫, 或者是2008年的原油危机.每个系统在波动巨大的时候都是有效的(意思是单向趋势), 但是只有一个稳健的系统才能在正常情况下始终保持稳定工作.不正常的情况时有发生, 我们知道将会面对它们, 然而市场在80%的时间中都是正常的, 当它变得疯狂导致风险太高时, 一个好的系统能够感知到.

#### 规则复杂度和自由度

多市场测试的首要目标是检查一个系统是否按照预期的方式执行(如果手动测试, 系统发出的信号与程序员想要的信号位于同一位置), 并且当它们应用到市场上是否有利可图.我们不应该期望系统在我们测试的每个市场上都能盈利, 但能够盈利市场越多, 系统就越优秀.

测试可以对系统在统计学上的有效性进行初步验证, 而优化则是关注市场的特定行为特征, 并以此为依据对系统进行微调.尽管这只是一个不完全的定义, 但它有助于澄清为什么优化是在测试后进行 - 此时我们已经确认系统是可运行的.

通常的结果是, 一个系统在相似的合约上都是有利可图的; 换句话说, 例如, 在所有的能源期货上, 它们的表现都是一样的, 但在完全不同的债券市场上表现会差一些, 而在货币市场上则表现平平.

测试系统时最重要的是测试窗口大小的选择; 也就是说, 我们需要将系统应用到什么样的价格序列.这个决定并不遵循明确的时间表或经验法则, 而是需要遵守两个统计要求：价格系列必须足够长才能囊括不同的市场走势, 并随之产生大量的交易信号.

变量的数量和它们消耗的数据, 一般被认为也和整个数据样本相关, 这种相关关系我们称之为 **自由度** -- 也就是说, 变量和条件的数量和他们使用的数据不应超过整个数据样本的 10%. 避免一件事情是至关重要的, 那就是在我们拥有 500 个交易日数据的情况下, 为交易系统选取 500 个不同的条件.可能每个条件都不同于其余的499个, 而且只适合于那个特定的交易日, 这样每一天都有一个能赚到最多的钱的最优条件, 但是却对未来的市场没有任何预测能力(详见第5章).

对于那些没有数学思维的人, 规则的复杂性和自由度是一个难题.即使很多数学家也不能说清楚什么是自由度.在解释自由度时(通常表示为df), 也许最恰当和容易理解的解释就是那个关于已婚男人的笑话: "我只有一个妻子, 我的自由度是零.我应该通过观察其他女性来增加我的"样本量"."

严格来讲, 从统计到数学, 几何, 物理, 和力学, 自由度这个概念有很多定义. 在互联网上有一篇免费的论文尝试简单解释这个概念, 当然这是个困难的任务. 自由度的第一个定义(Larry Toothaker, 1986)可能是 "独立组件的数量减去估计参数的数量(the number of independent components minus the number of estimated parameters)". 这个定义源于Walker(1940)的定义："观察的次数减去这些观察之间必要关系的数量(The number of observations minus the number of necessary relations among these observations)". 但是如果从实践出发, 最好的解释是 Robert Schulle 博士(俄克拉荷马大学)的一个说法:

**在只有一个数据点的散点图(scatter plot)中, 你不可能对回归线(regression line)做任何估计. 这条线可以走向任何方向...此时你没有自由度(n-1 = 0, 其中 n = 1)用于估计(这可能会让你想起那个关于已婚男人的笑话). 为了绘制回归线, 你必须至少有两个数据点(一个妻子和一个情妇). 在这种情况下, 你有一个自由度用于估计 (n-1 = 1, 其中 n = 2). 换句话说, 自由度告诉你用于估计的有效数据数量. 但是, 如果只有两个数据点, 则可以将它们连成一条直线回归线, 并得到完美的相关性 (确定指数 = 1.00). 因此自由度越低, 评估效果越差.**

所以我们从直觉上就能得出结论, **样本规模越大, 使用的变量数量越少, 评估效果越好**. 罗伯特·帕尔多是当前唯一能在文献中把这事讲清楚的作者, 他在自己的书中给出了精辟的描述：

**自由度的计算 = 整个数据样本 - 规则和条件 - 规则和条件消耗的数据**

一般来说, 剩余自由度少于 90% 都被认为是太少了. 除了帕尔多的公式, 从实践的角度来看, 如果要在一个有 20 个变量的系统上进行适当的优化, 不能在仅有 6 个月日线数据的数据集上进行, 这一点非常重要. 变量的数量以及交易系统的条件集合, 与测试周期的长度密切相关. 换句话说, 一些度量需要更多的信息. 度量所需自由度的值, 是基于数据集中信息独立片段的数量. 信息越多, 度量越准确, 自由度越高.

至少剩下 90% 自由度的理念, 也可以反过来应用, 这就是表示用于系统计算的数据与测试窗口长度之间关系的 10 倍法则. 如果你使用 30 天移动平均收盘价格进行拟合, 测试时至少需要 300天 (30 x 10) 的数据.

让我们举个例子：我们现在有一个数据集, 包括三年内每个交易日的最高价格, 最低价格, 开盘价格和收盘价格, 这样共有(每年260个交易日)260 x 3 x 4 = 3120 个数据点. 我们假设有一个交易策略使用基于最高价格的 20 日平均线和基于最低价格的 60 日平均线.第一个均线使用 21 个自由度：20 个最高价格加上另外 1 个作为一条规则, 第二个均线使用 61 个自由度：60 个最低价格加上 1 个作为一条规则. 合起来总共是 82 个自由度. 以百分比计, 结果是 82/3120 = 2.6%, 剩下 97.4% 的自由度.

在计算中使用两次的数据点仅被计为一次, 如果你同时使用 5 日收盘价均线和 10 日收盘价均线, 对于后一个条件来说你将有 11 个自由度(10 数据 + 1 规则), 而对于第一个条件, 你将只有 1 个自由度(1规则). 总共消耗了 12 个数据. 很明显, 因为 5 日均线包含在 10 日均线中, 只有后者才与自由度的度量有关.

衡量系统是否可信需要的交易数量也与测试窗口的长度有关. 如果一个测试在产生多笔交易的过程中, 始终让错误的风险保持在最低水平, 这样的测试结果对于评估系统是否可信是非常重要的. 测试窗口的长度应该考虑到这个因素. 标准错误应该依据交易样本从交易系统的报告范围中添加或减去.
标准差的定义是:
标准差 = n + 1 的平方根
其中 n 是交易的数量

交易次数越多, 交易系统指标的可能误差就越小. 换句话说, 如果我们很少交易, 那么这些交易只是意外盈利的风险很高. 如果你一枪就射中了靶心, 要么你是一个神射手, 要么说你纯粹就是运气好. 相反, 如果你 100 次都射中靶心, 你就有很大概率是一名神射手.

一个系统要被认为是值得信赖的, 至少需要交易 100 笔, 这样其标准误差将是 100 + 1 的平方根, +10.04% 或者 -10.04% .

所有的交易系统指标都会在 +10% 和 -10% 的范围内变化.也就是说, 如果净利润是 100 美元, 那么实际净利润可能分布在从 90 美元到 110 美元的区间内.

### 2.3 交易系统的预测能力

#### 优化

优化在很多交易者中名声并不好. 甚至可以是对系统交易者的一种冒犯(系统交易者总是认为自己的系统已经足够好). 优化系统意味着在系统中找到那些能使利润最大化的系统变量, 或者能让交易者趋向主要目标的约束条件(例如, 系统更倾向于减少利润回吐, 而不是最大化利润). 让我们举一个例子：你有一个移动平均线交叉系统; 即系统在短期移动平均线穿过长期均线时买入. 针对这个系统的优化问题是短期均线是多短, 长期均线又是多长. 优化意味着"适合"一个系统; 也就是说让系统适应我们打算交易的市场[4].

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

<p align="center"><font size=2>Figure 2.1: A graphical description of a  "rolling" and  "anchored" walk forward analysis</font></p>

前向测试中产生的净值线是交易系统开发过程中最接近真实的地方, 因为这就是真正的交易将带给我们的. 显而易见的是, 同基于整个价值序列测试或优化交易系统生成的净值线相比, 这种前向分析生成的净值线将完全不同. <font color=#fd0209 size=4 ><b>注: 一个关注长期趋势, 一个追踪短期波动</b></font> 所以交易员在决定是否放弃一个交易系统时往往会欺骗自己, 依赖交易系统在整个价格序列上生成的净值线, 实际上这样的净值线压根就没有反映经过定期重优化后的真实交易情况<font color=#fd0209 size=4 ><b>注: 定期重优化拟合的是最近的波动, 对于全周期未必是适用的</b></font>.

一种被广泛接受的衡量系统预测能力及其一致性的方法, 是计算前向测试年化净利润与优化期间年化净利润的比率. 这就是前向有效率(walk forward efficiency ratio). 如果该比率高于 100%, 那么系统是高效的, 在实际交易中保持预测能力的可能性也比较高. 如果交易者决定使用前向有效率为 50% 的系统进行交易(许多交易者认为这个水平已经是最低了), 他们应该期望该系统的实际表现至少是优化测试结果的一半. 统计学证据还指出, 一些优化的不够好的系统, 也可能在前向测试的一两步中幸运的有良好表现. 为了规避这个陷阱, 应该执行尽可能多的执行前向测试, 或者至少让测试窗口(即我们在优化后的交易系统上应用的数据窗口)前进 10 步, 并且测试窗口涉及的数据至少占整个优化价格序列的 10% 至 20%.

讨论 "静态的基于数据集中部分数据的 '样本外' 测试" 或 "如何优化交易系统" 都已经过时, 因为大多数专业交易系统开发软件都集成了前向分析功能. 这并不意味着交易者不用熟悉常见的测试和优化过程. 我们建议在使用 WFA 之前, 你应该做关于优化的作业, 从而获得系统及其性能的完整视图. 要运行完整的前向分析耗时巨大, 为了提升速度, 我们可以通过先进行一轮测试, 再进行一轮优化来检查系统的稳健性. 无论如何, 为了简单起见, 我们将总结一些好的优化提示.

如果我们有许多输入需要优化, 最好的方法是每轮测试一到两个输入, 与此同时其他输入都保持不变.<font color=#fd0209 size=4 ><b>注: 数学思维</b></font> 这样能将过度优化的风险降到最低, 因为当所有的输入不在同一轮优化时, 不可能简单的找到一组输入能让我们给方程的约束最大化.(since it is impossible to find the batch of inputs that will maximise the constraint we gave to the equation simply because the inputs will not be optimised together in the same run.)

#### 健壮性

我们是否能从后优化窗口 (post-optimisation window) 中推断出, 系统是健壮的, 抑或是过度优化的产物? 我们不需要迷信最优执行输入 (the best performing inputs), 将其视为必胜的条件. 如果足够的飞镖投掷在板上, 必然会有高分组, 或者换句话说, 如果把一只猴子放在钢琴前, 只要有足够的时间, 最终它能弹出一首奏鸣曲. 这个笑话表明, 如果我们相信性能最好的输入 (the most performing inputs) 的存在, 至少结果的平均值应该是盈利的. 如果仅有 1% 到 5% 的结果是有利可图的, 这可能是偶然的: 如果系统的变量有足够大的输入区间, 这个系统最终能依靠历史数据发财. 一个稳健的系统不仅仅在 5% 的测试中表现良好, 而是在所有测试的平均值. 换句话说, 如果平均结果是盈利的, 那么我们可以假设交易系统是稳健的. 如果你更倾向于统计结果, 你也可以从平均净利润和支票中减去标准偏差(或其倍数), 然后检查在这种情况下平均净利润是否仍为正数.

因此, 输入, 条件和变量的数量必须可控并尽量减少至最小. 但多少个输入, 条件和变量算太多? 这是一个存在争议的领域, 其中唯一的标志是必须始终遵守我们在前一段中描述的数值条件的自由度数. 在考虑输入之前, 如果输入变化或者在优化下结果没有任何变化, 则快速粗略地检查是非常重要的. 如果不是, 请保持不变以增加自由度.

要考虑的另一点是每个输入(在时间序列上)的扫描范围. 举个例子将更清楚地说明这个问题: 如果要测试一个基于每日数据的移动平均线交叉系统, 它包括短期移动平均线和长期移动平均线, 你不能在下面两条均线上进行测试 -- 从 1 到 20 (这里指日线中的短期)的短期移动平均线和 20 到 200 (通常被认为是长期移动平均线对应的日线间隔)的长期移动平均线. 对于短期均线(20 日均线)来说, 扫描窗口从第 1 项数据移动到第 2 项数据的变化是 100%, 而从第 19 项数据移动到第 20 项数据的变化是 5%. 而对于长期均线(200 日均线)来说, 扫描窗口边界从第 199 项数据移动到第 200 项数据只有 0.5% 的变化. 你需要在两个区间上进行等幅扫描, 即以 2 的步长执行从 1 到 20 的扫描, 同时以 20 的步长执行从 20 到 200 的扫描.

优化完成后, 应该做出关键的决定: 我们应该选择哪些输入批次? 首先我们需要做的是创建一个函数图表, 显示变量输入扫描范围与净利润的关系(或者选择其他标准进行优化).

![same_level](https://raw.githubusercontent.com/21moons/memo/master/res/img/Trading_Systems/Figure_2.2.png)

<p align="center"><font size=2>Figure 2.2: In the middle of the chart as the variable varies the net profit stays almost at the same level.</font></p>

我们寻找的是理想情况下尽可能接近水平线的线, 在这条线上净利润不依赖于输入值. 当然, 实际情况与理论有很大不同, 所以我们应该满足于这样一条线, 它轻柔地上升, 在顶点维持一段时间然后下降. 顶部的水平区域正是我们想要的, 在这个区域中, 即使改变输入, 净利润几乎保持不变. 这是输入值表现出 **鲁棒性** 的区域. 与利润尖峰截然不同, 它是净利润相对较高的一个点, 但在它的周围净利润陡然下降. 换句话说, 我们需要找到一个甚至在改变输入值后净利润仍然能够保持稳定的区域.

![profit_spike](https://raw.githubusercontent.com/21moons/memo/master/res/img/Trading_Systems/Figure_2.3.png)

<p align="center"><font size=2>Figure 2.3: As much as the variable changes the net profit shows deep and wide swings: there is no area where at the variable’s changing net profit stays more or less stable.</font></p>

<p align="left"><font size=2>鲁棒性是指控制系统在一定(结构, 大小)的参数摄动下, 维持其它某些性能的特性.</font></p>

总而言之, 我们可以得出以下结论: 输入和结果间应该有一条逻辑路径, 会产生一些与输入批次相关的东西. 当输入和净利润不存在线性关系时, 或者不存在回退关系, 或者不存在任何作为优化主要规则的约束, 整套结果必须是存疑的.

### 2.4 交易系统的评估

评估交易系统看起来很容易. 谨慎的交易者最后必须做的事情是违反直觉的: 乍一看, 我们确实会说净利润越高系统越好. 不幸的是, 这种感觉与事实毫无关系. 我们将提出一些不基于净利润和纯数字的通用方法标准, 以便淘汰这种具有欺骗性的方法. 然后我们将介绍指标 RINA 指数, 该指数由 TradeStation 详细说明. RINA 指数在系统交易者中越来越常见, 我们认为它的分析比任何其他工具都要好.

#### What to look for in an indicator

净利润是系统在测试期间带回家的钱. 即使绝对数量可以取悦读者, 它并没有从根本说明系统的真实性能, 而且它也没有说明风险. 在没有量化风险的情况下谈论利润是系统分析中的致命错误. 此外, 如果您添加适当的佣金和 **滑点**, 权益线形状可能会发生变化, 直至向下倾斜或变为负数(暗指亏损).

<p align="left"><font size=2>滑点是交易的预期价格与交易执行价格之间的差异</font></p>

因此我们得到两个初始的一般考虑因素, 如下:

1. 应对多功能回报指标进行标准化, 以便在多个资产类别或多个交易系统之间轻松进行比较.
2. 谨慎的指标总是将回报与风险进行比较, 而净利润指标两者都不包含.

此外, **一个指标应该始终传达的观念是他希望度量的是什么, 对于交易系统来说这就是"一致".** 什么是一致性? 一致性的同义词可以是 "稳定性": **一致性衡量指标的稳定程度.** 让我们以净利润为例: 净利润本身并未说明什么时候获利. 年与年之间利润可能变化很大, 甚至是仅在某一年内产生利润, 在其他年份都是亏损. 您会信任哪一个系统, 一个年复一年赚钱的系统, 或是一个 10 年前赚钱然后每年都亏钱的系统? 最后的结论是后面的系统更好, 因为 10 年前赚取的利润如此巨大, 后面的损失相比之下是可以接受的; 但是谁会遵循这个系统继续交易呢? 没有人会采用这样一个交易系统, 因为很明显 10 年前的意外收获可能是一个异常值, 一个永远不会重复的异常事件.

因此我们可以得出结论: 一个一致的交易系统不仅包括利润和亏损的均匀分布, 而且还包括连续且均匀分布的输赢交易序列. 如果您逐年查看一致交易系统的统计数据, 您会发现所有指标几乎相同. 如果逐年或逐月都出现显著差异, 则交易系统是不一致的. 盘中系统也可以采用类似的方式, 通过每周表现来衡量.

#### Average trade

优秀交易系统的一个重要指标是 **平均交易(the average trade)**, 即净利润除以交易数量. 平均交易告诉我们每笔交易赚取或者亏损多少钱. 从绝对意义上来讲, 平均交易应该在弥补滑点和佣金后, 仍能为交易者留下一些利润. 按百分比计算, 平均交易在整个测试期间应保持一致. 交易者通常将绝对平均交易(the absolute average trade)与特定交易的入场价格进行比较, 以便以百分比表示. 其他交易者将名义平均交易价值(the
nominal average trade value)与给定时期内合约的名义价值(the nominal value)进行比较. 我们推荐绘制交易的历史平均百分比值, 以便清楚地了解多年来系统的盈利趋势.

#### Percentage of profitable trades

Usually the logic is that if you win a lot of times
the average winning trade/average losing trade ratio will be low, while if conversely your
percentage of profitable trades is low then the ratio will be high (inverse relationship). A
50% percent profitable trades number is a healthy one. If it grows significantly over 50%
(for example to 60% or 70%) be watchful because something could be wrong: to
counterbalance such a high percentage the average winning trade/average losing trade 
ratio should be particularly low, often even under the alarm level of 1. If you are using
target exit (let’s assume you exit 50% of the position at a price limit over the entry) it is
normal for the percentage of profitable trades to go over 60% and the average winning
trade/average losing trade to go under 2.

盈利交易百分比表示盈利交易数量与总交易数量的比值. 重要的不是它本身(一个趋势跟随系统的盈利交易百分比可能会比较低, 如 35%, 但它仍然是一个可行的系统), 只是因为它可以用来衡量系统如何平衡, 系统平衡与 "平均赢利交易/平均亏损交易"的比值相关. 通常的逻辑是, 如果你赢了很多次, "平均赢利交易/平均亏损交易" 比值会很低, 而如果相反, 你的盈利交易百分比很低, 那么比值就会较高(两者之间是反比关系). 50% 盈利交易百分比是健康的. 如果它显著超过 50%(例如 60% 或 70%), 请小心, 因为可能是某些地方出了问题: 为了抵消如此高的"平均赢利交易/平均亏损交易"比值, 平均获胜交易/平均亏损交易比率应该特别低，通常甚至在警报下如果您使用目标退出（让我们假设您以超过该条目的价格限制退出 50% 的头寸), 那么盈利交易的百分比超过 60% 且平均获胜交易/平均亏损是正常的交易低于2.

The percentage of profitable trades number is also important for calculating the
mathematical expectancy of a trading system. The percentage of profitable trades
multiplied by the average winning trade should be higher than the percentage of losing
trades multiplied by the average losing trade. Mathematical expectancy, in other words,
should be positive and the higher the better. You can use this measure of mathematical
expectancy to rank systems and pick up the best.

有利可图的交易数量的百分比对计算也很重要
交易系统的数学期望. 有利可图的交易比例
乘以平均获胜交易应高于失败的百分比
交易乘以平均亏损交易. 数学期望,换句话说,
应该是积极的,越高越好. 你可以使用这种数学方法
期望排名系统,并拿起最好的.

As far as the percentage of profitable trades is concerned there is a caveat that must be
regarded with careful attention: a 50% profitable trades ratio does not mean that a loss is
followed by a win or vice versa. It is a controversial area if a trade sequence suggests it
is possible to claim that a win follows a win or vice versa in some order. Even if the topic
is fascinating, a prudent trade should always fight against the worst and hope for the best
so that in our experience to trust on trade dependency is particularly risky since the
assumption is quite strong. Indeed you are assuming that there is a recurring order in the
trade sequence; that is, probabilities in the past show that after three wins in a row it was
more likely to have two losses instead of a fourth win.

就盈利交易的百分比而言, 必须有一个警告
认真对待: 50% 的盈利交易比例并不意味着亏损
其次是赢, 反之亦然. 如果贸易顺序表明这是一个有争议的领域
有可能声称赢得胜利, 反之亦然. 即使这个话题
令人着迷的是, 谨慎的交易应该始终与最坏的和最好的希望作斗争
因此,从我们的经验来看,对贸易依存度的信任是特别危险的
假设很强. 事实上, 你认为中国经常出现一个订单
交易顺序; 也就是说, 过去的概率表明, 在连续三次获胜后
更有可能出现两次失利, 而不是第四次胜利.

There are roulette players which really believe that the chance of getting a red number in
the next run increases if you just had a black one. And even worse, they believe that the
chance of getting a red number becomes bigger and bigger if a row of subsequent black
numbers occurs, for example 7 or 10 times in a row. From logical thinking and statistical
theory, however, you know that each run of the roulette in the casino is completely
independent from another. So it has no meaning, neither good nor bad, if in the run just
before there was a red number, a black one or the green one. Each occurrence of a number
is completely independent from the other ones. So betting on a colour in the next run
your chance is always the same: 18/37 = 48.6% (since there are 18 black and 18 red
numbers plus one green 0).

有轮盘球员真的相信有机会获得红色数字
如果你只有一个黑色, 下一次运行会增加. 更糟糕的是, 他们认为
如果随后一排黑色, 获得红色号码的机会变得越来越大
数字发生, 例如连续7次或10次. 从逻辑思维和统计
理论, 但是, 你知道赌场里的每一轮轮盘赌都是完全的
独立于另一个. 所以它没有意义, 不管好坏, 只要在运行中
之前有一个红色的数字, 一个黑色的或绿色的. 每次出现一个数字
与其他人完全独立. 所以在下一轮投注颜色
你的机会总是一样的：18/37 = 48.6%(因为有18黑和18红
数字加一个绿色0).

Although the financial markets, especially for beginners, sometimes look like a casino,
they are very different and more complex. In many cases they behave accidentally, with
many movements happening up and down and nobody knows why. There are however
some special situations which are created by human psychology of greed and fear when
the market behaves differently to accident. It is these movements where, in special trading 
systems in special situations, trade dependencies can occur [5]. But this is a sophisticated
topic that goes beyond of the scope of this book. In any case we recommend readers
approach this topic with extreme prudence.

虽然金融市场,特别是初学者,有时看起来像赌场,
他们非常不同,也更复杂. 在许多情况下,他们的行为意外,与
许多运动发生在上下,没有人知道为什么. 然而,有
一些特殊情况是由人类的心理创造的贪婪和恐惧时
市场表现与事故不同. 正是这些运动,在特殊交易中
在特殊情况下的系统,贸易依赖可能发生[5]. 但这是一个复杂的
这个主题超出了本书的范围. 无论如何,我们都建议读者
极端谨慎地处理这个话题.

#### Profit factor

Profit factor is a perfect indicator for comparing different systems or the same system
plotted over different markets. Of course this indicator is a ratio so it does not suffer from
the usual drawback of the other absolute number ratios. Profit factor is gross profit divided
by gross loss and basically reveals the size of gross profit in relation to gross loss. The
higher the better. Usually a healthy trading system has a profit factor of 2, an average
winning trade/average losing trade ratio of 2 and a percentage of profitable trades number
equal to 50%. But there are also good systems with a profit factor of between 1.5 and 3.
Think again about what you did during optmisation and your system’s design and
development when your profit factor goes over 3.

利润因素是比较不同系统或相同系统的完美指标
绘制在不同的市场上. 当然,这个指标是一个不受其影响的比率
其他绝对数字比率的通常缺点. 利润因素是毛利分配
以毛损计算,并基本揭示毛利的大小与毛损的关系.该
越高越好. 通常一个健康的交易系统的平均利润因子是2
获胜的交易/平均失败交易比率为2和一定比例的盈利交易数量
等于50%. 但也有好的系统,利润因子在1.5到3之间.
再想一想在你的系统设计和系统设计中你做了什么
当你的利润因素超过3时发展.

#### Drawdown

A broad definition of drawdown could be the largest loss or the largest losing streak of a
trading system, whichever is the biggest [4]. In a more graphical way we can depict
drawdown as the dip in the equity line between a highest high point and the successive
lower point before a new high is made. In other words  "total equity drawdown" is the
open trade profits and losses plus the already closed out equity on your account. But
drawdown has more subtle meanings according to whether we consider only open
positions or closed out positions. In fact it is important to distinguish between three
different types of drawdown:

缩小的广义定义可能是a的最大损失或最大连续亏损
交易系统,以最大者为准[4]. 我们可以用更加图形化的方式描述
作为在最高点和连续点之间的净值线下跌
创下新高之前的低点. 换句话说, "总股本缩减"是
开放的交易盈利和亏损以及您账户中已经结清的权益. 但
根据我们是否只考虑公开, 缩编有更微妙的含义
职位或封闭职位. 事实上, 区分三者很重要
不同类型的缩编:

1. An end trade drawdown tells us how much of the open profit we had to give back
before we were allowed to exit a specific trade
2. A close trade drawdown is the difference between the entry and the exit price
without taking into consideration what is going on within the trade
3. A start trade drawdown measures how much the trade went against us after the entry
and before it started to go our way [6].

For sake of simplicity we will use the close trade drawdown definition since it is the most
significant. But it is important to remember that while trading real money we can endure an
open trade drawdown much bigger than the theoretically calculated closed trade drawdown.

为了简单起见,我们将使用近距离交易缩减定义,因为它是最多的
重大. 但重要的是要记住,在交易真钱时我们可以忍受
开放的交易缩减比理论上计算的封闭式交易缩减大得多.

It is undeniable that the absolute value of a drawdown has a deep psychological impact
on the trader because he will be forced to deal with it and this could be painful. But with
drawdown, as with profit indicators, we need to be careful to use this measure in a
comparative way. For this purpose the concept of  "underwater equity line" is useful; that
is, the absolute drawdown value divided by the equity line’s previous highest high value.
This expresses the drawdown relative to the equity line in percentage terms. So even if
we are testing a system on a 40-year long price series in which the value of the contract
changed dramatically, we still have a percentage point reference value in order to spot
what is the real expected drawdown at current values: just plot the highest underwater
equity line percentage at the current market value.

不可否认,缩编的绝对价值有着深刻的心理影响
对交易者而言,因为他将被迫应付,这可能是痛苦的. 但是与
如同利润指标一样,我们需要小心使用这一措施
比较方式. 为此目的, "水下权益线"的概念是有用的; 那
是,绝对亏损值除以股票系列之前的最高价值.
这表示相对于股权的百分比下降. 所以即使
我们正在测试一个长达40年的合约价值系列
我们仍然有一个百分点的参考值,以便发现
目前的真实预期缩水量是多少：只绘制最高的水下
以当前市场价值计算的股权百分比.

Many analysts try to fight against drawdown by optimising the exits and the initial stops
while it would be more appropriate to further understand the reason why drawdown is
taking place. If there was a freak occurrence in historical terms on the markets then it is
obvious that an abnormal drawdown occurred. If nothing special occurred then there is
something wrong in the logic of the system.

许多分析师试图通过优化出口和初始止损来对抗缩编
而进一步理解缩编的原因则更为恰当
发生. 如果市场上出现历史上的怪事,那么它就是
很明显,发生了异常缩水. 如果没有什么特别的事情发生,
系统的逻辑中有些问题.

In order to evaluate which kind of drawdown we have to worry about, and which kind of
drawdown is normal, it is paramount to calculate the average drawdown and its standard
deviation. If the largest drawdown lies between one and two standard deviations from
the average than we should expect a future drawdown quite close to what we had in the
past. If the average drawdown is beyond two standard deviations from the mean than we
need to rethink the logic of the system, provided that nothing special happened in the
past that could justify the freak drawdown.

为了评估我们不得不担心哪种类型的缩编以及哪种缩编
缩编是正常的,计算平均缩减及其标准是最重要的
偏差. 如果最大的跌幅在1至2个标准差之间
平均数比我们预计未来的降幅非常接近我们目前的水平
过去. 如果平均跌幅超过平均值两个标准偏差,那么我们就是这样
需要重新思考系统的逻辑,只要没有什么特别的事情发生
过去那可以证明这个怪胎缩水.

The average tolerance of a drawdown for most professional traders and money managers
ranges from 20% up to 30%. Let’s say that a drawdown of 10% is a wonderful
accomplishment while a drawdown of 30% is much more painful and worrisome. A
drawdown of 40 to 50% would be unbearable for most market players.

大多数专业交易员和基金经理的平均容许跌幅
范围从 20% 到 30%. 假设 10% 的缩减是一个很好的例子
而30%的降幅更加痛苦和令人担忧. 一个
对于大多数市场参与者来说, 缩减 40% 到 50% 是无法忍受的.

#### Time averages

Other important indicators include the time averages. Average time in trades, according
to the TradeStation terminology, displays the average time (years, days, minutes) spent
in all completed trades, during the specified period for the strategy. Other trading
platforms have similar indicators. Average time in trades is vital for portfolio construction
since to exploit negative correlation among equity lines your systems need to be in the 
market at the same time or at least to have the same  "average time in trades". If you make
up a portfolio with a system that trades seldom and stays in the market for long periods
of time, and another that trades often but with short individual trades, you will never level
off the cumulative equity line.

其他重要指标包括时间平均值. 根据行业平均交易时间
到TradeStation术语,显示花费的平均时间(年,日,分)
在所有完成的交易中,在该策略的特定时期内. 其他交易
平台有类似的指标. 交易平均时间对投资组合建设至关重要
因为要利用您的系统所需的股票系列之间的负相关性
市场同时或至少具有相同的 "平均交易时间". 如果你做
建立一个投资组合体系,该体系很少进行交易并长期在市场上停留
的时间,而另一种经常交易但短暂的个人交易,你永远不会水平
关闭累积的权益线.

A trading system should be balanced between the profit and risk it produces on the long
side and on the short side. TradeStation split the system report between long and short
trades and except in particular cases, when you can find a logical reason for it, longs
should always keep pace with short in profit generation. A trading system which is not
balanced in between long and short must always be regarded with suspicion.

一个交易系统应该长期平衡它产生的利润和风险
侧面和短边. TradeStation 将系统报告分成多份和多份
交易,除特殊情况外,当你可以找到合理的理由时,多头头寸
应该始终跟上盈利的步伐. 一个不是的交易系统
总之,平衡在多空之间总是被怀疑.

#### RINA Index

A powerful indicator created by RINA Systems and included in the TradeStation trading
system report is the RINA Index, which represents the reward-risk ratio per one unit of
time and it compares the  "select net profit" (net profit minus the positive and negative
outlier trades, that is minus the abnormal trades that overcome the three standard deviation
limit away from the average) divided by the average drawdown and again divided by the
percentage of time in the market indicator (the latter is always taken from the TradeStation
system report). This indicator should always be over 30 and the higher the better.

RINA系统创建的强大指标,包含在TradeStation交易中
系统报告是RINA指数,它代表每单位的奖励 - 风险比率
时间和它比较 "选择净利润"(净利润减去正面和负面
异常交易,即减去克服三个标准偏差的异常交易
远离平均水平)除以平均跌幅,再除以平均跌幅
市场指标中的时间百分比(后者始终取自TradeStation
系统报告). 这个指标应该总是在30以上,越高越好.

In order to measure consistency in results the TradeStation system report automatically
calculates the three standard variations positive and negative limit for the average trade
and for almost all the indicators. This gives an idea of how much the indicators could
naturally oscillate between these boundaries. Usually the coefficient of variation (standard
deviation divided by average) should never be higher than 250%.

为了衡量结果的一致性,TradeStation系统会自动报告
计算平均交易的三种标准差异正负限制
几乎所有的指标. 这给出了这些指标可以达到多少的想法
自然在这些边界之间摇摆. 通常变异系数(标准
偏差除以平均值)不应高于250%.

In this paragraph we have tried to figure out some practical guidelines for a system
developer without delving into much theory or philosophical considerations. Without
fretting about being considered sloppy system traders we dare to openly recognise that
after 20-years’ experience we can understand at the first glance whether a trading system
deserves to be considered with attention or not. The first aspect we check is the equity
line, which needs to be growing smoothly and without many deep drawdowns. Personally
we also appreciate many  "flat times", that is parts of the equity line that are horizontal:
it means that no trading was done in that period since a filter took the system out of the
market. We believe that there is no need to trade continuously and a good system should
know when there is some edge to be exploited over the markets and conversely when it 
is more appropriate to sit on the sidelines. Then, after the equity line, we immediately
check average trade, profit factor, percent profitable, average win/average loss and how
the monthly returns were distributed throughout the years. Just from these indicators a
proper judgment about the trading system can be drawn without much worry about being
on the wrong side.

在这一段中, 我们试图找出系统的一些实用指南
没有深入研究许多理论或哲学考量.没有
对于我们敢于公开承认的被认为是猖獗的系统交易者感到担忧
经过20年的经验, 我们可以乍一看地了解交易系统
值得注意与否. 我们检查的第一个方面是股权
线需要平稳增长并且没有太多深刻的消耗. 亲自
我们也欣赏许多 "平稳时代",这是水平股权的一部分：
这意味着在此期间没有进行交易,因为过滤器将该系统带出该系统
市场.我们认为,没有必要持续交易和建立一个良好的体系
知道什么时候在市场上有一定的优势,反之亦然
更偏向于观望.然后,在股权之后,我们立即
检查平均交易,利润因素,盈利百分比,平均赢/平均损失和如何
每年的回报是多年来分配的.只是从这些指标a
对于交易系统的正确判断可以不必担心
在错误的一面.

### 2.5 结论

So far we have covered the most important theoretical aspects of the trading systems’
optimisation and performance evaluation. It was a quick overview of the universe of
notions that this topic embraces, but we hope that this brevity will lead to a much more
powerful understanding.

到目前为止,我们已经涵盖了交易系统最重要的理论方面,
优化和性能评估. 这是一个关于宇宙的简要概述
这个主题所包含的概念,但我们希望这个简洁会带来更多
有力的理解.

We can assert in conclusion that trading systems are a scientific approach to trading where
nothing is left to discretion. It is not a certain business, obviously, but it is a business that
deals with probability and that allows the trader to trade the markets exploiting a statistical
edge.

我们可以断言,交易系统是一种科学的交易方式
没有任何事情可以自由裁量. 显然,这不是一项特定的业务,但它是一项业务
处理概率,并允许交易者利用统计数据交易市场
边缘.

You will be able to expand this knowledge, delving into the nuances of trading systems
evaluation and optimisation, by reading the texts we included in the bibliography at the
end of the book.

您将能够扩展这些知识,深入研究交易系统的细微差别
评估和优化,通过阅读我们包含在参考书目中的文本
这本书的结尾.

Unfortunately it is impossible to have a full grasp of the subject without dealing with the
practical application of trading systems. Trading systems’ development is not a theoretical
intellectual challenge, but a practical experimental approach to markets. Writing codes,
testing them and then optimising them is a process that allows the trader to acquire a
practical view of the markets that is sometimes much more important than theory.
Knowing how to follow this process will save much demanding work and it will help to
solve situations that otherwise will be out of reach for the average trader.

不幸的是,如果不处理这个问题, 就不可能全面掌握这个主题
交易系统的实际应用. 交易系统的发展不是一个理论
智力挑战, 而是一种对市场的实用实验方法. 编写代码, 测试它们然后优化它们是一个允许交易者获得一个交易的过程
市场的实际观点有时比理论更重要.
知道如何遵循这个过程将节省很多艰巨的工作, 这将有助于
解决平均交易者无法实现的情况.

In the following chapters you will have a practical view of what a systematic trader does
when they develop, evaluate and optimise a trading system. We believe that this is the
most important part of our work.

在接下来的章节中, 您将对系统交易员的实际操作有所了解
当他们开发, 评估和优化交易系统. 我们相信这是我们工作中最重要的部分.

# Part II: 交易系统开发&演进真实案例

## Chapter 3: How to develop a trading system step-by-step – using the example of the British pound/US dollar pair

* **Introduction**

The currency markets are attractive to all types of traders including individual day traders,
trading companies, financial and non-financial companies, banks and governments. They
trade 24 hours a day from Monday morning in New Zealand until Friday night in
America. Markets with strong movements like the British pound offer you all the
possibilities to develop any type of trading system from any different idea on any time
scale.

外汇市场对所有类型的交易者都很有吸引力, 包括个人日间交易者,
贸易公司, 金融和非金融公司, 银行和政府. 他们
从新西兰的星期一早晨到星期五晚上的一天 24 小时交易
美国. 像英镑一样强劲的市场会为您提供所有的支持
在任何时间从任何不同的想法开发任何类型的交易系统的可能性规模.

In this chapter we will not present the very best trading system, which promises the
highest profits. Instead our goal is to show you a trading system which is based on a
sound idea and improved for a high robustness. As an aid to understanding our concept
of trading system development you will find in the following pages a step-by-step
explanation of how a new trading system is developed and tested for stability.

在本章中, 我们不会介绍最好的交易系统,它承诺
最高的利润. 相反, 我们的目标是向您展示一个基于的交易系统
完善的想法和改进的高鲁棒性. 作为理解我们概念的帮助
交易系统开发的一部分,您将在后面的页面中逐步找到
解释如何开发新的交易系统并进行稳定性测试.

As an example we choose a trend-following system with a breakout component. We take
this system and show how you can improve it up to become a profitable trading system
in the following steps.

作为一个例子,我们选择一个具有突破组件的趋势跟踪系统. 我们采取
这个系统,并展示如何改进它成为一个有利可图的交易系统
在以下步骤中.

3.1 The entry logic and code. How to improve a normal moving average crossover
system with a breakout filter.

3.1 输入逻辑和代码. 如何改善正常移动平均交叉
带突破过滤器的系统.

3.2 Evaluation of the trading system without parameter optimisation and exits – the
importance of commissions and slippage.

3.2 没有参数优化和退出的交易系统评估 - 
佣金和滑点的重要性.

3.3 Variation of the input parameters: Optimisation and stability graphs.

3.3 输入参数的变化：优化和稳定性图.

3.4 Inserting an intraday time filter: the importance of time for short-term trading.

3.4 插入盘中时间过滤器：短期交易时间的重要性.

3.5 Determination of appropriate exits for your system by checking the development of
all the system’s trades. How John Sweeney’s Maximum Adverse and Maximum

3.5 通过检查你的系统的开发确定你的系统的适当出口
所有系统的交易. 约翰斯威尼的最大不利和最大值

Favourable Excursion can support you.

有利的游览可以支持你.

Let’s start with the description of the logic of the system.

我们首先描述系统的逻辑.

### 3.1 The birth of a trading system

As mentioned in the introduction there are lots of sources for developing your own pool
of trading systems. One of them is certainly the Strategy Trading and Development Club
(in short  "STAD") of Omega Research (TradeStation). As a starting point we take the
following entry logic, as explained in STAD, volume 13:

正如引言中所提到的, 开发自己的游泳池有很多来源
的交易系统. 其中之一肯定是战略交易和发展俱乐部
(简称"STAD") 的 Omega Research (TradeStation). 作为一个起点, 我们采取这一做法
按照 STAD 中的解释, 第 13 卷:

The Luxor system identifies set-ups for new trades by the crossing of two moving averages
– a fast one and a slow one. Of course, there are many types of moving averages; Luxor is
the first strategy in STAD Club to use triangular moving averages. The purpose of the
triangular moving average (TMA) is to increase the smoothing of the price data without
also increasing the lag time between prices and the indicator. TMAs begin with the
calculation of a simple arithmetic average of prices (the close is the price field most
commonly averaged). Then, the TMA indicator calculates a simple arithmetic average of
the first average.

卢克索系统通过穿越两条移动平均线来确定新交易的设置
- 一个快一个,一个慢一个. 当然,有多种移动平均线; 卢克索是
STAD俱乐部的第一个策略是使用三角形移动平均线. 的目的
三角移动平均线(TMA)是增加没有价格数据的平滑
也增加了价格和指标之间的滞后时间. TMA开始于
计算简单的算术平均价格(最接近的是价格领域
通常平均). 然后, TMA 指标计算一个简单的算术平均值
第一个平均水平.

So the key point in the description is the special type of moving average. When we tested
the trading system we found, however, that the type of moving average did not matter
and the best results were produced with a simple moving average instead of the more
complex triangular moving average! So you can forget how this triangular, complex
moving average works and stay with the normal ones. This confirms our observation
when developing trading systems that often the simplest things work best.

所以描述中的关键点是移动平均线的特殊类型. 当我们测试
我们发现的交易系统, 移动平均线的类型并不重要
最好的结果是用一个简单的移动平均数来产生的, 而不是更多
复杂的三角均线! 所以你可以忘记这个三角形, 复杂的
移动平均线运行并与正常线路保持一致. 这证实了我们的观察
在开发往往最简单的工作最好的交易系统时.

The main steps in system development are the following: to get ideas which fit the
personality of the traded market, to test them and to adapt them to your own needs. We
do this here for the LUXOR system. We only take the main idea of how to use the moving
averages and make some minor changes.

系统开发的主要步骤如下：获取适合自己的想法
交易市场的个性, 测试它们并使其适应您自己的需求. 我们
在这里为LUXOR系统做这个. 我们只考虑如何使用移动的主要想法
平均值和做一些小的改变.

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

