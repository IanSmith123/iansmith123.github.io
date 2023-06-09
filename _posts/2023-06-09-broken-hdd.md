---
layout:		post
title:		"坏掉的 SMR 硬盘"
subtitle:	"虽然说人生并没有什么意义，但是备份确实让生活更加美丽。"
date:		2023-06-09 09:12:03  +0800
author:		"Les1ie"
catlog:   true
tags: 
  - 随笔
---

# 序言
上一次写博客是2021年7月12日，再过一个月就是两年了。两年来整日忙忙碌碌，却又碌碌无为，近月偶有写博客之想法，却又懒得动手 ;) 


昨日主力台式机的硬盘突然歇菜了，感概良多，心情起起落落，决定记下来，当作一个回忆吧

# 硬盘损坏
台式机目前是我的主力机器，系统是 Arch + i3wm。

昨天(2023-06-08T01:00:00Z) 到工位，解锁屏幕，习惯性`sudo pacman -Syu --noconfirm`，然后准备开始新一天的浑浑噩噩，发现我的 firefox 异常卡顿。看了下 CPU和内存是正常的，我就挺疑惑，于是重启了 firefox. 接着发现我的终端也是卡卡的，不能及时响应我的回车。再去 glances 看了看，iowait 挺高，但是看了下 qbittorrent 还在正常工作，也就几 kb 的上行，而且我设定了上班时间限速 10kb , 看起来也不是 qb 读写造成的。 dstat 也没有看到啥奇怪的指标。

迷惑起来了，但我并不想去找原因，因为解决这个问题并不是我当前的主要矛盾，遇事不决，重启解决。

敲下 shutdown -r now 之后，我转向了旁边的 windows 笔记本，开始了新一天的网上冲浪 :)

过了两分钟，回头一看，怎么还没开机，按理说10秒就能重启完看到 SDDM 的。这才注意到，提示我进入到了 Emergency mode。输入 root 密码，看了看日志，一脸懵，没看到啥奇怪的东西啊，有看到说 fsck 失败，但是这会导致启动失败么，我想当然的认为了这会跳过这个硬盘，然后开机挂载失败。
突然，一道闪电划过，这是 fsck 失败啊，想到了什么不好的事情，因为半个月前我看到硬盘的 smart 信息不太正常。当时硬盘出现了一次 readonly 的问题，但是重启解决了，当时想了想这上面东西基本都有备份，也就没去管他了。

这主力机的硬盘的道道有点多，不只指的是坏道有点多，历史故事也有点多。

机器有3块硬盘，一块 120G 的 sata 固态，一块是希捷的 2T 的 [SMR 机械](https://www.seagate.com/cn/zh/products/cmr-smr-list/)（写这篇博客之前我都没注意），还有一块是前两周刚上机的用来接替前面这2T硬盘的希捷 EXOS CMR机械硬盘。

根路径是那个 120G 的辣鸡 sata 固态，好在 Arch + i3wm 不太吃资源，平时只用来敲代码，挂了一些服务，计算工作都在服务器上跑的，所以除了偶尔空间不太够，倒是一直流畅。

2T 的希捷被分成了两个 1T 的分区。一个分区是以前 windows+ubuntu 双系统时候的windows的数据盘,被格式化成 ext4挂载到了 `/data` ；另一个分区是 以前 ubuntu 的根分区，在我主力使用 Arch 之后挂载到了 `~/ubuntu` 下面，所以我要访问以前的数据，需要访问路径 `/home/{USERNAME}/ubuntu/home/{USERNAME}/old_path`。这次出问题的便是这 `~/ubuntu` 这个分区。

所以现在的挂载点如下（略去无关硬盘）
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sdb      8:16   0   1.8T  0 disk 
├─sdb1   8:17   0  1000G  0 part /data
├─sdb3   8:19   0   513M  0 part 
└─sdb4   8:20   0 862.5G  0 part /home/yuyu/ubuntu
sdc      8:32   0 119.2G  0 disk 
├─sdc1   8:33   0   511M  0 part /boot
└─sdc2   8:34   0 118.7G  0 part /
```

回到正题，把报错信息上网搜索了一番，发现有人遇到同样的[问题](https://blog.csdn.net/weixin_44444810/article/details/121524643)，于是我也决定试试 fsck 看看能不能修复。

以前网上也看到有人说 fsck 修复就完事了，我也没想太多，直接 `fsck -y /dev/disk/by-uuid/12121212` 敲进去，很快，飞快的跑了起来，看起来需要一点时间，于是我继续网上冲浪。

看到有人说 fsck 会把磁盘搞得很乱，这时候发现我已经没有回头路了。

等待 fsck 的时间，心理活动如下 :)

坏消息：硬盘坏了。

好消息：似乎看起来能修。

坏消息：实验代码在里面。

好消息：写完一个部分我都顺手 git 推送到了两个不同的服务器。

坏消息：有一部分比较大的数据文件可能没加到 git 里面。

好消息：可能他们没那么重要，也有部分被 pycharm 自动同步到远程服务器了。

坏消息：实验数据也在这块坏了的硬盘。

好消息：大部分数据都在服务器，数据集有多重备份。

坏消息：除了实验代码，还有以前突发奇想写的一些其他仓库的代码。

好消息：几年都没用一次，他们也似乎没那么重要。

坏消息：我有一些配置类的数据和日志，比如 prometheus 和 clash 都在这个路径。

好消息：都能重新配置。Prometheus 抓的本机和服务器的日志并不重要。

坏消息：/data 路径也是同一块磁盘，可能也会受到影响。

好消息：全部备份到了新的希捷硬盘。


心情起起落落落落落，最后得出结论，基本没影响。


等了快一个小时， fsck 终于跑完了。看了 fsck 跑出来的文件，完全没有我的工作路径的文件，所以结局是什么都没剩下。

现在重启电脑能正常进 SDDM 了，所以的确是硬盘故障导致的开机失败。

这块机械硬盘的两个分区都是长期 90% 以上的空间占用，而我也没其他的硬盘来缓解他的压力，于是只能长期用这一丁点的空间，造成了对部分扇区的反复擦写，这对于叠瓦来说，简直就是 debuff 加 debuff，光速坏掉也就是意料之中了。

看了 smart 信息，通电时间 23000小时。SMR 的寿命也还凑合吧。以后拿来当个下载盘，放点不重要的数据。
```
ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
  1 Raw_Read_Error_Rate     0x000f   080   042   006    Pre-fail  Always       -       93759304
  3 Spin_Up_Time            0x0003   098   098   000    Pre-fail  Always       -       0
  4 Start_Stop_Count        0x0032   100   100   020    Old_age   Always       -       66
  5 Reallocated_Sector_Ct   0x0033   098   098   010    Pre-fail  Always       -       6792
  7 Seek_Error_Rate         0x000f   085   060   045    Pre-fail  Always       -       348280899
  9 Power_On_Hours          0x0032   074   074   000    Old_age   Always       -       23282h+35m+49.427s
 10 Spin_Retry_Count        0x0013   100   100   097    Pre-fail  Always       -       0
 12 Power_Cycle_Count       0x0032   100   100   020    Old_age   Always       -       60
183 Runtime_Bad_Block       0x0032   099   099   000    Old_age   Always       -       1
184 End-to-End_Error        0x0032   100   100   099    Old_age   Always       -       0
187 Reported_Uncorrect      0x0032   001   001   000    Old_age   Always       -       65535
188 Command_Timeout         0x0032   100   099   000    Old_age   Always       -       5 5 5
189 High_Fly_Writes         0x003a   100   100   000    Old_age   Always       -       0
190 Airflow_Temperature_Cel 0x0022   062   053   040    Old_age   Always       -       38 (Min/Max 36/43)
191 G-Sense_Error_Rate      0x0032   100   100   000    Old_age   Always       -       0
192 Power-Off_Retract_Count 0x0032   100   100   000    Old_age   Always       -       817
193 Load_Cycle_Count        0x0032   097   097   000    Old_age   Always       -       7002
194 Temperature_Celsius     0x0022   038   047   000    Old_age   Always       -       38 (0 20 0 0 0)
195 Hardware_ECC_Recovered  0x001a   080   064   000    Old_age   Always       -       93759304
197 Current_Pending_Sector  0x0012   100   100   000    Old_age   Always       -       200
198 Offline_Uncorrectable   0x0010   100   100   000    Old_age   Offline      -       200
199 UDMA_CRC_Error_Count    0x003e   200   200   000    Old_age   Always       -       0
240 Head_Flying_Hours       0x0000   100   253   000    Old_age   Offline      -       20730h+21m+55.140s
241 Total_LBAs_Written      0x0000   100   253   000    Old_age   Offline      -       17738219158
242 Total_LBAs_Read         0x0000   100   253   000    Old_age   Offline      -       23154170983
```


前面说到数据同步到了服务器，但其实服务器也不是绝对的安全，50天以前刚被人  `rm -rf /* `，过程在 https://v2ex.com/t/933914#r_12974302

> 两天前，服务器 50T 的数据盘突然满了，我看日志找到了当事人，他说他清理下数据。过了三分钟，他给我说他把根目录删了。本来想输入 rm -rf ./*的，结果少写了个 . 惊出一身汗，还好他按 ctrl C 足够快，删了几秒钟就发现问题了。此时 /usr 和 /bin 路径已经被干掉了，啥命令都不好使。万幸的是，看 Grafana 日志能发现数据盘空间和 /home 占用没变，数据还在，去机房进 liveCD 把原来 /home 的数据拷到数据盘，重做系统，除了浪费了点时间，基本没啥损失。


# 跋
虽然最后没啥影响，但终归是硬盘坏了。 花了两个小时灾后重建，重新配了一些服务，这也算是提了个醒，数据需要多备份吧。

Take home message:
1. 重要数据务必要备份，定期备份很重要
2. 关注硬盘健康状态，准备好 Plan B
3. 不要让硬盘以长时间占用大部分硬盘空间的状态运行，这将造成反复擦写同一片区域，加速硬盘损坏速度。固态硬盘可能有损耗均衡算法加持，可以一定程度上缓解这个问题，但是保持关注 smart 信息仍然是很重要的，毕竟我的蜗牛星际上的 16G msata SSD 的 smart 长期报警，在去年也歇菜了。



Les1ie

2023.6.9