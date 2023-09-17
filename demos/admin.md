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



## Slurm 管理

```bash
#添加账户，指定账户名称和所属集群名称，这里的账户可以理解成用户组的概念
sacctmgr add account name=<your_account> cluster=cluster

#添加用户到slurm账户里，现在已有账号为imc_lab
sacctmgr add user name=x_peng account=imc_lab

#添加俩qos，分别叫normal和long
sacctmgr add qos normal
sacctmgr add qos long

#修改qos
sacctmgr modify qos normal set MaxWall=3-00:00:00 MaxTRES="gres/gpu=4" MaxTRESPU="gres/gpu=4" MaxJobsPU=4 MaxSubmitJobsPU=4
sacctmgr modify qos long set MaxWall=7-00:00:00 MaxTRES="gres/gpu=8" MaxTRESPU="gres/gpu=8" MaxJobsPU=1 MaxSubmitJobsPU=1

#设置account可使用的qoslevel
sacctmgr modify account my_account_name set QosLevel=normal,long


```

