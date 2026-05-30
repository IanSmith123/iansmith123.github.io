---
layout: post
title: 又一个坏掉的硬盘
subtitle: btrfs也救不了物理损坏:(
date: 2026-02-03 17:36:22  +0800
author: Les1ie
catlog: true
mathjax: true
tags:
  - 随笔
---
# 序

电脑上有一块16T的希捷酷狼 CMR 7200RPM，目前大概用了不到5T。今天写文件时发现提示 readonly，不妙的感觉油然而生，因为两年前我坏过一块2T的SMR硬盘。

# 发生甚么事了
赶紧看看smart 信息，万幸看起来没啥问题。
```
 sudo smartctl -a /dev/sda
smartctl 7.5 2025-04-30 r5714 [x86_64-linux-6.18.7-arch1-1] (local build)
Copyright (C) 2002-25, Bruce Allen, Christian Franke, www.smartmontools.org

=== START OF INFORMATION SECTION ===
Model Family:     Seagate Exos X16
Device Model:     ST16000NM001G-2KK103
Serial Number:    ******
LU WWN Device Id: ********
Firmware Version: SN03
User Capacity:    16,000,900,661,248 bytes [16.0 TB]
Sector Sizes:     512 bytes logical, 4096 bytes physical
Rotation Rate:    7200 rpm
Form Factor:      3.5 inches
Device is:        In smartctl database 7.5/5706
ATA Version is:   ACS-4 (minor revision not indicated)
SATA Version is:  SATA 3.3, 6.0 Gb/s (current: 6.0 Gb/s)
Local Time is:    Wed Feb  4 23:14:36 2026 CST
SMART support is: Available - device has SMART capability.
SMART support is: Enabled

=== START OF READ SMART DATA SECTION ===
SMART overall-health self-assessment test result: PASSED

General SMART Values:
Offline data collection status:  (0x82) Offline data collection activity
                                        was completed without error.
                                        Auto Offline Data Collection: Enabled.
Self-test execution status:      (   0) The previous self-test routine completed
                                        without error or no self-test has ever
                                        been run.
Total time to complete Offline
data collection:                (  567) seconds.
Offline data collection
capabilities:                    (0x7b) SMART execute Offline immediate.
                                        Auto Offline data collection on/off support.
                                        Suspend Offline collection upon new
                                        command.
                                        Offline surface scan supported.
                                        Self-test supported.
                                        Conveyance Self-test supported.
                                        Selective Self-test supported.
SMART capabilities:            (0x0003) Saves SMART data before entering
                                        power-saving mode.
                                        Supports SMART auto save timer.
Error logging capability:        (0x01) Error logging supported.
                                        General Purpose Logging supported.
Short self-test routine
recommended polling time:        (   1) minutes.
Extended self-test routine
recommended polling time:        (1439) minutes.
Conveyance self-test routine
recommended polling time:        (   2) minutes.
SCT capabilities:              (0x70bd) SCT Status supported.
                                        SCT Error Recovery Control supported.
                                        SCT Feature Control supported.
                                        SCT Data Table supported.

SMART Attributes Data Structure revision number: 10
Vendor Specific SMART Attributes with Thresholds:
ID# ATTRIBUTE_NAME          FLAG     VALUE WORST THRESH TYPE      UPDATED  WHEN_FAILED RAW_VALUE
  1 Raw_Read_Error_Rate     0x000f   078   064   044    Pre-fail  Always       -       0/65620608
  3 Spin_Up_Time            0x0003   094   090   000    Pre-fail  Always       -       0
  4 Start_Stop_Count        0x0032   100   100   020    Old_age   Always       -       82
  5 Reallocated_Sector_Ct   0x0033   100   100   010    Pre-fail  Always       -       0
  7 Seek_Error_Rate         0x000f   083   060   045    Pre-fail  Always       -       0/193591923
  9 Power_On_Hours          0x0032   074   074   000    Old_age   Always       -       23174
 10 Spin_Retry_Count        0x0013   100   100   097    Pre-fail  Always       -       0
 12 Power_Cycle_Count       0x0032   100   100   020    Old_age   Always       -       82
 18 Head_Health             0x000b   100   100   050    Pre-fail  Always       -       0
187 Reported_Uncorrect      0x0032   100   100   000    Old_age   Always       -       0
188 Command_Timeout         0x0032   100   099   000    Old_age   Always       -       0 0 1
190 Airflow_Temperature_Cel 0x0022   050   049   040    Old_age   Always       -       50 (Min/Max 37/51)
192 Power-Off_Retract_Count 0x0032   100   100   000    Old_age   Always       -       44
193 Load_Cycle_Count        0x0032   100   100   000    Old_age   Always       -       1223
194 Temperature_Celsius     0x0022   050   051   000    Old_age   Always       -       50 (0 18 0 0 0)
197 Current_Pending_Sector  0x0012   100   100   000    Old_age   Always       -       0
198 Offline_Uncorrectable   0x0010   100   100   000    Old_age   Offline      -       0
199 UDMA_CRC_Error_Count    0x003e   200   200   000    Old_age   Always       -       0
200 Pressure_Limit          0x0023   100   100   001    Pre-fail  Always       -       0
240 Head_Flying_Hours       0x0000   100   253   000    Old_age   Offline      -       22545h+00m+04.102s
241 Total_LBAs_Written      0x0000   100   253   000    Old_age   Offline      -       27851641371
242 Total_LBAs_Read         0x0000   100   253   000    Old_age   Offline      -       40780633303

SMART Error Log Version: 1
No Errors Logged

SMART Self-test log structure revision number 1
No self-tests have been logged.  [To run self-tests, use: smartctl -t]

SMART Selective self-test log data structure revision number 1
 SPAN  MIN_LBA  MAX_LBA  CURRENT_TEST_STATUS
    1        0        0  Not_testing
    2        0        0  Not_testing
    3        0        0  Not_testing
    4        0        0  Not_testing
    5        0        0  Not_testing
Selective self-test flags (0x0):
  After scanning selected spans, do NOT read-scan remainder of disk.
If Selective self-test is pending on power-up, resume after 0 minute delay.

The above only provides legacy SMART information - try 'smartctl -x' for more

```

dmesg 一看天塌了，btrfs 报错了
```
$ dmesg -T
[Mon Feb  2 15:52:00 2026] br-4a46b4a5d082: port 3(vethbae4d9c) entered disabled state
[Mon Feb  2 15:52:00 2026] vethbae4d9c (unregistering): left allmulticast mode
[Mon Feb  2 15:52:00 2026] vethbae4d9c (unregistering): left promiscuous mode
[Mon Feb  2 15:52:00 2026] br-4a46b4a5d082: port 3(vethbae4d9c) entered disabled state
[Mon Feb  2 15:52:02 2026] br-67bd5a23365c: port 8(veth4752ce3) entered disabled state
[Mon Feb  2 15:52:02 2026] veth99f7a6b: renamed from eth0
[Mon Feb  2 15:52:02 2026] br-67bd5a23365c: port 3(vethcdfe798) entered disabled state
[Mon Feb  2 15:52:02 2026] vethbe01bbe: renamed from eth0
[Mon Feb  2 15:52:02 2026] br-67bd5a23365c: port 8(veth4752ce3) entered disabled state
[Mon Feb  2 15:52:02 2026] veth4752ce3 (unregistering): left allmulticast mode
[Mon Feb  2 15:52:02 2026] veth4752ce3 (unregistering): left promiscuous mode
[Mon Feb  2 15:52:02 2026] br-67bd5a23365c: port 8(veth4752ce3) entered disabled state
[Mon Feb  2 15:52:02 2026] br-67bd5a23365c: port 3(vethcdfe798) entered disabled state
[Mon Feb  2 15:52:02 2026] vethcdfe798 (unregistering): left allmulticast mode
[Mon Feb  2 15:52:02 2026] vethcdfe798 (unregistering): left promiscuous mode
[Mon Feb  2 15:52:02 2026] br-67bd5a23365c: port 3(vethcdfe798) entered disabled state
[Mon Feb  2 15:52:08 2026] br-67bd5a23365c: port 2(veth18c2071) entered disabled state
[Mon Feb  2 15:52:08 2026] veth91b280a: renamed from eth0
[Mon Feb  2 15:52:08 2026] br-67bd5a23365c: port 2(veth18c2071) entered disabled state
[Mon Feb  2 15:52:08 2026] veth18c2071 (unregistering): left allmulticast mode
[Mon Feb  2 15:52:08 2026] veth18c2071 (unregistering): left promiscuous mode
[Mon Feb  2 15:52:08 2026] br-67bd5a23365c: port 2(veth18c2071) entered disabled state
[Mon Feb  2 17:24:34 2026] br-aa4dd7e44186: port 1(veth78c8e3f) entered blocking state
[Mon Feb  2 17:24:34 2026] br-aa4dd7e44186: port 1(veth78c8e3f) entered disabled state
[Mon Feb  2 17:24:34 2026] veth78c8e3f: entered allmulticast mode
[Mon Feb  2 17:24:34 2026] veth78c8e3f: entered promiscuous mode
[Mon Feb  2 17:24:34 2026] eth0: renamed from veth1dd6678
[Mon Feb  2 17:24:34 2026] br-aa4dd7e44186: port 1(veth78c8e3f) entered blocking state
[Mon Feb  2 17:24:34 2026] br-aa4dd7e44186: port 1(veth78c8e3f) entered forwarding state
[Mon Feb  2 17:37:22 2026] perf: interrupt took too long (3133 > 3128), lowering kernel.perf_event_max_sample_rate to 63000
[Mon Feb  2 21:51:53 2026] Scheduler frequency invariance went wobbly, disabling!
[Tue Feb  3 00:02:37 2026] ata1.00: exception Emask 0x50 SAct 0x7800520 SErr 0x48c0800 action 0xe frozen
[Tue Feb  3 00:02:37 2026] ata1.00: irq_stat 0x04000040, connection status changed
[Tue Feb  3 00:02:37 2026] ata1: SError: { HostInt CommWake 10B8B LinkSeq DevExch }
[Tue Feb  3 00:02:37 2026] ata1.00: failed command: READ FPDMA QUEUED
[Tue Feb  3 00:02:37 2026] ata1.00: cmd 60/08:28:28:9a:1e/00:00:42:02:00/40 tag 5 ncq dma 4096 in
                                    res 40/00:00:00:00:00/00:00:00:00:00/00 Emask 0x50 (ATA bus error)
[Tue Feb  3 00:02:37 2026] ata1.00: status: { DRDY }
[Tue Feb  3 00:02:37 2026] ata1.00: failed command: WRITE FPDMA QUEUED
[Tue Feb  3 00:02:37 2026] ata1.00: cmd 61/20:40:d0:73:1b/00:00:42:02:00/40 tag 8 ncq dma 16384 out
                                    res 40/00:01:00:00:00/00:00:00:00:00/00 Emask 0x50 (ATA bus error)
[Tue Feb  3 00:02:37 2026] ata1.00: status: { DRDY }
[Tue Feb  3 00:02:37 2026] ata1.00: failed command: READ FPDMA QUEUED
[Tue Feb  3 00:02:37 2026] ata1.00: cmd 60/20:50:c0:30:02/00:00:39:02:00/40 tag 10 ncq dma 16384 in
                                    res 40/00:00:00:00:00/00:00:00:00:00/00 Emask 0x50 (ATA bus error)
[Tue Feb  3 00:02:37 2026] ata1.00: status: { DRDY }
[Tue Feb  3 00:02:37 2026] ata1.00: failed command: WRITE FPDMA QUEUED
[Tue Feb  3 00:02:37 2026] ata1.00: cmd 61/a0:b8:40:ea:f3/00:00:37:02:00/40 tag 23 ncq dma 81920 out
                                    res 40/00:00:00:00:00/00:00:00:00:00/00 Emask 0x50 (ATA bus error)
[Tue Feb  3 00:02:37 2026] ata1.00: status: { DRDY }
[Tue Feb  3 00:02:37 2026] ata1.00: failed command: WRITE FPDMA QUEUED
[Tue Feb  3 00:02:37 2026] ata1.00: cmd 61/40:c0:c0:e9:d3/00:00:37:02:00/40 tag 24 ncq dma 32768 out
                                    res 40/00:00:00:00:00/00:00:00:00:00/00 Emask 0x50 (ATA bus error)
[Tue Feb  3 00:02:37 2026] ata1.00: status: { DRDY }
[Tue Feb  3 00:02:37 2026] ata1.00: failed command: WRITE FPDMA QUEUED
[Tue Feb  3 00:02:37 2026] ata1.00: cmd 61/a0:c8:40:ea:d3/00:00:37:02:00/40 tag 25 ncq dma 81920 out
                                    res 40/00:01:00:00:00/00:00:00:00:00/00 Emask 0x50 (ATA bus error)
[Tue Feb  3 00:02:37 2026] ata1.00: status: { DRDY }
[Tue Feb  3 00:02:37 2026] ata1.00: failed command: WRITE FPDMA QUEUED
[Tue Feb  3 00:02:37 2026] ata1.00: cmd 61/20:d0:e0:ea:d3/00:00:37:02:00/40 tag 26 ncq dma 16384 out
                                    res 40/00:00:00:00:00/00:00:00:00:00/00 Emask 0x50 (ATA bus error)
[Tue Feb  3 00:02:37 2026] ata1.00: status: { DRDY }
[Tue Feb  3 00:02:37 2026] ata1: hard resetting link
[Tue Feb  3 00:02:43 2026] ata1: link is slow to respond, please be patient (ready=0)
[Tue Feb  3 00:02:47 2026] ata1: hard resetting link
[Tue Feb  3 00:02:53 2026] ata1: link is slow to respond, please be patient (ready=0)
[Tue Feb  3 00:02:55 2026] ata1: SATA link up 6.0 Gbps (SStatus 133 SControl 300)
[Tue Feb  3 00:02:55 2026] ata1.00: configured for UDMA/133
[Tue Feb  3 00:02:55 2026] ata1: EH complete
[Tue Feb  3 00:03:42 2026] BTRFS error (device sda): parent transid verify failed on logical 5303776067584 mirror 1 wanted 2202467 found 2200906
[Tue Feb  3 00:03:42 2026] BTRFS error (device sda): parent transid verify failed on logical 5303776067584 mirror 2 wanted 2202467 found 2200906
[Tue Feb  3 00:03:42 2026] BTRFS error (device sda): failed to run delayed ref for logical 4732748439552 num_bytes 4096 type 178 action 1 ref_mod 1: -5
[Tue Feb  3 00:03:42 2026] BTRFS error (device sda state A): Transaction aborted (error -5)
[Tue Feb  3 00:03:42 2026] BTRFS: error (device sda state A) in btrfs_run_delayed_refs:2161: errno=-5 IO failure
[Tue Feb  3 00:03:42 2026] BTRFS info (device sda state EA): forced readonly
[Tue Feb  3 00:03:44 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2819062 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:44 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2819066 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:44 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2819067 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:44 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2819068 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:44 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2819069 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:49 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2820807 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:49 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2827515 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:49 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2841729 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:49 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2841731 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:49 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2841732 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:49 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2841734 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:49 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2841736 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:49 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2841737 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:49 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2841739 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:49 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2841740 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:54 2026] writepage_delalloc: 89 callbacks suppressed
[Tue Feb  3 00:03:54 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2842136 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:59 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2850319 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:03:59 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2852716 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:04 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2856299 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:04 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2866113 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:04 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2866115 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:04 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2866116 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:04 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2866118 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:04 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2866119 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:04 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2866120 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:04 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2866121 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:04 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2866122 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:04 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2866123 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:09 2026] writepage_delalloc: 10554 callbacks suppressed
[Tue Feb  3 00:04:09 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2882309 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:09 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2882310 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:09 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2882311 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:09 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2882314 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:09 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2882317 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:09 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2882320 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:09 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2882321 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:09 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2882323 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:09 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2882325 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:09 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2882326 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:14 2026] writepage_delalloc: 21297 callbacks suppressed
[Tue Feb  3 00:04:14 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2912402 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:14 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2912560 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:14 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2912613 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:14 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2912615 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:14 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2912622 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:14 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2912623 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:14 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2912624 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:15 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2912672 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:15 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2912942 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:15 2026] BTRFS error (device sda state EA): failed to run delalloc range, root=1632 ino=2912945 folio=0 submit_bitmap=0 start=0 len=4096: -30
[Tue Feb  3 00:04:43 2026] traps: postgres[3706] general protection fault ip:7fe62f5f850f sp:7ffee2d9a8d0 error:0 in libc.so.6[2650f,7fe62f5f8000+155000]
[Tue Feb  3 00:04:43 2026] br-0bb9d22f646d: port 2(veth4f44e67) entered disabled state
[Tue Feb  3 00:04:43 2026] veth54301a6: renamed from eth0
[Tue Feb  3 00:04:43 2026] overlay: filesystem on /segate/docker/overlay2/d8937bf4091189ffb42f332c7e5b71bda71c80a808a4144e69b616f3e871e8f1/diff is read-only
[Tue Feb  3 00:05:42 2026] veth0fe958f: renamed from eth0
[Tue Feb  3 00:05:42 2026] br-0bb9d22f646d: port 3(veth54dda0c) entered disabled state
[Tue Feb  3 00:05:42 2026] overlay: filesystem on /segate/docker/overlay2/20ac23b451c3e92daaa0c95a1eaf752de5638508f62be84d8bd60977053e6f87/diff is read-only
[Tue Feb  3 01:58:47 2026] perf: interrupt took too long (3935 > 3916), lowering kernel.perf_event_max_sample_rate to 50000
[Tue Feb  3 12:30:30 2026] perf: interrupt took too long (4934 > 4918), lowering kernel.perf_event_max_sample_rate to 40000
[Tue Feb  3 13:45:33 2026] /proc/cgroups lists only v1 controllers, use cgroup.controllers of root cgroup for v2 info
[Tue Feb  3 15:49:15 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 1 wanted 1 found 0
[Tue Feb  3 15:49:15 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 2 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 1 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 2 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 1 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 2 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 1 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 2 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 1 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 2 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 1 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 2 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 1 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 2 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 1 wanted 1 found 0
[Tue Feb  3 15:49:16 2026] BTRFS error (device sda state EA): level verify failed on logical 5324488294400 mirror 2 wanted 1 found 0
[Tue Feb  3 15:49:17 2026] BTRFS error (device sda state EA): parent transid verify failed on logical 5303756800000 mirror 1 wanted 2202467 found 2200906
[Tue Feb  3 15:49:17 2026] BTRFS error (device sda state EA): parent transid verify failed on logical 5303756800000 mirror 2 wanted 2202467 found 2200906
[Tue Feb  3 15:49:17 2026] BTRFS error (device sda state EA): parent transid verify failed on logical 5303756800000 mirror 1 wanted 2202467 found 2200906
[Tue Feb  3 15:49:17 2026] BTRFS error (device sda state EA): parent transid verify failed on logical 5303756800000 mirror 2 wanted 2202467 found 2200906
[Tue Feb  3 15:49:17 2026] BTRFS error (device sda state EA): parent transid verify failed on logical 5303756800000 mirror 1 wanted 2202467 found 2200906
[Tue Feb  3 15:49:17 2026] BTRFS error (device sda state EA): parent transid verify failed on logical 5303756800000 mirror 2 wanted 2202467 found 2200906
[Tue Feb  3 15:49:17 2026] BTRFS error (device sda state EA): parent transid verify failed on logical 5303756800000 mirror 1 wanted 2202467 found 2200906
[Tue Feb  3 15:49:17 2026] BTRFS error (device sda state EA): parent transid verify failed on logical 5303756800000 mirror 2 wanted 2202467 found 2200906
```

当时选btrfs就考虑到他的一个卖点是CoW和故障恢复，于是让我来看看是怎么个事情吧

Btrfs 默认情况下Metadata是有DUP冗余的，看上面的mirror1 mirror2 可以知道已经在尝试读两个 metadata 副本了，但两个副本都不满足当前父节点期望的 generation。

检查错误点
```
$ btrfs check --readonly /dev/sda

parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
parent transid verify failed on 5303866048512 wanted 2202467 found 2200906
Ignoring transid failure
[1]    2512 segmentation fault  sudo btrfs check --readonly /dev/sda
```
没想到遇到了段错误，可能某个区块的形式和预期不同，解析失败直接就歇菜了。看报错提示还是能发现有一些 data区块出现了脏数据

清理错误的事务
```
sudo btrfs rescue zero-log /dev/sda
$ sudo btrfs rescue chunk-recover -v /dev/sda
All Devices:
	Device: id = 1, name = /dev/sda

Scanning: 3472503767040 in dev0scan chunk headers error
Chunk tree recovery aborted
```
![](img/btrfs-repair-rescue-zero-log.png)

试图使用旧的 root 挂载，失败
```
sudo mount -o ro,usebackuproot /dev/sda /mnt/udisk
sudo mount -o ro,subvolid=5 /dev/sda /mnt/udisk
sudo mount -o ro,usebackuproot,subvolid=5 /dev/sda /mnt/udisk
```
![](img/btrfs-mount-failed.png)

试图重建 chunk tree
```bash
sudo btrfs rescue chunk-recover /dev/sda

Scanning: 3472489447424 in dev0
scan chunk headers error
```
![](img/btrfs-rescue-chunk-recover-error.png)

观测磁盘的 supberblock，目前看来 superblock 是正常的，但是 superblock 指向的数据出错了。结合前面的 `3472489447424` ，约等于 3.4 T，但是 superblock 提示 `total_bytes		16000900661248 bytes_used		4916850024448` ，所以实在数据恢复的半途中有数据无法解析，存在错误的 block
```
superblock: bytenr=65536, device=/dev/sda
---------------------------------------------------------
csum_type		0 (crc32c)
csum_size		4
csum			0xb97817dd [match]
bytenr			65536
flags			0x1
			( WRITTEN )
magic			_BHRfS_M [match]
fsid			dcb1b3da-0cc8-421b-93b7-4a7948914c60
metadata_uuid		00000000-0000-0000-0000-000000000000
label			segate
generation		2202467
root			5313911668736
sys_array_size		129
chunk_root_generation	2202464
root_level		1
chunk_root		23379968
chunk_root_level	1
log_root		0
log_root_transid (deprecated)	0
log_root_level		0
total_bytes		16000900661248
bytes_used		4916850024448
sectorsize		4096
nodesize		16384
leafsize (deprecated)	16384
stripesize		4096
root_dir		6
num_devices		1
compat_flags		0x0
compat_ro_flags		0x7
			( FREE_SPACE_TREE |
			  FREE_SPACE_TREE_VALID |
			  VERITY )
incompat_flags		0x361
			( MIXED_BACKREF |
			  BIG_METADATA |
			  EXTENDED_IREF |
			  SKINNY_METADATA |
			  NO_HOLES )
cache_generation	0
uuid_tree_generation	2202467
dev_item.uuid		33d4eddd-4409-491f-a7e6-0148710107d9
dev_item.fsid		dcb1b3da-0cc8-421b-93b7-4a7948914c60 [match]
dev_item.type		0
dev_item.total_bytes	16000900661248
dev_item.bytes_used	4978966003712
dev_item.io_align	4096
dev_item.io_width	4096
dev_item.sector_size	4096
dev_item.devid		1
dev_item.dev_group	0
dev_item.seek_speed	0
dev_item.bandwidth	0
dev_item.generation	0

superblock: bytenr=67108864, device=/dev/sda
---------------------------------------------------------
csum_type		0 (crc32c)
csum_size		4
csum			0x19193f13 [match]
bytenr			67108864
flags			0x1
			( WRITTEN )
magic			_BHRfS_M [match]
fsid			dcb1b3da-0cc8-421b-93b7-4a7948914c60
metadata_uuid		00000000-0000-0000-0000-000000000000
label			segate
generation		2202467
root			5313911668736
sys_array_size		129
chunk_root_generation	2202464
root_level		1
chunk_root		23379968
chunk_root_level	1
log_root		0
log_root_transid (deprecated)	0
log_root_level		0
total_bytes		16000900661248
bytes_used		4916850024448
sectorsize		4096
nodesize		16384
leafsize (deprecated)	16384
stripesize		4096
root_dir		6
num_devices		1
compat_flags		0x0
compat_ro_flags		0x7
			( FREE_SPACE_TREE |
			  FREE_SPACE_TREE_VALID |
			  VERITY )
incompat_flags		0x361
			( MIXED_BACKREF |
			  BIG_METADATA |
			  EXTENDED_IREF |
			  SKINNY_METADATA |
			  NO_HOLES )
cache_generation	0
uuid_tree_generation	2202467
dev_item.uuid		33d4eddd-4409-491f-a7e6-0148710107d9
dev_item.fsid		dcb1b3da-0cc8-421b-93b7-4a7948914c60 [match]
dev_item.type		0
dev_item.total_bytes	16000900661248
dev_item.bytes_used	4978966003712
dev_item.io_align	4096
dev_item.io_width	4096
dev_item.sector_size	4096
dev_item.devid		1
dev_item.dev_group	0
dev_item.seek_speed	0
dev_item.bandwidth	0
dev_item.generation	0

superblock: bytenr=274877906944, device=/dev/sda
---------------------------------------------------------
csum_type		0 (crc32c)
csum_size		4
csum			0xe49e6922 [match]
bytenr			274877906944
flags			0x1
			( WRITTEN )
magic			_BHRfS_M [match]
fsid			dcb1b3da-0cc8-421b-93b7-4a7948914c60
metadata_uuid		00000000-0000-0000-0000-000000000000
label			segate
generation		2202467
root			5313911668736
sys_array_size		129
chunk_root_generation	2202464
root_level		1
chunk_root		23379968
chunk_root_level	1
log_root		0
log_root_transid (deprecated)	0
log_root_level		0
total_bytes		16000900661248
bytes_used		4916850024448
sectorsize		4096
nodesize		16384
leafsize (deprecated)	16384
stripesize		4096
root_dir		6
num_devices		1
compat_flags		0x0
compat_ro_flags		0x7
			( FREE_SPACE_TREE |
			  FREE_SPACE_TREE_VALID |
			  VERITY )
incompat_flags		0x361
			( MIXED_BACKREF |
			  BIG_METADATA |
			  EXTENDED_IREF |
			  SKINNY_METADATA |
			  NO_HOLES )
cache_generation	0
uuid_tree_generation	2202467
dev_item.uuid		33d4eddd-4409-491f-a7e6-0148710107d9
dev_item.fsid		dcb1b3da-0cc8-421b-93b7-4a7948914c60 [match]
dev_item.type		0
dev_item.total_bytes	16000900661248
dev_item.bytes_used	4978966003712
dev_item.io_align	4096
dev_item.io_width	4096
dev_item.sector_size	4096
dev_item.devid		1
dev_item.dev_group	0
dev_item.seek_speed	0
dev_item.bandwidth	0
dev_item.generation	0


```

观测以前的旧的 superblock 存档
```
$ sudo btrfs-find-root /dev/sda

parent transid verify failed on 5303689560064 wanted 2202467 found 2200906
parent transid verify failed on 5303689560064 wanted 2202467 found 2200906
ERROR: failed to read block groups: Input/output error
Superblock thinks the generation is 2202467
Superblock thinks the level is 1
Found tree root at 5313911668736 gen 2202467 level 1
Well block 5102381727744(gen: 2202466 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 5102382366720(gen: 2202465 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 5101589790720(gen: 2202464 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811261460480(gen: 2202463 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811204411392(gen: 2202458 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811204395008(gen: 2202458 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811154538496(gen: 2202457 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811108171776(gen: 2202422 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811110187008(gen: 2202421 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811065393152(gen: 2202412 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811051892736(gen: 2202411 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811044847616(gen: 2202410 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811028381696(gen: 2202406 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4811027595264(gen: 2202405 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810990534656(gen: 2202370 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810974150656(gen: 2202362 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810974068736(gen: 2202361 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810974019584(gen: 2202359 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810969104384(gen: 2202358 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810972643328(gen: 2202357 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810954260480(gen: 2202349 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810932731904(gen: 2202348 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810906222592(gen: 2202347 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810756669440(gen: 2202343 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810712432640(gen: 2202299 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810711187456(gen: 2202298 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810667638784(gen: 2202294 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810665345024(gen: 2202293 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810660249600(gen: 2202292 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810644013056(gen: 2202291 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810621976576(gen: 2202260 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810520576000(gen: 2202219 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810521280512(gen: 2202218 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810496311296(gen: 2202194 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810495770624(gen: 2202191 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810468327424(gen: 2202172 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810463346688(gen: 2202170 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810457694208(gen: 2202152 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810455498752(gen: 2202147 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810399055872(gen: 2202127 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4810488512512(gen: 2202126 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4735230279680(gen: 2202107 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4735200624640(gen: 2202106 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734912315392(gen: 2202105 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734835343360(gen: 2202104 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734767677440(gen: 2202103 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734689738752(gen: 2202095 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734667997184(gen: 2202094 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734657101824(gen: 2202058 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734657085440(gen: 2202058 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734506582016(gen: 2202019 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734428053504(gen: 2202013 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734413668352(gen: 2202012 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734238244864(gen: 2201992 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734170808320(gen: 2201962 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4698681950208(gen: 2201946 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4698551238656(gen: 2201926 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4698575421440(gen: 2201925 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4698437959680(gen: 2201897 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4698300465152(gen: 2201889 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4698220691456(gen: 2201844 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4698199408640(gen: 2201840 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4698186285056(gen: 2201839 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4697735200768(gen: 2201838 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4697699860480(gen: 2201832 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4059460124672(gen: 2201814 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3981942767616(gen: 2201813 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3981919895552(gen: 2201812 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3981827784704(gen: 2201810 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3981796933632(gen: 2201809 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3981792968704(gen: 2201777 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3981751631872(gen: 2201776 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3981702037504(gen: 2201774 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3937469071360(gen: 2201713 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3880220131328(gen: 2201712 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3880096759808(gen: 2201684 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3879998177280(gen: 2201683 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3879996456960(gen: 2201682 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3879993540608(gen: 2201681 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3879856930816(gen: 2201673 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3879595540480(gen: 2201664 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3879530119168(gen: 2201663 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3879478263808(gen: 2201630 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3819233607680(gen: 2201627 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3819165270016(gen: 2201626 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3819058872320(gen: 2201602 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3818917724160(gen: 2201563 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3818836525056(gen: 2201562 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3818785226752(gen: 2201560 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3818631331840(gen: 2201553 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3818515202048(gen: 2201551 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3818361110528(gen: 2201541 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3763285737472(gen: 2201535 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3762917588992(gen: 2201534 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3762696847360(gen: 2201533 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3762668486656(gen: 2201531 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3762522816512(gen: 2201530 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3436719439872(gen: 2201529 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3436466503680(gen: 2201528 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3436008914944(gen: 2201526 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3146984275968(gen: 2201525 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3146649436160(gen: 2201524 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3146509172736(gen: 2201523 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3108291477504(gen: 2201521 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3107587915776(gen: 2201520 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3058931466240(gen: 2201519 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3058057363456(gen: 2201518 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3040265617408(gen: 2201517 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3040030965760(gen: 2201516 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 3039889211392(gen: 2201515 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2990121172992(gen: 2201514 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2989735559168(gen: 2201513 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2989548896256(gen: 2201512 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2989379878912(gen: 2201510 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2857054355456(gen: 2201509 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2856854208512(gen: 2201508 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2856633319424(gen: 2201507 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2842725580800(gen: 2201506 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2841798082560(gen: 2201505 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2791364689920(gen: 2201504 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2790968885248(gen: 2201503 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2790774816768(gen: 2201502 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2461021929472(gen: 2201501 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 2460305686528(gen: 2201500 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 756121600(gen: 2201499 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 5313679704064(gen: 2200918 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 5313139982336(gen: 2200915 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4901072584704(gen: 2199899 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4901025972224(gen: 2199895 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4900944248832(gen: 2199882 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4900785094656(gen: 2199880 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4900668735488(gen: 2199878 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4882200707072(gen: 2199837 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4882200494080(gen: 2199837 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4882039848960(gen: 2199744 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4881917558784(gen: 2199731 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4881867374592(gen: 2199707 level: 0) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4881659772928(gen: 2199691 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4881520099328(gen: 2199688 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4881291493376(gen: 2199686 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4881288642560(gen: 2199685 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4735098896384(gen: 2199410 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4735084740608(gen: 2199409 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4734965907456(gen: 2199403 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
Well block 4697968017408(gen: 2199286 level: 1) seems good, but generation/level doesn't match, want gen: 2202467 level: 1
```


看看旧的文件树
```
$ sudo btrfs restore -l -t 5102381727744 -v /dev/sda | more

parent transid verify failed on 5102381727744 wanted 2202467 found 2202466
parent transid verify failed on 5102381727744 wanted 2202467 found 2202466
parent transid verify failed on 5102381727744 wanted 2202467 found 2202466
Ignoring transid failure
 tree key (EXTENT_TREE ROOT_ITEM 0) 5102365687808 level 2
 tree key (DEV_TREE ROOT_ITEM 0) 5101587054592 level 1
 tree key (FS_TREE ROOT_ITEM 0) 5102380400640 level 3
 tree key (CSUM_TREE ROOT_ITEM 0) 5102362198016 level 3
 tree key (UUID_TREE ROOT_ITEM 0) 5102382628864 level 0
 tree key (FREE_SPACE_TREE ROOT_ITEM 0) 5102366195712 level 1
 tree key (378 ROOT_ITEM 714329) 3107570237440 level 2
 tree key (407 ROOT_ITEM 752283) 3108349165568 level 2
 tree key (433 ROOT_ITEM 785189) 2989470056448 level 2
 tree key (434 ROOT_ITEM 785192) 2989536821248 level 2
 tree key (503 ROOT_ITEM 868438) 3108092624896 level 2
 tree key (504 ROOT_ITEM 868441) 3107927031808 level 2
 tree key (559 ROOT_ITEM 920829) 3040381468672 level 2
 tree key (560 ROOT_ITEM 920832) 3108275650560 level 2
 tree key (615 ROOT_ITEM 982320) 3146578739200 level 2
 tree key (616 ROOT_ITEM 982323) 3818544365568 level 2
 tree key (685 ROOT_ITEM 1062529) 2843136491520 level 2
 tree key (686 ROOT_ITEM 1062532) 2989859192832 level 2
 tree key (741 ROOT_ITEM 1136854) 2841571262464 level 2
 tree key (742 ROOT_ITEM 1136857) 2856632958976 level 2
 tree key (797 ROOT_ITEM 1203406) 3762518867968 level 2
 tree key (798 ROOT_ITEM 1203409) 3818398728192 level 2
 tree key (867 ROOT_ITEM 1295853) 3981832175616 level 2
 tree key (868 ROOT_ITEM 1295856) 3982411612160 level 2
 tree key (909 ROOT_ITEM 1354390) 4058891239424 level 2
 tree key (910 ROOT_ITEM 1354395) 4059024883712 level 2
 tree key (955 ROOT_ITEM 1417994) 2843243708416 level 2
 tree key (956 ROOT_ITEM 1417997) 3436819726336 level 2
```


grok的建议
```
问题根源很可能是在写文件时发生了bus error，BTRFS的CoW（Copy-on-Write）机制检测到transid不匹配（当前generation 2202467，但某些block的transid停留在2200906），触发保护机制。BTRFS check segfault可能是因为解析损坏的chunk tree时崩溃。你的尝试（zero-log, super-recover, chunk-recover）失败，因为损坏影响了chunk tree和某些metadata block（e.g., 逻辑地址5303776067584附近）。

好消息：数据块本身可能大部分完好（bytes_used约4.9TB），你可以优先恢复数据，而不是直接修复文件系统（修复有风险，可能加剧损坏）。
```


试图在内网启动一个 nfs，本地挂载，然后把能恢复的数据放进去
```bash
➜  ~ take vdisk
➜  vdisk pwd
/data/cola/vdisk
➜  ~ sudo cat /etc/exports
# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/data/cola/vdisk 192.168.123.214(rw,sync,no_subtree_check,all_squash,anonuid=1000,anongid=1000)
➜  ~ l vdisk
total 8.0K
drwxrwxr-x  2 cola cola 4.0K Feb  4 16:50 .
drwxrwxr-x 36 cola cola 4.0K Feb  4 16:52 ..
➜  ~ stat vdisk
  File: vdisk
  Size: 4096            Blocks: 8          IO Block: 4096   directory
Device: 820h/2080d      Inode: 610174963   Links: 2
Access: (0775/drwxrwxr-x)  Uid: ( 1005/cola)   Gid: ( 1005/cola)
Access: 2026-02-04 16:51:20.269935719 +0000
Modify: 2026-02-04 16:50:00.005932973 +0000
Change: 2026-02-04 16:50:00.005932973 +0000
 Birth: 2026-02-04 16:50:00.005932973 +0000
➜  ~ sudo chown -R 1000:1000 vdisk
➜  ~ sudo chmod 0755 vdisk
➜  ~ sudo exportfs -rav

exporting 192.168.123.214:/data/cola/vdisk
➜  ~ sudo systemctl enable --now nfs-server

➜  ~ sudo exportfs -v

/data/cola/vdisk
                192.168.123.214(sync,wdelay,hide,no_subtree_check,anonuid=1000,anongid=1000,sec=sys,rw,secure,root_squash,all_squash)

```

本地挂载
```
sudo mkdir -p /mnt/vdisk
sudo pacman -S nfs-utils
sudo systemctl enable --now rpcbind
sudo mount -t nfs4 192.168.48.155:/data/cola/vdisk /mnt/vdisk
```

验证挂载是否成功，可以发现一切正常。不过注意nfs是没有鉴权的，放文件时候注意安全
```
$ mount|grep vdisk
192.168.48.155:/data/cola/vdisk on /mnt/vdisk type nfs4 (rw,relatime,vers=4.2,rsize=1048576,wsize=1048576,namlen=255,hard,fatal_neterrors=none,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=192.168.123.214,local_lock=none,addr=192.168.48.155)

$ df -h | grep vdisk
192.168.48.155:/data/cola/vdisk  118T  6.5T  106T   6% /mnt/vdisk

# cola @ arch in /mnt/vdisk [26-02-05 1:00:57] C:1
$ dd if=/dev/zero of=1g count=1024 bs=1M
1024+0 records in
1024+0 records out
1073741824 bytes (1.1 GB, 1.0 GiB) copied, 9.79063 s, 110 MB/s

```

本地用户uid是1000，服务端是1005，通过 nfs的配置实现转换


尝试恢复文件，可以看到已经能够看到文件夹和内部的文件了。由于是 nfs 挂载的磁盘，因此速度受限于内网的千兆带宽。
```
sudo btrfs restore -v /dev/sda /mnt/vdisk/tactic
```
![241](img/btrfs-repair-recover-net-speed.png)
![](img/btrfs-restore-to-vdisk.png)

限定路径前缀提取文件
```
$ sudo btrfs restore -v --path-regex '^/code' /dev/sda /mnt/vdisk/bbb
```
这里我原来的 btrfs 挂载在 /segate 路径下面，写的路径是以这个磁盘为 /，而不是以原来操作系统的挂载路径，所以原来的 `/segate/code` 需要重写为 `^/code` 路径
![](img/btrfs-restore-vdisk-code.png)

暂时无法看到原始的 / 路径下的目录名字，因此需要从以前的 cd 命令中找到以前常用的路径。

数据备份完成后，准备激进的修复
```
sudo btrfs check --repair /dev/sda
```

![](img/btrfs-check-repair.png)


很遗憾，修复失败
```
ERROR: child eb corrupted: parent bytenr=5313631633408 item=209 parent level=1 child bytenr=5303682891776 child level=1
ERROR: failed to repair root items: Input/output error
```

![](img/btrfs-repair-failed.png)

尝试更激进的修复
```
$ sudo btrfs check --repair --init-csum-tree /dev/sda

parent transid verify failed on 4698644283392 wanted 1902349 found 2202468
Ignoring transid failure
ERROR: child eb corrupted: parent bytenr=4947315195904 item=66 parent level=2 child bytenr=4698644283392 child level=0
ERROR: checksum tree refilling failed: -5
```

失败了，直接放弃吧

# 格式化重开
```
$ sudo mkfs.btrfs -f -L segate /dev/sda
btrfs-progs v6.17.1
See https://btrfs.readthedocs.io for more information.

Label:              segate
UUID:               e4dd6f41-9a78-4cf2-90cc-6af190cb08b4
Node size:          16384
Sector size:        4096	(CPU page size: 4096)
Filesystem size:    14.55TiB
Block group profiles:
  Data:             single            8.00MiB
  Metadata:         DUP               1.00GiB
  System:           DUP               8.00MiB
SSD detected:       no
Zoned device:       no
Features:           extref, skinny-metadata, no-holes, free-space-tree
Checksum:           crc32c
Number of devices:  1
Devices:
   ID        SIZE  PATH
    1    14.55TiB  /dev/sda
```

新建子卷
```
sudo btrfs subvolume create /segate/code
sudo btrfs subvolume create /segate/var
sudo btrfs subvolume create /segate/401
sudo mkdir /segate/.snapshot
```
打个快照
```
sudo btrfs subvolume snapshot -r /segate/code /segate/.snapshot/code_$(date +%Y%m%d)
```

看看效果
```
$ sudo btrfs subvolume list /segate
ID 256 gen 12 top level 5 path code
ID 257 gen 11 top level 5 path var
ID 258 gen 11 top level 5 path 401
ID 260 gen 12 top level 5 path .snapshot/code_20260206
```
fstab 挂载
```
UUID=$(sudo blkid -s UUID -o value /dev/sda)
echo "UUID=$UUID /segate btrfs defaults 0 0" | sudo tee -a /etc/fstab
```

注意这里如果在 `/segate`路径下新建文件 `/segate/example`，那么这个 `example`是不属于上述子卷的，也就难以快照处理，因此可以考虑把挂载到顶层子卷替换成其他子卷。`subvolid=5`是默认的顶层子卷。

# 跋
用了btrfs不等于高枕无忧了，定期异地多备份还是很重要的。我的这块磁盘里面的重要数据使用 syncthing实时备份到了nas和台式机上了，所以当我发现磁盘阵亡的时候情绪倒是稳定 :)

btrfs-scrub@.timer 默认是禁用状态，然而定期scrub很重要，可以及时发现错误。虽然我一直懒得弄，但我还是建议各位读者搞一个定时任务。

btrfs snapshot 针对人为的失误删数据很有用，但是硬盘坏了也得凉凉。几年来一直是用btrbk每日定时备份根目录，但是16T的数据盘因为太大了导致较难操作，本来是想通过btrfs send每天发送数据出去，但我这没有第二块硬盘给我接收这庞大的且没那么重要的数据，也就不了了之了。

多分几个数据卷，重要的数据卷可以考虑将Data/System/Metadata 都DUP存储，浪费一点点空间提高数据的生命力。

