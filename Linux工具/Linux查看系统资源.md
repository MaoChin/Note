# `Linux`查看系统资源

## 1.  查看磁盘使用情况

### 1. `df`

```shell
df
# 更常用，根据大小适当显式单位
df -h
```

### 2. `du`

查看某一目录大小。

```shell
#查看当前目录大小
du -sh
#返回该目录/文件的大小
du -sh [目录/文件]
```

## 2. 查看内存使用情况

### 1. `free`

### 2. `/proc/meminfo`

### 3. `vmstat`

### 4. `top`



## 3. 查看网络使用情况

### 1. `netstat`

```shell
netstat -nltp   # tcp协议
netstat -nlup   # udp协议
```

### 2. `ifconfig`

### 3. `tcpdump`

抓包用。

### 4. `ping`



## 4. 查看`CPU`使用情况



## 5. 查看进程情况

### 1. `ps`

```shell
ps ajx 
ps -aL
```

