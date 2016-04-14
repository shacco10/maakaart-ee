# Silja riistvara #

## cat /proc/cpuinfo ##
```
processor       : 0                        
vendor_id       : AuthenticAMD
cpu family      : 16
model           : 9
model name      : AMD Opteron(tm) Processor 6128
stepping        : 1
cpu MHz         : 2000.219
cache size      : 512 KB
physical id     : 0
siblings        : 8
core id         : 0
cpu cores       : 8
apicid          : 16
initial apicid  : 0
fpu             : yes
fpu_exception   : yes
cpuid level     : 5
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext fxsr_opt pdpe1gb rdtscp lm 3dnowext 3dnow constant_tsc rep_good nonstop_tsc extd_apicid amd_dcm pni monitor cx16 popcnt lahf_lm cmp_legacy svm extapic cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw ibs skinit wdt nodeid_msr
bogomips        : 4000.43
TLB size        : 1024 4K pages
clflush size    : 64
cache_alignment : 64
address sizes   : 48 bits physical, 48 bits virtual
power management: ts ttp tm stc 100mhzsteps hwpstate
```
## cat /proc/meminfo ##
```
MemTotal:        8194216 kB
MemFree:           45932 kB
Buffers:           18660 kB
Cached:          7597644 kB
SwapCached:            0 kB
Active:          1054400 kB
Inactive:        6740228 kB
Active(anon):     136372 kB
Inactive(anon):    51844 kB
Active(file):     918028 kB
Inactive(file):  6688384 kB
Unevictable:           0 kB
Mlocked:               0 kB
SwapTotal:      16466584 kB
SwapFree:       16466584 kB
Dirty:                28 kB
Writeback:             0 kB
AnonPages:        178372 kB
Mapped:            35128 kB
Shmem:              9876 kB
Slab:             294472 kB
SReclaimable:     259088 kB
SUnreclaim:        35384 kB
KernelStack:        3352 kB
PageTables:         9868 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:    20563692 kB
Committed_AS:    1749820 kB
VmallocTotal:   34359738367 kB
VmallocUsed:      295260 kB
VmallocChunk:   34354740412 kB
HardwareCorrupted:     0 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
DirectMap4k:        4608 kB
DirectMap2M:     2091008 kB
DirectMap1G:     6291456 kB
```

## cat /proc/diskstats | grep sd**##
```
   8       0 sda 8211 6802 357416 42300 27733 2962281 23922282 9848100 1 132850 9890430
   8       1 sda1 259 1325 2242 360 0 0 0 0 0 280 360
   8       2 sda2 1 0 2 10 0 0 0 0 0 10 10
   8       5 sda5 7370 4384 342136 39440 2736 44873 383136 114640 1 28150 154120
   8       6 sda6 523 1093 12572 2170 24988 2917408 23539138 9733420 0 106800 9735580
   8      16 sdb 368 911 3872 190 9 3 26 40 0 220 230
   8      17 sdb1 297 890 3136 130 3 3 18 0 0 130 130
   8      32 sdc 2483 336 66776 5630 9360 9947 133288 50850 0 21030 56480
   8      33 sdc1 2414 333 66200 5550 6663 9947 133280 39950 0 10140 45500
   8      48 sdd 2385 268 63820 5340 8929 9046 122232 52310 0 20240 57630
   8      49 sdd1 2316 265 63244 5180 6232 9046 122224 42620 0 10650 47780
   8      64 sde 1005 66 28602 2350 490 1 1474 1180 0 1560 3530
   8      80 sdf 859 61 29122 2780 420 0 1326 770 0 1180 3550
   8      96 sdg 798 59 28524 3400 489 1 1486 1130 0 1360 4530
   8     112 sdh 884 85 27396 3050 485 1 1431 370 0 1010 3420

```**

## lcpci ##
```
[root@silja anz]# lspci
00:00.0 Host bridge: ATI Technologies Inc RD890 PCI to PCI bridge (external gfx0 port A) (rev 02)
00:04.0 PCI bridge: ATI Technologies Inc RD890 PCI to PCI bridge (PCI express gpp port D)
00:09.0 PCI bridge: ATI Technologies Inc RD890 PCI to PCI bridge (PCI express gpp port H)
00:0a.0 PCI bridge: ATI Technologies Inc RD890 PCI to PCI bridge (external gfx1 port A)
00:11.0 SATA controller: ATI Technologies Inc SB700/SB800 SATA Controller [AHCI mode]
00:12.0 USB Controller: ATI Technologies Inc SB700/SB800 USB OHCI0 Controller
00:12.1 USB Controller: ATI Technologies Inc SB700 USB OHCI1 Controller
00:12.2 USB Controller: ATI Technologies Inc SB700/SB800 USB EHCI Controller
00:13.0 USB Controller: ATI Technologies Inc SB700/SB800 USB OHCI0 Controller
00:13.1 USB Controller: ATI Technologies Inc SB700 USB OHCI1 Controller
00:13.2 USB Controller: ATI Technologies Inc SB700/SB800 USB EHCI Controller
00:14.0 SMBus: ATI Technologies Inc SBx00 SMBus Controller (rev 3d)
00:14.1 IDE interface: ATI Technologies Inc SB700/SB800 IDE Controller
00:14.3 ISA bridge: ATI Technologies Inc SB700/SB800 LPC host controller
00:14.4 PCI bridge: ATI Technologies Inc SBx00 PCI to PCI Bridge
00:14.5 USB Controller: ATI Technologies Inc SB700/SB800 USB OHCI2 Controller
00:18.0 Host bridge: Advanced Micro Devices [AMD] K10 [Opteron, Athlon64, Sempron] HyperTransport Configuration
00:18.1 Host bridge: Advanced Micro Devices [AMD] K10 [Opteron, Athlon64, Sempron] Address Map
00:18.2 Host bridge: Advanced Micro Devices [AMD] K10 [Opteron, Athlon64, Sempron] DRAM Controller
00:18.3 Host bridge: Advanced Micro Devices [AMD] K10 [Opteron, Athlon64, Sempron] Miscellaneous Control
00:18.4 Host bridge: Advanced Micro Devices [AMD] K10 [Opteron, Athlon64, Sempron] Link Control
00:19.0 Host bridge: Advanced Micro Devices [AMD] K10 [Opteron, Athlon64, Sempron] HyperTransport Configuration
00:19.1 Host bridge: Advanced Micro Devices [AMD] K10 [Opteron, Athlon64, Sempron] Address Map
00:19.2 Host bridge: Advanced Micro Devices [AMD] K10 [Opteron, Athlon64, Sempron] DRAM Controller
00:19.3 Host bridge: Advanced Micro Devices [AMD] K10 [Opteron, Athlon64, Sempron] Miscellaneous Control
00:19.4 Host bridge: Advanced Micro Devices [AMD] K10 [Opteron, Athlon64, Sempron] Link Control
01:04.0 VGA compatible controller: Matrox Graphics, Inc. MGA G200eW WPCM450 (rev 0a)
02:00.0 Ethernet controller: Intel Corporation 82574L Gigabit Network Connection
03:00.0 Ethernet controller: Intel Corporation 82574L Gigabit Network Connection
04:00.0 SCSI storage controller: Marvell Technology Group Ltd. MV64460/64461/64462 System Controller, Revision B (rev 01)
```