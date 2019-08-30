# 使用应用发现服务自动发现整理线下 IT 资产 {#concept_1680910 .concept}

在企业上云规划中，需要先整理线下 IT 资产才能制定详细上云计划。本文将以一个企业上云场景为例，帮助您了解如何使用应用发现服务自动发现并整理线下 IT 资产。

## 背景信息 {#section_klv_bbt_42o .section}

若某小型企业有上云需求，其机房中有运行有 10 台普通主机和 1 台中心主机（IP 地址为：172.\*\*.\*\*.138），但是对于主机上运行的进程、主机的资源水位、各应用组件之间的拓扑关系等详情不清晰。企业希望梳理主机资源，为上云做准备。针对此类场景，应用发现服务可以分析识别主机和进程信息、资源使用水位以及各引用和组件之间的依赖关系。

使用应用发现服务来自动发现并整理线下 IT 资产的步骤如下：

-   [步骤一：安装采集器](#section_o18_pnt_orm)
-   [步骤二：安装探针](#section_aj0_g4j_4lp)
-   [步骤三：创建项目](#section_p75_3of_1r2)

## 前提条件 {#section_dsq_o14_q0j .section}

-   已开通应用发现服务，参见[开通应用发现服务](cn.zh-CN/快速入门/开通应用发现服务.md#)。
-   已在安装采集器的中心主机上安装 Java 环境。

## 步骤一：安装采集器 {#section_o18_pnt_orm .section}

在中心主机上安装采集器后，可以将探针收集到的数据形成日志文件。安装采集器具体步骤如下：

1.  登录[应用发现服务控制台](https://apds.console.aliyun.com)，然后在左上角选择地域。
2.  在概览页单击**新手引导**，然后在新手引导页面查看并保存 license。

    ![新手引导](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1332370/156715188156979_zh-CN.png)

3.  在概览页单击**下载采集器**。

    ![下载采集器](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1332370/156715188156973_zh-CN.png)

4.  将安装包拷贝到待安装采集器的服务器上，并执行以下命令解压安装包。

    ``` {#codeblock_0d0_20v_au9}
    unzip apds-collector.zip
    ```

5.  在解压文件中找到 apds-collector.config 文件并修改各参数为真实配置。
    1.  打开 apds-collector.config 文件。
    2.  按 i 键进入编辑模式，按需修改各参数为真实配置。示例如下：

        ``` {#codeblock_82j_hnh_y35}
        logger.level=INFO
        management.server.port=-1
        server.port=8082
        agent.port=9528
        file.rolling.interval=10
        file.max.size.mb=10240
        file.path=/home/admin/apds-collector/data
        file.zip.path=/home/admin/apds-collector/zip
        file.max.time.hour=168
        encrypt=false
        //<license> 为真实 license。
        license=<license>
        agent.survival.time.hour=24
        loggingRoot=/home/admin
        ```

        **说明：** 所有配置参数均可缺省，各参数的详细说明请参见[参数含义](../../../../cn.zh-CN/操作指南/准备工作/安装采集器.md#table_ldn_rvz_z5q)。

    3.  按 Esc 键退出编辑模式。
    4.  输入 `:wq!` 命令保存并退出 apds-collector.config。
6.  使用 admin 用户执行以下命令启动脚本启动采集器。

    ``` {#codeblock_bch_1zh_qoz}
    sh start.sh
    ```


完成后，执行 `less apds-collector.log` 命令在 apds-collector.log 查看采集器运行日志，若出现以下日志则表示启动成功

``` {#codeblock_ouf_fji_oad}
bind success, ip : 172.**.**.138, port : 9528
```

。

## 步骤二：安装探针 {#section_aj0_g4j_4lp .section}

在 10 台普通主机上分别安装探针以抓取主机、进程的元信息和监控数据，以及各应用组件间的依赖关系等数据。具体步骤如下：

1.  在控制台概览页单击**下载探针**。

    ![下载探针](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1332370/156715188156980_zh-CN.png)

2.  将安装包拷贝到待安装探针的服务器上，并执行以下命令解压安装包。

    ``` {#codeblock_ols_fns_ytu}
    unzip apds-agent.zip
    ```

3.  使用 admin 用户执行以下命令启动探针。

    ``` {#codeblock_ivo_2ip_8af}
    sh start.sh <ip> <port> <license>
    ```

    **说明：** 

    -   `<ip>` 为中心主机 IP 地址。
    -   `<port>` 为采集器 `agent.port`。
    -   `<license>` 为真实 license。
    例如：

    ``` {#codeblock_yun_53k_axy}
    //172.**.**.138 为中心主机 IP 地址
    //9528 为采集器 agent.port
    //7E1023431D**** 为 license
    sh start.sh 172.**.**.138 9528 7E1023431D****
    ```


完成后，执行 `less apds-agent.log` 命令在 apds-agent.log 文件中查看探针运行日志，若出现以下日志则表示启动成功。

``` {#codeblock_a18_zyc_g8d}
Start transport service successfully
```

## 步骤三：创建项目 {#section_p75_3of_1r2 .section}

数据采集完成后，在中心主机上将生成采集器离线文件。在应用发现服务控制台创建项目并上传 ZIP 文件后，应用发现服务将对文件进识别和解析。在控制台创建项目具体步骤如下：

1.  在中心主机上执行 `sh compress.sh` 命令将采集器收集的数据编译成一个 ZIP 文件，并将 ZIP 文件下载至本地。

    **说明：** 编译成的文件在[步骤一的第 5 小步](#config)中配置的`file.zip.path` 路径下，例如本示例为 `/home/admin/apds-collector/zip`。

2.  在控制台概览页单击**新建项目**。

    ![新建项目](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1332370/156715188256983_zh-CN.png)

3.  在新建项目对话框中填写**项目名称**，选择**所在行业**并上传数据集 ZIP 包，然后单击**确认**。

    ![db_新建项目](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/1332370/156715188256985_zh-CN.png)

    项目上传成功后，应用发现服务将检测分析项目中的主机数和进程数并展示在概览页面。


## 后续步骤 {#section_j7t_7de_l21 .section}

您可以在控制台查看主机和进程的分析结果，快速了解硬件资产的各项状态。

-   查看主机详情，参见[查看主机](../../../../cn.zh-CN/操作指南/控制台指南/查看主机.md#)。
-   查看进程详情，参见[查看进程](../../../../cn.zh-CN/操作指南/控制台指南/查看进程.md#)。

