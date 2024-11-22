+++
title = "Hyperthreading and DAWS"
slug = "hyperthreading-and-daws" # Set manually if title is not usable as slug
date = "2024-11-19T23:44:53-07:00"
# lastmod = ""
tags = ["audio", "linux", "wisdom-with-walter"]
description = "Quick guide on how to disable hyperthreading on a Ryzen 7 4800H Ubuntu 24.10 system."
showFullContent = false
hideComments = true
draft = false
+++

# Overview

*insert overview here*

## Hyperthreading on Ryzen 7 4800H in Ubuntu 24.10
### Check if HT is enabled
With lscpu stat and search with grep:
```bash
$ lscpu | grep "Thread(s) per core"
Thread(s) per core: 2
```
HT is enabled as 2 threads per core. 
Alternatively, we can also inspect the file that contains the flag with the activation mode of SMT:
```bash
$ cat /sys/devices/system/cpu/smt/active
1
```
A **value of 1 indicates that we have HT enabled,** while a 0 indicates that we have it disabled.
Moreover, we also need **to know the thread siblings list, i.e., which physical CPUs are using HT and which logical CPUs they refer to**. We can use _grep_ (with the _-H_ option to display the filename):

```bash
$ grep -H . /sys/devices/system/cpu/cpu*/topology/thread_siblings_list 
/sys/devices/system/cpu/cpu0/topology/thread_siblings_list:0-1  
/sys/devices/system/cpu/cpu10/topology/thread_siblings_list:10-11  
/sys/devices/system/cpu/cpu11/topology/thread_siblings_list:10-11  
/sys/devices/system/cpu/cpu12/topology/thread_siblings_list:12-13  
/sys/devices/system/cpu/cpu13/topology/thread_siblings_list:12-13  
/sys/devices/system/cpu/cpu14/topology/thread_siblings_list:14-15  
/sys/devices/system/cpu/cpu15/topology/thread_siblings_list:14-15  
/sys/devices/system/cpu/cpu1/topology/thread_siblings_list:0-1  
/sys/devices/system/cpu/cpu2/topology/thread_siblings_list:2-3  
/sys/devices/system/cpu/cpu3/topology/thread_siblings_list:2-3  
/sys/devices/system/cpu/cpu4/topology/thread_siblings_list:4-5  
/sys/devices/system/cpu/cpu5/topology/thread_siblings_list:4-5  
/sys/devices/system/cpu/cpu6/topology/thread_siblings_list:6-7  
/sys/devices/system/cpu/cpu7/topology/thread_siblings_list:6-7  
/sys/devices/system/cpu/cpu8/topology/thread_siblings_list:8-9  
/sys/devices/system/cpu/cpu9/topology/thread_siblings_list:8-9
```

Physical CPUs (0-2-4-...-14) and virtual CPUs (1-3-5-...-15).
### Permanently disable HT
Check grub version:
```bash
$ grub-install --version
grub-install (GRUB) 2.12-5ubuntu5.1
```
To disable hyper threading permanently append '`noht`' under '`GRUB_CMDLINE_LINUX`' in '`/etc/sysconfig/grub`' file as shown below
```bash
$ sudo nano /etc/default/grub
GRUB_CMDLINE_LINUX="nosmt"
$ sudo update-grub
```

## Results with Reaper

# References