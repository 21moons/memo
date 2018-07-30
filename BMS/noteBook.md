# Communication

* ACM         Air Combat Maneuvering
* ADI         Attitude Direction Indicator
* BATR        Bullets at Target Range
* BIT         Built-In Tests
* CAS         Calibrated Air Speed(校准空速)
* CCIP        Constantly-calculated Impact point
* CCRP        Constantly-calculated release point
* CG          Center of Gravity
* CMS         Countermeasures Management
* CNI         Communication, Navigation & Identification
* CRS         Course
* DBS         Doppler Beam Sharpening
* DBU         Digital Backup
* DCS         Data Command Switch(ICP 上的一个四向拨动开关)
* DED         Data Entry Display
* DMS         Display Management Switch(HOTAS 上的按钮)
* DTC         Data Transfer Cartridge
* DTOS        DTOS is a visual CCRP
* EPU         Emergency Power Unit
* ETA         Estimated Time of Arrival
* ETE         Estimated Time Enroute
* EWS         Electronic Warfare System
* FCC         Fire Control Computer
* FCR         Fire Control Radar
* FLCS        Flight Control System
* FLCP        FLT CONTROL Panel
* FLIR        Forward Looking Infra-Red(需要 LANTIRN 吊舱)
* FOV         Field Of View
* FPM         Flight Path Marker
* GM          Ground Mapping
* GMT         Ground Moving Target
* GS          Ground Speed
* HADB        High Altitude Dive Bombing
* HDG         Heading
* HMCS        Helmet Mounted Cueing System
* HSD         Horizontal Situation Display
* HSI         Horizontal Situation Indicator
* HTS         HARM Targeting Systems
* HUD         Heads Up Display
* ICP         Integrated Control Panel
* LANTIRN     Low Altitude Navigation & Targeting Infrared for Night
* LARA        Low Altitude Radar Altimeter
* LG          Landing Gear
* LPI         Low Probability of Intercept
* LRS         Long Range Scan
* HAD         HARM Attack Display
* INS         Inertial Navigation System
* MFD         Multi Function Displays
* MFL         Maintenance Fault List
* MSA         Minimum Safe Altitude
* MSL         Main Sea Level
* MSL FLOOR   Minimum Safe Level floor
* OA          Offset Aimpoint
* OBOGS       On Board Oxygen Generating System
* OSB         Option Selection Button
* PFL         Pilot Fault List
* PFLD        Pilot Fault List Display
* PMG         Permanent Magnet Generator
* PPTs        Pre-Planned Threat points
* PUP         Pull Up Point
* QNH         Query: Nautical Height(修正海平面气压)
* RWR         Radar Warning Receiver
* RWS         Range While Search
* SCP         Set Clearance Plane
* SOI         Sensor of Interest
* SPI         Steerpoint of Interest/System Point of Interest
* TAS         True Airspeed(TAS 是您实际在空中移动的速度)
* TF          Terrain Following
* TMS         Target Management
* TOS         Time Over Steerpoint
* TWS         Threat Warning System
* TWS         Track While Scan
* SMS         Stores Management Set
* UFC         Up Front Controller
* VIP         Visual Initial Point
* VLC         Very Low Clearance
* VRP         Visual Reference Point
* VS          Velocity Search
* VMS         Voice Message Service
* VVI         Vertical Velocity Indicator
* WEZ         Weapon Engagement Zone
* WDP         Weapon Delivery Planner

# ICP(Integrated Control Panel)

* 7 Master Modes:
1. Air to Air(A-A ICP button)
2. Air to Groud(A-G ICP button)
3. NAV (when none of the A-A or A-G modes are engaged)
4. DGFT - Dogfight (toggle switch on the throttle)
5. MRM/SRM – Missile Override (toggle switch on the throttle)
6. S-J Selective Jettison (SMS page on MFD)
7. E-J Emergency Jettison (while the E-J button is depressed)

Master Mode 按钮自动配置系统以执行特定操作. 他们可以同时更改 MFD 页面, DED 页面和 HUD 页面.

* 5 Override Modes:(貌似可以叠加在主模式上)
1. COM1 (ICP button)
2. COM2 (ICP button)
3. IFF (ICP button) Not implemented in BMS
4. LIST (ICP button)
5. F-ACK (left glareshield pushbutton)

Override Mode 提供对按钮对应功能的直接访问. 你可以通过再次按下 Override Mode 模式按钮来回退到初始页面. 除了上面提到的 5 种 Override Mode 之外, 还有一种特殊的 Override Mode, 可以将 UFC 返回到 DED 上的 CNI(通信, 导航和识别)页面. 通过将 DCS(数据命令开关)向左拨动(RTN 位置)来访问该模式.

* 8 Priority/secondary buttons:

这些是标有 T-ILS, A-LOW(Altitude-LOW), STPT(Steerpoint), CRUS(Cruise), TIME, MARK, FIX 和 A-CAL 的 ICP 方形按钮. 最后两个没有在 BMS 中实现.
这些按钮具有双重功能: 根据上面列出的标签, 它们用于将数据输入 UFC/DED 或 UFC/DED 子页面. 第二个功能是在暂存器中输入数值. 暂存器是 DED 显示中位于两个星号之间的区域. 只要您看到暂存器处于活动状态, 当您按下这些按钮时, 将输入范围从 0 到 9 的的数值. 请注意, 数字 0 通过 M-SEL 按钮输入, 此按钮没有二级页面调用功能.
当暂存器未激活时, 按下 Priority/secondary 按钮即可进入相关子页面.

PREV/NEXT 按钮在 DCS 按钮左边.

# DED(Data Entry Display)

## CNI

CNI(通信, 导航和识别)页面是 DED 的默认页面.
只有当 AUX COMMS 面板上的 CNI 开关设置为 UFC 时, 才能访问 CNI 页面. 当置于 BACKUP 中时, UFC 不起作用, 所有备用系统都处于活动状态并由侧面板控制.

## CRUS

CRUS 包含4个子模式 TOS (Time Over Steerpoint), RNG, HOME 和 EDR. 子模式通过 M-SEL 0 按钮选择.
CRUS 的子页面通过 DCS SEQ 开关进入.

当选择 TOS 模式时, HUD 速度条上会显示一个插入符号. 当你的空速与插入符号匹配时, 你将按照 TOS 时间准时到达路点. 到转向点的 ETA(预计到达时间)也显示在HUD中.

当选择 RNG 模式时, 在 HUD 速度条上会显示插入符号, 提示当前下的经济航速. 经济航速随高度变化.

当选择 HOME 模式时, HUD上会在速度条和高度条上显示插入符号, 这 2 个插入符号标识到达 HOME 点的最优路线(或者是到达任意被指定为 HMPT 的路点).

选择 EDR 模式时, 会在速度条上显示插入符号, 标识在当前高度下最大滞空时间对应的参考速度.

## MARK

MARK 页面用于创建标记点. 自建的标记点用 26-30 导航点存储. 标记点可以由 4 个不同的系统生成. OFLY(飞越 GPS/INS), FCR(火控雷达), HUD(抬头显示器)或 TGP(目标吊舱). 因此, MARK 页面中有 4 个子模式. 要在子模式之间切换, 请使用 DCS SEQ. 根据当前主模式和 SOI, 系统默认为特定子页面，并且可以启用自动标记点记录。

* 如果主模式是 NAV 或 A-G 模式且 FCR 是 SOI, MARK 页面将默认为 FCR MARK. TMS 向上移动之前将创建第一个标记点.
* 如果主模式为 NAV 或 A-G 模式且 TGP 为 SOI 且接地稳定, MARK 页面将默认为 TGP MARK. 与 FCR 一样, TMS up 将创建标记点.
* 如果主模式为 NAV 或 A-G 模式, FCR 或 TGP 不是 SOI, 并且指定或接地稳定, 则进入 MARK 页面将默认为 HUD MARK. 必须通过移动 HUD 标记(HMC - 在 FPM 附近出现的小的可回转圆圈)并向上移动 TMS 来手动创建标记点. 第一次按下 TMS up 将使 HMC 稳定, 第二次按下 TMS up 将保存标记点. HMC 必须指向地面上才能标记, 指向天空则不会生效.
* 如果主模式为 A-A, 进入 MARK 页面将默认为 OFLY MARK, 并将创建自动标记点.

模式选择(M-SEL 0 按钮)任何有效的标记点将使其转换为导航点.

标记点在 HSD 页面上显示为青色十字.

在任意 MARK 模式下, 可以选择正确的 MARK 子页面, 通过激活的传感器来创建手动标记点. 将光标移动到所需的位置, 并依据 MARK 模式将 TMS up 键按下一次或两次. 如果记录了先前的自动标记点, 则下一个标记点编号将递增, 并且选择 MARK 库中的下一个可用导航点. 自建标记点存储在编号为 26 到 30 的导航点. 一旦编号超过 30, 下一个标记点将覆盖 26 号导航点, 依此类推.

## LIST

LIST 页面用于访问其他子页面. 按下相关的 ICP 按钮可以访问每个页面: 1 表示 DEST, 2 表示 BNGO 等. 请注意: RCL, ENTR 和 M-SEL 0 按钮也分别用于进入 INTG, DLINK 和 MISC 子页面.

* 1 DEST 可以用来设置 OA 点
* 2 BNGO 页面用于设置燃油不足告警
* 3 VIP(Visual Initial Point) 数据可以从 DTC 加载
* 5 MAN 页面用于设置机炮射击的 GUN EEGS 漏斗宽度.
* 8 MODE
* 9 VRP 设置 VRP 点
* 11 A-G DL  数据链设置
* 0 MISC
  ** 5 LASER
  此页面用于设置激光系统. 它由 4 行组成.第一个用于设置 TGP CODE, 它必须与武器用来识别目标的激光脉冲编码一致(在 LOADOUT 屏幕中设置). 如果 TGP CODE 与武器代码不匹配, GBU 将不会得到指导并且按照弹道学坠落. 因此, 现在可以通过输入你的僚机挂载炸弹的武器代码来进行伙伴激光照射. 第二行设置激光点跟踪代码. 第三行使用 M-SEL 0 按钮将激光从训练模式切换到战斗模式. 第四行设置激光器计时. 在炸弹命中目标前 16 秒进行激光照射以进行末端引导, 并且该设置项可以通过 DTC 加载. 建议将其设置为 20 秒, 并在攻击移动目标和使用宝石路 III 时进行手动照射.

  ** 8 BULLSEYE(靶心)
  Bullseye 默认分配给 25 号导航点, 但您可以使用 PREV/NEXT ICP 按钮将其更改为任何导航点(最大 25).
  是否显示 Bullseye 信息取决于是否选择了 BULLSEYE 模式. 模式选择由 M-SEL 0 按钮切换, 当暂存器星号环绕 "BULLSEYE" 文本时开启 BULLSEYE 模式, 默认情况下该模式处于打开状态。
  当 Bullseye 处于模式选择状态时, HUD 的左下角显示到 Bullseye 的方位和距离. 如果未选择模式, 您将无法在 HUD 中对 Bullseye 进行方位和距离指示.
  在 MFD(FCR 和 HSD 页面)中, 当 BULLSEYE 模式被选择时, 显示当前位置相对于 Bullseye 位置的方位和距离信息, 当 BULLSEYE 模式未选择时, 显示相对于当前导航点的方位和距离信息.

# A-G attack

## VIP&VRP&OA

VIP(目视接触点), OA(偏移瞄准点)和 VRP(视觉参考点)的基本用途是规划空对地攻击路线. 相较于用于任务导航的正常航路点, VIP, OA 和 VRP 是可以围绕目标航路点设置的附加参考点, 以改善态势感知(但是, 它们仅在 HUD 中可见并且仅在选择目标航点时). 它们可用于微调攻击路线, 因此允许高精度跃升(pop-up)攻击. 跃升攻击是一种安全的轰炸方式, 因为你只给敌人很少的时间来对你做出反应, 比如说用 AAA 或 MANPADS 对你进行射击. 但是, 这种攻击需要精心准备. 此外, 通过仔细的对任务进行预先规划, VIP, OA 和 VRP 允许对地面目标实施多人攻击. VIP, VRP 和 PUP 在 HUD 中显示为圆圈, OA 则显示为三角形.

VIP(Visual Initial Point): 当参考点(RP)不是目标时, 我们使用 VIP.
OA: 与跃升点相关, 用作目标航向的参考点, 当飞行员跃升完成并打算将机头指向目标时, OA 可以提供有关目标方位的额外信息(因为我们将其设置为跃升点和目标之间虚线的扩展).
VRP: 当参考点是目标时, 我们使用 VRP, VRP 通常用作退出点.
PUP: 跃升点, 开始转向和爬升的点, 相对参考点的方位和距离已知.

## 低空突防和高空突防(High or Low level ingress)

当你靠近攻击目标时, 依据 SAM 和其他威胁进行攻击路线规划.

当你进入目标半径 30 nm 时, 此时你将直面敌方战斗机和防空导弹的威胁, 在这个阶段有两种选择-低空突防和高空突防.
低空突防通常适合下面的场景: 目标区域受到中高空 SAM 导弹的保护, 敌方战斗机也很活跃. 通过低空突防你就可以躲开敌方雷达的探测并尝试远离敌人的战斗机(战斗机下视能力受限). 在这个过程中你需要始终保持空对空模式(探测敌方战斗机)直到最后一刻.
在另一个场景中你最可能选择第二个选项-高空突防. 大多数 SAM 导弹已被摧毁, 只剩下 MANPAD 和 AAA. 现在我们可以做一个 "安全" 的高空突防. 对敌方战斗机实施远程攻击并远离地面威胁.

## 空对地攻击的方式

下面两种方式需要低空突防:

* A Pop-up attack

使用这种方式, 您可以避开大多数 SAM(防空导弹) 的雷达. 虽然你飞的很快, 但要注意的是-你仍然在 MANPADs 和 AAA 的打击范围内. 这种攻击方式对所有静态目标都很有用. 想想建筑物, 桥梁, 跑道甚至车辆. 可以使用的军械包括低阻炸弹到高阻炸弹, 集束炸弹以及激光制导炸弹.

![Pop-up](https://raw.githubusercontent.com/21moons/memo/master/res/img/BMS/Pop_up.png)

* TOSS delivery attack

TOSS 将使你一直在低空并与目标保持距离, 但攻击准确性较小. LAT 代表低海拔 TOSS. 在这个过程中, 炸弹将在战机的爬升过程中被向上甩出去. 攻击范围将变大同时准确度会变小. 因此, 这种攻击方式一般选择集束炸弹. 这是一种攻击 SA-2 或 SA-3 等车辆的好方法. 但也可以使用自由落体炸弹对抗大型目标.

![TOSS](https://raw.githubusercontent.com/21moons/memo/master/res/img/BMS/TOSS(LAT).png)

HADB 对应高空突防:

* High Altitude Dive Bombing

HADB 会让你待在大多数 MANPAD 和小型 SAM 的攻击范围外, 但你将成为 SA-5 等远程防空导弹的一个很好目标. 攻击过程中你应该保持在计划的高度以上, 适合打击所有类型的静态目标, 如建筑物, 桥梁和车辆. 可以使用任何类型的炸弹, 甚至包括像 AGM-65 这样的导弹.

![HADB](https://raw.githubusercontent.com/21moons/memo/master/res/img/BMS/HADB.png)

## Ordnance

攻击中使用何种武器取决于攻击方式, 天气和你要摧毁的目标.

炸弹的数量也是一个因素. 你必须考虑到飞机的全重. 挂载 2x MK84 和 2x副油箱, 然后采用 Pop-up 攻击并爬升至 12000 英尺? 这是无法完成的, 你会失去太多的速度. 采用 2x MK84 和 2x副油箱, 突防高度为 20000 英尺, 这种情况下 HADB 是合适的.
天气可能是一个因素. 如果你知道目标被云层覆盖, 则用于制导武器的激光将无法穿过云层工作. 在这种情况下, 你必须使用非制导的炸弹.

# DTC

在现实生活中, 一般通过电脑或笔记本电脑完成任务计划. 这些数据需要带到飞机上并加载到战机的任务计算机. 这些都是通过 DTC 完成的. 您可以将 DTC 视为用来将数据从一台计算机传输到另一台计算机的记忆棒.
在 BMS 中, DTC 通过程序进行配置, 并保存在 callsign.ini 文件中.

DTS 包括如下配置:
EWS programs and settings
MFD settings
Radio/Nav
Nav Offsets
Aircraft systems
Weapons settings

# MFD(Multi Function Displays)

MFD 由右侧控制台上 AVIONICS POWER 面板上的 MFD 开关供电.

MFD 顶部, 左侧和右侧的按钮的功能取决于显示的页面, 而底部按钮(OSB11 至 15)的功能通常与当前显示的页面无关.

OSB#15 始终是一个 SWAP 按钮，它将交换左右 MFD 的显示内容.
OSB#11 按钮在大多数页面上标记为 DCLT. 这代表 "declutter", 可用于删除非必要信息以使页面更易于阅读.
OSB#11 不用于 declutter 的唯一页面是 SMS 页面, 在该页面中按下 OSB#11 将切换到 S-J(选择性 Jettison)主模式.

底部中间的 3 个按钮 OSB(#12, #13, #14)是直接访问(Direct Access)按钮, 可以依据主模式(master mode)来配置, 配置保存在 DTC 中. 在不同的主模式下, 每个 MFD 可以最多为 DA 按钮分配三个不同页面. 设置完成的页面格式会用显示 DA 助记符显示在 MFD 上. 通过按下对应的 OSB 按钮, 可以在三个不同模式间轻松切换, 也可以通过使用 HOTAS 上的 DMS 按钮在不同模式间循环: DMS 右键选择当前 DA 模式右侧的模式, DMS 左键选择当前 DA 模式左侧的模式. 请注意, 您不能同时在两个 MFD 上显示相同的页面, 因此当左侧 MFD 显示 FCR 时, 你想尝试在右侧 MFD 上显示 FCR,  则 FCR 对应的左侧 MFD DA 按钮将变为空槽.

虽然建议在 DTC 中为每个主模式设置三个最需要的 MFD 页面, 但也可以通过 MFD 操作直接修改 DA 按钮对应的页面. 首先选择主模式, 然后 MFD 会显示默认页面, 此时按下高亮显示的 DA 按钮, MFD 显示 MENU 页面, 从页面中选择您想要访问的子页面, DA 按钮上会显示新页面助记符, 替换掉前一个关联的子页面.

* SMS(Stores Management System) page
* HSD(Horizontal Situation Display) page
* DTE(Data Transfer Equipment) page
* TEST page
* FLCS page
* FLIR(Forward Looking Infra Red) page
* TFR(Terrain Following Radar) page
* WPN(Weapon) page
* TGP(Targeting Pod) page
* FCR(Fire Control Radar) page
* BLANK page
* HAD(HARM Attack Display) page
* RCCE(Reconnaissance) page
* RESET page.

## Sensor of Interest (SOI)

有时候你需要选择两个 MFD 中的一个开始工作. 要让系统知道您选择的是哪个 MFD, 您需要使用 SOI 机制. 想象一下这个例子: 左侧 MFD 显示的 FCR 页面是 SOI, 右侧 MFD 显示 HSD 页面. 你想删除右侧 HSD 页面上的威胁环, 但如果你移动光标, 两个 MFD 会同时响应. 为了告诉系统你想在 HSD 页面上做操作, 你需要将 HSD 设置为 SOI. 要做到这一点, 只需在向下移动 HOTAS 上的 DMS 按钮. SOI 将从一个 MFD 切换到另一个. SOI 的视觉提示是 MFD 外侧的大方框. 如果 MFD 不是 SOI, NOT SOI 将显示在屏幕中央.

## HSD page

HSD 显示页面提供了以飞机为中心的上帝视角, 其中包括您的雷达锥, 同心距离环, 雷达光标位置, 靶心位置(或方位和距离), 带有转向点的 INS 飞行计划, 路线, PPT(预先计划威胁点)及其可编程范围环, IDM 信息等.

OSB #19 和 #20 用来设置 HSD 范围.

HSD 内环被 4 个基数标记分开. 小旗指示正北. 较长的线表示正南, 两条较小的线分别表示东和西. 这些基本方向对于快速确定你的航向非常有用. 它们对于感知靶心方位来说也非常有帮助. 通过连接正北和正南标记, 你可以绘制一条参考线, 用来快速识别靶心的方位.

OSB #1 标记为 DEP(抑制), 按下后按钮标记为 CEN(居中).
在 DEP 中, 飞机标记显示在 MFD 中心下方, 从底部向上四分之一的位置, 此时飞机前方的可见范围要好于后方. 当选择 CEN 时, 飞机标记显示在 MFD 中间, 飞机前方的可见范围和后方一样. 请注意, DEP 或 CEN 显示的范围是不同的. DEP 的最小范围是 8Nm, 而 CEN 的最小范围可以低至 5Nm, 另外 CEN 模式可以更好的匹配 FCR 范围.

OSB #2 标记为 DCPL(解耦), 当按下时切换到 CPL(耦合), 将 HSD 显示范围关联到 FCR 范围. 在 CPL 模式下, OSB #19 和 #20 被禁用(不显示箭头), HSD 将根据 FCR 标度(范围)改变标度.

为了保持 HSD 和 FCR(有助于 SA)之间的良好对应, 建议在 CPL 和 CEN 模式下使用 HSD. 这可确保 HSD 和 FCR 页面显示的范围相同, HSD 显示的范围将与 FCR 范围一起同步变化.

OSB #3 标记为 NORM/EXPAND, 当 HSD 为 SOI 时, 改变 HSD 的 FOV. 这也可以使用 HOTAS 小指开关(inky switch)完成.

OSB #5 是 HSD 的 CNTL(控制)页面. 按下时, 大多数 OSB 按钮上会显示一系列选项. 高亮的选项表示已经处于激活状态, 当按下已经高亮显示的 OSB 时, 该选项变为去使能状态(未高亮显示), 并且相关的符号在 HSD 页面隐藏.

显示选项包括:
FCR: FCR 锥形和幽灵雷达光标
PRE: 计划引导点及威胁环
AIFF: IFF响应(未实现)
LINE1: DTC 第 1 行 (导航点 31 到 35)
LINE2: DTC 第 2 行 (导航点 36 到 40)
LINE3: DTC 第 3 行 (导航点 41 到 45)
LINE4：DTC 第 4 行 (导航点 46 到 50)
RINGS: 以 ownship 为中心的同心环
ADLINK: 空对空数据链信息
GDLINK: 空对地数据链信息
NAV1,2 和 3: INS飞行计划, 但 BMS 只实现了 NAV1

OSB #7 在主 HSD 页面上标记为 FZ, 作用是冻结当前世界位置和本机方向的显示. 飞机现在可以继续移动, 但是 MFD 上的世界位置保持不变而飞机位置不再固定在 MFD 中心. 再次按下 FZ 按钮可解除 HSD 世界位置冻结.

### HSD 游标

当 FCR和 HSD 同时显示且 FCR 为 SOI 时, HSD 将在雷达扫描区域内显示幽灵雷达光标的移动. 当 HSD 成为 SOI 时, 幽灵雷达光标被替换为 HSD 光标, 其显示为交叉十字. 然后, 任何后续的光标移动都将移动 HSD 光标而不是雷达光标. HSD 光标可用于选择感兴趣的导航点, 并在 HSD 上显示的 PPT 上打开或关闭特定威胁环. 在 HSD 显示的导航点上按下 TMS up 将激活该导航点成为感兴趣的新导航点, 在 PPT 上按下 TMS down 将停用相关的威胁环. 该光标在 HSD 右侧有一个自己的靶心读数, 位于 OSB #9 和 #10 按钮之间.

### 光标碰撞

要更改 HSD 的显示范围, 您可以在 DCPL 模式下使用 OSB #19 和 #20, 或在 CPL 模式下更改 FCR 范围. 还有第三种方法可以改变 HSD 显示范围(对 FCR 也有效), 称为光标碰撞. 通过将光标移动到显示屏的上边缘或下边缘来完成. 当光标到达时, 范围将相应地自动向上或向下变化. 这主要是在 FCR 上完成的, 因为大多数时候 FCR 是 SOI, 但是当 HSD 是 SOI 时它也在 HSD 上也有效. 如果 HSD 处于 CPL 模式, 则任何顶部或底部边缘的第一次碰撞都会将 HSD 切换到 DCPL 模式，随后的碰撞将改变范围.

### 靶心符号

HSD 页面上有许多不同的靶心符号. 您可以找到当前位置相对于靶心的方向和距离(BRAA)信息, 也可以找到雷达光标相对于靶心的方向和距离信息, 以及 HSD 光标相对于靶心的方向和距离信息.

本机 Bullseye 读数始终显示在 HSD 的左下角. 当在 UFC(LIST-MISC-BULL 页面)中选择 Bullseye 模式时, 它与 HUD 左下角显示的信息相同. 如果 Bullseye 模式未选择, 此符号将由与当前激活导航点相关的 mini flight director 替换. 当然, Bullseye 是青色的, 包括圆圈下方的方位和圆圈中的距离. 距离限制为 2 位数, 因此如果Bullseye 距离超过 99Nm, 则距离显示为空.
幽灵雷达光标靶心信息在 MFD 的左侧以白色显示. 第一个数字是方位, 第二个数字是以 Nm 为单位的距离.
HSD 光标的 Bullseye 读数(HSD SOI)位于屏幕的右侧, 并且具有与幽灵雷达光标 Bullseye 读数相同的结构, 即先方位后距离.

![Bullseye_symbols](https://raw.githubusercontent.com/21moons/memo/master/res/img/BMS/Bullseye_symbols.png)

## TEST page

TEST 页面显示各种内置测试(BIT). BIT1 和 BIT2 显示在飞行期间遇到的维护故障列表(MFL). 每次故障时都会记录以下内容:
1. 发生故障的子系统(与 PFL 相同)
2. 测试失败编号
3. 该子系统的故障数量
4. 首次故障发生时间(自 FCC 上电以来)

此外有两个伪故障会被强制记录; 起飞时间(TOF)和着陆时间(LAN). 每当空速达到 120节且机轮收起时, 就会记录 TOF. 当机轮放下且空速小于 80 节时, 就会记录 LAND.

按 CLR 按钮将清除故障列表并启动故障调查. 最多可记录 17 个故障(包括两个伪故障). 如果存在超过 17 个故障, 则最先发生的故障将被新的故障替换. 飞行结束后飞行员可以在 DTC 文件中查看飞行过程中的完整 MFL. 所有 MFL 都在飞行结束后都会记录在 dtc_last_flight_faults.txt (\User\Logs) 文件中. 每次新任务都会覆盖该文件.

在冷启动时, TEST 页面上显示故障是正常的. 系统上线后这些故障将会清除. 如果遇到实际问题, 可能需要使用OSB #3 清除 MFL 以启动故障调查. 在清除 MFL 后如果系统仍然存在故障, 故障将再次显示在 MFL 上, 允许飞行员采取适当的措施来解决问题.

PAGE 1
OSB #1 BIT1 Indicates BIT1 tests. Pressing this button changes to the BIT2 page
OSB #3 CLR Clears the Maintenance Fault List (MFL) if displayed in the centre of the MFD
OSB #6 MFDS MFD Self-Test (N/I)
OSB #7 RALT Radar Altimeter test
OSB #8 TGP Targeting Pod test (N/I)
OSB #9 FINS Fixed Imaging Navigation Set (N/I)
OSB #10 TFR Terrain Following Radar Test (N/I)
OSB #16 RSU Rate Sensor Unit (N/I)
OSB #17 INS Inertial Navigation System test (N/I)
OSB #18 SMS Stores Management System test (N/I)
OSB #19 FCR Fire Control Radar test switches the MFD to the FCR page and starts the FCR BIT
OSB #20 DTE Data Test Loading (N/I)

PAGE 2
此页面包含其他内置测试. OSB 1(BIT2)表示这些是 BIT2 测试. 再次按此按钮将切换到 BIT1 页面.
OSB 3 CLR Clear fault list (N/I)
OSB 6 IFF1 IFF1 self-test (N/I)
OSB 7 IFF2 IFF2 test (N/I)
OSB 8 IFF3 IFF3 test (N/I)
OSB 9 IFFC IFF Mode C test (N/I)
OSB 10 TCN TACAN Test (N/I)
OSB 19 TISL Target Identification Set, Laser (N/I)
OSB 20 UFC Up-Front Controls (N/I)

## SMS page

SMS 页面将根据您选择的主模式而有所不同:

### SMS in NAV mode

SMS 页面将以图形方式显示系统清单, 描绘飞机本身及其挂架的装载情况.

在真正的喷气机中, 这个页面是完全可编程的, 但在 BMS 中只是忠实的反映了预加载情况.

请注意, OSB #11(DA) 上常见的 DCLT 页面被替换为 S-J 页面. 按下该按钮进入 S-J 主模式和并显示相关的 MFD 子页面.

### SMS in A-G mode

![SMS_in_A-G_mode](https://raw.githubusercontent.com/21moons/memo/master/res/img/BMS/SMS_in_A-G_mode.png)

MFD 中心显示的信息是当前的武器的投放设置(CNTL 页面设置)
* OSB #1 显示当前的主模式, 按下后将进入 A-G strafe gun 子页面.
* OSB #2　用于切换投弹模式 CCIP-CCRP-DTOS-LADD&MAN. 每个都有一个相关的子页面.
* OSB #4 标记为 INV 并显示 Inventory 页面, 一般非 NAV 主模式才需要它.
* OSB #5(CNTL) 是当前选定武器的控制页面.
* OSB #6 显示当前激活的武器, 武器类型和数量. 按下时将按顺序选择另一种 A-G 武器. 请注意, 侧杆(sidestick)上的 MSL STEP 按钮不执行相同的功能-它会切换到下一个装有相同类型 A-G 武器的挂架. 这允许飞行员在需要的情况下对相同类型的导弹进行独立的预编程, 例如用于 POS EOM 发射的 HARM 导弹.
* OSB #7 设置当前使用的用于自由落体的 A-G 武器投放的配置. SMS 能够保存两个不同的武器配置文件 PROF1 和 PROF2. 默认情况下, CNTL 设置关联 PROF1, 但是如果你按下 OSB #7 并选择 PROF2, 所有设置将记录到 PROF2. 这允许飞行员保存两个武器投放配置并根据实际情况轻松的从一个切换到另一个.
* OSB #8 设置 A-G 武器的单个投放(SinGLe)或成对投放(PAIR). 按下按钮将在 SGL 和 PAIR 之间切换.
* OSB #9 是 A-G 武器的 ripple release (涟漪投放)的投放间距. 按下后 SMS 进入特定的页面, 可以输入以英尺为单位的间距值. 当投放多个炸弹时, 可以用来正确地分布杀伤范围.
* OSB #10 是 A-G 武器 ripple release (涟漪投放)的投放数量. 每次你按下 pickle 时都会投放对应数量的武器, 按下后会进入特定页面并输入新值.
* OSB #18 是 A-G 商店的引信设置. 按下后将会在 NOSE, TAIL 和 NSTL(包括 NOSE 和 TAIL)之间切换.

#### SMS A-G Control page

按下 OSB #5 显示 CNTL 页面.

顶部和底部 OSB 行与主 SMS 页面相同. 不同的是左边一排 OSB 按钮(#16 到 #20)和右侧的 OSB #6, 对应 5 种不同的武器设置: C1, C2, C3, C4 和 LADD.

C1(OSB #20)与通用武器或激光制导武器有关, 提供两种不同的引信开启延迟(投放后). 一个用于 NOSE 引信, 第二个用于 TAIL 引信. 按下 OSB #20 可以进入子页面设置两个时间.

C2(OSB #19) 与集束炸弹或任何需要空中散布 Burst Altitude(BA) 的武器有关. 按 OSB #19 将进入一个 SMS 子页面, 其中包括引信开启延迟(投放后)和可以设置散布爆破的高度.

C3(OSB #18) 与 C2 相同, 是集束炸弹的第二个配置.

C4(OSB #17)专门用于双引信集束炸弹. 在此页面上可以设置第一个引信开启延迟(AD1), 第二个引信开启延迟(AD2)和散布爆破的高度(BA).

OSB #10是计划释放角度的设置, 计算机需要此值来计算 DTOS 投弹的正确符号.

### SMS in A-A mode

* OSB #1 显示当前的主模式, 按下后将进入 A-A gun 子页面.
* 当携带红外制导武器时, OSB #3 处于活动状态并显示 SPOT. 它将 AIM 导引头从 SPOT 改为 SCAN.
* OSB #4 标记为 INV 并显示 Inventory 页面
* OSB #5(CNTL) 是当前选定武器的控制页面.
* OSB #6 显示当前激活的武器, 武器类型和数量. 按下时将按顺序选择已挂载另一种 A-A 导弹的挂点. 请注意, 侧杆(sidestick)上的 MSL STEP 按钮在按下 0.5 秒后将执行相同的功能, 如果按下的时间不到 0.5 秒, 它会切换到下一个挂有相同类型 A-A 导弹的挂点.
* 携带红外制导武器时会显示 OSB #8(WARM/COOL), 标示导弹红外导引头状态. 按下 WARM 可以冷却导引头. 如果主模式是 DGFT 且 MASTER ARM 开关处于 ARM 或 SIM 位置, 该操作将自动完成.
* OSB #18 依赖于导弹类型. 对于雷达制导导弹, 它设定 PRF 范围, 并在未知, 大, 中和小之间选择目标类型. 大型用于轰炸机, 中型用于战斗机, 小型用于拦截导弹(未实现). 对于红外制导导弹, 它在 BP(旁路) 和TD (阈值检测)之间选择. 设置为 TD 时, 导弹会自动解禁制导头(Uncaged). 当设置为 BP 时, 导弹需要手动解禁制导头.
* OSB #19 切换 SLAVE 和 BORE 模式. 雷达制导导弹可以设置为 SLAVE 模式或 BORE 模式. 当设置为 SLAVE 模式时, 导弹由火控雷达引导, 当设置为 BORE(孔径)时, 导弹指向 HUD 上机炮标记下方六度, 并将无制导发射, 发射后导弹将立即打开自己的雷达开始引导. 这是一个 MADDOG 发射, 因为导弹将跟踪它看到的第一个目标(无论敌友).

## Selective Jettison (S-J) page

当主模式是 NAV, A-G或A-A时, 可以进入 SMS 页面并按下 OSB #11 来访问 S-J 页面. Selective Jettison 也是一种主模式.
这使得飞行员能够从飞机上丢弃未解锁和未制导的武器和挂架. 该页面只会展示可丢弃的挂载供选择. 飞行员按下 S-J 页面上显示的挂载附近的 OSB 按钮, 所选挂载将在 S-J 页面上突出显示, 表示它已被选中. 如果选中挂载对应的挂架也是可抛式的, 可以通过再次按下同一个 OSB 来选择, 第三次按下 OSB 将取消当前的所有选择. 飞行员可以在 S-J 主模式下预选一个 S-J 配置, 在主模式切换时该配置将被保存. 当 MASTER ARM 开关在 ARM 位置时, 使用 pickle 按钮即可丢弃挂载. 在挂载被释放后, 对应显示将从 S-J 页面中移除, 相关武器数量为零. S-J 模式可以无视任何其他武器设置.

## TFR page

该页面仅适用于搭载了 LANTIRN 吊舱的 F-16(Block 25/30/32/40 EAF/42/52+, KF-16 & F-16I).

TFR 是一种短程(36000 英尺)前下视雷达, 可让你在非常低的高度跟踪地形, 并具有自动爬升保护功能.

TFR 的主要功能是沿飞机飞行路径检测地形, 并为飞行员(在手动模式下) 或 FLCS(在自动模式下)生成垂直方向上的爬升/下降命令(g 命令), 以便飞行员在飞行中与地面保持设置的距离.

TFR 功能包括地形跟踪, 障碍物警告和有限制的恶劣天气飞行. 在正常模式下, 可以将 SCP 设置为 200 到 1000 英尺 AGL 之间. 除了 TFR 的正常操作之外, 其他模式可用于特定的操作条件. 这些模式包括天气(WX), 低拦截概率(LPI)和极低净空(VLC).

![TFR_MFD](https://raw.githubusercontent.com/21moons/memo/master/res/img/BMS/TFR_MFD.png)

TER 按钮位于 MFD 菜单的 OSB #18 按钮.

TFR 使能的条件是机头下方的挂载点(chin hardpoints)上电且雷达高度计可操作.

TFR 包含七种操作模式 OFF - BIT - STBY - NORM - LPI - WX - VLC.

* OFF: TFR 未通电.
* BIT: 从 MFD TEST 页面进入.
* STBY: 吊舱处于待机模式且无法操作.
* NORM: 正常操作模式, 可通过 OSB #20 访问. 它有 3 个子模式:

  + MAN TF: 飞行员通过 HUD 上显示的 FPM 提示(MAN TF框)来保持选定的高度. 该模式下飞行员需要自行操作驾驶杆, 不过如果 MANUAL TF FLYUP 开关处于启用状态, TFR 也可以提供自动爬升保护服务. 此时MISC 面板上的 ADV 指示灯未亮起.
  + AUTO TF: 飞行控制计算机使用 LANTIRN 吊舱中生成的垂直加速度指令来操纵飞机维持所选 SCP(Set Clearance Plane 设定间隙平面)的高度. 在 AUTO TF 模式下, MISC 面板上的 ADV 指示灯点亮顶部指示灯(top indicator), 并在 HUD 上的 FPM 附近显示水平线. 飞行员可以通过按下操纵杆上的 paddle switch 来旁路 TFR(就像自动驾驶仪一样). 在按下 paddle switch 时，MISC 面板上的 STBY(ADV 底部)灯亮起.
  + Blended TF: 自动驾驶仪用于保持特定的气压高度. 如果 LANTIRN 吊舱检测到飞机高度低于了所选的最小 AGL, 则系统将自动操纵飞机以保持最小 AGL, 直到远离起伏不平的地形. A/P PITCH 开关处于 ALT 或 ATT HOLD 位置. STBY ADV 指示灯亮起. 水平线可见.

* LPI: 低拦截概率. 此时 RF 开关置于 QUIET 位置, TFR 在保证功能的情况下尽可能减少辐射. 该模式通过按下 OSB #19 激活, 或者在 RF 开关置于 QUIET 时自动激活.(RF 开关置于 SILENT 会自动将 TFR 置于 STBY 模式)
* WX: 用于恶劣天气(下雨), 以最大限度地减少由于雨或雾中的雷达回波冲突导致的非指令飞行. 它可以通过 MFD TFR 页面 OSB #17 或 ICP 上的 WX 按钮激活.
* VLC: 非常低的间隙(100 英尺的 SCP), 从 OSB #10 进入. 此模式仅适用于相对平坦的地形或水面.

The TFR E-scope display will start displaying a visual representation of the terrain ahead. The pilot can then decide if he wants MAN TF or AUTO TF. AUTO TF is engaged by depressing the ADV indicator/pushbutton on the MISC panel. Automatic fly-up protection is provided in AUTO TF; in MAN TF you must have the MANUAL TF FLYUP switch in ENABLE.

TFR 有 3 种骑行选项(Ride options): 通过 OSB #2 在 HARD - SOFT - SMTH(平滑) 模式中切换. 通过将 TFR 置于 NORM 模式来激活 TFR 并选择所需的 SCP. 两个选项都 TFR 页面上高亮显示. DED 中的 A-LOW 应设置为 SCP 的 90%. 此时 TFR 电子示波器显示将开始显示前方地形的直观表示. 然后飞行员可以决定他是否需要使能 MAN TF 或 AUTO TF. 通过按下 MISC 面板上的 ADV 指示灯/按钮启用 AUTO TF. AUTO TF 提供自动爬升保护; 而在 MAN TF 中, 您必须将 MANUAL TF FLYUP 开关置于 ENABLE 来使能自动爬升.

## DTE page

通过按下 OSB#8 从 MFD 菜单页面访问 DTE 页面.

它用于将任务规划期间准备的数据盒(Data Cartridge)加载到飞行器计算机中. 通过按下 OSB #3 完成加载(通常在冷启动完成后或在 CNI 切换到 UFC 之前).

每个显示系统(FCR, DLINK 等)在加载过程中将按加载顺序高亮显示, DTC 更改应在 DED 中可见(VHF 和 UHF 等的预设).

## FLCS page

通过按下 OSB #10 从 MFD 菜单页面访问 FLCS 页面.

FLCS 页面提供与 FLCS 相关的所有内容的概述. 该页面通过显示最多 5 个 FLCS 故障或告警为 FLCS 故障提供备份显示. 与 PFL 一样, 警告显示在 "> <" 中间. 当故障超过 5 个时, 可以通过按标记为 MORE 的 OSB #20 来查看.

这在 UFC 故障的情况下非常有用, 这种情况下 DED 和 PFL 都会变为空白, 飞行员感知不到任何故障, 而通过 FLCS 页面, 至少还可以查看与 FLCS 相关的故障.
在配备数字 FLCS 的飞机上, FLCS 页面内容可以通过切换到 DBU 模式然后返回来重置, FLCS 页面还将报告 FLCS BIT 的状态和 DBU(数字备份)的状态.
FLCS RESET 开关也可以清除 FLCS 页面.

BUS FAIL 会导致 FLCS 页面显示 OFF, 不能查看 FLCS 故障.
如果飞机配备数字 FLCS, 当飞机在地面时, 维护功能和 addresses 显示在 MFD 的中部和底部. 在飞行中维护功能会被删除, FLCS 页面通常是空白的, 除非存在 FLCS 故障.

## Forward Looking Infra-Red (FLIR) page

FLIR 页面仅在兼容的 F-16(Block 25/30/32/40 EAF / 42/52 +, KF-16 和 F-16I)的机头挂架上挂载 LANTIRN 吊舱时可用.

FLIR 是一款用于夜间低空导航的前视红外摄像机. FLIR 是 LANTIRN 系统中的导航吊舱, 与 TFR 吊舱比邻. 在 FLIR 正常工作前必须给机头下方的吊舱挂锁点通电.

FLIR 需要在 8 到 15 分钟的冷却后才能使用, 因此如果任务需要 FLIR, 请在冷车启动期间尽快启动. 一旦 NOT TIMED OUT 消息从 MFD 上消失, 意味着 FLIR 已经准备好运行.

OSB #18 将 FLIR 置于待机状态. OSB #20 将 FLIR 置于操作模式(OPER). 一旦进入操作模式, FLIR 页面将显示 pod 前面的红外视图. 通过向上旋转 BRT ICP 轮, 可以在 HUD 上显示红外图像. 可以使用 ICP 上面的上下 FLIR 箭头更改 FLIR 级别. 当前增益和电平值显示在 FLIR MFD 的左上角.

OSB #10是视轴选项(boresight option). 在地面上, FLIR 摄像机对准 HUD 顶部. 它可能会导致视差错误. 视轴用于更好地匹配 HUD 中的图像与 FLIR 相机在特定范围内的成像. 按下 OSB #10 按钮, 对应的 BSGT 助记符将高亮. 然后可以使用光标旋转 HUD FLIR 图像. 不要将视轴设置在近物上, 建议在飞行中对准大型物体, 例如山的边缘或远方的道路. 一旦两个图像正确叠加, 再次按下 OSB #10 关闭 BSGT. 一旦 FLIR 图像显示在 HUD 上, MFD FLIR 页面就不需要处于活动状态, 但建议选择一个 DA 按钮放置以便于进行视轴操作.

HUD 提供 LOOK-INTO-TURN(LIT) 和 SNAPLOOK 功能.
LIT: 当倾斜角度高于 5° 时, 按住 DMS UP 可以将 FLIR 视图稍微偏移至转向处以观察障碍物. 当释放 DMS UP 时, FLIR 图像恢复为前视.
SNAPLOOK: 通过按住 DMS UP 并沿任何方向移动光标来进行大范围观察, 即使在转弯时也可以操作. 当释放 DMS UP 时, 视图将恢复为前视.
当 LIT 或 SNAPLOOK 处于活动状态时, HUD 上的 FPM 标记会变为虚线.

## WPN page

通过按下 OSB #18 从 MFD 菜单页面访问 WPN 页面. 它使飞行员能够访问机载武器传感器.

诸如 AGM-65 小牛 和 AGM-88 哈姆等一些武器自带可用于获取和锁定目标的传感器. 由于传感器在导弹上, 所以一旦导弹发射, MFD 就无法再显示传感器图像.

哈姆(HARM)
选择 HARM 并准备就绪后, POS EOM 将显示在 WPN 页面上. 详情请参阅武器特定文档.

小牛(Mavericks)
一旦红外线导引头被解锁(并且移除了任何球罩), 来自导弹头的红外图像将显示在 WPN MFD 页面中. 详情请参阅武器特定文档.

* OSB #1: 报告状态
* OSB #2: 切换 PRE-VIS-BORE 模式
* OSB #3: 设置视野
* OSB #5: 访问 "控制" 页面
* OSB #6: 显示当前所选武器的类型
* OSB #7: 切换极性(polarity)
* 底行具有通常的DCLT，直接访问和SWAP按钮
* OSB #20: 选择 SLAVE 选项

## TGP page

通过选择 MFD 菜单页面中的 OSB #19 进入 TGP 页面.

当机头下方挂架上挂载 LANTIRN 或 SNIPER 吊舱并通电时, TGP 处于激活状态. 上电通过 SNSR PWR 面板的 RIGHT HDPT 开关完成, 上电后 TGP 需要冷却, TGP 页面将一直显示 NOT TIMED OUT 直至 pod 准备就绪.

## HAD page

通过选择 MFD 菜单页面中的 OSB #2 进入 "HARM 攻击显示" 页面. (如果你已经安装了 HTS 吊舱并且左侧挂载点已上电).

任何主模式中都可以选择 HAD, 并且 HAD 与 HSD 共享许多常见的显示特性. HAD 光标移动功能和 FOV 扩展功能(OSB #3 或小指开关)类似于 HSD. 飞行员可以设置 HAD 范围(HAD 被设置为 SOI), 方法是将光标上下调整到 MFD 显示范围之外或按 OSB #19 和 #20.

HAD 不是发射 AGM-88 的唯一方式. 只要战机挂载了 HARM, 还可以通过 WPN 页面下的 POS 和 HAS 模式(此时 HAD 不能设置为 SOI)来进行发射. HARM WEZ 标记基于 AGM-88 的最大攻击范围, 并根据战机的速度和高度增加或减小. 如果 HARM WEZ 大于所选择的显示范围, 那么将用虚线表示.

检测到的辐射源用以下颜色标记:
绿色 = 辐射源未激活
黄色 = 辐射源激活
红色 = 辐射源正在跟踪
闪烁红色 = 辐射源正在制导

## BLANK page

通过选择 MFD 菜单页面中的 OSB #1 显示空白页面.

## RCCE page

RCCE 页面(菜单页面中的 OSB #4)用于与侦察吊舱连接, BMS 中未实现.

## RESET MENU page

通过菜单页面中的 OSB #5 访问 RESET 页面, 该页面但在 BMS 中没有任何用途.

## FCR page

通过菜单页面中的 OSB #20 访问 Fire Control Radar 页面.
FCR 本身应该用一份单独的文件来描述, 这里只是一个简短的介绍, 希望能够满足你的需求. 显而易见的是, 这个页面显示的是雷达看到的内容. 基于当前的主模式, 它可以设置为 A-A 雷达(包括其子模式)或 A-G 雷达.

### FCR in A-A modes

FCR 通过 SNSR 面板上的 FCR 开关开启供电, 一旦通电将首先进入可能持续几分钟的 BIT(内置测试). 如果 FCR 在飞行中关闭超过 4 秒, 重新开启后需要再次进行 BIT. BIT 完成后,FCR 变为可用, 通常默认为 A-A CRM(混合雷达模式)模式.

OSB #1 表示 FCR 的当前模式, 如果按下会显示一个页面, 页面列举了可以选择的所有其他模式: 左侧是 CRM 和 ACM(空战机动), 右侧是 GM(地面映射), GMT(地面移动目标), SEA(反舰)和 STBY(待机). 按下任何对应的 OSB 按钮将进入该模式. 请注意: BCN(Beacon) 模式当前未实现.

OSB #2 选择相应的子模式. 如果 FCR 在 CRM 中, 则子模式是 RWS(边测距边扫描), ULS(= LRS 远距扫描), VSR(= VS 敏捷搜索), TWS(边跟踪边扫描), 如果 FCR 处于 ACM 模式, 则子模式为 20(HUD), 摆动(Slew), 凝视(Bore), 60(垂直).

OSB #3 是 FOV, 并非在所有模式下都可用. 它仅在当前模式支持时显示, 在 NORM 和 EXP 之间切换. 也可以通过你的操作杆上的小指开关(S3)来进行切换. 切换到 EXP 时, 光标周围的区域会展开, 并由 MFD 上的蓝色方块表示. EXP 标签也会闪烁.

OSB #4 将 FCR 置于待机模式, 该模式会从 MFD 中删除所有符号, 并高亮显示 OVRD.再次按下 OSB #4 将恢复为可操作模式.

OSB #5 进入 FCR 控制页面. 按下后 MFD 左右两边的 OSB 将显示不同的选项, 但其中大部分尚未实现. 例外情况是目标历史记录(TGT HIS), 它设置接敌后发现的其他敌人的数量. A-A 和 A-G FCR 模式下的控制页面相同, 本章后面的 A-G FCR 部分对此进行了详细说明.

OSB #6 是 IDM 模式. 它默认为 ASGN(分配), 但可以切换为 CONT(连续)或 DMD(按需).

OSB #7, #8, #9 和 #10 标记为 1, 2, 3 和 4, 对应于您的团队成员. 用于选择 IDM 数据的发送对象.

MFD 底部是常见的直接访问按钮, OSB #11 上的 DCLT 和 OSB #15 上的 SWAP.

OSB #17 是条形扫描(bar scan), 可以从 1 bar 切换到 4 bar. FCR 通过移动天线来对前方进行扫描. 天线发射的波束在垂直方向上的扫描角度通常不超过 4.9°. 因此, 如果将 bar 设置为 1, 雷达将仅扫描一个 4.9° 的空域切片. 设置为 2 雷达将扫描 2 个 4.9° 的空域, 设置为 4 它将扫描 4 个. 扫描 1 bar 需要 2.5 秒, 另外需要花 0.5 秒将天线移动到下一个 bar, 因此完整的 4 bar 扫描需要 12 秒, 而 1 bar 扫描只需 2.5 秒. 显然减少 bar 的数量会加快扫描速度, 但同时也会缩小搜索范围. 无论 bar 如何设置, 雷达都可以通过节流阀上的 ANTENNA 俯仰控制来上下倾斜.

![bar_scan](https://raw.githubusercontent.com/21moons/memo/master/res/img/BMS/bar_scan.png)

OSB #18 是方位角设置, 在 60°(A6), 30°(A3) 和 10°(A1) 的锥形之间切换. 在 FCR 上使用蓝色垂直线以说明缩小的搜索区域, 并且 HSD 上也用类似的图标显示. 显而易见的是, 搜索区域越小, 扫描速度越快. 缺点则是会错过扫描区域以外可能的敌机. 当方位角小于 60° 时, 搜索区域可以使用光标移动.

在模式允许时, OSB #19 和 20 用于设置 FCR 扫描范围, 按下 OSB #19 会降低范围, 按下 OSB #20 会增加范围. 设置范围的另一种方法是将光标移动到 MFD 的顶部或底部边缘(此时 FCR 为 SOI).

![A-A_FCR_PAGE](https://raw.githubusercontent.com/21moons/memo/master/res/img/BMS/A-A_FCR_PAGE.png)

### FCR in A-G modes

进 入A-G 主模式后, FCR 将自动切换到 A-G FCR. 如果需要将 FCR 设置为其他模式, 可以使用菜单页面选择任何子模式. Ground 子模式包括:

GM: Ground Map
GMT: Ground Moving Target
SEA: for naval targets
BCN: Beacon (not implemented in BMS)

对于所有的 A-G 子模式, A-G FCR 的工作机制是相同的, 只有存在针对不同目标的灵敏度差异. 本章中我们将使用 Ground Map(GM) 模式来说明 A-G FCR.

初次进入 GM 模式时, FCR 扫描前方并指向当前的 SPI. 到达 SPI 所需的时间显示在 FCR 的右下角, 在图中是 33 秒. 该计时器也可用于其他场景, 例如拉起提示和 LGB 炸弹的命中计时. 这取决于当前主模式, SOI 和 SMS.

就像 A-A FCR 一样, 依据 Bullseye 的使能情况, OSB #17 附近要么显示到 Bullseye 的方位和距离(Bullseye 使能), 要么显示到当前航向点的方位和距离(Bullseye 不使能). 下图显示的方位和距离为 189 00, Bullseye 未使能. 如果在 DED(LIST 0 8 BULL 页面)中选择了 Bullseye 模式, 则自建 Bullseye 的相对位置将显示在 MFD 的左下角. 下图中 Bullseye 未使能, 因此 MFD 左下角显示的是飞机参考符号(W)及方位角转向线(竖直的线段). 在 BMS 4.33 中, 这可能会根据分段而有所不同, 即使选择了 BULLSEYE 模式, 较新的分段也始终显示飞行操作符号(flight director symbol).

A-G 雷达能够使用不同的颜色深度来绘制地形, 目标雷达回波返回显示为白色亮点. 可以使用节流阀上的 RNG 旋钮或机舱左上角的 GAIN 摇臂开关来设置 A-G FCR 增益. 增益会改变地形图像的对比度, 并显示在 MFD 左上角的增益标记. 下面的两张图片说明了 A-G FCR 增益设置为最大值(左图 - 注意增益计设置为最高点)与设置为最小值(右图 - 注意增益计一直到刻度底部). 请注意, 增益刻度在 A-A 和 A-G FCR 模式都显示.

OSB #1 显示当前的子模式. 按下后 FCR 将显示所有子模式.

OSB #2 设置 AUTO 范围切换或 MANUAL 范围切换. 这种切换可用于 GM, EXP, DBS1, DBS2, GMT 和 SEA. 如果设置为 MANUAL, 必须使用 OSB #19 和 20手 动更改范围. 在 AUTO 中, 当光标位置进入 FCR 的下一个可用范围设置时, 范围会自动切换. 它默认为 AUTO.

OSB #3 是视场(FOV)设置, 具有 4 个级别: NORM, EXP, DBS1 和 DBS2(多普勒波束锐化). 当A-G FCR 设为当前 SOI 时, 操纵杆上的小指开关可以切换可用的 FOV 设置. EXP 模式扩展了光标周围的雷达显示, 并将其置中显示. DBS1 改进了 EXP 模式, 供了更多细节但不再放大. DBS2 是最高放大倍数. EXP 可用于 GM, GMT 和 SEA. DBS1 和 2 仅适用于 GM. 它们不适用于 GMT 和 SEA.

OSB #4 是暂停模式. 按下后雷达进入待机状态, FCR MFD 变为空白. OVRD 高亮显示. 要恢复雷达到可操作必须再次按下 OVRD 按钮.

OSB #5 进入控制页面. 请注意在 A-G 和 A-A FCR 模式下 FCR CNTL 页面是相同的. 按下后 MFD 显示内容保持(并继续更新), 但 OSB 按钮变为 CNTL 选项. 目前, BMS 没有为 A-G FCR 启用这些控制选项. 可以点击这些选项, 但代码中没有对应的实现. 在真实的飞机中, OSB #6(未实现)改变雷达信道以避免来自其他飞机的干扰, OSB #7 是标记强度按钮, 范围从 1 到 4. 这允许范围标记(range markers)的强度不同于通过 SYM 摇杆(未实现)设置的全局符号强度(overall symbol intensity). OSB #8 按照从窄到宽的顺序切换带宽(未实现). OSB #9 用来设置信标延迟, 设置区间为 0.00 到 99.9 (未实现). OSB #10 是电源管理, 在 PM ON 和 PM OFF 间切换(未实现). OSB #17 在 ECCM 1 级和 2 级间切换(未实现). OSB #18 设置了目标历史记录, 这是 A-A FCR 控制页面中唯一可用的选择. 历史记录可以设置为 1 到 4, 为 A-A 中的雷达目标提供踪迹. OSB #19 是 Altitude Liner 消隐器选项, ON 或 OFF(未实现). OSB #20 是 A-G&A-A 雷达上的水平衰减选项, 过滤掉低于某些径向速度的目标(未实现).

OSB #6 是 BAROMETRIC(未实现).

OSB #7 FZ 就是 Freeze. 按下时, A-G FCR 图像被冻结, 地面稳定点的坐标显示在 MFD 的右上部分. 方位角和距离显示在 MFD 的左下部分. 只要 FREEZE 模式处于激活状态, FZ 助记符就会保持高亮显示. 如果要放大并精确锁定特定目标, 该模式特别有用. 解冻请再次按下 OSB #7, 更改 FOV 也可以取消冻结模式.

OSB #9 是 CZ(Cursor ZERO), 也就是光标零点. 在 BMS 4.33 中, 移动地面光标位置(设置为 SPI 才能移动)将给所有转向点添加系统增量, 当然也包括当前的转向点. CZ 将清零或擦除任何先前创建的系统增量, 因此所有 STPT 将返回到其原始位置, 并自然地将 SPI 设置为当前 STPT 位置. 如果存在系统增量, 则在具有 Nav EGI 升级的飞机中将高亮显示 CZ 助记符.

飞行员应使用以下例程将系统解决方案恢复为原始导航解决方案: TMS down - Cursor Zero - Wide Field of View(OSB #3). 这种习惯应该确保在每个光标转换后实施, 并且如果已经进行光标转换, 则应在每个 IP(Initial Point?) 处实施.

OSB #10 是瞄准点旋转(sighting point rotary). 它是 NAV 主模式下的 STP, A-G 主模式下的 TGT(target), 如果可用的 SPI 数据已经输入则是 OA1 或 OA2, VRP 模式下的 RP, VIP 模式下的 IP. 请注意: TMS 右键也可以改变瞄准点旋转选择.

像往常一样, 底部的 OSB #11 - 15 是直接访问按钮, DCLT 和 SWAP 分别位于 OSB #11 和 OSB #15.

OSB #17 允许方位角设置为 120°, 60° 和 20°. 6,3 和 1 分别代表纵向参考线两侧的 60°, 30° 和 10°, 选择 A6 时总扫描为 120°, 选择 A3 时为60°, 选择 A1 时为20°. 与 A-A 模式一样, 方位角设置越小, 雷达扫描越快.

OSB #19 和 20 用于设置 FCR 范围. OSB #19 降低范围, OSB #20 增加范围. 在 A-G 模式下, 范围从 10 到 80 Nm 不等. 通常在 20 Nm 以下会获得最佳功效.

# HUD(HEAD UP DISPLAY)

在所有主模式中, 速度或空速始终显示在左侧, 海拔高度显示在右侧, 航向位于顶部或底部(VAH).

空速可以显示为CAS(校准空速), TAS(真空速) 或 GND SPD(地速).
FPM 和俯仰标度线可以移除.
DED 或 PFL 可以显示在 HUD 的底部.
亮度可以自动调整适应 "白天/夜晚" 环境.
可以显示垂直速度指示器.
高度可以显示 BAROmetric(气压高度), RADAR(雷达高度) 或 AUTO 高度.

![HUD_SYMBOLS](https://raw.githubusercontent.com/21moons/memo/master/res/img/BMS/HUD_SYMBOLS.png)


