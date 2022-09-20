# Nmap安装和使用详解

Nmap被誉为"扫描器之王"，Nmap是一个开源工具，提供跨平台（Windows、linux、mac os）

**功能**

1. 查看主机存活
2. 扫描目标主机开放端口
3. 识别目标主机操作系统
4. 查看目标主机服务的版本信息
5. 漏洞探测

## 基础

> nmap -h查看使用教程  

**选择目标**

```bash
Can pass hostnames, IP addresses, networks, etc.
Ex: scanme.nmap.org, microsoft.com/24, 192.168.0.1; 10.0.0-255.1-254
-iL <inputfilename>: Input from list of hosts/networks
-iR <num hosts>: Choose random targets
--exclude <host1[,host2][,host3],...>: Exclude hosts/networks
--excludefile <exclude_file>: Exclude list from file
```

*  批量扫描：`***.***.***.***/24` `192.168.1.0-255` `192.168.1.1 192.168.1.2`
* `-iL filename`： 从文件中读取目标，支持ip和网段格式
* `–exclude <host1 [, host2] [, host3] …>`:  不扫哪个ip或网段
* `–excludefile filename` 不扫文件里面包含的ip

1. | 命令                        | 介绍                                                         |
    | --------------------------- | ------------------------------------------------------------ |
    | -F                          | Fast mode –       快速模式，仅扫描TOP 100的端口              |
    | -r                          | 顺序扫描指定的端口，默认是随机扫描                           |
    | -6                          | 启动ipv6扫描                                                 |
    | -v：                        | 显示细节，-vv细节更多                                        |
    | --top-ports       <number>: | 扫描开放概率最高的number个端口 nmap默认是top1000个的tcp端口，不包含redis的6379端口 |

## 主机发现

```bash
主机发现的原理与Ping命令类似，发送探测包到目标主机，如果收到回复，那么说明目标主机是开启的。

  -sL: List Scan - simply list targets to scan
  -sn: Ping Scan - disable port scan
  -Pn: Treat all hosts as online -- skip host discovery
  -PS/PA/PU/PY[portlist]: TCP SYN/ACK, UDP or SCTP discovery to given ports
  -PE/PP/PM: ICMP echo, timestamp, and netmask request discovery probes
  -PO[protocol list]: IP Protocol Ping
  -n/-R: Never do DNS resolution/Always resolve [default: sometimes]
  --dns-servers <serv1[,serv2],...>: Specify custom DNS servers
  --system-dns: Use OS's DNS resolver
  --traceroute: Trace hop path to each host
```

* -sP：仅执行ping扫描，好像不让用了，用-sn
* **-sn**  ：**Ping Scan 只进行主机发现，不进行端口扫描。**
    * Nmap会发送四种不同类型的数据包来探测目标主机是否在线。只要收到一个回复就证明主机在线。
    * ICMP echo request
    * a TCP SYN packet to     port 443(https)
    * a TCP ACK packet to     port 80(http)
    * an ICMP timestamp     request

* **-Pn：跳过ping扫描，也就是跳过主机发现，直接进行完整的端口扫描，如果靶机不允许ping，那么我们必须要使用这条命令，不然会显示该ip不在线。**
* **--traceroute: 追踪每个路由节点**
* -PS/PA/PU/PY[portlist]:  使用TCP SYN/TCP ACK或SCTP INIT/ECHO方式进行发现。

- -PE/PP/PM: 使用ICMP echo、 ICMP timestamp、ICMP netmask 请求包发现主机。
    - -PM 的ICMP address     maskPing地址掩码扫描会试图用备选的ICMP等级Ping指定主机，通常有不错的穿透防火墙的效果
    - -PP 的ICMP time     stamp时间戳扫描在大多数防火墙配置不当时可能会得到回复，可以以此方式来判断目标主机是否存活。倘若目标主机在线，该命令还会探测其开放的端口以及运行的服务！      

- -PU：进行udp 31338端口扫描，绕过过滤了tcp的防火墙，如果目标机器的端口是关闭的，UDP探测应该马上得到一个ICMP端口无法到达的回应报文。 这对于Nmap意味着该机器正在运行。
- -n：不用域名解析，加快扫描速度
- -R：为所有目标IP地址作反向域名解析


- –system-dns：使用系统域名解析器，一般不使用该选项，因为比较慢
- **-PR：arp请求，适用于局域网**
- 当探测内网ip时：会先发送arp包，如果回复了就表明主机在线。而且会返回主机的mac地址。

    - 使用 nmap -sn 内网ip  这个命令会发送arp请求包探测目标ip是否在线，如果有arp回复包，则说明在线。此命令可以探测目标主机是否在线，如果在线，还可以得到其MAC地址。但是不会探测其开放的端口号。
    - 使用 nmap -PE/PP/PM 内网ip 探测主机的开启情况，使用的是ARP请求报文，如果有ARP回复报文，说明主机在线。-PP/PE/PM命令探测到主机在线后，还会探测主机的端口的开启状态以及运行的服务

## 端口扫描

```bash
查看目标主机开放了哪些端口
  
端口扫描是Nmap最基本最核心的功能，用于确定目标主机的TCP/UDP端口的开放情况。
默认情况下，Nmap会扫描1000个最有可能开放的TCP端口
  
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
  -sU: UDP Scan
  -sN/sF/sX: TCP Null, FIN, and Xmas scans
  --scanflags <flags>: Customize TCP scan flags
  -sI <zombie host[:probeport]>: Idle scan
  -sY/sZ: SCTP INIT/COOKIE-ECHO scans
  -sO: IP protocol scan
  -b <FTP relay host>: FTP bounce scan
```

Nmap将目标主机端口分成6种状态：

* open（开放）
* closed（关闭）
* filtered(被过滤的)
* unfiltered(未被过滤) 可访问但不确定开放情况
* open|filtered(开放或被过滤)无法确定端口是开放的还是被过滤的
* closed|filtered(关闭或被过滤)无法确认端口是关闭的还是被过滤的

Nmap产生结果是基于机器的响应报文，而这些主机可能是不可信任的，会产生一些迷惑或者误导Nmap的报文

- **-sS ：**

    * **TCP SYN扫描，半开放状态，扫描速度快隐蔽性好（不完成TCP连接），能够明确区分端口状态**

    * **这是Nmap默认的扫描方式。**该方式发送SYN到目标端口，如果收到SYN/ACK回复，那么可以判断端口是开放的；如果收到RST包，说明该端口是关闭的。如果没有收到回复，那么可以判断该端口被屏蔽了。因为该方式仅发送SYN包对目标主机的特定端口，但不建立完整的TCP连接，所以相对比较隐蔽，而且效率比较高，适用范围广。

- **-sT **
    - **TCP连接扫描，容易产生记录，效率低**
    - TCP connect方式使用系统网络API connect向目标主机的端口发起连接，如果无法连接，说明该端口关闭。该方式扫描速度比较慢，而且由于建立完整的TCP连接会在目标主机上留下记录信息，不够隐蔽。所以，TCP connect是TCP SYN无法使用才考虑使用的方式

- **-sA**
    - TCP ACK扫描 ，但无法发现端口是open还是closed：
    - 向目标主机的端口发送ACK包，如果收到RST包，说明该端口没有被防火墙屏蔽；没有收到RST包，说明被屏蔽。该方式只能用于确定防火墙是否屏蔽某个端口，可以辅助TCP SYN的方式来判断目标主机防火墙的状况

- -sN/sF/sX: 
    - TCP Null, FIN, and Xmas scans
    - 这三种扫描方式被称为秘密扫描，因为相对比较隐蔽。FIN扫描向目标主机的端口发送的TCP     FIN 包或Xmas     tree包或NULL包，如果收到对方的RST回复包，那么说明该端口是关闭的；没有收到RST包说明该端口可能是开放的或者被屏蔽了。其中Xmas     tree包是指flags中FIN URG PUSH被置为1的TCP包；NULL包是指所有的flags都为0的TCP包。

- **–sO：IP协议扫描，可以确定目标机支持哪些IP协议（TCP, ICMP, IGMP）**

- **-sU：UDP扫描，DNS，SNMP，DHCP就是UDP服务，可以和sS结合，扫描两种服务(很慢）：**
    -  UDP扫描用于判断UDP端口的情况，向目标主机的UDP端口发送探测包，如果收到回复ICMP port unreachable就说明该端口是关闭的；如果没有收到回复，那说明该UDP端口可能是开放的或者屏蔽的。因此，通过反向排除法的方式来判断哪些UDP端口是可能处于开放状态的。
- 其他方式(-sY/-sZ)：
    * 除了以上几种常用的方式外，Nmap还支持多种其他的探测方式。例如使用SCTP      INIT/Cookie-ECHO方式是来探测SCTP的端口开放情况；使用IP      protocol方式来探测目标主机支持的协议类型(tcp/udp/icmp/sctp等等)；使用idle      scan方式借助僵尸主机来扫描目标主机，以达到隐蔽自己的目的；或者使用FTP bounce      scan，借助FTP允许的代理服务扫描其他的主机，同样达到隐蔽自己的目的

- `-sI <zombiehost[:probeport]>`: 指定使用idle scan方式来扫描目标主机（前提需要找到合适的zombie host）
- `-b <FTP relay host>`: 使用FTP bounce scan扫描方式
- **-A：完整扫描**
    - 这个命令不仅列出目标主机开放的端口号，对应的服务，还较为详细的列出了服务的版本，其支持的命令,到达目标主机的每一跳路由等信息。在进行完全扫描时，扫描机与目标主机之间存在大量的数据流量交互，扫描时长随之增加。
    - 完全扫描不仅仅是TCP协议上的通信交互，还有例如ICMP、HTTP、NBSS、TDS、POP等等协议的交互，这些协议的交互是因为在完全扫描开始时首先对目标主机的开放端口进行了确认，之后再根据不同对应的不同服务进行服务版本信息探测、账户信息等信息的探测！

## 端口说明和扫描顺序

```bash
  -p <port ranges>: Only scan specified ports
    Ex: -p22; -p1-65535; -p U:53,111,137,T:21-25,80,139,8080,S:9
  --exclude-ports <port ranges>: Exclude the specified ports from scanning
  -F: Fast mode - Scan fewer ports than the default scan
  -r: Scan ports consecutively - don't randomize
  --top-ports <number>: Scan <number> most common ports
  --port-ratio <ratio>: Scan ports more common than <ratio>
```

* **–p ：**
    * **只扫描指定的端口，单个端口和用连字符表示的端口范围都可以；**
    * **当既扫描TCP端口又扫描UDP端口时，您可以通过在端口号前加上T: 或者U:指定协议。协议限定符一直有效您直到指定另一个。**
    * **例如，参数 -p U:53，111，137，T:21-25，80，139，8080 将扫描UDP 端口53，111，和137，同时扫描列出的TCP端口。**
    * **–p ：扫描指定的端口名称，如`nmap –p smtp,http 10.10.1.44`**
    * **–p U:[UDP ports],T:[TCP ports]：对指定的端口进行指定协议的扫描**

* **–F：快速扫描（仅扫描100个最常用的端口），nmap-services文件指定想要扫描的端口；可以用—datadir选项指定自己的小小nmap-services文件**
* **–top-ports ： 扫描开放概率最高的number个端口 nmap默认是top1000个的tcp端口，不包含redis的6379端口   **
* **–r：不要按随机顺序扫描端口，默认情况下按随机（常用的端口前移）**

`nmap ip`：按照 nmap-services 文件中指定的端口进行扫描，然后列出目标主机开放的端口号，以及端口号上运行的服务。在一次简单扫描中，Nmap会以默认TCP SYN扫描方式进行，仅判断目标端口是否开放，若开放，则列出端口对应的服务名称

`nmap -T4 -A -v xx.xx.xx.xx`：

- -A     选项用于使用进攻性方式扫描
- -T4     指定扫描过程使用的时序，总有6个级别（0-5），级别越高，扫描速度越快，但也容易被防火墙或IDS检测并屏蔽掉，在网络通讯状况较好的情况下推荐使用T4

## 服务与版本探测

>用于确定目标主机开放端口上运行的具体的应用程序及版本信息。

```bash
  -sV: Probe open ports to determine service/version info
  --version-intensity <level>: Set from 0 (light) to 9 (try all probes)
  --version-light: Limit to most likely probes (intensity 2)
  --version-all: Try every single probe (intensity 9)
  --version-trace: Show detailed version scan activity (for debugging)
```

nmap-services是一个包含服务的数据库，Nmap通过查询该数据库可以报告那些端口可能对应于什么服务器，但不一定正确。在用某种扫描方法发现TCP/UDP端口后，版本探测会询问这些端口，确定到底什么服务正在运行；nmap-service-probes数据库包含查询不同服务的探测报文和解析识别响应的匹配表达式；当Nmap从某个服务收到响应，但不能在数据库中找到匹配时，就打印出一个fingerprint和一个URL给您提交。
参数含义：

* **–sV：进行端口版本扫描**
    * --version-intensity <level>: 指定版本侦测强度（0-9），默认为7。数值越高，探测出的服务越准确，但是运行时间会比较长。
        --version-light: 指定使用轻量侦测方式 (intensity 2)
        --version-all: 尝试使用所有的probes进行侦测 (intensity 9)
        --version-trace: 显示出详细的版本侦测过程信息

* **-O：操作系统版本检测**，当-O探测不出来时，根据服务版本（可用是udp，tcp）来探测os
    * Nmap使用TCP/IP协议栈指纹来识别不同的操作系统和设备。在RFC规范中，有些地方对TCP/IP的实现并没有强制规定，由此不同的TCP/IP方案中可能都有自己的特定方式。Nmap主要是根据这些细节上的差异来判断操作系统的类型的。
    * --osscan-limit:      限制Nmap只对确定的主机的进行OS探测（至少需确知该主机分别有一个open和closed的端口）。
    * --osscan-guess:      大胆猜测对方的主机的系统类型。由此准确性会下降不少，但会尽可能多为用户提供潜在的操作系统


## 时间和性能

```bash
  Options which take <time> are in seconds, or append 'ms' (milliseconds),
  's' (seconds), 'm' (minutes), or 'h' (hours) to the value (e.g. 30m).
  -T<0-5>: Set timing template (higher is faster)
  --min-hostgroup/max-hostgroup <size>: Parallel host scan group sizes
  --min-parallelism/max-parallelism <numprobes>: Probe parallelization
  --min-rtt-timeout/max-rtt-timeout/initial-rtt-timeout <time>: Specifies
      probe round trip time.
  --max-retries <tries>: Caps number of port scan probe retransmissions.
  --host-timeout <time>: Give up on target after this long
  --scan-delay/--max-scan-delay <time>: Adjust delay between probes
  --min-rate <number>: Send packets no slower than <number> per second
  --max-rate <number>: Send packets no faster than <number> per second
```

- `-T4`    : 指定扫描过程使用的时序，总有6个级别（0-5），级别越高，扫描速度越快，但也容易被防火墙或IDS检测并屏蔽掉，在网络通讯状况较好的情况下推荐使用T4

* –min-hostgroup ;–max-hostgroup ：调整并行扫描组的大小，用于保持组的大小在一个指定的范围之内；Nmap具有并行扫描多主机端口或版本的能力，Nmap将多个目标IP地址空间分成组，然后在同一时间对一个组进行扫描。通常，大的组更有效。缺点是只有当整个组扫描结束后才会提供主机的扫描结果
* –min-parallelism; --max-parallelism ：调整探测报文的并行度，用于控制主机组的探测报文数量；默认状态下， Nmap基于网络性能计算一个理想的并行度，这个值经常改变

## 输出选项

```bash
 -oN/-oX/-oS/-oG <file>: Output scan in normal, XML, s|<rIpt kIddi3,
     and Grepable format, respectively, to the given filename.
  -oA <basename>: Output in the three major formats at once
  -v: Increase verbosity level (use -vv or more for greater effect)
  -d: Increase debugging level (use -dd or more for greater effect)
  --reason: Display the reason a port is in a particular state
  --open: Only show open (or possibly open) ports
  --packet-trace: Show all packets sent and received
  --iflist: Print host interfaces and routes (for debugging)
  --append-output: Append to rather than clobber specified output files
  --resume <filename>: Resume an aborted scan
  --noninteractive: Disable runtime interactions via keyboard
  --stylesheet <path/URL>: XSL stylesheet to transform XML output to HTML
  --webxml: Reference stylesheet from Nmap.Org for more portable XML
  --no-stylesheet: Prevent associating of XSL stylesheet w/XML output
```

* **–oN ：标准输出**
* **–oX ：XML输出写入指定的文件**
* –oS ：脚本小子输出，类似于交互工具输出
* **–oG ：Grep输出**
* –oA ：输出至所有格式
* –v：提高输出信息的详细度
* **–d [level]： 开启debug模式，设置调试级别，9最高**
    * --packet-trace：跟踪发送和接收的报文
    * --iflist：输出检测到的接口列表和系统路由
    * --append-output：表示在输出文件中添加，而不是覆盖原文件
    * --resume ：继续中断的扫描，
    * --stylesheet ：设置XSL样式表，转换XML输出；Web浏览器中打开Nmap的XML输出时，将会在文件系统中寻找nmap.xsl文件，并使用它输出结果
    * --packet-trace: Show all packets sent and received


## 常用扫描命令

```bash
nmap -v -sn 172.17.57.74/24   对一个网段进行主机发现
 
nmap -sS -sU –sV  -O -T4 192.168.56.101    探测主机开放的端口，端口的服务，操作系统类型。默认扫1000个tcp和1000个udp端口

namp -sn -traceroute  baidu.com   查看路由的跳转情况

nmap -A  127.0.0.1 包含1-10000端口的ping扫描，操作系统扫描，路由跟踪，服务探测
```

## 防火墙/IDS规避和欺骗

- nmap提供了多种规避技巧通常可以从两个方面考虑规避方式：**数据包的变换(Packet Change)和时序变换(Timing Change)**
- 分片：
    - 将可疑的探测包进行分片处理(例如将TCP包拆分成多个IP包发送过去)，某些简单的防火墙为了加快处理速度可能不会进行重组检查，以此避开其检查
- IP伪装：
    - IP伪装就是将自己发送的数据包中的IP地址伪装成其他主机的地址，从而目标机认为是其他主机与之通信。需要注意的是，如果希望接收到目标主机的回复包，那么伪装的IP需要位于统一局域网内。另外，如果既希望隐蔽自己的IP地址，又希望收到目标主机的回复包，那么可以尝试使用idle scan 或匿名代理等网络技术
- 指定源端口：
    - 某些目标主机只允许来自特定端口的数据包通过防火墙。例如，FTP服务器的配置为允许源端口为21号的TCP包通过防火墙与FTP服务器通信，但是源端口为其他的数据包被屏蔽。所以，在此类情况下，可以指定数据包的源端口
- 扫描延时：
    - 某些防火墙针对发送过于频繁的数据包会进行严格的侦查，而且某些系统限制错误报文产生的频率。所以，我们可以降低发包的频率和发包延时以此降低目标主机的审查强度

```bash
-f; --mtu <val>: 指定使用分片、指定数据包的 MTU.
-D <decoy1,decoy2[,ME],...>: 用一组 IP 地址掩盖真实地址，其中 ME 填入自己的 IP 地址。
-S <IP_Address>: 伪装成其他 IP 地址
-e <iface>: 使用特定的网络接口
--proxies <url1,[url2],...>:   Relay connections through HTTP/SOCKS4 proxies  使用代理
-g/--source-port <portnum>: 使用指定源端口

--data-length <num>: 填充随机数据让数据包长度达到 Num。
--ip-options <options>: 使用指定的 IP 选项来发送数据包。
--ttl <val>: 设置 time-to-live 时间。
--spoof-mac <mac address/prefix/vendor name>: 伪装 MAC 地址
--badsum: 使用错误的 checksum 来发送数据包（正常情况下，该类数据包被抛弃，如果收到回复，
 说明回复来自防火墙或 IDS/IPS）
```

## 脚本扫描

```bash
  -sC: equivalent to --script=default
  --script=<Lua scripts>: <Lua scripts> is a comma separated list of
           directories, script-files or script-categories
  --script-args=<n1=v1,[n2=v2,...]>: provide arguments to scripts
  --script-args-file=filename: provide NSE script args in a file
  --script-trace: Show all data sent and received
  --script-updatedb: Update the script database.
  --script-help=<Lua scripts>: Show help about scripts.
           <Lua scripts> is a comma-separated list of script-files or
           script-categories.
```

NSE脚本引擎(Nmap     Scripting Engine)是nmap最强大，最灵活的功能之一，允许用户自己编写脚本来执行自动化的操作或者扩展nmap的功能。

nmap的脚本库的路径：`/usr/share/nmap/scripts` 或` /xx/nmap/scripts/`     ，该目录下的文件都是nse脚本

NSE使用Lua脚本语言，并且默认提供了丰富的脚本库，目前已经包含了14个类别的350多个脚本。NSE的设计初衷主要考虑以下几个方面

- 网络发现（Network     Discovery）
- 更加复杂的版本侦测（例如     skype 软件）
- 漏洞侦测(Vulnerability     Detection)
- 后门侦测(Backdoor     Detection)
- 漏洞利用(Vulnerability     Exploitation)

**Nmap的脚本主要分为以下几类：**

- Auth：负责处理鉴权证书(绕过鉴权)的脚本
- Broadcast：在局域网内探查更多服务去开启情况，如DHCP/DNS等
- Brute：针对常见的应用提供暴力破解方式，如HTTP/HTTPS
- Default：使用-sC或-A选项扫描时默认的脚本，提供基本的脚本扫描能力
- Discovery：对网络进行更多的信息搜集，如SMB枚举，SNMP查询等
- Dos：用于进行拒绝服务攻击
- Exploit：利用已知的漏洞入侵系统
- External：利用第三方的数据库或资源     。如，进行whois解析
- Fuzzer：模糊测试脚本，发送异常的包到目标机，探测出潜在漏洞
- Intrusive：入侵性的脚本，此类脚本可能引发对方的IDS/IPS的记录或屏蔽
- Malware：探测目标是否感染了病毒，开启后门等
- Safe：与Intrusive相反，属于安全性脚本
- Version：负责增强服务与版本扫描功能的脚本
- Vuln：负责检查目标机是否有常见漏洞，如MS08-067

**例如：**

```bash
nmap -script smb-vuln-ms17-010 192.168.10.34 #可以探测该主机是否存在ms17_010漏洞
nmap --max-parallelism 800 --script http-slowloris scanme.nmap.org #可以探测该主机是否存在http拒绝服务攻击漏洞
nmap -script http-iis-short-name-brute 192.168.10.34 #探测是否存在IIS短文件名漏洞
nmap -script mysql-empty-password 192.168.10.34 #验证mysql匿名访问
nmap -p 443 -script ssl-ccs-injection 192.168.10.34 #验证是否存在openssl CCS注入漏洞
nmap --script-brute 192.168.1.1 #nmap可对数据库、SMB、SNMP等进行简单密码的暴力破解
nmap --script-vuln 192.168.1.1 #扫描是否有常见漏洞
--script=http-waf-detect #验证主机是否存在WAF
--script=http-waf-fingerprint #验证主机是否存在WAF

--script-updatedb #更新脚本数据库
--script-help #输入脚本对应的使用方法
```





















