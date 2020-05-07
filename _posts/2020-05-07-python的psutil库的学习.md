---
layout:     post
title:      python3得psutil库
subtitle:   库得学习
date:       2010-05-07
author:     eddie
header-img: img/home-bg-o.jpg
catalog: true
tags:
    - python
---

```
import psutil


def cpu_monitor():
    '''
    获取cpu的信息
    '''
    get_cpu_count=psutil.cpu_count()
    get_cpu_percent=psutil.cpu_percent()
    get_cpu_times=psutil.cpu_times()
    cpu_info={"cpu个数":get_cpu_count,"cpu使用率":get_cpu_percent,"cpu使用时间":get_cpu_times}
    #print(cpu_info["cpu使用时间"].user)
    return cpu_info

def memory_monitor():
    '''
    获取内存的相关信息
    '''
    get_mem=psutil.virtual_memory()
    mem_total=str(get_mem.total/1024/1024) + 'MB'     
    mem_used=str(get_mem.used/1024/1024) + 'MB'        
    mem_free=str(get_mem.free/1024/1024) + 'MB'       
    mem_active=str(get_mem.available/1024/1024) + 'MB'   
    mem_percent=str(get_mem.percent) + '%'
    mem_info={"总内存":mem_total,"已使用内存":mem_used,"剩余内存":mem_free,"可使用内存":mem_active,"内存使用率":mem_percent}
    return mem_info

def swap_monitor():
    '''
    获取交换分区内存信息
    '''
    get_swap_mem=psutil.swap_memory()
    mem_total=str(get_swap_mem.total/1024/1024) + 'MB'
    mem_used=str(get_swap_mem.used/1024/1024) + 'MB'
    mem_free=str(get_swap_mem.free/1024/1024) + 'MB'
    mem_percent=str(get_swap_mem.percent) + '%'
    swap_info={"总内存":mem_total,"交换分区使用内存":mem_used,"交换分区空闲内存":mem_free,"交换分区内存使用率":mem_percent}
    return swap_info

def disk_monitor():
    '''
    磁盘使用情况
    '''
    get_disk_usage=psutil.disk_usage('/')
    disk_total=str(get_disk_usage.total/1024/1024) + 'MB'
    disk_used=str(get_disk_usage.used/1024/1024) + 'MB'
    disk_free=str(get_disk_usage.free/1024/1024) + 'MB'
    disk_percent=str(get_disk_usage.percent) + '%'
    disk_usage_info={"磁盘总大小":disk_total,"磁盘使用量":disk_used,"磁盘剩余量":disk_free,"磁盘使用率":disk_percent}

    get_disk_io=psutil.disk_io_counters()
    io_read=get_disk_io.read_count
    io_write=get_disk_io.write_count
    io_read_bytes=get_disk_io.read_bytes
    io_write_bytes=get_disk_io.write_bytes
    io_read_time=get_disk_io.read_time
    io_write_time=get_disk_io.write_time
    disk_io_info={"读io数":io_read,"写io数":io_write,"io读字节数":io_read_bytes,"io写字节数":io_write_bytes,"磁盘读时间":io_read_time,"磁盘写时间":io_write_time}
    
    disk_partitions_info=psutil.disk_partitions()
    return [disk_usage_info,disk_io_info,disk_partitions_info]

def net_monitor():
    '''
    获取网络信息
    '''
    
a=psutil.net_connections()
print(a)

```