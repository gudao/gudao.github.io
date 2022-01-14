---
title: "[转]Linux——查看系统硬件信息"
date: 2012-07-06
draft: false
tags: [Linux,命令查看系统硬件]
categories: [Ubuntu]
---

前言：下面实验的信息是我在几台配置不同服务器的测试结果，操作系统分别为Red Hat Enterprise Linux Server release 6.0 (Santiago)、Ubuntu 7.10。所以你看到我实验信息的不同时，请不要大惊小怪。而且有些命令也不是所有Linux操作系统都支持（例如在Ubuntu 7.10就不支持dmesg），下面的内容是收集整理的，如有错误或新的方法，也会不停整理、更新。 

一：查看CPU信息 

1、CPU详细信息 

方法1：CPU信息一般保存在proc目录下的cpuinfo文件中。如下所示。 

![](http://www.cnblogs.com/Images/OutliningIndicators/ExpandedBlockStart.gif) 

<pre>[root@DB-Server ~]# more /proc/cpuinfo
processor       : 0
vendor_id       : AuthenticAMD
cpu family      : 16
model           : 5
model name      : AMD Athlon(tm) II X3 450 Processor
stepping        : 3
cpu MHz         : 800.000
cache size      : 512 KB
physical id     : 0
siblings        : 3
core id         : 0
cpu cores       : 3
apicid          : 0
initial apicid  : 0
fpu             : yes
fpu_exception   : yes
cpuid level     : 5
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic mtrr pge mca cmov pat 
pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext fxsr_opt pdpe1gb rdtscp lm 
3dnowext 3dnow constant_tsc rep_good nonstop_tsc extd_apicid pni monitor cx16 po
pcnt lahf_lm cmp_legacy svm extapic cr8_legacy abm sse4a misalignsse 3dnowprefet
ch osvw ibs skinit wdt npt lbrv svm_lock nrip_save
bogomips        : 6428.41
TLB size        : 1024 4K pages
clflush size    : 64
cache_alignment : 64
address sizes   : 48 bits physical, 48 bits virtual
power management: ts ttp tm stc 100mhzsteps hwpstate

processor       : 1
vendor_id       : AuthenticAMD
cpu family      : 16
model           : 5
model name      : AMD Athlon(tm) II X3 450 Processor
stepping        : 3
cpu MHz         : 800.000  --注意：AMD的CPU的频率会调整
cache size      : 512 KB
physical id     : 0
siblings        : 3
core id         : 1
cpu cores       : 3
apicid          : 1
initial apicid  : 1
fpu             : yes
fpu_exception   : yes
cpuid level     : 5
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext
 fxsr_opt pdpe1gb rdtscp lm 3dnowext 3dnow constant_tsc rep_good nonstop_tsc extd_apicid pni monitor cx16 popcnt lahf_lm cmp_legacy 
svm extapic cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw ibs skinit wdt npt lbrv svm_lock nrip_save
bogomips        : 6428.64
TLB size        : 1024 4K pages
clflush size    : 64
cache_alignment : 64
address sizes   : 48 bits physical, 48 bits virtual
power management: ts ttp tm stc 100mhzsteps hwpstate

processor       : 2
vendor_id       : AuthenticAMD
cpu family      : 16
model           : 5
model name      : AMD Athlon(tm) II X3 450 Processor
stepping        : 3
cpu MHz         : 800.000
cache size      : 512 KB
physical id     : 0
siblings        : 3
core id         : 2
cpu cores       : 3
apicid          : 2
initial apicid  : 2
fpu             : yes
fpu_exception   : yes
cpuid level     : 5
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext
 fxsr_opt pdpe1gb rdtscp lm 3dnowext 3dnow constant_tsc rep_good nonstop_tsc extd_apicid pni monitor cx16 popcnt lahf_lm cmp_legacy 
svm extapic cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw ibs skinit wdt npt lbrv svm_lock nrip_save
bogomips        : 6428.63
TLB size        : 1024 4K pages
clflush size    : 64
cache_alignment : 64
address sizes   : 48 bits physical, 48 bits virtual
power management: ts ttp tm stc 100mhzsteps hwpstate </pre>


方法2：使用dmesg命令（注意CPU应为大写） 


<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@m1 ~]$dmesg | grep CPU

SMP: Allowing 8 CPUs, 5 hotplug CPUs

NR_CPUS:4096 nr_cpumask_bits:8 nr_cpu_ids:8 nr_node_ids:1 PERCPU: Embedded 31 pages/cpu @ffff880028200000 s95064 r8192 d23720 u262144

CPU: Physical Processor ID: 0 CPU: Processor Core ID: 0 mce: CPU supports 6 MCE banks

CPU0: AMD Athlon(tm) II X3 450 Processor stepping 03 Brought up 3 CPUs

processor LNXCPU:00: registered as cooling_device0

processor LNXCPU:01: registered as cooling_device1

processor LNXCPU:02: registered as cooling_device2</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


方法3：使用dmidecode查看CPU信息 

![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code  

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# dmidecode -t processor

# dmidecode 2.10 SMBIOS 2.4 present.

Handle 0x0004, DMI type 4, 35 bytes

Processor Information

Socket Designation: Socket M2

Type: Central Processor

Family: Athlon

Manufacturer: AMD

ID: 53 0F 10 00 FF FB 8B 17 Signature: Family 16, Model 5, Stepping 3 FlagsPGE (Page global enable)

:

FPU (Floating-point unit on-chip)

VME (Virtual mode extension)

DE (Debugging extension)

PSE (Page size extension)

TSC (Time stamp counter)

MSR (Model specific registers)

PAE (Physical address extension)

MCE (Machine check exception)

CX8 (CMPXCHG8 instruction supported)

APIC (On-chip APIC hardware supported)

SEP (Fast system call)

MTRR (Memory type range registers)MCA (Machine check architecture)

CMOV (Conditional move instruction supported)

PAT (Page attribute table)

PSE-36 (36-bit page size extension)

CLFSH (CLFLUSH instruction supported)

MMX (MMX technology supported)

FXSR (Fast floating-point save and restore)

SSE (Streaming SIMD extensions)

SSE2 (Streaming SIMD extensions 2)

HTT (Hyper-threading technology)

Version: AMD Athlon(tm) II X3 450 Processor

Voltage: 1.6 V

External Clock: 200 MHz

Max Speed: 3000 MHz

Current Speed: 3200 MHz

Status: Populated, Enabled

Upgrade: Socket 754 L1 Cache Handle: 0x0008 L2 Cache Handle: 0x000A L3 Cache Handle: Not Provided

Serial Number:

Asset Tag:

Part Number:</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 



2、查看物理CPU个数 


<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# cat /proc/cpuinfo | grep &#34;physical id&#34; | sort | uniq | wc -l 1 [root@DB-Server ~]# dmesg | grep CPU | grep &#34;Physical Processor ID&#34; | uniq | wc -l 1</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


3、查看逻辑CPU个数 


<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# cat /proc/cpuinfo | grep &#34;processor&#34; | wc -l 3 [root@DB-Server ~]# dmesg | grep &#34;CPU&#34; | grep &#34;processor&#34; | wc -l 3</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


4、查看CPU是几核的 



<pre>[root@DB-Server ~]# cat /proc/cpuinfo | grep &#34;cores&#34; | uniq cpu cores : 3</pre>


5、查看CPU的主频 


<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# cat /proc/cpuinfo | grep MHz | uniq cpu MHz : 800.000 [root@DB-Server ~]# cat /proc/cpuinfo | grep MHz

cpu MHz : 800.000 cpu MHz : 800.000 cpu MHz : 800.000</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


6、查看CPU型号信息 



<pre>[root@DB-Server ~]# cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c 3 AMD Athlon(tm) II X3 450 Processor</pre>


7、通过physical id 可以判断物理CPU个数 


<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# cat /proc/cpuinfo | grep physical | uniq -c 1 physical id : 0 1 address sizes : 48 bits physical, 48 bits virtual 1 physical id : 0 1 address sizes : 48 bits physical, 48 bits virtual 1 physical id : 0 1 address sizes : 48 bits physical, 48 bits virtual</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


8、查看CPU是否支持64位运算 



<pre>[root@DB-Server ~]# cat /proc/cpuinfo | grep flags | grep &#39;lm&#39; | wc -l 3 结果大于0，说明支持64位运算，lm指long mode 支持lm则是64bit</pre>



<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# getconf LONG_BIT 64 etl:/home/etl/$getconf LONG_BIT（另外一台服务器） 32 说明当前CPU运行在32位模式下，当不代表CPU不支持64位</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


 

二、 查看内存信息 

![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code  

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# more /proc/meminfo
MemTotal: 7541288 kB
MemFree: 215388 kB
Buffers: 186228 kB
Cached: 6433572 kB
SwapCached: 77404 kB
Active: 5489928 kB
Inactive: 1346252 kB
Active(anon): 5193596 kB
Inactive(anon): 1015024 kB
Active(file): 296332 kB
Inactive(file): 331228 kB
Unevictable: 0 kB
Mlocked: 0 kB
SwapTotal: 9781240 kB
SwapFree: 9430432 kB
Dirty: 0 kB
Writeback: 0 kB
AnonPages: 139432 kB
Mapped: 3878064 kB
Shmem: 5992240 kB
Slab: 328284 kB
SReclaimable: 159572 kB
SUnreclaim: 168712 kB
KernelStack: 2056 kB
PageTables: 99256 kB
NFS_Unstable: 0 kB
Bounce: 0 kB
WritebackTmp: 0 kB
CommitLimit: 13551884 kB
Committed_AS: 6943792 kB
VmallocTotal: 34359738367 kB
VmallocUsed: 301620 kB
VmallocChunk: 34359431420 kB
HardwareCorrupted: 0 kB
AnonHugePages: 30720 kB
HugePages_Total: 0 HugePages_Free: 0 HugePages_Rsvd: 0 HugePages_Surp: 0 Hugepagesize: 2048 kB
DirectMap4k: 8128 kB
DirectMap2M: 2611200 kB
DirectMap1G: 5242880 kB</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 



你也可以用free命令查看 

![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code  

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# free -k
             total       used free shared    buffers     cached
Mem: 7541288 7296708 244580 0 184228 6423360 -/+ buffers/cache: 689120 6852168 Swap: 9781240 351944 9429296 [root@DB-Server ~]# free -m
             total       used free shared    buffers     cached
Mem: 7364 7125 238 0 179 6272 -/+ buffers/cache: 672 6691 Swap: 9551 343 9208 [root@DB-Server ~]# free -g
             total       used free shared    buffers     cached
Mem: 7 6 0 0 0 6 -/+ buffers/cache: 0 6 Swap: 9 0 8</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 



查看内存条型号 

![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code  

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# dmidecode -t memory
# dmidecode 2.10 SMBIOS 2.4 present.

Handle 0x0005, DMI type 5, 20 bytes
Memory Controller Information
        Error Detecting Method: 64-bit ECC
        Error Correcting Capabilities:
                None
        Supported Interleave: One-way Interleave
        Current Interleave: One-way Interleave
        Maximum Memory Module Size: 4096 MB
        Maximum Total Memory Size: 8192 MB
        Supported Speeds: 70 ns 60 ns 50 ns
        Supported Memory Types:
                Standard
                DIMM
        Memory Module Voltage: 2.9 V
        Associated Memory Slots: 2 0x0006 0x0007 Enabled Error Correcting Capabilities:
                None

Handle 0x0006, DMI type 6, 12 bytes
Memory Module Information
        Socket Designation: A0
        Bank Connections: 1 Current Speed: 53 ns
        Type: Other Unknown EDO
        Installed Size: 4096 MB (Double-bank Connection)
        Enabled Size: 4096 MB (Double-bank Connection)
        Error Status: OK

Handle 0x0007, DMI type 6, 12 bytes
Memory Module Information
        Socket Designation: A1
        Bank Connections: 2 Current Speed: 53 ns
        Type: Other Unknown EDO
        Installed Size: 4096 MB (Double-bank Connection)
        Enabled Size: 4096 MB (Double-bank Connection)
        Error Status: OK

Handle 0x0020, DMI type 16, 15 bytes
Physical Memory Array
        Location: System Board Or Motherboard
        Use: System Memory
        Error Correction Type: None
        Maximum Capacity: 8 GB    --支持的最大内存容量
        Error Information Handle: Not Provided
        Number Of Devices: 2 --内存插槽数

Handle 0x0021, DMI type 17, 27 bytes
Memory Device
        Array Handle: 0x0020 Error Information Handle: Not Provided
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 4096 MB            --内存的大小
        Form Factor: DIMM
        Set: None
        Locator: A0
        Bank Locator: Bank0/1 Type: Unknown
        Type Detail: None
        Speed: 1333 MHz         --内存的频率
        Manufacturer: None --制造厂商
        Serial Number: None
        Asset Tag: None
        Part Number: None

Handle 0x0022, DMI type 17, 27 bytes
Memory Device
        Array Handle: 0x0020 Error Information Handle: Not Provided
        Total Width: 64 bits
        Data Width: 64 bits
        Size: 4096 MB
        Form Factor: DIMM
        Set: None
        Locator: A1
        Bank Locator: Bank2/3 Type: Unknown
        Type Detail: None
        Speed: 1333 MHz
        Manufacturer: None
        Serial Number: None
        Asset Tag: None
        Part Number: None</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 



三：查看磁盘信息 

![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code  

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[etl@DB-Server proc]$ cat /proc/diskstats 1 0 ram0 0 0 0 0 0 0 0 0 0 0 0 1 1 ram1 0 0 0 0 0 0 0 0 0 0 0 1 2 ram2 0 0 0 0 0 0 0 0 0 0 0 1 3 ram3 0 0 0 0 0 0 0 0 0 0 0 1 4 ram4 0 0 0 0 0 0 0 0 0 0 0 1 5 ram5 0 0 0 0 0 0 0 0 0 0 0 1 6 ram6 0 0 0 0 0 0 0 0 0 0 0 1 7 ram7 0 0 0 0 0 0 0 0 0 0 0 1 8 ram8 0 0 0 0 0 0 0 0 0 0 0 1 9 ram9 0 0 0 0 0 0 0 0 0 0 0 1 10 ram10 0 0 0 0 0 0 0 0 0 0 0 1 11 ram11 0 0 0 0 0 0 0 0 0 0 0 1 12 ram12 0 0 0 0 0 0 0 0 0 0 0 1 13 ram13 0 0 0 0 0 0 0 0 0 0 0 1 14 ram14 0 0 0 0 0 0 0 0 0 0 0 1 15 ram15 0 0 0 0 0 0 0 0 0 0 0 7 0 loop0 0 0 0 0 0 0 0 0 0 0 0 7 1 loop1 0 0 0 0 0 0 0 0 0 0 0 7 2 loop2 0 0 0 0 0 0 0 0 0 0 0 7 3 loop3 0 0 0 0 0 0 0 0 0 0 0 7 4 loop4 0 0 0 0 0 0 0 0 0 0 0 7 5 loop5 0 0 0 0 0 0 0 0 0 0 0 7 6 loop6 0 0 0 0 0 0 0 0 0 0 0 7 7 loop7 0 0 0 0 0 0 0 0 0 0 0 11 0 sr0 0 0 0 0 0 0 0 0 0 0 0 8 0 sda 290401 173708 13610308 1138669 5592296 7339424 82186744 31033036 0 29103930 32162184 8 1 sda1 747 51 23682 826 21 1 176 197 0 859 1022 8 2 sda2 289492 173644 13585226 1137643 2933735 7339423 82186568 3822703 0 2288942 4956541 8 16 sdb 8378007 25553 1398248786 14372436 522826 4256363 36431016 14438999 0 13799072 28771502 8 17 sdb1 8377829 25537 1398247234 14372299 297454 4256363 36431016 11238446 0 10599179 25571609 253 0 dm-0 306988 0 12180714 1735363 12012557 0 80931896 90407053 0 28852187 92142257 253 1 dm-1 71655 0 573240 524681 136010 0 1088080 2444268 0 84622 2968950 253 2 dm-2 8490208 0 1399074474 15306343 4761273 0 36597608 1394194132 0 14056741 1409501653</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 



 

![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code  

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[etl@DB-Server proc]$ df -m
Filesystem           1M-blocks      Used Available Use% Mounted on /dev/mapper/vg_localhost-lv_root 50397 20475 27363 43% / tmpfs 3683 1 3683 1% /dev/shm /dev/sda1 485 42 419 9% /boot /dev/mapper/vg_localhost-lv_home 3695288 726656 2780922 21% /home</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 



![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code  

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[etl@DB-Server proc]$ more /proc/partitions 
major minor  #blocks  name 8 0 1953514584 sda 8 1 512000 sda1 8 2 1953001472 sda2 8 16 1953514584 sdb 8 17 1953513472 sdb1 253 0 52428800 dm-0 253 1 9781248 dm-1 253 2 3844296704 dm-2</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 



![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code  

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# fdisk -l

Disk /dev/sda: 2000.4 GB, 2000398934016 bytes 255 heads, 63 sectors/track, 243201 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk identifier: 0xb5acb5ac Device Boot      Start         End      Blocks   Id  System /dev/sda1   * 1 64 512000 83 Linux
Partition 1 does not end on cylinder boundary. /dev/sda2 64 243202 1953001472 8e  Linux LVM

Disk /dev/sdb: 2000.4 GB, 2000398934016 bytes 255 heads, 63 sectors/track, 243201 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk identifier: 0xccfbccfb Device Boot      Start         End      Blocks   Id  System /dev/sdb1 1 243202 1953513472 8e  Linux LVM

Disk /dev/dm-0: 53.7 GB, 53687091200 bytes 255 heads, 63 sectors/track, 6527 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk identifier: 0x00000000 Disk /dev/dm-0 doesn&#39;t contain a valid partition table  Disk /dev/dm-1: 10.0 GB, 10015997952 bytes 255 heads, 63 sectors/track, 1217 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk identifier: 0x00000000 Disk /dev/dm-1 doesn&#39;t contain a valid partition table  Disk /dev/dm-2: 3936.6 GB, 3936559824896 bytes 255 heads, 63 sectors/track, 478592 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk identifier: 0x00000000 Disk /dev/dm-2 doesn&#39;t contain a valid partition table</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 



![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code  

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# lvscan
  ACTIVE &#39;/dev/vg_localhost/lv_root&#39; [50.00 GiB] inherit
  ACTIVE &#39;/dev/vg_localhost/lv_home&#39; [3.58 TiB] inherit
  ACTIVE &#39;/dev/vg_localhost/lv_swap&#39; [9.33 GiB] inherit
[root@DB-Server ~]# pvscan
  PV /dev/sda2   VG vg_localhost   lvm2 [1.82 TiB / 0 free]
  PV /dev/sdb1   VG vg_localhost   lvm2 [1.82 TiB / 0 free]
  Total: 2 [3.64 TiB] / in use: 2 [3.64 TiB] / in no VG: 0 [0 ]</pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 



四：查看网卡信息 

1. mii-tool 指令来查看网卡信息。它主要是用于配置网卡工作模式的指令，同时也可以用于查询、监控工作。 

2. 直接输入dmesg可以看到开机检测到硬件信息 

3. ethtool eth0 采用此命令查到网卡相关的技术指标 (不一定所有网卡支持都支持此命令) 

4. more /etc/sysconfig/network-scripts/ifcfg-eth0 用这个可以看到网卡的信息 

5. ifconfig 

6. lspci 

网卡配置文件 

每一个网络介质都对应一个配置文件，超级用户可以通过这些配置文件来配置网卡 

网卡配置文件存放在/etc/sysconfig/network-scripts/目录中 

/etc/sysconfig/network-scripts/ifcfg-interface-name 

设置文件ifcfg-interface-name包含了初始化接口所需的大部分周详信息，其中interface-name将根据网卡的类型和排序而不同，一般其名字为eth0、eth1、ppp0等， 

其中eth表示以太(eth0)类型网卡，0表示第一块网卡、1表示第二块网卡，而ppp0则表示point-to-poirt protocol网络接口。在ifcfg文件中定义的各项目取决于接口类型。 

DEVICE=name 其中name是物理设备名 

IPADDR=addr 其中addr是IP地址 

NETMASK=mask 其中mask是网络掩码值 

NETWORK=addr 其中addr是网络地址 

BROADCAST=addr 其中addr是广播地址 

GATEWAY=addr 其中addr是网关地址 

ONBOOT=answer 其中answer是yes(引导时激活设备)或no（引导时不激活设备） 

USECTL=answer 其中answer是yes（非root用户能控制该设备）或no 

BOOTPROTO=proto 

[root@DB-Server ~]# ethtool eth0 

Settings for eth0: 

Supported ports: [ TP ] 

Supported link modes: 10baseT/Half 10baseT/Full  

100baseT/Half 100baseT/Full  

1000baseT/Full  

Supports auto-negotiation: Yes 

Advertised link modes: 10baseT/Half 10baseT/Full  

100baseT/Half 100baseT/Full  

1000baseT/Full  

Advertised pause frame use: No 

Advertised auto-negotiation: Yes 

Speed: 100Mb/s 

Duplex: Full 

Port: Twisted Pair 

PHYAD: 0 

Transceiver: internal 

Auto-negotiation: on 

MDI-X: Unknown 

Supports Wake-on: pg 

Wake-on: d 

Current message level: 0x00000001 (1) 

Link detected: yes 

ethtool -i eth0 加上-i查看网卡驱动 

[root@DB-Server ~]# ethtool -i eth0 

driver: ATL1C 

version: 1.0.1.14 

firmware-version: L1e 

bus-info: 0000:02:00.0 

[root@DB-Server ~]# dmesg | grep eth0 

atheros_eth 0000:02:00.0: ATL1C: eth0 NIC Link is Up&lt;100 Mbps Full Duplex&gt; 

[root@DB-Server ~]# more /etc/sysconfig/network-scripts/ifcfg-eth0 

DEVICE=eth0 

IPADDR=172.20.32.97 

NETMASK=255.255.248.0 

GATEWAY=172.20.39.251 

DNS1=172.20.0.11 

ONBOOT=yes 

HWADDR=50:e5:49:df:b0:8b 

BOOTPROTO=none 

TYPE=Ethernet 

IPV6INIT=no 

USERCTL=no 

[root@DB-Server ~]# mii-tool 

eth0: negotiated 100baseTx-FD, link ok 

五：查看主板信息 

![](http://images.cnblogs.com/OutliningIndicators/ExpandedBlockStart.gif)View Code  

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 


<pre>[root@DB-Server ~]# dmidecode|more 查看Base Board Information信息：如下所示
Base Board Information
        Manufacturer: Gigabyte Technology Co., Ltd.
        Product Name: GA-78LMT-S2P
        Version: x.x
        Serial Number:  
或用
[root@DB-Server ~]# dmidecode -t 2 # dmidecode 2.10 SMBIOS 2.4 present.

Handle 0x0002, DMI type 2, 8 bytes
Base Board Information
        Manufacturer: Gigabyte Technology Co., Ltd.
        Product Name: GA-78LMT-S2P
        Version: x.x
        Serial Number:  

[root@DB-Server ~]# dmidecode |grep -A16 &#34;System Information$&#34; System Information
        Manufacturer: Gigabyte Technology Co., Ltd.
        Product Name: GA-78LMT-S2P
        Version:  
        Serial Number:  
        UUID: 35304535-3439-4446-4230-3842FFFFFFFF
        Wake-up Type: Power Switch
        SKU Number:  
        Family:  

Handle 0x0002, DMI type 2, 8 bytes
Base Board Information
        Manufacturer: Gigabyte Technology Co., Ltd.
        Product Name: GA-78LMT-S2P
        Version: x.x
        Serial Number: </pre>

<a title="复制代码">![复制代码](http://common.cnblogs.com/images/copycode.gif)</a> 



六：查看显卡信息 

[root@DB-Server ~]# lspci |grep VGA 

01:05.0 VGA compatible controller: ATI Technologies Inc 760G [Radeon 3000] 

来源：http://www.cnblogs.com/kerrycode/archive/2012/07/06/2578658.html 


 
- - -
 