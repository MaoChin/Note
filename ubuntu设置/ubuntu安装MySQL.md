# `ubuntu`安装`MySQL`

## 1. 更新源

```shell
sudo apt-get update
```

## 2. 安装

```shell
sudo apt-get install mysql-server
# 查看版本
mysql --version
```

## 3. 验证是否安装成功

```shell
systemctl status mysql
```

![image-20231015094358007](E:\Note\ubuntu设置\ubuntu安装MySQL.assets\image-20231015094358007.png)

## 4. 登录

```shell
sudo mysql -u root -p
```

