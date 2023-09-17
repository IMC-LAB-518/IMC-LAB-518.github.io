# 管理员操作指南

## 添加新用户以及加入分组
```bash
sudo su # 进入root模式
adduser <username>
# 输入新的用户密码，以及一连串用户信息

sudo usermod -a -G <groupname> <username> # 将新用户添加进制定分组，目前群组我们只有“IMC_LAB”， 注意组外普通用户是无法访问显卡以及核心工作区

```

## 文件权限管理

目前 /work 是所有用户开放权限，  /work/imc_lab/ 是只有IMC_LAB组内成员才可以读写和操作的。更多文件权限编辑可参考： [https://www.python100.com/html/77753.html](https://www.python100.com/html/77753.html)