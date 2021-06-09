# 有网友想要图床，今天它来了！

## 前言

很多同学在创作的时候，苦于没有一款稳定并且方便快捷的图床，选择第三方又怕哪一天图片消失或无法访问，不如选择自建图床，自己的图片自己管理，今天就给大家带来一款颜值很高、功能强大的图床。

## 兰空图床

[兰空图床](https://github.com/wisp-x/lsky-pro)是一个开源的多功能图床，已在Github上斩获 **1.3K+** Star 。

[![16.jpg](https://tc.southbaa.com/2021/06/08/d1bb4f0749302.jpg)](https://tc.southbaa.com/2021/06/08/d1bb4f0749302.jpg)

兰空图床注重功能实用性，界面 UI 部分是以 Material Design 设计语言实现，颜色更鲜艳，动画效果更突出。上传图片的操作部分，使用了强大的 bootstrap-fileinput 组件，支持拖拽、粘贴、多文件同时上传，并且上传后可以一键复制图片链接。

图片储存在服务器本地可能会消耗大量带宽，所以它还支持配置第三方储存，并可以通过角色组配置不同用户的储存策略，将图片上传至第三方储存中，即使哪一天服务器意外宕机、被销毁导致图片无法访问的情况。

目前支持第三方储存的平台有阿里云oss、腾讯云cos、七牛云kodo、又拍云uss，无论是那种储存策略，在兰空图床的个人中心都可以进行管理，同时它还支持配置图片的储存路径，以及上传的文件大小、拓展名限制等等强大功能，接下来就和大家讲解如何在云开发环境中部署兰空图床吧。

## 部署教程

### 准备工作

开通 CloudBase 云开发环境

注意：

\- 包年包月环境无法开通云托管。

\- 目前，来源为“微信小程序”的环境暂不支持开通云托管。

### **第 1 步：开通**

登录 云开发 CloudBase 控制台，选择一个环境，进入云托管管理页面，单击「立即开通」。

![图片](https://mmbiz.qpic.cn/mmbiz_png/l7x0FXB2eW74Yqn38oy6NfZibu0T07cPtekrRcgKUYUBicVENEwfE7gVic5TS2UuvnxibJQQiaSTnDNPvqTBRnFUGRA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

### **第 2 步：配置云托管**

### **所在地域**

目前，云托管已开放上海和广州地域，更多地域将陆续开放。

### **云托管网络**

网络设置包括 私有网络 VPC 和 子网。以环境为维度，云托管中创建的所有服务，都将部署在这同一个 VPC 之中，且成功开通云托管后不支持更换网络设置。

网络设置分为如下两种模式：

#### **系统默认配置**

云托管将为您自动新建一个 VPC 和子网，并将其绑定您当前的云开发环境。

后续您在当前环境下的云托管中创建的所有服务，都将被部署到这个 VPC 和子网中。

此过程无需您手动进行任何设置。后续您可以在 私有网络控制台 中查看并管理这个由云托管自动创建的 VPC 及子网，也可以将更多云资源（例如云服务器，云数据库等）部署在这个 VPC 之内，以便这些云资源进行互动。

#### 自定义配置

如果您之前已经创建过私有网络 VPC 和子网，并在其中已经部署有其他云资源（例如云服务器、云数据库等），且需要您的云托管应用与这些已存在的云资源进行互动，则您可以选择「自定义配置」，然后选择具体的 VPC 和子网。

VPC 只能选择一个，子网支持选择多个。![图片](https://mmbiz.qpic.cn/mmbiz_png/l7x0FXB2eW74Yqn38oy6NfZibu0T07cPtkRdE8OSyJVxCK9Fh7PSbR1yONpM2iaTw5G0BTOnskOPxpaCyxxFvMAw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

###  **开通成功**

单击「提交」，状态变为**开通中**，请等待数秒。

![图片](https://mmbiz.qpic.cn/mmbiz_png/l7x0FXB2eW74Yqn38oy6NfZibu0T07cPtuuJApsvX7bcjZPXx5TKjEgxu7JdfbQrpYC4DcBZIShRRlDr4IMx0WA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


开通成功后，您将自动跳转到云托管的服务列表页面。

至此您已经成功开通后云托管服务，您可以单击「新建服务」开始部署我们的图床啦。

### 项目初始化

进入至项目根目录，键入

```shell
touch cloudbaserc.json 
```

### 配置Cloudbaserc.json

创建好`cloudbaserc.json`文件后，编辑文件

```json
{
    "version": "2.0",
    "envId": "{{env.ENV_ID}}",
    "framework":{
        "name":"lsky-pro",
        "plugins":{
            "client": {
                "use": "@cloudbase/framework-plugin-container",
                "inputs": {
                "cpu": 0.5,
                "mem": 1,    
                "serviceName": "lsky-pro",
                "servicePath": "/",
                "localPath": "/",
                "mode":"high-availability",
                "uploadType": "package",
                "containerPort": 80,
                "volumeMounts": {
                    "/var/www/html": "lsky-cfs"
                  }
                }
            }
        },
        "requirement": {
            "addons": [
              {
                "type": "CFS",
                "name": "lsky-cfs"
              },
              {
                "type": "CynosDB",
                "name": "lsky"
              }
            ]
          }
    }
}

```

更多参数说明，可参考[云托管容器插件](https://docs.cloudbase.net/framework/plugins/framework-plugin-container.html)。

### 创建Dockerfile

根据项目的需求，编辑好Dockerfile文件以及相关的文件，这里我们就不一一详细说明啦，具体可参考示例仓库内的`Dockerfile`

### 部署至云托管

在完成上述的一系列工作之后，我们可以进行最关键的一步 —— 部署至云托管。

将项目上传至Github或者Gitee等代码托管平台，进入至云开发的控制台，单击「新建」

[![1.jpg](https://tc.southbaa.com/2021/06/08/8df76c3411c76.jpg)](https://tc.southbaa.com/2021/06/08/8df76c3411c76.jpg)

填写好相关信息后，点击下一步

[![3.jpg](https://tc.southbaa.com/2021/06/08/0a2119662b28d.jpg)

选择地区，并输入我们要创建的数据库管理员密码后，点击下一步

[![4.jpg](https://tc.southbaa.com/2021/06/08/24f5c539b241c.jpg)](https://tc.southbaa.com/2021/06/08/24f5c539b241c.jpg)

上述工作完成后，点击「立即开通」

在等待片刻后（部署可能需要5-10分钟），我们的项目就部署至云开发·云托管啦，我们可以在云环境内看到我们创建的应用。

[![6.jpg](https://tc.southbaa.com/2021/06/08/021789473d7cc.jpg)](https://tc.southbaa.com/2021/06/08/021789473d7cc.jpg)

*注:应用需要依赖CFS以及Mysql数据库，因此会创建相关资源*

一般人我不会告诉他，还可以根据[云开发Framework文档](https://docs.cloudbase.net/framework/deploy-button.html)，做一个一键部署按钮哦！

[![15.jpg](https://tc.southbaa.com/2021/06/08/e4e2ee4d3059e.jpg)](https://tc.southbaa.com/2021/06/08/e4e2ee4d3059e.jpg)

### 配置相关服务

完成上述步骤后，我们已经把我们的应用部署至云托管啦，但是还差最后一步，给我们的兰斯图床创建一个数据库（部分应用可自动创建）。我们点击腾讯云控制台菜单，点击「TDSQL-C」

[![7.jpg](https://tc.southbaa.com/2021/06/08/40f54cf7ee793.jpg)](https://tc.southbaa.com/2021/06/08/40f54cf7ee793.jpg)

点击「登陆」

[![8.jpg](https://tc.southbaa.com/2021/06/08/9cd9c47b064b6.jpg)](https://tc.southbaa.com/2021/06/08/9cd9c47b064b6.jpg)

在输入密码后，创建数据库

[![9.jpg](https://tc.southbaa.com/2021/06/08/afeb0ffdb8a2d.jpg)](https://tc.southbaa.com/2021/06/08/afeb0ffdb8a2d.jpg)

创建完成后，我们可以回到刚刚的云托管应用界面啦。点击「访问」即可跳转至应用安装页

[![6.jpg](https://tc.southbaa.com/2021/06/08/021789473d7cc.jpg)](https://tc.southbaa.com/2021/06/08/021789473d7cc.jpg)

数据库地址输入我们的内网地址哦！

[![12.jpg](https://tc.southbaa.com/2021/06/08/388a26ea4a2d5.jpg)](https://tc.southbaa.com/2021/06/08/388a26ea4a2d5.jpg)

[![11.jpg](https://tc.southbaa.com/2021/06/08/e0203fc105f33.jpg)](https://tc.southbaa.com/2021/06/08/e0203fc105f33.jpg)



安装完成后，就大功告成啦！!

[![13.jpg](https://tc.southbaa.com/2021/06/08/81b113a983695.jpg)](https://tc.southbaa.com/2021/06/08/81b113a983695.jpg)

### 添加域名（可选）

点击「访问服务」，添加域名信息

[![14.jpg](https://tc.southbaa.com/2021/06/08/acc47ab1d46ac.jpg)](https://tc.southbaa.com/2021/06/08/acc47ab1d46ac.jpg)

最后根据要求配置好就完成添加啦

## 注意事项

1、在此示例中，云托管的副本模式为高可用，保留了一个副本数供连接使用

2、TDSQL- C数据库会在无连接状态(默认10分钟)下进行暂停，届时访问应用会出现数据库无法连接情况，刷新再次访问则会启动数据库

3、在配置域名后，可进行CDN加速，保证了图片访问的速度

## 特别鸣谢

**兰空图床 (Lsky Pro) ** Github:https://github.com/wisp-x/lsky-pro

本**示例**仓库地址 Github:https://github.com/Handsomedoggy/lsky-pro

