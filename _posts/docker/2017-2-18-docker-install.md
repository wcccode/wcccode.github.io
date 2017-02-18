docker容器最早受到RHEL完善的支持是从最近的CentOS 7.0开始的，官方说明是只能运行于64位架构平台，内核版本为2.6.32-431及以上（即>=CentOS 6.5，运行docker时实际提示3.8.0及以上），升级内核请参考CentOS 6.x 内核升级（2.6.32 -> 3.10.58）过程记录
需要注意的是CentOS 6.5与7.0的安装是有一点点不同的，CentOS-6上docker的安装包叫docker-io，并且来源于Fedora epel库，这个仓库维护了大量的没有包含在发行版中的软件，所以先要安装EPEL，而CentOS-7的docker直接包含在官方镜像源的Extras仓库（CentOS-Base.repo下的[extras]节enable=1启用）。前提是都需要联网，具体安装过程如下。
