# Getting Started
Welcome to the Getting Started guide for our server. This section is designed to assist both newcomers and experienced users in accessing and utilizing our computing resources.

## Getting Access

请先联系管理员获取权限，向管理员提供用户名<user-name>（默认为名简_姓全，e.g., 马冬梅： dm_ma）和初始密码等信息用于后续连接至服务器。权限获取后，服务器会为用户自动开辟个人文件空间在`/home/<user-name>`下， 他人的目录空间无权访问，因此建议将自己的私人数据和代码存放在这里。

## Connecting to Computing Server

获取访问权限后，你需要通过连接至服务器以开展你的工作。 本服务器目前只支持局域网连接，所以连接之前请先确保您的主机网络通过有线连接或者通过wifi连接至“518”或“518-5G”。

* **Using SSH**: Connect using SSH from a command-line interface. Here is an example for Linux/Mac users:
```plain
ssh <user-name>@10.10.115.27   
```
* **Mac Users**: Connect a Mac via terminal using SSH.
* **Windows Users**: You may use tools like Powershell or PuTTY for SSH connections for connecting on Windows.
* **File Transfers**: Use SCP or SFTP for secure file transfers.
```plain
scp -r <source-path> <user-name>@10.10.115.27:<target-path>
```
* Accessing GUI Applications: 一般服务器是建议用X11服务回传画面，但为了大家方便可以使用实验室的服务器显示器进行查看（建议是非必要不要通过服务器显示器操作，防止打开奇怪的应用程序导致服务器卡死，如果需要用类似open3d这种gui可以通过服务器显示器查看，而图片和视频可以在本地通过vscode连接直接预览，无需通过服务器显示器查看。）
## Organzing your work space

如前面第一节所提到，/home/username会自动生成，在这里建议存放自己的私人数据和代码。而大型文件例如数据集，模型权重，git库，anaconda环境建议存放在 `/iml_lab/<user-name>` ，这里需要用mkdir自己创建目录。大型数据集例为了方便大家访问可以下载或上传至`/iml_lab/datasets/`



# Slurm
Slurm（用于资源管理的简单 Linux 实用程序）是一个开源、高度可配置、容错和适应性强的工作负载管理器。它广泛应用于高性能计算（HPC）环境。

Slurm 专为满足大规模计算工作负载的复杂需求而设计。它可以在由数千个节点组成的集群中高效地分配和管理任务，提供对资源、调度和作业队列的无缝控制。它是 HPC 上的一款软件，提供 Slurm 阵列作业和依赖关系、监控和管理作业、查看账户信息以及检查群集和节点状态等功能：sinfo。

## Training for Batch Jobs: `sbatch`

The `sbatch` command is used to submit a job script for later execution. The script includes the `SBATCH` directives that control the job parameters like the number of nodes, CPUs per task, job name, etc.

### Syntax: `sbatch`

```plain
sbatch [options]  <script_file>
```
### Options and Usage: `sbatch`

* `n, --ntasks= <number>` : specify the number of tasks
* `N, --nodes=<minnodes[-maxnodes]>` : specify the number of nodes
* `J, --job-name=<jobname>` : specify a name for the job
```plain
#!/bin/bash
#SBATCH -J MyJob               # Job name
#SBATCH -N 2                   # Number of nodes
#SBATCH -n 16                  # Number of tasks
#SBATCH -o output_%j.txt       # Standard output file
#SBATCH -e error_%j.txt        # Standard error file

# Your program/command here
srun ./my_program
```
To submit this job script, save it as `my_job.sh` and run:
```plain
sbatch my_job.sh
```

### 本服务器训练样例

sbatch的功能是向slurm提交一个训练任务，如果当下没有可用的资源，该任务会自动进如入队列等候资源释放再开启工作。在这里强烈建议所有用户训练时利用sbatch进行训练任务提交，使资源利用最大化，debug可以直接进行调试无需向slurm申请资源，但不建议在服务器上debug太长时间以防止占用训练资源。

首先创建一个train.sh文件用于存放训练指令，如下：

```plain
#!/bin/sh

conda activate mdm  #  激活anaconda 环境

cd my_project  # 进入你的工程目录

python train.py --[options]  # 启动训练代码
```

输入下面一串指令提交slurm job 开始训练：

```plain
sbatch --partition=debug  --cpus-per-task=8 --mem=24G --ntasks-per-node=1 --gres=gpu:1 --signal=B:USR1@600  --mail-user=pengxiaogang@hdu.edu.cn --mail-type=FAIL,END --time=120:00:00 ./train.sh 
```
* --partition=debug     # debug为当下我们服务器的分区，目前只有一个
* --cpus-per-task=8     # 一张GPU开8个CPU核， N张GPU就填8N （可以稍微开大点但尽量不要超过16）
* --mem=24G              #   一张GPU开24G内存
* --ntasks-per-node=1       # 申请一个节点 ，目前只有一个节点
* --gres=gpu:1                 #  1 为申请GPU的个数
* --signal=B:USR1@600  
* --mail-user=pengxiaogang@hdu.edu.cn    #训练报错或结束后会通过邮件通知
* --mail-type=FAIL,END 
* --time=120:00:00 ./train.sh     # train.sh为前面写好的训练指令
## **Monitoring and Managing Jobs**

**sinfo   查看当前服务器资源利用情况， 包括CPU, GPU利用率**

```plain
sinfo -p debug --Format=time,cpusload,freemem,nodehost,statecompact,gres,gresused
```

**squeue  查看当前资源队列信息，包括运行的排队的, 以及任务的所有者等**

```plain
squeue -p debug 
```

**scancel  取消提交的任务**

```plain
scancel <job id>  # sbatch 提交会回自动返回一个id，或者通过squeue查看你的job_id
```

>更多有关slurm的操作可以参考下面
[https://zhuanlan.zhihu.com/p/345387783](https://zhuanlan.zhihu.com/p/345387783)


# Remote Development
这里建议使用VS CODE进行远程开发，相对来说要比PyCharm更加稳定。

使用教程请参考：
1. [Offical Docs](https://code.visualstudio.com/docs/remote/remote-overview)
2. [Zhihu](https://blog.csdn.net/liqfyiyi/article/details/120055064)

# Local Package Installation
为了防止用户随意安装一些不常用的包导致系统崩溃，因此除了管理员其余所有用户没有sudo权限。管理员会将一些常用的包安装到全局环境，而不常用包这里建议采用一种本地安装包环境管理工具spack（不使用sudo权限）进行安装。该工具目前已安装成在`/imc_lab/spack` 下, 你可已将其添加自己环境路径下即可启用spack。Spack使用教程请参考：
1. [https://spack.readthedocs.io/en/latest/](https://spack.readthedocs.io/en/latest/)


2. [https://zhuanlan.zhihu.com/p/426743137](https://zhuanlan.zhihu.com/p/426743137) 

如若遇到一些棘手的安装包问题可以向管理员申请sudo权限。