---
layout:		post
title:		"业余无线电B证不完全指南"
subtitle:	"DE BI1QDZ"
date:		2024-06-01 22:57:03  +0800
author:		"Les1ie"
catlog:   true
mathjax: true
tags: 
  - 随笔
---



# 序
拿到A证一年了，虽然并没有通联多少 :) 以前还偶尔在协会中继和自由无线中继听听聊聊，后来协会中继不知为何没了，自由无线也没了，便偶尔在昌平中继上听听，昌平中继的老哥们贼能聊，都是老熟人，一个比一个客气 hhhhh

恰巧报上了B证的考试，于是花了一周时间抽空学习，速通了B证，B证相对于A证的特点是添加了更多理论计算和一些需要死记硬背的知识。在预习过程中在网上搜了很多理论知识的计算方法，也参考了很多大神 ham 总结的考试经验，还有 “ham模拟考试”这个微信小程序的很多网友们脑洞大开的评论。其中一些信息帮助我理解了题目，也有一些信息大大大大减少了我的记忆量，在此表示感谢。

今天中午刚考完试，感觉良好，所以趁着我还没忘光，我也写写笔记，一来是自己备忘，二来是随缘帮助那些需要准备B证的 ham。

题库在北京业余无线电协会可以下载到，下载链接是 [v20211022](http://bjwxdxh.org.cn/news/html/?1001.html)。强烈建议打印这个整理好的[在线版本](https://exam.ham.upall.cn/?t=b)，打印出来装订成册方便做笔记。B类的很多题目在反复考一个知识点，做笔记能够提高效率，在手机上刷题不方便总结。

我不会把所有的题目都列一遍，工作量太大了，建议参考这几位 ham 总结的内容。
- https://zgc261.com/post/20220856.html
- https://bokjan.com/2022/05/crac-ar-license-exam-b-class-review.html
- https://zhuanlan.zhihu.com/p/663985104
- https://www.zhihu.com/tardis/zm/art/290652855?source_id=1003
- https://www.jimmytian.com/archives/crac-aro-licence-b-review-guide.html

# 一些题目的涉及知识和做题技巧
这里我写一些我对题目的理解，可能存在谬误，欢迎指正。

## 涉及业余无线电违法的相关处罚
A证里面也有这个题目，技巧很简单，只要是涉及到处罚相关的，包括题目 LK0094-LK0104 以及题目LK0110，一律选处罚最严格的项目，最轻的是拆除警告没收，其次是罚款，最严厉的是刑事处罚。
```
0079. 业余电台随意变更核定项目、发送和接收与业余无线电无关的信号的，国家无线电管理机构或者地方无线电管理机构可以根据其具体情况给予设置业余无线电台的单位或个人下列处罚： [LK0096]

1. 警告、查封或者没收设备、没收非法所得；情节严重的，可以并处一千元以上，五千元以下的罚款
2. 劝告拆除非法设置的电台；情节严重的，可以并处警告、查封或者没收设备
3. 责令停止使用非法设置的电台；情节严重的，可以并处警告、查封或者没收设备
4. 责令停止使用非法设置的电台并作出书面检查；情节严重的，可以并处一千元以下的罚款

0080. 超出核定范围使用频率或者有其他违反频率管理有关规定的行为的，无线电管理机构可以根据其具体情况给予设置业余无线电台的单位或个人下列处罚： [LK0097]

1. 责令限期改正，可以处警告或者三万元以下的罚款
2. 责令限期改正，可以处警告或者一千元以上，五千元以下的罚款
3. 责令限期改正，可以处警告或者一千元以下的罚款
4. 责令限期改正，情节严重的，可以并处警告、查封或者没收设备

0093. 业余电台违反无线电管制命令和无线电管制指令的，可以依法规受到下列处罚： [LK0110]

1. 责令改正；拒不改正的，关闭、查封、暂扣或者拆除相关设备；情节严重的，吊销电台执照；违反治安管理规定的，由公安机关处罚
2. 处警告或者三万元以下的罚款
3. 处警告或者一千元以上，五千元以下的罚款
4. 责令改正；并开除业余无线电协会会籍、罚没无线电通信设备
```

## 发射类别和信号调制
这个记住下表即可

| 调制类型      | 调制信号        | 信息类型    |
| --------- | ----------- | ------- |
| A 双边带调幅   | 1 单通道数字     | A 电报-人工 |
| F 调频      | 2 单通道数字-副载波 | B 电报-自动 |
| G 调相      | 3 单通道模拟     | E 话音    |
| J 单边带抑制载波 |             |         |


所以这里CW电报是 A1A
单边带话是J3E，事实上题目LK0128询问的单边带话音这里只有这一个 E的选项，直接选出答案
单表带话传输的RTTY是F2B，因为他是调频的自动电报
PSK31是 P 是 Phase，调相，选择G 开头的
调频话就 Freq，所以 F3E

## 短波各段可以实际占用的频率
题目 LK0164-LK0170 涉及的数字极多，难以记忆。最初我是准备放弃这个题目的，但是凑巧在 ham 模拟考试的小程序中看到了一个通用的解决方案：“实际占用开头选最大，一样选最长”。瞬间解决了一个硬骨头。

```
0131. 我国短波业余电台在7MHz频段进行LSB通话时可以实际占用的频率为： [LK0164]

1. 7.030-7.200MHz
2. 7.000-7.100MHz
3. 7.023-7.200MHz
4. 7.000-7.200MHz

0132. 短波业余电台在14MHz频段进行USB通话时可以实际占用的频率为： [LK0165]

1. 14.100-14.350MHz
2. 14.030-14.350MHz
3. 14.000-14.250MHz
4. 14.070-14.250MHz

0133. 短波业余电台在18MHz频段进行USB通话时可以实际占用的频率为： [LK0166]

1. 18.1105-18.168MHz
2. 18.110-18.170MHz
3. 18.068-18.186MHz
4. 18.1005-18.180MHz

0134. 短波业余电台在21MHz频段进行USB通话时可以实际占用的频率为： [LK0167]

1. 21.125-21.45MHz，除去21.1495-21.1505
2. 21.125-21.45MHz
3. 21-21.45MHz
4. 21-21.35MHz
```

## 俗称开头的波段和业务使用状态
这个只能硬记了，难搞
40米选 7.0-7.2 开头的
160米选 1800-2000, 2x8=16 辅助记忆
80米选 3.5-3.9 主要
20米选 14-14.25
15米和10米，选尾数和其他选项相同的专用
6米2米选主要
0.7米选次要

## 波长和频率
题目考了不少关于波长和频率的知识，记住高频 HF 是 High Frequence 的缩写，考了B证可以玩短波这个基本事实，即可推出所有的频段和波长了。频率按照10倍递增，波长按照10倍递减。值得注意的是，题目里面的名称和波长对应关系都是正确的，所以记住了频段名称或者波长都能做题。

| 频率         | 缩写     | 含义                  | 频段名称   | 波长     |
| ---------- | ------ | ------------------- | ------ | ------ |
| 30k-300k   | LF     | Low Freq            | 低频     | 长波     |
| 300k-3000k | MF     | Mean Freq           | 中频     | 中波     |
| **3M-30M** | **HF** | **High Freq**       | **高频** | **短波** |
| 30M-300M   | VHF    | Very High Freq      | 甚高频    | 米波     |
| 300M-3000M | UHF    | Ultra High Freq     | 特高频    | 分米波    |
| 3G-30G     | SHF    | Super High Freq     | 超高频    | 厘米波    |
| 30G-300G   | EHF    | Extremely High Freq | 极高频    | 毫米波    |


## Q简语和单词缩写
Q简语很多，部分理解记忆和联想记忆，少部分只能硬记。理解和联想基本能够覆盖大部分常用的内容。考试涉及的大部分是QR开头的，QS开头的较少。这里只提到考试需要选择的选项，错误选项的不写。

| Q简语 | 联想记忆                  | 含义          |
| --- | --------------------- | ----------- |
| QRL |                       | 有人使用这个频率么   |
| QRM | QR(Man)               | 有其他人/其他台干扰  |
| QRN | QR(Nature)            | 天电干扰        |
| QRO | QRP相邻，但是相反            | 增加功率        |
| QRP | QRP是一种小功率玩法           | 减小功率        |
| QRQ | QR(Quick)             | 加速发送        |
| QRS | QR(Slow)              | 减速发送        |
| QRT | QR(sTop)              | 停止发送        |
| QRU | QR(U事，有事？)            | 找我有事        |
| QRV | QR(V，胜利的v,表示准备好)      | 准备好了        |
| QRZ |                       | 谁在呼叫        |
| QSA |                       | 信号强度        |
| QSB | QS(Behind)            | 信号衰落（记忆为落后） |
| QSD |                       | 发信手法有毛病     |
| QSL | QSL卡片                 | QSL卡片，收妥    |
| QSO | eyeball QSO，眼睛通联也是一种  | 和xxx通信      |
| QSP | QS(Post)              | 发信中转        |
| QSX |                       | 将在xxx频段守听   |
| QSY | 和QSX联合记忆，x主动收听，y让别人改频 | 改频段到 xxx    |
| QTH | QT(There)             | 位置          |

单词缩写的题目，自行查询词典和上网检索即可，我只写几个常见的

| 缩写   | 缩写前                            | 含义         |
| ---- | ------------------------------ | ---------- |
| ATT  | attenuator                     | 衰减、衰减器     |
| GA   | Go Ahead/ Good Afternoon       | 继续、请过来、下午好 |
| HST  | High Speed Telegraph           | 快速收发报      |
| ANT  | Antenna                        | 天线         |
| BK   | Break in                       | 打断、插入      |
| ES   | ampersand                      | & 和        |
| FB   | fabulous                       | 极好         |
| MTRS | meters                         | 米          |
| SK   | Stop Keying                    | 停止拍发，停止按电键 |
| WTS  | watts                          | 瓦特         |
| LW   | long wire                      | 长线天线       |
| NB   | Noise Block                    | 抑制噪音       |
| ALC  | Auto Power Level Control       | 发信自动电平控制   |
| AT   | Auto antenna Tuner             | 自动天线调谐     |
| VOX  | Voice Operated Tx              | 发信机声控      |
| RIT  | Receive(RX) Incremental Tuning | 接收增量调谐     |
| XIT  | TX Incremental Tuning          | 发送增量调谐     |


## 分贝相关的计算
需要知道的是，关于 dB, dBw, dBm, dBu, dBi, dBd 以及档位 S0-S9 的计算，只要弄懂其中原理，答案全部是可以现场推出来的。我花了点时间才了解这几个定义的含义，这里可以简单看看下表再看这个详细的推理计算过程[说明](https://www.espressif.com/zh-hans/media/blog/%E5%A2%9E%E7%9B%8A%E6%AF%94%E5%80%BC-db-%E4%BB%A5%E5%8F%8A-dbw-dbmv-%E7%AD%89%E4%B9%8B%E8%AF%A6%E8%A7%A3)。如果学过初中数学和初中物理，那么预计一小时内可以完全掌握。

看懂 dB 的计算之后就能秒杀上面提到的几个题目了。

写博客的初衷是为了让尽量多的人都能看懂，所以我尽量把详细的基础知识列出来。先了解对数计算的基本公式

$\log_b(x \cdot y) = \log_b x + \log_b y$

$\log_b \left(\frac{x}{y}\right) = \log_b x - \log_b y$

$\log_b(x^k) = k \log_b x$

$\log_b b = 1$

$\log_b 1 = 0$

$\log_{10} k =\lg k$

$\lg2=0.3$ 

$\lg10=1$ 

以及指数计算的公式

$\frac{1}{x^n}=x^{-n}$

和上面结合可以得到

$\log_b\frac{1}{x^k} = k \log_b x^{-1}=-k\log_b x$

符号解释如下：

| 符号  | 含义                                   | 存在的换算关系                                                                                                                            |
| --- | ------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------- |
| dB  | 分贝，这是一个把倍数化简的写法，和数学除法的倍数完全一样，只是写法不同  | $dB=10\lg\frac{A}{B}$                                                                                                              |
| dBw | 分贝瓦，这里用下标 $o$ 指代作为参考的单位功率或者单位电流、单位电压 | $0dBw=10\lg\frac{P}{Po}=10\lg\frac{I^2R}{I^2_oR}=10\lg\frac{\frac{U^2}{R}}{\frac{U^2_o}{R}}=20\lg\frac{I}{I_o}=20\lg\frac{U}{U_o}$ |
| dBm | 分贝毫瓦（ 毫瓦是10^-3瓦）                     | $0dBw=10\lg\frac{1w}{1mw}=10\lg\frac{1000mw}{1mw}=30dBm$                                                                           |
| dBu | 分贝 $\mu$瓦  ($\mu$瓦是10^-6瓦）           | $0dBm=10\lg\frac{1mw}{1\mu w}=10\lg\frac{1000\mu w}{1\mu w}=30dB\mu$ 因此 $0dBw=30dBm=60dB\mu$                                       |
| dBi | 分贝点源天线功率，一般计算的时候以这个为 0dBw 作为相对值计算    | 0dBi=0dBd-2.15dB                                                                                                                   |
| dBd | 分贝偶极天线功率                             | 0dBd=2.15dBi                                                                                                                       |

根据值 $\lg2=0.3$  和 $\lg10=1$ ，以及 $0dB=10\lg \frac{A}{B}$ 三个基本知识，就能推理出以下内容

dB 值加3,对应倍数乘以 2
dB 值加10,对应倍数乘以 10
dB 值减3,对应倍数除以 2
dB 值减10,对应倍数除以 10

这里以题目 LK0543 为例
```
0415. 某电路输出信号功率是输入信号功率的100万倍，该电路的增益为： [LK0543]

1. 60dB
2. 100dB
3. 99万dB
4. 100万dB
```
计算过程便是 $x dB= 10\lg\frac{10^6}{1}=60dB$ 

题目 LK0544
```
0416. 某电路输出信号功率是输入信号功率的5倍，该电路的增益约为： [LK0544]

1. 7dB
2. 3.5dB
3. 5dB
4. 14dB
```
计算过程便是 $xdB=10\lg{5}{1}=10\lg\frac{10}{2}=10(\lg10-\lg2)=10(1-\lg2)=10(1-0.3)=7dB$  
需要记住特殊值 $\lg2=0.3$ ，其他的都能推理出来了。

题目 LK0555
```
0427. 某电路输出信号电压是输入信号电压的万分之一，该电路的增益为：（”x＾m”表示“x的m次方”） [LK0555]

1. -80dB
2. -10,000 dB
3. 1/10,000 dB
4. 10＾-4dB
```
因为这里是电压，所以lg前面的倍数是20,计算过程 $xdB=20\lg \frac{1}{10000} =20\lg \frac{1}{10^4}=20\lg 10^{-4}=-80dB$

题目 LK0565
```
0437. 功率为0 dBW的射频信号通过衰减量为 40 dB的衰减器后，输出功率为： [LK0565]

1. 100μW
2. 40mW
3. 140μW
4. 0.40 W
```
计算过程也很简单 $0dBw-40dB=-40dBw=-10dBm=20dB\mu=0dB\mu +20dB =1\mu w \times 10 \times 10 =100\mu w$
也可以计算为 $0dBw-40dB=-40dBw=1w \div 10\div10\div10\div10=10^{-4}w=10^{-1}mw=100\mu w$

## 滤波器相关
这里提几个滤波器的概念

| 滤波器   | 功能         |
| ----- | ---------- |
| 低通滤波器 | 低于该频段的都能通过 |
| 高通滤波器 | 高于该频段的都能通过 |
| 带通滤波器 | 这个频带内的都能通过 |
| 带阻滤波器 | 这个频带内的都能阻止 |

知道这四个概念，那么题目 LK0573-LK0576 就没问题了。
注意中继台的上行是指接受其他电台发送的信号。下行是指发送出去信号。和普通的设备是相反的。

## 镜像频率干扰的计算
只需记住干扰频率是 $RF\pm 2\times IF$ 即可算出 LK0841-LK0844
例如题目 LK0844

```
0532. 在超外差式收信机电路中，信号通道的有用信号频率比本振频率低（或者高）一个中频频率。但比本振频率高（或者低）一个中频频率的信号也可能窜入信号通道，称为“镜像频率干扰”或“镜频干扰”。某UHF对讲机的使用说明书技术指标部分给出了第一中频（IF）为58.525MHz，但没有更多的资料。由此可推测当接收435.00MHz信号时下述频率之一的强信号可能造成镜频干扰： [LK0844]

1. 317.95MHz或552.05MHz
2. 376.475MHz或493.525MHz
3. 58.525MHz或117.05MHz
4. 234.10.05MHz或.468.20MHz
```

计算
$435.00+58.525\times 2=552.05$ 
和 
$435.00-58.525\times 2=317.95$

## 天线振子长度计算
题目 LK0916
```
0563. 制作工作频率为f（单位：兆赫兹）的某相控天线阵列需要长度为1/4波长的同轴电缆。其大致长度（单位：米）为： [LK0916]

1. 48.8 / f
2. 149.8 / f
3. 75 / f
4. 71.3 / f
```

计算过程根据电磁波速度等于频率乘以波长，即 $c=\lambda f$ 以及电磁波在介质中的传播速率计算可以推出。传播速率跟相对介电常数$\varepsilon_r$相关，这里不作过多引入。只需记住相控阵的铜导线中电磁波速率是0.65c，偶极天线中的计算用 0.95c即可。这里需要1/4波长电缆，电缆中电磁波速度是0.65c，那么
$\frac{1}{4}\lambda=\frac{1}{4} \times \frac{0.65c}{f(mHz)}=\frac{1}{4}\times \frac{3\times 10^8\times 0.65}{f\times 10^6}=48.75$

```
0564. 制作工作频率为f（单位：兆赫兹）的半波长偶极天线。每个振子的大致长度（单位：米）为： [LK0917]

1. 71.3 / f
2. 48.8 / f
3. 142.6 / f
4. 150 / f
```

半波长偶极天线有两个振子。两个振子总长度是半波长，每个振子长度是 1/4 波长。
$\frac{1}{4}\lambda=\frac{1}{4} \times \frac{0.95c}{f(mHz)}=\frac{1}{4}\times \frac{3\times 10^8\times 0.95}{f\times 10^6}=71.25$


# 跋
业余无线电的考试相当于考了汽车驾照，拿到驾照才是开车的基础，考试可以突击应付，但是人生还得认真过。


73

BI1QDZ
