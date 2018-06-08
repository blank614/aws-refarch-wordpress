![](images/AWS_logo_PMS_300x180.png)

![](images/100x100_benefit_available.png)![](images/100x100_benefit_ingergration.png)![](images/100x100_benefit_ecryption-lock.png)![](images/100x100_benefit_fully-managed.png)![](images/100x100_benefit_lowcost-affordable.png)![](images/100x100_benefit_performance.png)![](images/100x100_benefit_scalable.png)![](images/100x100_benefit_storage.png)

# 在AWS托管WordPress**



此参考架构提供了一套用于在AWS上使用AWS部署WordPress的YAML模板:[Amazon Virtual Private Cloud (Amazon VPC)](http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Introduction.html), [Amazon Elastic Compute Cloud (Amazon EC2)](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html), [Auto Scaling](http://docs.aws.amazon.com/autoscaling/latest/userguide/WhatIsAutoScaling.html), [Elastic Load Balancing (Application Load Balancer)](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html), [Amazon Relational Database Service (Amazon RDS)](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html), [Amazon ElastiCache](http://docs.aws.amazon.com/AmazonElastiCache/latest/UserGuide/WhatIs.html), [Amazon Elastic File System (Amazon EFS)](http://docs.aws.amazon.com/efs/latest/ug/whatisefs.html), [Amazon CloudFront](http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html), [Amazon Route 53](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html), [Amazon Certificate Manager (Amazon ACM)](http://docs.aws.amazon.com/acm/latest/userguide/acm-overview.html)  with [AWS CloudFormation](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html).


## Overview

![architecture-overview](images/aws-refarch-wordpress-v20171026.jpeg)


这套知识库由一组嵌套模板组成，这些模板按照主模板的顺序运行。运行主模板创建整个堆栈，输入适当的参数。可以按顺序单独运行嵌套模板，为每个堆栈输入适当的输入参数。

## Update
请参阅[history.md](/HISTORY.md)获取2.0.2中更改的详细列表。

## Parameters

![](images/aws-refarch-wordpress-mastertemplate-parameters.png)

## Steps to Run
要启动整个堆栈并在AWS上部署WordPress网站，请单击下面的*** Launch Stack ***链接之一或下载主模板并在本地启动它。

您可以使用您的帐户在以下AWS区域中启动此CloudFormation堆栈：


| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=WordPress&templateURL=https://s3.amazonaws.com/aws-refarch/wordpress/latest/templates/aws-refarch-wordpress-master-newvpc.yaml) |
| us-east-2 |US East (Ohio)| [![cloudformation-launch-stack](images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/new?stackName=WordPress&templateURL=https://s3.amazonaws.com/aws-refarch/wordpress/latest/templates/aws-refarch-wordpress-master-newvpc.yaml) |
| us-west-2 |US West (Oregon)| [![cloudformation-launch-stack](images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=WordPress&templateURL=https://s3.amazonaws.com/aws-refarch/wordpress/latest/templates/aws-refarch-wordpress-master-newvpc.yaml) |
| eu-west-1 |EU (Ireland)| [![cloudformation-launch-stack](images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-west-1#/stacks/new?stackName=WordPress&templateURL=https://s3.amazonaws.com/aws-refarch/wordpress/latest/templates/aws-refarch-wordpress-master-newvpc.yaml) |
| eu-central-1 |EU (Frankfurt)| [![cloudformation-launch-stack](images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=eu-central-1#/stacks/new?stackName=WordPress&templateURL=https://s3.amazonaws.com/aws-refarch/wordpress/latest/templates/aws-refarch-wordpress-master-newvpc.yaml) |
| ap-southeast-2 |AP (Sydney)| [![cloudformation-launch-stack](images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=ap-southeast-2#/stacks/new?stackName=WordPress&templateURL=https://s3.amazonaws.com/aws-refarch/wordpress/latest/templates/aws-refarch-wordpress-master-newvpc.yaml) |

#### Select WordPress version
可以选择WordPress的版本。可能现在的是最新的，4.5,4.6,4.7,4.8,4.9。

#### 通过从Amazon S3下载重写ini文件来覆盖PHP.ini的默认值

创建一个自定义的.ini文件，其中包含PHP覆盖并使其公开在S3存储桶中可用。这些可能是常见的覆盖**memory_limit**, **post_max_size**, **upload_max_filesize**, **max_input_time**, **max_execution_time**,等等。Amazon S3对象路径应使用https格式(e.g.https://s3.amazonaws.com/aws-refarch/wordpress/latest/bits/20-aws.ini).示例PHP覆盖在下面和 [samples/20-aws.ini](samples/20-aws.ini) 目录中


; 在AWS上启用用于托管WordPress的php.ini覆盖 - https://github.com/blank614/aws-refarch-wordpress

memory_limit = 128M

post_max_size = 0

upload_max_filesize = 64M

max_input_time = 60

max_execution_time = 30

#### 使用Bastion（堡垒机）访问Wordpress实例

在EC2控制台中，导航到Auto Scaling组并找到堆栈的Bastion启动配置。编辑启动配置并将所需实例设置为1.按下Save（保存），将会创建Bastion实例。堡垒机是实现增强安全性的入口。

在每个实例上启用Wordpress CLI，SSH进入Bastion，然后SSH进入实例。从wordpress安装目录`/var/www/wordpress/<site directory>` 用 `wp` 命令与您的wordpress安装进行交互。


#### 亚马逊EFS资源和仪表板

此AWS Cloudformation模板和嵌套模板将创建Amazon EFS文件系统和其他AWS资源，以在文件系统的突发信用余额降至预定义阈值以下时监控和发送通知。这些警报和其他AWS CloudWatch指标（包括文件系统大小自定义指标）作为小组件添加到CloudWatch仪表板。

随着文件系统的增长，Amazon EFS上的吞吐量也随之增加。由于基于文件的工作负载通常很尖锐，所以在短时间内驱动高吞吐量，其余时间吞吐量较低 - Amazon EFS旨在在一段时间内达到高吞吐量级别。 Amazon EFS使用信用系统来确定文件系统何时可以爆发。每个文件系统以基准速率获得一段时间的信用额度，该速度由文件系统的大小决定，并在读取或写入数据时使用信用额度。基准速率是每TiB存储50 MIB/s（相当于每GiB存储50 KiB/s）。累计突发额度赋予文件系统驱动吞吐量超过其基准速率的权限。当文件系统具有正突发信用余额时，它可能爆发。每个TiB存储的突发速率为100 MiB/s（相当于每GiB存储100 KiB/s）。

如果访问文件系统的WordPress部署依赖于正常操作的突发吞吐量，则突发信用消失可能会对工作负载产生负面影响，因此监视文件系统的突发信用余额至关重要。 efsalarms模板将创建两个Amazon CloudWatch警报，如果突发信用余额降至两个预定义阈值以下，则会发送电子邮件通知，即“警告”阈值和“严重”阈值。这些阈值基于如果文件系统以最高吞吐速率（允许吞吐速率）驱动时完全使用所有突发信用所花费的分钟数。您可以在Cloudformation模板中输入这些微小变量作为输入参数。 “警告”阈值，默认值为180分钟。这意味着CloudWatch警报将根据最新的允许吞吐率，在信用余额降至零之前180分钟发送电子邮件通知。第二次警报和通知是“重要”通知，默认值为60分钟。根据最新的允许吞吐率，该警报将在信用余额降至零之前60分钟发送电子邮件通知。允许的吞吐量是动态的，随着文件系统的增长和扩展随着文件系统的缩小而扩大。因此，创建第三个和第四个警报来监视允许的吞吐量。如果允许的吞吐量增加或减少，将发送电子邮件通知，Auto Scaling Group将启动EC2实例，根据最新的允许吞吐量速率动态重置“警告”和“关键”阈值。此EC2实例将自动终止，并且只有在允许的吞吐率增加或减少时才会启动新实例以重置阈值。

新的AWS CloudWatch仪表板也使用一些Amazon EFS，Amazon RDS，Amazon ELB和自定义指标创建。如果选择创建亚马逊EFS突发信用余额阈值警报，也会显示。


#### 示例仪表板

![](images/aws-refarch-wordpress-alarms.png)

#### 可选：加密Amazon Aurora DB数据以防未然

现在，此参考体系结构允许您使用通过AWS Key Management Service（KMS）管理的密钥来加密数据库。在使用Amazon Aurora加密运行的数据库实例上，在底层存储中静态存储的数据将被加密，同一集群中的自动备份，快照和副本也会被加密。无缝处理加密和解密，因此您无需修改应用程序即可访问您的数据。启动此AWS Cloudformation堆栈时，您可以选择通过主模板中的参数启用数据库加密。您可以使用在您的帐户中自动创建的默认RDS密钥，方法是将*“现有AWS KMS CMK for RDS”*参数留空或使用您使用KMS创建的密钥来加密数据。


#### 可选：加密Amazon EFS数据和元数据以防未然

Amazon EFS与AWS密钥管理服务（KMS）集成以支持使用AWS KMS客户主密钥（CMK）加密文件系统内容。在加密文件系统中，数据和元数据在写入文件系统之前会自动加密。同样，随着数据和元数据的读取，它们会在提交给应用程序之前自动解密。这些进程由Amazon EFS透明地处理，因此您不必修改WordPress即可利用加密文件系统。默认情况下，为使用此云构建模板创建的文件系统启用静态加密。此选项作为可配置的Cloudformation模板参数公开。 'true'创建一个加密的文件系统。 '假'创建一个未加密的文件系统。要为Amazon EFS使用AWS管理的CMK（密钥别名：aws/elasticfilesystem），请将*“EFS的现有AWS KMS CMK”*参数留空。要使用特定客户管理的CMK（在此AWS账户或其他AWS账户中），请在“现有AWS KMS CMK for EFS”参数字段中输入CMK ARN。


#### 可选：Amazon证书管理器SSL/TLS证书

AWS证书管理器（ACM）是一项服务，可让您轻松配置，管理和部署用于AWS服务的安全套接字层/传输层安全性（SSL/TLS）证书。通过AWS Certificate Manager配置的SSL/TLS证书是免费的。

如果您的域名尚未拥有SSL/TLS证书，建议您使用ACM申请一个证书。有关使用ACM请求SSL/TLS证书的更多信息，请阅读 [AWS Certificate Manager User Guide](http://docs.aws.amazon.com/acm/latest/userguide/acm-overview.html).

使用ACM请求证书或将证书导入ACM。要在CloudFront中使用ACM证书（可选输入参数），您必须在US East (N. Virginia) region.申请或导入证书。要对Amazon ELB使用ACM证书 - 应用程序负载均衡器（可选输入参数），您必须在创建CloudFormation堆栈的区域中请求或导入证书。验证证书中域名的所有权后，ACM将提供证书。使用ACM证书Amazon资源名称（ARN）作为主模板的可选Cloudfront和/或公共ALB ACM证书输入参数。


#### 堆栈创建

主模板有两个输出URL。 WPSiteURL将带你到你的新的WordPress网站。如果您提供了一个，则它将成为网站域名，如果您选择创建CloudFront分配，则为CloudFront URL，或公共应用程序负载平衡器的域名。第二个输出URL将带您进入AutoScaling组中每个EC2实例的OpCache状态页面。通过刷新页面，您将能够验证每个实例上的OpCache已启用。

#### OPcache
OPcache是一个在每个EC2实例上运行的字节码缓存引擎，缓存预编译的PHP脚本，可以提高PHP应用程序的性能，如WordPress。当为Amazon EFS的网站提供PHP页面时，建议使用OpCache之类的缓存引擎。可以将OPcache配置为将其缓存存储在内存中或EBS卷上。


##### 推荐的OPcache配置设置

- 使用 [Amazon EFS User Guide](http://docs.aws.amazon.com/efs/latest/ug/mounting-fs-mount-cmd-general.html).中标识的默认Linux安装选项挂载EFS文件系统。更改某些激活缓存选项的默认值，例如actimeo，acregmax或acdirmax可以通过更频繁地更新属性缓存来生成更高的元数据操作。如果不使用默认值，建议进行仔细的测试。

- 增加realpath_cache_size的大小。将其设置为512k是一个好的开始，但要找出实际路径缓存的实际使用情况将有助于您微调此设置并更精确。要找出您实际使用的实际路径缓存的数量，请将以下php代码片段放在一个php文件中（您可以使用任何名称 - 例如realpathcache.php）并将其放置在您的WordPress目录中。打开一个浏览器并指向这个php文件。刷新您的页面多次。返回的值是实数路径高速缓存正在使用的以字节为单位的内存量。记下多次刷新此页面后返回的最大值。这加上一点余量应该是realpath_cache_size设置的值。
```
<?php
 print_r(realpath_cache_size());
?>
```
- 请在你的WordPress目录中使用 “find . -type f -print | grep php | wc -l”获取php文件的数量。该数字应该小于你设置的opcache.max_accelerated_files。此设置控制多少PHP文件可以最多一次性保存在内存中。在您的项目里面拥有比您设置的更少的文件，这很重要。

- opcache.memory使用的默认值是64 MB。增加此设置可以通过在内存中缓存更多文件来提高性能。考虑将此值设置为512MB（opcache.memory_consumption = 512）或更多，以提高性能。建议测试不同的opcache.memory消耗值以优化特定工作负载的性能。如果内存大小成为限制因素，则cloudformation模板还会将opcache.file_cache配置为使用本地存储（EBS或实例存储卷）。在测试期间，我们建议禁用opcache.validate_timestamps，以避免调用NFS服务器来确保opcache的一致性。不建议在生产中禁用opcache.validate_timestamps。


要了解更多关于OPcache的信息，请阅读： http://php.net/manual/en/book.opcache.php

#### 卸载静态资产

WordPress拥有庞大的合作伙伴生态系统，可进一步增强WordPress部署的可用性，性能和易维护性。与W3-Total-Cache一样，插件可让您利用Amazon S3和Amazon CloudFront等其他AWS服务来卸载和存储静态内容。其他人可能喜欢在Amazon EFS上存储所有内容的简单性，并避免安装和管理第三方插件。


#### 设置 W3-Total-Cache

W3-Total-Cache插件必需对于参考架构具有最佳性能。 W3允许卸载静态资产，并使memcached缓存对象，数据库查询等。

要设置W3-Total-Cache，请在插件中激活它（将在新的CloudFormation启动时自动安装）。导航到AWS中的ElasticCache - > Memcached，找到为堆栈创建的集群。复制 **Configuration Endpoint**（格式：wor-el-1d6494yen6xl1.xxxxxx.cfg.usw2.cache.amazonaws.com:11211）。现在在 performance -> General,下，找到以下选项：


- Page Cache - enable, select Memcached
- Minify - enable, select Disk (Minified files will be offloaded)
- Database Cache - enable, select Memcached
- Object Cache - enable, select Memcached
- CDN - enable, select Amazon Cloudfront - Or Amazon Simple Storage Service
- Fragment Cache - enable, select Memcached

按 Save All & Purge Cache

你会看到错误说127.0.0.1:11211不可访问。
现在，在侧栏导航中的每个菜单中，滚动到Memcached server option (Advanced)，然后粘贴ElasticCache配置端点。按 test 并确保通过。

在浏览器缓存下，同时启用**Set expires header** and **Set cache control header** （当测试这可能是combersome）

在CDN下，粘贴有效且唯一创建的可访问S3或S3存储桶的IAM密钥和秘密。粘贴使用堆栈创建的Cloudfront前缀。也滚动到Advanced，然后选择**Export changed files automatically**

由于文件将被卸载到S3，请确保将S3存储桶作为CloudFront Distribution上的源点添加。只需导航至distribution，选择Origin选项卡， Create Origi，单击Origin Domain Name文本字段并找到用于CDN的S3存储桶。 **注意：**这将需要一些时间，直到CDN复制完成后，分发将进行中。


## 主模板

主模板接收所有输入参数并将它们传递给适当的嵌套模板，这些嵌套模板根据条件和依赖关系按顺序执行。
在这里查看模板[aws-refarch-wordpress-master.yaml](templates/aws-refarch-wordpress-master.yaml)

### AWS资源创建:

- Amazon Virtual Private Cloud (Amazon VPC)
- Internet Gateway (IGW)
- NAT Gateway (across all public subnets)
- Amazon VPC subnets (public, private (data, web)) in all the Availability Zones (AZs) selected
- Routing tables for public subnets - routing through IGW
- Routing tables for private subnets - routing through NAT Gateway
- Mulitple VPC Security Groups
- Bastion Auto Scaling Group (launching no instances) - in public subnets (public)
- Amazon Relational Database Service (Amazon RDS) Aurora cluster - in private subnets (data)
- Amazon Elastic File System (Amazon EFS) file system - with mount targets in private subnets (data) w/ optional dummy data to grow the file system to achieve higher levels of throughput and IOPS
- File System Auto Scaling Group (launching 1 instance) - to add dummy data (it auto terminates by setting ASG desired count to 0)
- File System Auto Scaling Group (launching 1 instance) - to dynamically adjust warning and critical threshold values when the permitted throughput value changes (it auto terminates by setting ASG desired count to 0)
- Amazon CloudWatch alarms to monitor Amazon EFS burst credit balance
- Amazon ElastiCache cache cluster (optional) - in private subnets (data)
- Amazon Elastic Load Balancing (Amazon ELB) Application Load Balancer (ALB) - in public subnets (public)
- Web Auto Scaling Group (launching 2 instances) - in private subnets (web)
- Amazon CloudFront distribution (optional)
- Amazon Route53 DNS record set (optional)
- Amazon CloudWatch dashboard

### Input Parameters

#### General AWS
- EC2 Key Name Pair
- SSH Access CIDR block (to access bastion host)
- Email address for WordPress administration and SNS notifications
- Site Domain Name (e.g. 'example.com') - use this only if you will use your own custom domain name
- Select if you want to create a DNS record set for your custom dist domain name
- Select if you want to use a AWS CloudFront to cache images at AWS edge locations (3rd party plugins are required to leverage a CDN)
- Enter the ARN of the AWS Certificate Manager certificate you created in us-east-1 for your custom site domain name

#### Network
- Number of Avilability Zones (AZs) - from 2 to 6
- Select the individual Availability Zones where resources will be launched (the number of selected AZs must match the number of AZs selected above)
- VPC CIDR block
- VPC tenancy
- CIDR block for public subnets 1 to 6
- CIDR block for web subnets 1 to 6
- CIDR block for data subnets 1 to 6

#### File System
- Amazon EFS performance mode
- Encrypted file system (boolean)
- AWS KMS Customer Master Key ARN (if enabling encryption and using customer-managed CMK)
- Clone EFS, use the System ID of an existing EFS Filesystem. The data is copied to the Site Directory. If the Site Directory exists on the source filesystem, only the contents of the site directory are copied. So make sure if you are copying from an existing Wordpress Cloudformation template, that the site directory is the same.
- Add dummy data to the file system to achieve higher throughput & IOPS beyond the amount of data your WordPress environment will use. This value is in GiB.
- The instance type that will be used to dd dummy data into the file system
- Select if you want to create alarms that send SNS notifications when the file system's burst credit balance drops below certain thresholds.
- The instance type that will be used to dynamically adjust alarm thresholds as permitted throughput changes.
- The 'Warning' threshold has a default value of 180 minutes. This means that a CloudWatch alarm will send an email notification 180 minutes before the credit balance drops to zero, based on the latest permitted throughput rate.
- The 'Critical' threshold has a default value of 60 minutes. This means that a CloudWatch alarm will send an email notification 60 minutes before the credit balance drops to zero, based on the latest permitted throughput rate.

#### Database
- Database Name
- Database Master Username
- Database Master Password
- DB Restore from Snapshot, enter the cluster Snapshot name from the RDS console. (formatted rds:wordpress-stack-name-rds-xxxxxxxx-databasecluster-apzdbrozmzcn-snapshot-date)
- Database Size
- Database Instance Class Type
- Encrypted database storage (boolean)
- AWS KMS Customer Master Key ARN (if enabling encryption and using customer-managed CMK)
- Create ElastiCache cluster (boolean)
- ElastiCache Node Type

#### Bastion Parameters
- Bastion Instance Type

#### Web Parameters
- Create CloudFront distribution (boolean)
- Create Route 53 record set (boolean)
- PHP Version (5.5, 5.6, or 7.0 - recomended)
- Web Instance Type
- The maximum number of instances in the web tier auto scaling group
- The minimum (and desired) number of instances in the web tier auto scaling group

#### WordPress Parameters
- WordPress Title
- WordPress Administrator Username
- WordPress Administrator Username Password
- WordPress Main Language of the site
- Wordpress Site Directory

## Master Template
主模板接收所有输入参数，并将它们传递给适当的嵌套模板，这些嵌套模板将根据依赖性按顺序执行。
在这里查看模板 [aws-refarch-wordpress-master.yaml](templates/aws-refarch-wordpress-master.yaml)

## New VPC Template
在这里查看模板 [aws-refarch-wordpress-01-newvpc.yaml](templates/aws-refarch-wordpress-01-newvpc.yaml)

### Default VPC and subnet IP ranges

'newvpc'堆栈默认为以下网络设计（但可以通过主参数更改）：:

| Item | CIDR Range | Usable IPs | Description |
| --- | --- | --- | --- |
| VPC | 10.0.0.0/16 | 65,536 | The whole range used for the VPC and all subnets |
| Web Subnet | 10.0.0.0/22 | 1022 | Private subnet in first Availability Zone |
| Web Subnet | 10.0.4.0/22 | 1022 | Private subnet in second Availability Zone |
| Web Subnet | 10.0.8.0/22 | 1022 | Private subnet in third Availability Zone |
| Web Subnet | 10.0.12.0/22 | 1022 | Private subnet in fourth Availability Zone |
| Web Subnet | 10.0.16.0/22 | 1022 | Private subnet in fifth Availability Zone |
| Web Subnet | 10.0.20.0/22 | 1022 | Private subnet in sixth Availability Zone |
| Data Subnet | 10.0.100.0/24 | 254 | Private subnet in first Availability Zone |
| Data Subnet | 10.0.101.0/24 | 254 | Private subnet in second Availability Zone |
| Data Subnet | 10.0.102.0/24 | 254 | Private subnet in third Availability Zone |
| Data Subnet | 10.0.103.0/24 | 254 | Private subnet in fourth Availability Zone |
| Data Subnet | 10.0.104.0/24 | 254 | Private subnet in fifth Availability Zone |
| Data Subnet | 10.0.105.0/24 | 254 | Private subnet in sixth Availability Zone |
| Public Subnet | 10.0.200.0/24 | 254 | Public subnet in first Availability Zone |
| Public Subnet | 10.0.201.0/24 | 254 | Public subnet in second Availability Zone |
| Public Subnet | 10.0.202.0/24 | 254 | Public subnet in third Availability Zone |
| Public Subnet | 10.0.203.0/24 | 254 | Public subnet in fourth Availability Zone |
| Public Subnet | 10.0.204.0/24 | 254 | Public subnet in fifth Availability Zone |
| Public Subnet | 10.0.205.0/24 | 254 | Public subnet in sixth Availability Zone |

## Security Groups Template
在这里查看模板[aws-refarch-wordpress-02-securitygroups.yaml](templates/aws-refarch-wordpress-02-securitygroups.yaml)

## Bastion Template
在这里查看模板 [aws-refarch-wordpress-03-bastion.yaml](templates/aws-refarch-wordpress-03-bastion.yaml)

## Amazon EFS File System Template
在这里查看模板 [aws-refarch-wordpress-03-efsfilesystem.yaml](templates/aws-refarch-wordpress-03-efsfilesystem.yaml)

## Amazon EFS Alarms Template
在这里查看模板 [aws-refarch-wordpress-03-efsalarms.yaml](templates/aws-refarch-wordpress-03-efsalarms.yaml)

## Amazon ElastiCache Template
在这里查看模板 [aws-refarch-wordpress-03-elasticache.yaml](templates/aws-refarch-wordpress-03-elasticache.yaml)

## Amazon Elastic Load Balancing - Application Load Balancer Template
在这里查看模板 [aws-refarch-wordpress-03-publicelb.yaml](templates/aws-refarch-wordpress-03-publicalb.yaml)

## Amazon RDS Template
在这里查看模板 [aws-refarch-wordpress-03-rds.yaml](templates/aws-refarch-wordpress-03-rds.yaml)

## Amazon CloudFront Template
在这里查看模板 [aws-refarch-wordpress-04-cloudfront.yaml](templates/aws-refarch-wordpress-04-cloudfront.yaml)

## WordPress Web Template
在这里查看模板 [aws-refarch-wordpress-04-web.yaml](templates/aws-refarch-wordpress-04-web.yaml)

## Amazon Route 53 Template
在这里查看模板 [aws-refarch-wordpress-05-route53.yaml](templates/aws-refarch-wordpress-05-route53.yaml)

## Amazon CloudWatch Dashboard Template
在这里查看模板 [aws-refarch-wordpress-06-dashboard.yaml](templates/aws-refarch-wordpress-06-dashboard.yaml)


## 将新项目添加到此列表中

如果您发现自己希望这组常见问题解答了特定问题，请 [submit a pull request](https://help.github.com/articles/creating-a-pull-request-from-a-fork/).。有可能是其他人也会从这里列出的答案中受益。


## License

Portions copyright.

- WordPress is licensed under the General Public License (GPLv2 or later) from the Free Software Foundation.

- OPcache is licensed under PHP License, version 3.01.

Please see LICENSE.txt for applicable license terms and NOTICE.txt for applicable notices.


