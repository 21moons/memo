# Communication



* ADI   Attitude Direction Indicator
* CG    Center of Gravity
* CMS   Countermeasures Management
* CNI   Communication, Navigation & Identification
* CRS   Course
* DCS   Data Command Switch(ICP 上的一个四向拨动开关)
* DED   Data Entry Display
* DBU   Digital Backup
* DMS   Display Management
* EPU   Emergency Power Unit
* EWS   Electronic Warfare System
* FLCS  Flight Control System
* FLCP  FLT CONTROL Panel
* HDG   Heading
* HSI   Horizontal Situation Indicator
* HUD   Heads Up Display
* ICP   Integrated Control Panel
* LG    Landing Gear
* OBOGS On Board Oxygen Generating System
* PFLD  Pilot Fault List Display
* PMG   Permanent Magnet Generator
* RWR   Radar Warning Receiver
* TF    Terrain Following
* TMS   Target Management
* TWS   Threat Warning System
* SMS   Stores Management Set
* UFC   Up Front Controller
* VVI   Vertical Velocity Indicator

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

* 5 Override Modes:
1. COM1 (ICP button)
2. COM2 (ICP button)
3. IFF (ICP button) Not implemented in BMS
4. LIST (ICP button)
5. F-ACK (left glareshield pushbutton)

Override Mode 提供对按钮对应功能的直接访问. 你可以通过再次按下 Override Mode 模式按钮来回退到初始页面. 除了上面提到的 5 种 Override Mode 之外, 还有一种特殊的 Override Mode, 可以将 UFC 返回到 DED 上的 CNI(通信, 导航和识别)页面. 通过将 DCS(数据命令开关)向左拨动(RTN 位置)来访问该模式.

* 8 Priority/secondary buttons:

这些是标有 T-ILS, A-LOW, STPT, CRUS, TIME, MARK, FIX 和 A-CAL 的 ICP 方形按钮. 最后两个没有在 BMS 中实现.
这些按钮具有双重功能; 根据上面列出的标签, 它们用于将数据输入 UFC/DED 或 UFC/DED 子页面.
可以在暂存器中输入数值。 暂存器是DED中显示的两个星号之间的区域。 只要您看到暂存器处于活动状态，当您按下这些按钮时，将使用从0到9的ICP按钮的数值。 请注意，使用M-SEL 0按钮输入数字零，此按钮没有辅助页面调用功能。