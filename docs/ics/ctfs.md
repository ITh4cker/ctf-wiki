> 本栏目内容，ICS CTF竞赛内容来自于作者自身打比赛经验，如果不对之处，请各位老师批评指正

## 国内工控比赛考察点

采用CTF分类模型，总结分析当前工控ICS比赛中的关键点

|比赛类型|考察点|与CTF异同|
|-------|------|-------|
|内网渗透|Web端渗透测试、CMS系统、工控发布展示系统、数据库系统|与Web渗透相关
|逆向分析|固件分析、工控软件逆向|实际场景逆向|
|工控协议|工控流量分析、Misc类|Misc 流量分析，工控场景流量特征|
|工控编程|PLC组态、HMI组态、RTU组态等|工控实际组态软件使用，梯形图识别与分析|

根据漏洞类型其实还可以区分细化题目类型，包括常见的Web注入类、固件弱口令、后门程序、协议重放与逻辑问题、组态部署问题等常见的工控场景安全问题。

|比赛类型|漏洞类型|
|-------|------|
|内网渗透|Web类（SQL、XSS、命令注入、敏感文件泄露.git/.idea/.project等、）
|逆向分析|固件分析、工控软件逆向|实际软件、DLL、ELF、MIPS逆向|
|工控协议|工控流量分析、Misc类|Misc 流量分析，工控场景流量特征|
|工控编程|PLC组态、HMI组态|工控实际组态软件使用，梯形图识别与分析|

针对目前出现或曾经出现的ICS CTF题目类型，其实与CTF竞赛有许多重合点，因此不再此赘述，主要讨论下工控CTF中与CTF竞赛不太一致的地方。

## Web渗透类（Web)

该节主要谈工控Web渗透的特点：

- 与业务场景高度契合，例如工业控制中，Web端主要为显示当前使用场景中的控制参数、运行状态等信息，如果在内网中被中间人劫持，当HMI显示设备无法与PLC等实时运行设备同步时，系统会报警或出错。
- 一般采用通用技术展示Web界面，以windows操作系统为主要平台，包括WinCC、Windows Server、Windows 98/2000/XP等看似古老的系统为主。
- Web渗透的同时会保留多个端口，例如FTP、HTTPS、Telnet、SNMP、NTP等服务端口，在Web渗透无法打穿的同时可以试试其他端口。
- 工控由于一般处于内网环境，内网劫持往往比较有效，但是如果内网配置了静态IP或其他防护措施，ARP欺骗方式等内网劫持方法无法起效。
- 敏感信息泄露，配置文件不完善是工控Web发布常见问题，不仅仅包括.git/.idea/.project等工程信息协议，还可能出现路径遍历、命令注入、弱口令等问题。

## 逆向分析（Reverse）

该节主要讨论工控逆向的特点：

- 工控操作系统一般为RTOS（Real Time Operate System）,例如vxworks、uc-os等实时操作系统，在逆向前需要对其架构和指令集需要比较熟悉，如果不懂请自行学习。
- 工控固件逆向常见的目标是工控工程加密算法、硬编码秘钥、硬编码后门等常见固件逆向漏洞，如果发现了堆栈溢出类漏洞，往往能够导致目标设备宕机（即DOS后果）。
- 工控固件往往存在加密和压缩情况，需要在第一步解压过程对其进行解压或解密，这部分依据具体厂商来定，不能一概而论。
- 工控固件存在逆向分析不出的情况

## 工控协议（Protocol）

该节主要谈论工控协议类题目的相关特点：

- 工控协议针对工控场景设计，具有简单、高效、低延时等特点，所以针对此类的攻击完全可以考虑采用重放、命令注入等简单攻击手段。
- 工控协议不仅仅采用公开协议、还包括众多的私有协议，这部分协议具体细节需要逆向或者采集数据来实现数据功能的还原。例如Modbus、DNP3、Melsec-Q、S7、Ethernet/IP等。
- 工控协议可能导致目标PLC、DCS、RTU等设备出现宕机、不可重启等问题，采用基于Fuzz的方法可以快速高效发现PLC 宕机类漏洞。
- 工控协议中可能有众多针对PLC等设备的操作，用户需要区分哪些是合法请求、哪些是异常请求，这需要经验，需要研究推断当前流量的使用逻辑。这个场景很适合机器学习的条件，这可以考虑是个探索的方向。
- 针对工控场景的实际防御方案其实最好的还是旁路检测，通过分光将流量接入分析系统，在不影响正常业务使用的同时对目标系统进行安全监控。


## 工控编程（Program）

工控编程是工控系统运行的核心和重点，此类题目特点一般是：

- 工控编程核心为明白工控业务逻辑，而工控编程遵循IEC61131-3（工控史上首例实现PLC、DCS、运动控制、 SCADA等联合编程的标准——IEC61131-3），包括5种编程语言标准，3种是图形化语言（梯形图、顺序功能图和功能块图），两种是文本化语言（指令表和结构文本）。
- 工控设备往往可以在线调试，从而可以控制某些输入输出端口，实现强制启停的功能，如果这些功能可以采用Remote方式重发，那攻击危害就更为严重。
- 工控设备的连接方式多样，一般采用串口，但是目前设备发展支持以太网、USB接口等新的方式，如果网口不行试试串口、USB。
- 工控组态可以非常复杂，甚至连接成百上千个输入输出都有可能，组态中会由于添加了新的组件而更加麻烦，这时候要慢慢看，一点一点缕出来。

以上是我参加工控比赛的一些心得体会，希望能为后来参赛的小伙伴多些指导。
