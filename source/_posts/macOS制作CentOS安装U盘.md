---
title: macOS制作CentOS安装U盘
date: 2017-12-14 19:23:23
tags:
- MAC
- macOS
- CentOS
---
# 查看U盘
第一步，先插入U盘，打开终端使用下面的命令查看U盘是否已经mount到系统，这时在Finder下也能看到U盘。
```bash
diskuitl list
```
终端输出类似如下内容
```bash
wangjindeMacBook-Pro:~ wangjin$ diskutil list
/dev/disk0 (internal):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                         251.0 GB   disk0
   1:                        EFI EFI                     314.6 MB   disk0s1
   2:                 Apple_APFS Container disk1         250.7 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +250.7 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD            222.4 GB   disk1s1
   2:                APFS Volume Preboot                 23.1 MB    disk1s2
   3:                APFS Volume Recovery                506.6 MB   disk1s3
   4:                APFS Volume VM                      3.2 GB     disk1s4

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *62.1 GB    disk2
   1:               Windows_NTFS WANGJIN                 62.1 GB    disk2s1
```
可以看到，上面的/dev/disk2即是U盘挂载点

# 取消U盘挂载
```bash
diskutil unmountDisk /dev/disk2
```

# 写入系统镜像到U盘
```bash
dd if=/Users/wangjin/Downloads/CentOS-7-x86_64-DVD-1708.iso of=/dev/disk2 bs=1m
```
`bs=1m`表示设置写入块大小