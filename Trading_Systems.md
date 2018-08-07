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

<p align="left" style="color:red;"><font size=5><b>注: 因为短期趋势不断变化, 不可捉摸, 所以减小分析的时间跨度, 保持对趋势的跟踪, 提升灵敏度.</b></font></p>

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

前向测试中产生的净值线是交易系统开发过程中最接近真实的地方, 因为这就是真正的交易将带给我们的. 显而易见的是, 同基于整个价值序列测试或优化交易系统生成的净值线相比, 这种前向分析生成的净值线将完全不同. <font color=#fd0209 size=5><b>注: 一个关注长期趋势, 一个追踪短期波动</b></font> 所以交易员在决定是否放弃一个交易系统时往往会欺骗自己, 依赖交易系统在整个价格序列上生成的净值线, 实际上这样的净值线压根就没有反映经过定期重优化后的真实交易情况<font color=#fd0209 size=5 ><b>注: 定期重优化拟合的是最近的波动, 对于全周期未必是适用的</b></font>.

一种被广泛接受的衡量系统预测能力及其一致性的方法, 是计算前向测试年化净利润与优化期间年化净利润的比率. 这就是前向有效率(walk forward efficiency ratio). 如果该比率高于 100%, 那么系统是高效的, 在实际交易中保持预测能力的可能性也比较高. 如果交易者决定使用前向有效率为 50% 的系统进行交易(许多交易者认为这个水平已经是最低了), 他们应该期望该系统的实际表现至少是优化测试结果的一半. 统计学证据还指出, 一些优化的不够好的系统, 也可能在前向测试的一两步中幸运的有良好表现. 为了规避这个陷阱, 应该执行尽可能多的执行前向测试, 或者至少让测试窗口(即我们在优化后的交易系统上应用的数据窗口)前进 10 步, 并且测试窗口涉及的数据至少占整个优化价格序列的 10% 至 20%.

讨论 "静态的基于数据集中部分数据的 '样本外' 测试" 或 "如何优化交易系统" 都已经过时, 因为大多数专业交易系统开发软件都集成了前向分析功能. 这并不意味着交易者不用熟悉常见的测试和优化过程. 我们建议在使用 WFA 之前, 你应该做关于优化的作业, 从而获得系统及其性能的完整视图. 要运行完整的前向分析耗时巨大, 为了提升速度, 我们可以通过先进行一轮测试, 再进行一轮优化来检查系统的稳健性. 无论如何, 为了简单起见, 我们将总结一些好的优化提示.

如果我们有许多输入需要优化, 最好的方法是每轮测试一到两个输入, 与此同时其他输入都保持不变.<font color=#fd0209 size=5><b>注: 数学思维</b></font> 这样能将过度优化的风险降到最低, 因为当所有的输入不在同一轮优化时, 不可能简单的找到一组输入能让我们给方程的约束最大化.(since it is impossible to find the batch of inputs that will maximise the constraint we gave to the equation simply because the inputs will not be optimised together in the same run.)

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

<p align="left" style="color:red;"><font size=5><b>注: 鲁棒性是指控制系统在一定(结构, 大小)的参数摄动下, 维持其它某些性能的特性.</b></font></p>

总而言之, 我们可以得出以下结论: 输入和结果间应该有一条逻辑路径, 会产生一些与输入批次相关的东西. 当输入和净利润不存在线性关系时, 或者不存在回退关系, 或者不存在任何作为优化主要规则的约束, 整套结果必须是存疑的.

### 2.4 交易系统的评估

评估交易系统看起来很容易. 谨慎的交易者最后必须做的事情是违反直觉的: 乍一看, 我们确实会说净利润越高系统越好. 不幸的是, 这种感觉与事实毫无关系. 我们将提出一些不基于净利润和纯数字的通用方法标准, 以便淘汰这种具有欺骗性的方法. 然后我们将介绍指标 RINA 指数, 该指数由 TradeStation 详细说明. RINA 指数在系统交易者中越来越常见, 我们认为它的分析比任何其他工具都要好.

#### What to look for in an indicator

净利润是系统在测试期间带回家的钱. 即使绝对数量可以取悦读者, 它并没有从根本说明系统的真实性能, 而且它也没有说明风险. 在没有量化风险的情况下谈论利润是系统分析中的致命错误. 此外, 如果您添加适当的佣金和 **滑点**, 权益线形状可能会发生变化, 直至向下倾斜或变为负数(暗指亏损).

<p align="left" style="color:red;"><font size=5><b>注: 滑点是交易的预期价格与交易执行价格之间的差异</b></font></p>

因此我们得到两个初始的一般考虑因素, 如下:

1. 应对多功能回报指标进行标准化, 以便在多个资产类别或多个交易系统之间轻松进行比较.
2. 谨慎的指标总是将回报与风险进行比较, 而净利润指标两者都不包含.

此外, **一个指标应该始终传达的观念是他希望度量的是什么, 对于交易系统来说这就是"一致".** 什么是一致性? 一致性的同义词可以是 "稳定性": **一致性衡量指标的稳定程度.** 让我们以净利润为例: 净利润本身并未说明什么时候获利. 年与年之间利润可能变化很大, 甚至是仅在某一年内产生利润, 在其他年份都是亏损. 您会信任哪一个系统, 一个年复一年赚钱的系统, 或是一个 10 年前赚钱然后每年都亏钱的系统? 最后的结论是后面的系统更好, 因为 10 年前赚取的利润如此巨大, 后面的损失相比之下是可以接受的; 但是谁会遵循这个系统继续交易呢? 没有人会采用这样一个交易系统, 因为很明显 10 年前的意外收获可能是一个异常值, 一个永远不会重复的异常事件.

因此我们可以得出结论: 一个一致的交易系统不仅包括利润和亏损的均匀分布, 而且还包括连续且均匀分布的输赢交易序列. 如果您逐年查看一致交易系统的统计数据, 您会发现所有指标几乎相同. 如果逐年或逐月都出现显著差异, 则交易系统是不一致的. 盘中系统也可以采用类似的方式, 通过每周表现来衡量.

#### Average trade

如何评判一个交易系统是否优秀, 关于这个问题有一个重要指标 -- **平均交易收益(the average trade)**, 即净利润除以交易数量. 平均交易告诉我们每笔交易赚取或者亏损多少钱. 从绝对意义上来讲, 平均交易在弥补滑点和佣金后, 应该仍能为交易者留下一些利润. 按百分比计算, 平均交易在整个测试期间应保持一致. 通常情况下, 交易者将绝对平均交易(the absolute average trade)与特定交易的入场价格进行比较, 并以百分比的形式表示. 其他交易者将名义平均交易值(the nominal average trade value)与给定时期内合约的名义价值(the nominal value)进行比较. 我们推荐绘制交易的历史平均百分比值, 以便清楚地了解多年来系统的盈利趋势.

#### Percentage of profitable trades(盈利交易百分比)

盈利交易百分比表示盈利交易数量与总交易数量的比值. 它本身并不重要(一个趋势跟随交易系统的盈利交易百分比可能会比较低, 如 35%, 但它仍然是一个可行的系统), 重要的是它可以用来衡量系统如何平衡, 而系统平衡与 "平均交易赢利/平均交易亏损"的比值相关. 通常情况下, 如果你赢了很多次, "平均交易赢利/平均交易亏损" 比值会很低(盈利平均分布在每笔交易上, 导致每笔交易的盈利变少), 相反, 如果你的盈利交易百分比很低, 那么比值就会较高(两者之间是反比关系). 50% 的盈利交易百分比是健康的. 但是如果它大大超过 50%(例如 60% 或 70%), 请务必小心, 因为这意味着某些地方出了问题: 为了抗衡如此高的百分比, "平均交易赢利/平均交易亏损"比值应该特别低, 甚至常常低于 1(警戒水平). 如果您使用目标点位退出(让我们假设你以止盈价格退出 50% 的头寸), 那么盈利交易百分比超过 60% 是一件很正常的事情, 同时 "平均交易赢利/平均交易亏损" 比值会低于 2.

盈利交易百分比对于计算交易系统的数学期望也很重要. 盈利交易的百分比乘以平均交易赢利应该高于亏损交易的百分比乘以平均交易亏损. 换句话说, 数学期望应该是正数, 并且越高越好. 您可以使用这种数学期望度量来对系统进行排名, 然后选出最好的系统.

就盈利交易的百分比而言, 必须谨慎注意一个警告: 50% 的盈利交易比绝不意味着亏损之后就是盈利, 反之亦然. 如果一个交易序列声称两次盈利/亏损的交易之间有着特定的排列, 这其实进入了一个存在争议的领域. 即使这个话题很吸引人, 谨慎的交易也应该始终与最坏的交易作斗争, 并期待最好的交易, 就我们的经验来说, 相信"交易之间存在依赖"特别危险, 因为这个假设非常强大. 实际上你是假设在交易序列中存在重复的排列; 也就是说, 过去的概率表明, 在连续三场胜利后, 更有可能有两场失利, 而不是第四场胜利.

轮盘赌玩家真的相信, 如果你这次得到了一个黑色的数字, 那么在下一轮中获得红色数字的机会就会增加. 更糟糕的是, 他们认为如果随后出现了一排黑色数字, 那么获得红色数字的机会会变得越来越大, 例如连续 7 次或 10 次. 然而, 从逻辑思维和统计理论来看, 赌场中轮盘赌的每一轮都是完全独立的. 上一轮是否有红色数字, 黑色数字或绿色数字, 其实是没有任何意义的, 既不意味着好也不预示着坏. 每次出现的数字都完全独立于其他数字. 因此, 在下一轮中投注, 正确的概率总是相同的：18/37 = 48.6%(因为有 18 个黑色和 18 个红色数字加上一个绿色 0).

虽然金融市场, 特别是对初学者来说, 有时看起来特别像赌场, 但它们实际上是非常不同的, 且金融市场会更复杂. 在许多情况下, 他们表现得很意外, 指数上蹿下跳并且没人知道原因. 然而, 人类的贪婪和恐惧心理会产生一些特殊情况, 这些情况与市场的偶然行为不同. 正是在这些走势中, 特殊情况下的特殊交易系统, 可能会发生交易依赖. 但这是一个超出本书范围的复杂主题. 无论如何, 我们建议读者以极其谨慎的态度对待这一主题.

#### Profit factor(利润系数)

利润系数是一个完美指标, 用来比较不同交易系统或不同市场上的相同交易系统. 当然, 该指标是一个比率, 因此它不会受其他绝对值比率缺点的影响. 利润系数是毛利润除以毛损, 基本上揭示了毛利与毛损相关的规模. 当然是越高越好. 通常, 健康的交易系统的利润系数为 2, 平均交易盈利/平均交易亏损的比率为 2, 盈利交易数量的百分比等于 50%. 但也有一些利润系数介于 1.5 和 3 之间的良好系统. 考虑一下, 当你的利润系数超过 3 时, 你将如何优化系统, 以及如何进行交易系统的设计和开发.

#### Drawdown(亏损)

亏损的广义定义是在交易系统中最大损失和最大的连跌中, 选取最大的那个. 从图形上来看, 我们可以将亏损描述为在权益线创出新高之前, 权益线最高点和连续低点之间的下跌. 换句话说, "总权益亏损" 是指您账户中的未平仓合约的损益加上已经平仓的权益. 只考虑未平仓头寸还是只考虑平仓头寸, 这两种条件下亏损具有更微妙的含义. 事实上, 区分三种不同类型的亏损非常重要:

1. 终止交易亏损(end trade drawdown)告诉我们在允许退出指定交易之前, 我们允许回撤多少浮盈(账面盈利).
2. 关闭交易亏损(close trade drawdown)是进场和出场价格之间的差异, 而不考虑交易中的情况.
3. 初始交易亏损(start trade drawdown)度量的是在入场后和开始出现浮盈之间, 逆向走势导致的亏损.

为简单起见, 我们将使用 "关闭交易亏损" 定义, 因为它是最重要的. 同时有一点也很重要, 在用真金白银交易时, 我们可以忍受比依据理论计算出的 "关闭交易亏损" 规模大得多的浮亏(open trade drawdown).

不可否认的是, 亏损的绝对值对交易者产生了深刻的心理影响, 因为他将被迫处理它, 这可能是让人痛心的. 但是亏损就像盈利指标一样, 我们需要谨慎地以比较的方式使用这一度量. 为了达到这个目的, "水下权益线" (underwater equity line)的概念出现了; **水下权益线即亏损绝对值除以权益线的前最高值.**  这表示相对于权益线的缩减百分比. 因此, 即使我们基于 40 年的长期价格序列中测试系统, 这期间合约价值发生了巨大变化, 我们仍然能够有一个百分点参考值, 能够发现基于当前价格的实际预期亏损: 只需要基于当前市场价值绘制出最高的 "水下权益线" 百分比.

许多分析师正试图通过优化退出规则和初始止损来对抗亏损, 实际上进一步了解亏损发生的原因会更合适. 如果市场上出现历史性的怪异事件, 那么显然会出现异常亏损. 如果没有什么特别的事情发生, 那么才会怀疑系统的逻辑出现了问题.

为了评估我们必须担心哪种亏损, 以及哪种亏损是正常的, 计算平均亏损及其标准偏差是至关重要的. 如果最大亏损介于平均值的一到两个标准差之间, 那么我们预计未来的亏损将与我们过去的水平接近. 如果平均亏损超过平均值的两个标准差, 那么我们需要重新思考系统的逻辑, 前提是确认过去没有发生任何导致异常亏损的特殊情况.

<p align="left" style="color:red;"><font size=5><b>注: 统计学思维</b></font></p>

大多数专业交易员和基金经理的能够忍受的亏损是 20% 至 30%. 10% 的亏损可以算是一个精彩的成就, 但是 30% 的亏损则让人痛苦和令人担忧. 对于大多数市场参与者来说, 40% 到 50% 的亏损是无法忍受的.

<p align="left" style="color:red;"><font size=5><b>注: 这是暗示交易中的浮亏无法避免?</b></font></p>

#### Time averages(平均时间)

其他重要指标包括时间平均值. 根据 TradeStation 术语, 交易的平均时间显示在策略的指定期间内完成的所有交易花费的平均时间(年, 日, 分钟). 其他交易平台也有类似指标. 平均交易时间对于投资组合构建至关重要, 因为利用权益线之间的负相关性(套利), 您的系统需要同时进入市场或者至少具有相同的 "平均交易时间". 如果你使用两个系统组成投资组合, 其中一个较少交易但是需要长时间保持在线, 而另一个交易频繁, 但是每笔交易交易时间较短(快进快出), 那么您的累积权益线永远不会平稳.

<p align="left" style="color:red;"><font size=5><b>注: 这是是说不同周期的叠加, 导致累积权益线频繁出现上升下降, 无法保持水平.</b></font></p>

交易系统通过长期交易和短期交易产生的利润和风险应该是平衡的. TradeStation 分拆了长期交易和短期交易的系统报告, 除非在特殊情况下, 并且当您可以找到合理的原因, 否则长期交易产生的利润应该始终能跟上短期交易产生的利润. 长期和短期不平衡的交易系统必须得到质疑.

#### RINA Index

RINA 指数是由 RINA Systems 创建并包含在 TradeStation 交易系统报告中的强大指标, 它代表单位时间内的回报风险比率, 这个指标实际上比较的是: "选择净利润(select net profit)" (净利润减去正负外部交易, 即减去超出平均值的三个标准差限制的异常交易) 除以平均亏损并再次除以市场指标中的时间百分比(the percentage of time, 总是取自 TradeStation 系统报告). 该指标应始终超过 30, 并且越高越好.

为了衡量结果的一致性, TradeStation 系统报告会针对平均交易收益(the average trade)和几乎所有指标自动计算三个标准差异正负极限. 这可以了解指标在这些边界之间自然振荡的程度. 通常, 变异系数(the coefficient of variation, 标准差除以平均值)不应高于 250%.

在本段中, 我们试图在不需要深入研究太多理论或哲学的前提下, 为系统开发人员整理出一些实用指南. 拥有 20 年经验的我们敢于公开承认, 我们可以一眼就看出交易系统是否值得被关注, 并且不担心任何人的质疑. 我们检查的第一个方面是权益线, 它需要平稳增长, 并且没有多少深度回撤. 就个人而言, **我们也很欣赏许多 "平静的时段", 即权益线的一部分是水平的: 这意味着由于过滤器让系统退出市场, 因此在此期间没有进行任何交易.** 我们认为没有必要不断进行交易, 当一些可以被利用的优势出现时, 一个好的系统应该能够及时感知, 反之则更适合待在场外. 然后, 除了权益线, 我们立即检查平均交易, 利润系数, 盈利百分比, "平均赢利/平均损失"比值以及多年来每月回报的分布方式. 仅从这些指标中可以得出关于交易系统的正确判断, 而不必担心掉入错误的陷阱.

### 2.5 结论

到目前为止, 我们已经涵盖了交易系统优化和性能评估中最重要的理论方面. 它简要概述了本主题所包含的概念, 但我们希望这份简洁将引导你进入更深刻的理解.

我们可以断言, 交易系统是一种科学的交易方法, 不需要任何人为介入. 显然, 这不是一个稳定的业务, 但它是一个应对概率的业务, 允许交易者利用统计优势入场搏杀.

通过阅读本书末尾参考书目中的文本, 您将会扩展这些知识, 深入研究交易系统评估和优化的细微差别.

不幸的是, 如果不处理交易系统的实际应用, 就不可能完全掌握这个主题. 交易系统的发展不是理论的挑战, 而是一种实用的市场实验方法. 编写代码, 测试它们然后优化它们是一个过程, 它允许交易者认识实际市场, 这有时比理论更重要. 知道如何遵循这个过程将节省大量的工作, 同时这个过程将有助于解决普通交易者无法接触到的情况.

在接下来的章节中, 您将对系统交易者在开发, 评估和优化交易系统时所做的工作有一个切实的看法. 我们相信这是我们工作中最重要的部分.

# Part II: 交易系统开发&演进真实案例

## Chapter 3: How to develop a trading system step-by-step – using the example of the British pound/US dollar pair

* **Introduction**

货币市场对所有类型的交易者都很有吸引力, 包括个人日间交易者, 交易公司, 金融和非金融公司, 银行和政府. 从新西兰的周一早上到美国的周五晚上, 他们每天24小时交易. 像英镑这样流动性充沛的市场, 为交易者提供了在任何时间尺度上, 实现任何不同想法, 开发任何类型的交易系统的可能性.

在本章中, 我们不会介绍最好的交易系统, 它承诺最高的利润. 相反, 我们的目标是向您展示一个交易系统, 该系统基于一个合理的想法并且经过改进以获得高稳健性. 为了帮助理解我们的交易系统开发概念, 您将在以下章节中逐步了解如何开发一个全新的交易系统并测试其稳定性.

我们选择具有突破组件(breakout component)的趋势跟随系统作为例子. 我们采用这个系统, 并展示如何通过下面的步骤改进它, 以使其成为一个有利可图的交易系统.

3.1 输入逻辑和代码. 如何利用突破过滤器(breakout filter)改进一个常见的移动平均线交叉系统.

3.2 评估没有参数优化和退出(exits)的交易系统 - 佣金和滑点的重要性.

3.3 输入参数的变化: 优化和稳定性图.

3.4 插入日内时间过滤器: 短期交易中时间重要性.

3.5 通过检查所有系统交易的演化来决定系统的适当出口. John Sweeney 的最大不利和最大有利游览将如何为您提供支持.

我们首先描述系统的逻辑.

### 3.1 The birth of a trading system

正如在介绍中提到的, 有很多来源可以用来开发自己的交易系统池. 其中之一必须是欧米茄研究(TradeStation)的战略交易和发展俱乐部(简称 "STAD"). 作为起点, 我们采用以下入口逻辑, 如 STAD 第 13 卷中所述:

卢克索系统通过两条移动平均线 - 快速平均线和慢速平均线的相交来触发交易. 当然, 有许多类型的移动平均线; 卢克索是 STAD 俱乐部中第一个使用三角形移动平均线的策略. 三角形移动平均线(TMA)的目的是增加价格数据平滑度,同时避免增加价格到指标之间的滞后时间. TMA 首先简单计算价格的算术平均值(通常使用收盘价). 然后, TMA 指标基于刚刚计算出的平均值再次计算算术平均值.

所以描述中的关键点是移动平均线的特殊类型. 然而, 当我们测试交易系统时, 我们发现移动平均线的类型并不重要, 最好的结果是用简单的移动平均线而不是更复杂的三角形移动平均线产生的! 因此, 您可以忘记这个三角形, 复杂的移动平均线并没有比普通移动平均线表现更好. 这证实了我们在开发交易系统时的一个观点, 最简单的也是最有效的.

系统开发的主要步骤如下: 获得符合交易市场个性的想法, 测试它们并使其符合您自己的需求. 我们在这里为 LUXOR 系统做这件事. 我们只讨论如何使用移动平均线并进行一些小改动.

#### The free LUXOR system code

想要实现这种逻辑的程序员可以在下面 TradeStation 的 Easy Language 中找到交易系统的代码. 我们在代码中添加了一些注释, 以便您了解所做的工作, 同时也便于您根据需要轻松修改代码.

<p align="center"><font size=2>Text 3.1: Easy Language Code of the LUXOR trading system. Bold letters: Code for the entries. Normal letters: added time filter. In comment brackets: possible simple exits.</font></p>

>{Copyright 2000. OMEGA RESEARCH, INC. MIAMI, FLORIDA.
>Strategy Trading and Development Club STAD, Volume 13,
>
>Modified 18 June 2006 and 15 July 2008 by Urban Jaekle
>Modified 1 January 2007 by Russell Stagg}
>
>{1. Definition of necessary inputs and variables}
>Inputs:
>FastLength( 3 ),  {The input parameters of the two moving averages… }
>SlowLength( 30 ),
>tset(1600),       {…start time for the intraday time window filter…}
>WindowDist(100);  {…window distance for the intraday time window filter…}
>                  {…can be changed – this makes optimisation possible}
>Variables:        {Definition of needed variables}
>    MP(0), Fast(0), Slow(0), GoLong(False), GoShort(False), BuyStop(0),
>SellStop(0), BuyLimit(0), SellLimit(0), tEnd(1700);
>
>MP = MarketPosition;
>
>{2. Time window filter; see below: 3.4, "Inserting an intraday time filter"}
>tend = tset + WindowDist;         {time window of 1 hour}
>if time > tset - 5 and time < tend then begin
>
>{3. Definition of moving averages and entry conditions}
>Fast = Average(Close, FastLength);
>Slow = Average(Close, SlowLength);
>
>GoLong = Fast > Slow;
>GoShort = Fast < Slow;
>
>{4. Entry Setup}
>If Fast crosses above Slow then begin
>    BuyStop = High + 1 point;
>    BuyLimit = High + 5 points;
>end;
>
>If Fast crosses below Slow then begin
>    SellStop = Low - 1 point;
>    SellLimit = Low - 5 points;
>end;
>
>If GoLong and C < BuyLimit then
>    Buy ("Long") next bar at BuyStop Stop;
>If GoShort and C > SellLimit then
>    Sell Short ("Short") next bar at SellStop Stop;
>
>{5. Exits: Derived from the slow moving average. These exits are not used here since
>we take different standard exits! Feel free to change the exits according to your needs}
>
>{If MP = 1 then Begin
>    Sell next bar at Slow - 1 point Stop;
>End;
>If MP = -1 then Begin
>    Buy to Cover next bar at Slow + 1 point Stop;
>End;}
>end;

Easy Language 代码可以分为不同部分:

1. 定义输入和变量.
2. 时间过滤器(在 3.4 中讨论).
3. 入场和出场设置.

由于本章的第一部分侧重于入场逻辑, 我们将交易系统的退出部分放在括号中的 Easy Language 代码中. 这意味着首先我们忽略出场并仅从该系统中生成交易. 在本章的后面, 我们将基于这些生成的交易应用出场逻辑.

#### The entry logic

Now let’s explain what this code means for the construction of the entries (Figure 3.1).
The entry is based on a usual moving average system and works as following: you enter
the market long on the bar where a fast moving average crosses above a slow moving
average and in the same way you go short if the fast moving average crosses below the
slower moving average.

现在让我们解释这个代码对于构造条目的意义(图 3.1). 该条目基于常见的移动平均系统, 其工作原理如下: 您在快速移动平均线超过慢速的条形区域进入市场 移动平均线, 如果快速移动平均线低于移动平均线, 则以同样的方式做空.


Trend following methods like these are well known to be able to capture huge profits
during long steady trends. The LUXOR entry logic takes this basic idea of such trend-
following methods by just using two simple moving averages as an entry signal generator.
However it is modified in the following way: an entry after the average crossover is only
allowed after a confirmation of the price itself occurs. The crossing of the moving average
alone is not enough to initiate a market position. In case of a long entry you want the
current price to exceed a recent high to enter a trade (Figure 3.1). Analogously the price
must go below a recent low to trigger a short entry. Please note that we only explain here
the long side in the system code since the short entries are built symmetrically.

众所周知，遵循这些方法的趋势能够在长期稳定趋势中获取巨额利润。 LUXOR入口逻辑通过仅使用两个简单的移动平均值作为入口信号发生器来采用这种趋势跟踪方法的基本思想。 但是，它按以下方式进行修改：只有在确认价格本身发生后才允许平均交叉后的条目。 仅仅移动平均线并不足以启动市场地位。 如果是长期进场，您希望当前价格超过近期高点进入交易（图3.1）。 类似地，价格必须低于近期低点才能触发短线。 请注意，我们只在这里解释系统代码中的长边，因为短条目是对称构建的。

<p align="center"><font size=2>Figure 3.1: Entry Logic. The entry is not triggered by the crossing of the two moving averages. Instead, at the crossover bar the high is kept and used as a long entry level. Short entries are taken symmetrically. Chart example was taken from British pound/US dollar, 30 min, FOREX from 26 Dec 2007. Chart and datafeed from TradeStation 8.</font></p>

![Entry Logic](https://raw.githubusercontent.com/21moons/memo/master/res/img/Trading_Systems/Figure_3.1.png)

The system has the following two input parameters which can be varied and optimised:
Inputs: FastLength (3), SlowLength (30);

These two input parameters “FastLength” and “SlowLength” are used for the fast and
slow moving average:

Fast = Moving Average (Close, FastLength);
Slow = Moving Average (Close, SlowLength);

Now the important breakout filter is added. At the bar when the fast moving average
crosses above the slow moving average the trade is not directly initiated. We take the
high of this bar (“crossover bar”, marked in red colour in Figure 3.1) and keep it as the
entry stop point as long as the fast moving average stays above the slow moving average:

If Fast crosses above Slow Then EntryLevel = High;
If Fast > Slow then Buy (“Long”) next bar at BuyStop Stop;

This simple but effective condition improves the probability of the simple trend following
system capturing the most profitable breakouts and not just any moving average crossover
which occurs. It is different to common moving average crossover systems where every
trade is taken, since the additional filter has to confirm the moving averages and in this
way prevents trading some false breakouts.

### 3.2 First evaluation of the trading system

----------

#### Calculation without slippage and commissions

The strategy is now applied to 30 minute FOREX data from 21/10/2002 to 4/7/2008. All
the following calculations in this chapter are based on a one contract basis. Keeping the
beginning simple we calculate the trading system’s results without any slippage and
commissions. These will be added in the next section where we will examine their impact
on system performance. Furthermore please note that at first we check the system just
with entries and trade reversals, leaving out exits.

As first input parameters for the trading system’s entries we choose 10 bars for the fast
and 30 bars for the slow moving average. With 30 minute bars this means the fast moving
average is calculated from the last 5 trading hours whereas the slow moving average
relies on the last 15 hours. Figure 3.2 shows the resulting equity curve in a detailed form.
With “detailed form” we mean that this curve shows all run-ups and drawdowns of the
trades which happen during their lifetime. Like this the equity line is more informative
compared to a form where just end-of-day or even end-of-month results are shown.

<p align="center"><font size=2>Figure 3.2: Detailed Equity Curve of the trading system LUXOR on British pound/US dollar (FOREX), 30 minute bars, 21/10/2002-4/7/2008. Input parameters: SLOW=30, FAST=10. System without exits, always in the market. Back-test without any slippage and commissions. Chart from TradeStation 8.</font></p>

![Detailed Equity Curve](https://raw.githubusercontent.com/21moons/memo/master/res/img/Trading_Systems/Figure_3.2.png)

The equity line looks like a good starting point for a viable trading system. Although
some drawdowns occur the system always recovers quickly and achieves new highs, so
that you get a relatively steady growth of the initial capital. The profitability of the trading
system is also revealed by the trading figures (Table 3.1). Here you see that LUXOR
gains a total net profit of $66,000 with only one traded contract within the testing period
from October 2002 until July 2008. The biggest drawdown within this period was
$16,000. If you assume a starting capital of $30,000 then this would mean that your total
profit is more than 200% in the last six years, with a maximum drawdown of about 50%.

<p align="center"><font size=2>Table 3.1: Main system figures of the LUXOR system. British pound/US dollar (FOREX), 30 minute bars, 21/10/2002-4/7/2008. Input parameters: SLOW=30, FAST=10. System without exits, always in the market. Back-test calculation without any slippage and commission.</font></p>

![Main system figures](https://raw.githubusercontent.com/21moons/memo/master/res/img/Trading_Systems/Table_3.1.png)

The considered system shows the main properties of trend following trading strategies:
• The percentage of profitable trades is low (36.5%). From the 1913 performed trades,
only 698 are profitable whereas the majority (1215) end with a loss.
• The overall gains of the system result from the high ratio of average win/average
losing trade. The average winning trade is with US$846, which is bigger than the
average losing trade (US$435) by a factor of two.
• The average time in winning trades is about three times longer than the average time
which the system stays in losing trades (62 bars versus 24 bars).

This shows that the system logic follows perhaps the most important rule in trading which
everybody knows but which is yet difficult to follow: cut the losses short and let the
profits run. This trading rule is psychologically hard to adhere to since you often suffer
directly from your losses and on the other hand you have to wait a long time until you
can earn your rare but hefty gains.

It is worth mentioning that the long side of the trading system is much more profitable
than its short side ($56,900 vs. $9,400 net profit). This observation will be examined in
Chapter 5.3 again when we discuss the so called “market bias”. The “market bias” means
the tendency of a market to favour special features or parts of a trading system, like in
this case the better profitability of the trend-following system’s long side in an overall
upward trend of the tested market. The good point for our trading system here is that
although there is such a market bias with an up-trend, the short side of our trading system
is still in the profitable range. This underlines the stability of this symmetrically built
system.

Furthermore, you of course get nearly the same number of short trades (956) as long
trades (957) because the trading system only reverses positions. Since we have not added
any exits the system stays in the market 100% of the time, holding either a long or a short
position.

Finally we want to underline a fact which should never be underestimated when
developing trading systems: the statistical significance of your performed tests. If you
develop a new system and in testing you have only 100 signals, or even less, the
probability of achieving profitable results just by accident is very high. With nearly 2000
trades in our back-test the statistical probability is high that this strategy will perform in
a similar way in the (near) future.

So what have you gained so far? Statistics show that the entry logic is sound and has a
certain probability of maintaining its behaviour in the future. If you however take a closer
look at the trading figures you will see that the system produces only an average profit
of US$35; this level of average profit per trade without any trading costs is very low! So
what you have so far is just a trading rule which detects a tiny profitable bias in prices.

Therefore we are now at a point when the trading system development work has just
started. There are lots of steps to perform until you can work out a complete trading
system. The profitability of this system must be increased and exits must be added. Before
we do this we take trading costs into consideration to make the whole approach more
realistic.

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

