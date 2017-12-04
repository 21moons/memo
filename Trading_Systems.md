Trading Systems
A new approach to system development and
portfolio optimisation


#Part I: 交易系统开发&评估实践指导

##Chapter 1: 什么是交易系统? 
交易系统就是一个自动定义入场点和出场点的，不包含任何人为介入因素的规则集合。
因为交易规则并没有定义交易时间和交易地点，这样使得交易系统是静态可测试的。
通过历史数据，我们可以在一定程度上计算交易系统的可信度。
如果我们向已有规则中添加资金管理规则和投资组合规则，我们就拥有了一个交易策略，或者换句话说，就是对市场进行完全自动化模拟。

我们提到的资金管理并不是我们通常认为的风险管理，而是如何去止损和止盈。

###1.1 简易交易系统示例
* 入场规则
买入2份最近20天内价格创最高的合约;
卖空2份最近20天内价格创新低的合约;
* 离场规则
如果 marketposition = 1，那么在最接近 avgtruerange(14) 的价位卖出平仓;
如果 marketposition = -1，那么在最接近 avgtruerange(14) 的价位买入平仓。

quantitative traders 量化交易者

###1.2 为什么你需要交易系统
统计结果毫无疑问地表明只有百分之几的交易者能够年复一年地打败市场。大部分是私人和机构交易者迟早会破产。如果你不属于幸运的随意交易者，那么为了生存，唯一的选择就是使用交易系统。如果你购买了这本书，你很可能并不是一个成功的自由交易者。按照我的经验，成功的自主交易者拥有天生的直觉，能够依靠本能预测市场动向。与之对应的是，有许多成功的基金经理，机构和私募交易者利用预设的交易策略和投资方法获利。如果有人认为交易系统可以很容易地战胜所有的交易中的阻碍，那么这是对交易系统的一种误解。一方面交易系统可以帮助交易者击败市场，另一方面它也会引入一些新的问题。

首先, 如果交易者在勇气方面有些问题，面对机会时没有足够的魄力及时行事。这种情况下交易系统并非一个完美的解决方案。正如 Larry Williams 所说，“trading systems work, system traders do not”。对应一个有系统的交易者而言，没有什么比忽略交易信号更大的恶行，Bill Eckhardt 写到：

*如果你做了一笔坏交易，你还有资金管理，你还有一大堆东西能够帮助你，做一笔坏交易真的没有你想的这么严重。但是，如果你错过了一笔好的交易，不要找任何理由。如果你遇到了一笔注定盈利的交易, 而你却错过了它，你注定会在这个游戏中完蛋。*

其次，为了信任交易系统，特别是在利润回撤抹去交易者对交易系统能力的信心时，一定要做大量的研究和统计工作，这并不是每个人都能够做到的。开发，实施，测试和评估一个交易系统并不是一个简单的事情。

最后，影响自由交易的许多因素同样影响着系统性交易，例如 缺乏足够的启动资金，投资组合多样化的可能性，全职，24小时，敬业。

更重要的是，我们可以说做交易并不像在一个理性的企业工作，你做出计划，得出结论，一切可以用逻辑的方式来解释。 恐惧和贪婪用一种大脑无法掌控的方式操纵价格。 当然有一些幸运交易者可以凭直觉地打败市场，但在这些情况下，他们并不能清晰的解释他们为什么买或卖。 如果这一切都是真的，意味着你需要一个不理智和不合逻辑的工具来进入和退出市场，这东西不能让你完全理解，而且违反直觉。 通常情况下，那些你认为是不合逻辑的，或者只会导致失败的信号会让你成为赢家。

使用机械的交易系统意味着你需要放弃自己持有的关于金融的信念，放弃所有让你“感觉良好”的交易方式：通常每个人买入下跌中的合约会感觉良好，买入价格创新高的合约则会感觉不舒服，但有很大概率是后者的方法更好。 测试交易系统，意味着数字的野蛮力量将会强加于你，让你感觉倍受约束。 成为一名完全机械的交易者意味着与自己搏斗。 这是赢利的唯一途径，除非你是一个幸运的劫匪，日复一日地赚钱，但却不知道如何赚钱。


###1.3 交易系统的相关知识

主观技术分析(subjective technical analysis)和客观技术分析(objective technical analysis)之间有很大的区别。

客观的技术分析方法是定义清晰的，可重复的，能够产生明确交易信号的过程。这使得它们可以用计算机程序来实现，并且可以使用历史数据做回测。 同时，回测产生的结果可以通过严格的评估来做定量分析。主观技术分析方法没有明确的分析过程。由于其天生的模糊性，必须依赖分析师的私人解释。这种特性妨碍了电脑化，回测和客观的性能评估。换句话说，我们没有方法能确认或否定主观方法的功效。因为这个原因，他们很难被证实是有效的。

因为其模糊性和缺乏科学的方法，主观技术分析在学术圈和严肃的市场从业人员中并没有获得良好的声誉。 要成为一个图表或技术分析师，而不是投资组合经理，有一套个人的技术分析绝活可能是金融业中最好的跳板。关于为什么人们会相信奇怪的东西，如教条，信仰，神话和轶事，已经有很多的社会学和心理学的文献来解释，同样这也是使用科学方法推导反而更容易错过好的科学技术分析的原因。 当然，我并不是要在这里讲授关于科学哲学，我只是想简要提醒读者什么是科学知识。科学知识是经验的，是基于对现实的观察：

*技术分析的实质是统计推断。它试图从历史数据中归纳出模式，规则等形式并进行概括，然后推断他们的未来。*

因此，技术分析利用统计推断工具，从单个样本开始观察，目的是评估整个人群的统计学属性。 通过这种方式，技术分析是可以量化的，就像统计学一样。此外，技术分析和科学一样，试图通过变量之间的函数关系来预测未来。 如果一个变量变了，那么因变量会随之变化。技术分析中的规则对应统计学中的函数，这里函数是有一定概率的。科学技术分析和统计学之间没有任何障碍，那些不喜欢主观技术分析的人提出的疑惑突然消失了。定量技术分析借用了应用科学中的典型分析方法：由牛顿发起并由波普尔推广的假设 - 演绎方法。该假设演绎法有五个阶段：
1. 观察。系统开发人员，通过持续观察金融市场上的日内波动及日间波动，设计变量之间的关系，例如当天交易量和收盘价之间的关联，或者指数和第二天开盘价之间的关联。
2. 假设。假设来自系统开发人员的创新思维 - 一个智力火花，其中的起源无法得知。系统开发人员必须清晰的知道他的假设不是来源于分析样本的特殊性，而是从全部数据中推演出来，对其中的大部分数据都是适用的。
3. 预测。如果这种关系是真的，那么可以依据假设来构建一个条件命题或一个预测，“如果这个假设确实是真实的，预测将准确的告诉我们可以在新的集合中观察到什么”。
4. 验证。系统开发人员验证预测在新集合中是否成立。
5. 结论。系统开发人员，通过使用统计推断工具(例如信任区间和假设测试)，将通过衡量实际观察结果与预测是否一致，确认假设是否成立。
这个过程与化学或生物学等应用科学中用到的科学评估方法没有任何区别。


##Chapter 2: 设计, 测试, 优化和评估一个交易系统 

###2.1 设计

交易系统从一个像创业愿景一样的想法开始。 创新是介于创意和幻想之间的一类东西，但是想法能否成为现实则取决于你为它奉献了多少时间。 有些系统交易员说一个关于交易系统的好主意是在你最不期待的时候出现的，但是与优秀交易者聊天很好会有些帮助，强烈建议你参加研讨会，大会和专业交易者之间的聚会。即使能够亲眼看到自由交易如何交易也是有用的。不幸的是，创新并没有确定的道路，但下面是
提示可以促使你走出想象的边界。

####开始

书单：
1. Traders www.traders-mag.co.uk
2. Active Trader www.activetradermag.com
3. Futures www.futuresmag.com
4. The Technical Analyst www.technicalanalyst.co.uk
5. Technical Analysis of Stocks & Commodities www.traders.com

TradeStation 论坛
“System Traders and Development Club” (STAD)

####编程目标

一个系统由一个入场公式，一个离场公式和一个资金管理公式组成。 离场公式与 “风险管理” 有关，即初始止损，追踪止损，目标退出，通俗一点来讲就是我们承担多少风险以及在每一笔交易如何承担风险。 “资金管理”关心的是我们在每笔交易中投资多少，就是我们买或卖多少股票或期货合约。 系统交易的初学者不愿意相信的是，只要积极地使用杠杆和资金管理技术，回报就会是惊人的，换句话来说，如果没有合适的资金和风险管理工具，即使交易系统优秀的令人屏息，也不会成为可行的投资工具。

####交易时机
期货价格系列没有任何问题, 股票要考虑回购，分红，增发

###2.2 测试
####市场数据的重要性

####回测时间长度

文献指出，一个交易系统为了确保健壮性和一致性，必须在多时段多市场测试(multi-period multi-market test)中达到一定的成功率。
一些机械交易者(mechanical traders)声称，支持多市场的交易规则是不存在的，因为交易系统有自己的个性，交易系统只能适合部分而非全部市场。如果多市场测试是指在许多不同的市场 (债券，股票，商品，货币，股票，等等) 中表现的都一样好，从这个角度来讲，能够通过多市场测试的系统确实很少。我们与这些交易者看法一致 - 不过，经过20年的不断尝试，今天的我们称得上是屈指可数的真正多市场交易系统之一。

反过来说，正因为多周期测试是令人厌恶的，机械交易者才认为过去只能是过去(意思是对未来并没有指导意义)：市场是随着经济结构和社会的变化而不断变化的。为什么会认为市场是一成不变呢？很多使用日内交易系统的系统交易者的绝不会用12个月之前的数据做回测，甚至有些只用3个月内的数据。

我们认为，回测周期的长度由长期交易中形成的智慧决定。举个例子，一支银行股的价格是波动的，在2000年的泡沫期间，这家银行突然与一家网上银行合并。那么在2001年，使用之前的交易系统进行测试是不合适的，因为股票的走势已经因为合并而改变了。所以严肃的交易者会去掉非正常环境下的数据，比如说 1999-2000 的股市泡沫，或者是2008年的原油危机。每个系统在波动巨大的时候都是有效的(意思是单向趋势)，但是只有一个稳健的系统才能在正常情况下始终保持稳定工作。不正常的情况时有发生，我们知道将会面对它们，然而市场在80％的时间中都是正常的，当它变得疯狂导致风险太高时，一个好的系统能够感知到。

####规则复杂度和自由度

多市场测试的首要目标是检查一个系统是否按照预期的方式执行（如果手动测试，系统发出的信号与程序员想要的信号位于同一位置），并且在它们应用到市场上是否有利可图 。我们不应该期望系统在我们测试的每个市场上都能盈利，但能够盈利市场越多，系统就越优秀。

测试可以对系统在统计学上的有效性进行初步验证，而优化则是关注市场的特定行为特征，并以此为依据对系统进行微调。尽管这只是一个不完全的定义，但它有助于澄清为什么优化是在测试后进行 - 此时我们已经确认系统是可运行的。

通常的结果是，一个系统在相似的合约上都是有利可图的; 换句话说，例如，在所有的能源期货上，它们的表现都是一样的，但在完全不同的债券市场上表现会差一些，而在货币市场上则表现平平。

测试系统时最重要的是测试窗口大小的选择; 也就是说，我们需要将系统应用到什么样的价格序列。这个决定并不遵循明确的时间表或经验法则，而是需要遵守两个统计要求：价格系列必须足够长才能囊括不同的市场走势，并随之产生大量的交易信号。

The number of variables and the data they consume are also considered in relation to the
whole data sample under an approach known as “degrees of freedom” – that is, the
number of variables and conditions and the data they use should not be more than a 10%
fraction of the whole data sample considered. It is of critical importance to avoid a
situation where we have 500 trading days and a trading system with 500 different
conditions. It could be that each condition is different from the remaining 499 and it only
fits to that particular trading day, so that every day will have its own proper condition
that will make the most money from the market in sample, but it will have no forecasting
power (see Chapter 5).

变量的数量和它们消耗哪些数据，一般认为与整个数据样本相关的，我们采用一种被称为“自由度”的方法来决定 - 也就是说，变量和条件的数量和他们使用的数据不应超过考虑整个数据样本的 10%。 避免一件事情是至关重要的，那就是在我们有500个交易日数据的情况下，为交易系统选取500个不同的条件。 可能每个条件都不同于其余的499个，而且只适合于那个特定的交易日，这样每一天都会有最适当自己的条件，理论上从样本数据构成市场上赚到最多的钱，但是却对未来的市场没有任何预测能力(详见第5章)。

Rule complexity and degrees of freedom are a hard topic for those not mathematically
oriented. But even among mathematicians there are many that would not be at ease in
explaining what degrees of freedom are. When explaining degrees of freedom (usually
indicated as df) maybe the most appropriate and easy to grasp explanation is the joke of
the married man that comments, ‘There is only one subject, my wife, and my degree of
freedom is zero. I should increase my “sample size” by looking at other women.’

对于那些没有数学思维的人，规则的复杂性和自由度是一个难题。 但是即使在数学家中，也有许多人不能轻易的解释什么是自由度。 在解释自由度时(通常表示为df)，也许最恰当和容易理解的解释就是笑话了那个已婚的男人说：“我只有的妻子，我的自由度是零。 我应该通过观察其他女性来增加我的‘样本量’。”

Coming to a more serious approach we should say that there are many definitions of the
concept “degrees of freedom” varying from statistics to mathematics, geometry, physics
and mechanics. An interesting paper available free on the internet performs the difficult
task of making the concept simple[3]. A first definition (Larry Toothaker, 1986) could be
‘the number of independent components minus the number of estimated parameters’.
This definition is based upon the Walker (1940) definition: ‘The number of observations
minus the number of necessary relations among these observations’. But the best practical
way to explain the concept is an illustration introduced by Dr. Robert Schulle (University
of Oklahoma):

严格来讲，自由度这个概念有很多定义，从统计到数学，几何，物理，和力学。 在互联网上有一篇免费的论文可以让这个概念变得简单，当然这是个困难的任务。 自由度的第一个定义(Larry Toothaker，1986)可能是 “独立部分的数量减去估计参数的数量”。这个定义源于Walker(1940)的定义：“观察次数减去这些观察之间必要关系的数量“。 但最好的实际解释这个概念的方法是Robert Schulle博士(俄克拉荷马大学)介绍的一个例子：

In a scatter plot when there is only one data point, you cannot make any estimation of the
regression line. The line can go in any direction … Here you have no degrees of freedom
(n-1 = 0 where n = 1) for estimation (this may remind you of the joke about the married
man). In order to plot a regression line you must have at least two data points (a wife and
a mistress). In this case you have one degree of freedom for estimation (n-1 = 1 where n =
2). In other words, the degree of freedom tells you the number of useful data for estimation.
However, when you have two data points only, you can always join them to be a straight
regression line and get a perfect correlation (determination index = 1.00). Thus the lower
the degree of freedom is, the poorer the estimation is.

在只有一个数据点的散点图中，你不可能在图上对完成回归线的估计。 这条线可以走向任何方向...此时你没有自由度(n - 1 = 0，其中 n = 1)用于估计。 为了绘制回归线，你必须至少有两个数据点(妻子和情妇)。 在这种情况下，你有一个自由度用于估计 (n - 1 = 1，其中 n = 2)。 换句话说，自由度告诉你用于估计的有效数据数量。但是，如果只有两个数据点，则可以将它们连成一条直线回归线，并得到完美的相关性 (确定指数 = 1.00)。因此自由度越低，评估效果越差。

So even in an intuitive way we arrive at the conclusion that the wider the sample size
and the lower the number of variables, the better the estimation. Robert Pardo is the only
author in the current literature that is able to keep the topic manageable and he gives the
following short-cut guidelines in his book [4]:

所以我们从直觉上就能得出结论，样本规模越大变量的数量越少，评估效果越好。 罗伯特·帕尔多是当前唯一能在文献中把这事讲清楚的作者，他在自己的书中给出了精辟的描述：

Calculation of the degrees of freedom = whole data sample –
rules and conditions – data consumed by rules and conditions

自由度的计算 = 整个数据样本 - 规则和条件 - 规则和条件消耗的数据

Generally, less than 90% remaining degrees of freedom is considered too few. Beyond
the Pardo’s formulas that can help from a practical standpoint it is important to remember
that a system with 20 variables cannot be tested on just 6 months of daily data in order
to decide, if going ahead with a proper optimisation. The number of variables and
conditions of the trading system are intimately connected to the length of the testing
period. Put in another way, some estimates are based on more information than others.
The number of degrees of freedom of an estimate is the number of independent pieces of
information on which the estimate is based. The more information, the more accurate the
estimate. The more information the higher the number of the degrees of freedom.

一般来说，剩余自由度少于 90% 都被认为是太少了。除了帕尔多的公式，从实践的角度来看，如果要在一个有20个变量的系统上进行适当的优化，不能仅仅在6个月的日线数据上进行，这一点非常重要。 变量的数量以及交易系统的条件集合，与测试周期的长度密切相关。 换句话说，一些评估需要更多的信息。评估所需自由度的值，是基于信息独立片段的数量。信息越多，评估越准确。 信息越多，自由度越高。

The same concept of the at least 90% degrees of freedom left could be applied in reverse
as a rule of thumb with a multiple of 10 to the relationship between data used by the
system’s calculations and the testing window length. If you apply a 30-day moving
average of the closing price you need to test it over at least 300 days (30 x 10).

剩下至少 90% 自由度的理念，也可以反过来应用，这就是用来表示系统计算使用数据与测试窗口长度之间关系的 10 倍法则。 如果你使用30天移动平均收盘价格，测试时至少需要 300天 (30 x 10) 的数据。

Let’s make one example: we consider a data sample of three years of highs, lows, opens
and closing prices for a total 260 day per year x 3 x 4 = 3120 data points. We consider
then a trading strategy uses a 20-day average of highs and a 60-day average of lows. The
first average uses 21 degrees of freedom: 20 highs plus 1 more as a rule, and the second
average uses 61 degrees of freedom: 60 lows plus 1 as a rule. The total is 82 degrees of
freedom used in the example. The result in percentage terms is 82/3120 = 2.6% so that
97.4% degrees of freedom are left.

举一个例子：我们现在有一个数据集，包括三年内每个交易日的最高价格，最低价格，开盘价格和收盘价格，这样共有(每年260个交易日)260 x 3 x 4 = 3120 个数据点。 我们假设有一个交易策略使用最高价格的20日均线和最低价格的60日均线。第一个均线使用21个自由度：20个最高价格加上另外1个作为一条规则，第二个均线使用61个自由度：60个最低价格加上1个作为一条规则。 合起来总共是82个自由度。 以百分比计，结果是82/3120 = 2.6%，剩下97.4% 的自由度。

Data points used twice in calculations are counted once so that if you are using a 5-day
moving average of the closes and a 10-day moving average of the closes you will have
for the latter condition 10 data + 1 rule while for the first condition you will have just 1
rule. The total is 12 data consumed. It is obvious that since the 5-day moving average is
included into the longer one only the latter will be relevant for the degrees of freedom
calculations.

在计算中使用两次的数据点仅被计为一次，如果你同时使用收盘价5日均线和10日均线，对于后一个条件来说你将有10数据+1规则，而对于第一个条件，你将只有1规则。 总共消耗了12个数据。 很显然，因为5日均线包括在10日均线中，只有后者才会参与自由度的度量。

The number of trades required in order to trust a system is also connected to the length
of the testing windows. A test is significant if it produces a number of trades that will
allow the risk of being wrong to be kept at the lowest level. The test window’s length
should take care of this. Let’s say that the obvious standard error should be added to or
subtracted from all the trading system’s report parameters according to the trade sample.
Standard error is:
Standard Error = square root of n + 1
Where n = number of the trades

可信系统需要的交易数量也与测试窗口的长度有关。 如果一个测试在产生多笔交易的过程中，始终让错误的风险保持在最低水平，这样的测试结果对于验证系统是否可信是非常重要的。 测试窗口的长度应该考虑到这个因素。 标准错误应该依据交易样本从交易系统的报告范围中添加或减去。标准错误是：
标准误差 = n + 1 的平方根
其中 n = 交易的数量

The higher the number of trades, the lower the possible error in the trading system’s
metrics. In other words if we have few trades, the risk that these trades are profitable by
accident is high. If you shoot once and you hit the bull’s-eye it is possible either that you
are a good marksman or simply that you are lucky. Conversely if you shoot 100 times
and you hit the mark every time the probabilities that you are a good marksman are higher.

交易笔数越多，交易系统度量时可能出现的错误就越低。 换句话说，如果我们很少交易，那些看上去交易有利可图的交易带来的风险越高。 如果你射击一次就击中了靶心，要么你是一个神射手，要么说你纯粹是运气好。 相反，如果你射中靶心100次，你就有很大概率是一名神射手。

To be considered trustworthy, a system needs at least 100 trades, so that its standard error
will be the square root of 100 + 1 = + - 10.04%.

一个系统要被认为是值得信赖的，至少需要100笔交易，这样其标准错误将是 100 + 1 的平方根，+10.04% 或者 -10.04% 。

All the trading system metrics will vary in between the boundaries of +10% and -10%.
That is, if the net profit is $100 the possible real net profit will vary more or less as a rule
of thumb from a high at $110 to a low at $90.

所有的交易系统指标都会在 +10% 和 -10% 的范围内变化。也就是说，如果净利润是100美元，那么实际净利润可能分布在从90美元到110美元的区间内。

### 2.3 交易系统的预测能力
#### 优化

Optimisation has earned a bad reputation among many traders. It can even be an offense
for a systematic trader. Optimising a system means to find those inputs in the system’s
variables that maximise profits or that fulfill whichever constraints a trader decides to be
the leading criteria for optimisation (for instance instead of maximising profits a system
could tend to minimise drawdown). Let us give an example: you have a moving average
crossover system; that is, the system buys when the short-term moving average crosses
the long-term moving average. The question optimisation replies to is how many days
will be the input of the short-term moving average and how many days will be the input
of the long-term moving average. Optimisation means “to make fit” a system; that is, to
adapt a system to the market we intend to trade [4].

优化已经在许多交易者中赢得了不好的声誉。 甚至可以是对系统交易者的一种冒犯。 优化系统意味着在系统中找到这些输入使利润最大化或实现交易者所决定的任何约束的变量优化的主要标准（例如，而不是最大化利润系统可能会降低缩减）。 让我们举一个例子：你有一个移动平均线交叉系统; 即系统在短期移动平均线穿越时买入长期均线。 优化回复的问题是多少天将是短期均线的输入，多少天将作为输入的长期均线。 优化意味着“适合”一个系统; 也就是说使系统适应我们打算交易的市场[4]。

But optimisation is a two-edged sword: it is one thing to adapt a system to a market in
terms of volatility, initial risk, return, etc. But it is another thing to look for those inputs
that by chance made the most money in the past but have no forecasting power. Let’s
assume that we have a system that every day will buy at the lowest price and sell at the
highest price with two inputs that will have the precise entry and exit price by which the
maximum profit is reaped. This is a wrong kind of optimisation since we looked for a
value that changes every day and only with hindsight is the best value for that particular
day able to be defined. This system has no forecasting power.

但是，优化是一把双刃剑：一个系统适应市场是一回事波动性，初始风险，回报等等。但是，寻找这些投入是另一回事偶然是过去赚钱最多，却没有预测能力。 让我们假设我们有一个系统，每天都会以最低的价格购买和出售最高的价格与两个输入，将具有确切的进入和退出价格由此获得最大的利润。 这是一种错误的优化，因为我们寻找一个价值每天都在变化，而事后才是最具价值的一天能够被定义。 这个系统没有预测能力。

It is absolutely impossible to avoid optimisation in trading systems’ development. Just
think of what every trader is currently doing and you will understand that optimisation is
something we need to face. There are traders that refuse the inputs optimisation process
since, according to their view, a system should work forever with the same inputs. But
then they decide to trade a system among a batch of other systems simply because in the
past it made more money than other systems. Isn’t this a kind of optimisation? Again
they change the original system code adding constraints and conditions in order to adjust
the system to market price behaviour and then they chose the variation of the system that
worked the best in the past: isn’t this also a kind of optimisation? If you are currently not
so much inclined towards optimisation please review your standpoint and consider how
many times you have used optimisation involuntarily.

在交易系统的发展中避免优化是绝对不可能的。 只是想想每个交易者目前正在做什么，你会明白，优化是我们需要面对的东西。 有交易者拒绝投入优化过程因为根据他们的观点，一个系统应该以相同的投入永远工作。 但那么他们决定在一批其他系统之间交换一个系统过去它比其他系统赚了更多的钱。 这不是一种优化吗？ 再次他们改变原来的系统代码，添加约束和条件以进行调整系统对市场价格行为进行选择，然后选择系统的变化过去做得最好：这不就是一种优化吗？ 如果你现在不是这么多倾向于优化，请审查您的立场，并考虑如何很多时候你不经意地使用了优化。

Optimisation is something useful in system trading and we need to distinguish between
the normal optimisation process and its aberration, namely curve fitting or over-
optimisation. For example: we trade a daily system on bond futures that will be
consequently affected by monetary policy. Monetary policy is not something that changes
every day but it suits the economic cycle of expansion-recession so that we are talking
about something that lasts years. It will be clear in this case that we need to have an
optimisation window that is 6, 12 or 18 months long – something of a reasonable length
in order to fine-tune the system with the market and the monetary policy.

优化在系统交易中是有用的，我们需要区分正常的优化过程及其像差，即曲线拟合或过度曲线拟合，优化。 例如：我们交易债券期货的日常系统受到货币政策的影响。 货币政策不会改变每天都适合经济衰退的经济周期，让我们谈谈关于持续多年的事情 在这种情况下，我们需要明确一点优化时间为6,12或18个月 - 这是一个合理的长度用市场和货币政策来调整制度。

Provided the system produces a significant number of trades, we will test the system on
the preceding two years at the beginning and then we will fine-tune the system, re-
optimising it every 6, 12 or 18 months. This approach is directed toward real trading and
not a theoretical appraisal of the system. Surely the system must be tested on the longest
price series we have at our disposal and optimised accordingly in order to check at a first
glance if the system is viable. But this process is not something that will help us in finding
the appropriate parameters to place the next trades. It is simply an evaluation process that
will help us in deciding if the system is suitable for that particular market; that is, if the
equity line is growing (the equity line may not be growing in a smooth way as we would
wish, but it should at least be decisively on the upside).

如果系统产生了大量的交易，我们将测试系统在开始的前两年，我们将微调系统，每6,12或18个月优化一次。 这种方法是针对真正的交易和不是对系统的理论评估。 当然系统必须进行最长的测试价格系列，我们在我们掌握和相应优化，以检查在第一一瞥系统是否可行。 但这个过程并不能帮助我们找到答案适当的参数来放置下一个交易。 这只是一个评估过程将帮助我们决定该系统是否适合该特定市场; 那就是如果股权正在增长(股权可能不会像我们那样顺利增长希望，但至少应该果断地向上)。

In other words during testing over the longest price series available we check if the system
is adapted to catch the moves of a particular market, while during optimisation we see if
there is room for improvement with a change of inputs. Then through periodic re-
optimisation within a 6 to 12 month window we fine-tune the system, in terms of inputs,
to the characteristics of that particular market and keep the system abreast of the market
changes.

换句话说，在测试最长的价格系列可用我们检查系统适应于捕捉特定市场的动向，而在优化过程中我们可以看到随着投入的变化，还有改进的空间。 然后，我们在6到12个月的时间内优化系统，根据输入，针对特定市场的特点，使系统与市场保持同步变化。

For an intraday system all the testing, optimising and re-optimising periods will be shorter
than for daily or weekly systems.

对于日内系统来说，所有的测试，优化和重新优化周期将会缩短比每天或每周系统。


#### 走向分析
#### 健壮性


###2.4 交易系统的评估
What to look for in an indicator 27
Average trade 28
Percentage of profitable trades 28
Trading Systems
iv
Profit factor 30
Drawdown 30
Time averages 31
RINA Index 32
###2.5 结论
