### 1. 项目背景

云上的高性能计算（ Cloud High-Performance Computing, Cloud HPC ），与本地集群相比，有多方面的区别。其中最为显著的区别，就是云资源的弹性、动态与灵活性。理论上讲，您可以轻松使用多个云计算机房的计算、存储、网络资源，您的超算集群规模可以扩展到数百、数千核心，也可能根据实际情况减小到 0 个计算核心。这种动态特点就意味着，您在一定程度上需要关心底层资源 —— 例如集群的规模、集群的数量等。

然而，资源管理对于广大的 Cloud HPC 用户来说，是比较陌生的；尤其是云资源的管理，更是无从下手。云提供海量的底层资源，但是 Cloud HPC 用户首先需要让这些资源组成具备超级计算能力的集群。这也就意味着需要一个  **构建和运行** 的过程。这个过程，对于大多数用户来说，难度非常高，原因在于它涉及到多方面的 IT 应用知识和云计算技能，包括但不限于：

- 什么是网络、虚拟私有网络、子网、网段、ACL、公有和私有 IP ……
- 什么是云主机、主机镜像、安全组、公钥私钥 ……
- 什么是云硬盘、文件存储、对象存储、挂载不同类型的存储 ……
- 什么是任务调度器、调度器如何配置和运行、超算用户如何使用和提交任务 ……
- ……

以上种种，都成为了制约 Cloud HPC 走向更广阔应用的一道门槛。目前，这些工作要么是由云厂商以自研的方式、面向 HPC 用户推出自有的 Cloud HPC 服务，要么是由第三方服务商进行集成开发之后以类 SaaS 平台的方式提供给客户。无论是哪一种方案，都旨在将客户留在各自的平台上；尽管在商业方面是完全合理而且完全值得尊重的，但是无形之中会限制最终用户对于超算的掌控权以及选择 Cloud HPC 服务的自由度。

为了打造全面开放且超级简单的 Cloud HPC 平台，让用户能够以极低的门槛开启 Cloud HPC，加速科研创新，我们打造了开源的云超算平台 HPC-NOW。这里，NOW 有两层含义：

- 现在即可开始，无需等待
- No Operation Workload，无需繁重的运行维护工作，即可在云上轻松管理您的超算集群和超算服务

目前，HPC-NOW 服务支持的云平台列表如下：
____________________________________________
阿里云	    | Alibaba Cloud	        | CLOUD_A
腾讯云	    | Tencent Cloud	        | CLOUD_B
亚马逊	    | Amazon Web Services   | CLOUD_C
华为云	    | Huawei Cloud	        | CLOUD_D
百度云      | Baidu BCE	            | CLOUD_E
微软云	    | Azure	CLOUD_F         | CLOUD_F
谷歌云      | Google Cloud Platform	| CLOUD_G
____________________________________________

希望与您共同携手打造开源、开放的 Cloud HPC 生态！

本项目基于 MIT 协议开源，协议条款详见 COPYING 。

### 2. 核心组件

首先，在此鸣谢卓越、开源、面向多云的 IaC（ 基础设施即代码 ）平台 [Terraform](http://www.terraform.io)。正是 Terraform 强大的功能和良好的生态为 HPC-NOW 提供了坚实的基础，我们可以不必重复造轮子。本项目的核心工作，在于如何驱动 Terraform 来构建和管理一个或多个 Cloud HPC 集群。

本项目的核心组件如下：

-  **installer**  ：HPC-NOW 服务的安装器。主要负责服务的安装、卸载、更新三项工作。该安装器被设计为必须由管理员权限执行。
-  **hpcopr**     ：意即 HPC Operator，是 HPC-NOW 的核心程序，也是用户需要执行的主程序。为了确保安全性和隔离性，该程序被设计为必须由专属 OS 用户 "hpc-now" 执行，其他用户，即使是管理员用户或者根用户也无法执行。由 hpcopr 管理基础设施代码，并调用 Terraform 对云资源进行全生命周期管理。
-  **now-crypto** ：核心程序，主要作用是文本文件加密和解密，以确保敏感信息不以明文形式存放。请注意，该程序通过偏移字符的方式对文本信息进行随机修改，您的密文文件仍需要妥善保管。
-  **hpcmgr**    ：强大的集群内管理工具，包括集群的连接、SLURM 服务的启动、以及 HPC 软件包的自动化编译安装等。
-  **infra-as-code** ：模板文件是 IaC（基础设施及代码）的核心要素，我们已经针对阿里云、腾讯云、AWS、华为云、百度云、Azure（国际区）、Google Cloud Platform 七家公有云厂商制作了专用的资源模板。
-  **scripts**    ：启动脚本包含了集群各个节点启动过程的编排，包括各类必要组件的自动化安装。

### 3. 如何部署、使用、卸载、构建与开发

请查阅 Docs/UserManual-CN.pdf 。

### 4. Bug与技术沟通

欢迎随时联系 info@hpc-now.com

### 5. 开发路线图

HPC-NOW 的开发将遵循以下总体路线（其中的优先级为暂定）：

资源接入层：接入 GCP

调度器层：接入 openPBS（一般优先级）

软件仓库优化：

- HPCMGR 操作逻辑优化（最高优先级）
- 提供PreBuild 仓库与源代码仓库（高优先级）
- 本地软件仓库功能（低优先级）

UI/UE：（高优先级）

- hpcopr、installer 的GUI客户端（一般优先级）
- 基于 Web 的在线平台（高优先级）

平台核心层：（以下皆为最高优先级）

- 日志清理功能（清理 error archive、logtrash、tf_prep.log.archive）
- 默认 Disable root ssh 和远程桌面连接