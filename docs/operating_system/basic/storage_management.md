# 存储管理

由于内存系统太小而且不能永久保存所有数据和程序，因此计算机系统必须提供外存来备份内存。现代计算机系统采用磁盘作为信息（程序与数据）的主要在线存储介质。文件系统提供机制，以便在线存储和访问磁盘的数据与程序。文件是由创建者定义的相关信息的集合。这些文件由操作系统映射到物理设备。文件通常按目录来组织，以便使用。

计算机连接的设备在许多方面都有差异。有的设备一次传输一个字符或一个字符块。有的只能顺序访问，有的可以随机访问。有的同步传输数据，有的异步传输数据。有的是专用的，有的是共享的。有的是只读的，有的是可读写的。它们的速度差别很大。在许多方面，它们也是最慢的主要计算及组件。

由于所有的这些设备差异，操作系统需要提供各种功能，以便应用程序控制设备的各个方面。操作系统I/O子系统的一个关键目标是为系统的其他部分提供最为简单的接口。由于设备是性能瓶颈，另一个关键是优化I/O，以便实现并发的最大化。

## 文件系统

### 文件概念

### 访问方法

### 目录与磁盘结构

### 文件系统安装

### 文件共享

### 保护


## 文件系统实现

### 文件系统结构

### 文件系统实现

### 目录实现

### 分配方法

### 空闲空间管理

### 效率与性能

### 恢复

###　NFS

### 例子：WAFL文件系统


## 大容量存储结构

###　大容量存储结构概述

### 磁盘结构

### 磁盘连接

### 磁盘调度

### 磁盘管理

### 交换空间管理

### RAID结构

### 稳定存储实现


## IO系统

###　概述

### I/O硬件

### 应用程序I/O接口

### 内核I/O子系统

### I/O请求转成硬件操作

### 流

### 性能
