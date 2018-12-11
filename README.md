# JD Fusion CLI Project

## 安装

JD Fusion CLI程序的运行，需要相应的运行环境。
目前，我们提供了两种方式来安装JD Fusion CLI。第一种RPM包，第二种Dokcer容器。

### 准备信息

要安装、运行JD Fusion CLI需要准备一些必要信息
>* JD Fusion平台的用户AK
>* JD Fusion平台的用户SK

下面的信息为可个性化配置信息：
>* JD Fusion平台的服务入口
>* JD Fusion平台大区编号

若不知如何获取，请查看[JD Fusion平台用户认证](https://github.com/jdfusion/jdfusion.github.io)

### 以RPM的方式安装运行

使用本文档中提供的[fusion-cli.rpm](http://jd-fusion-pub-storage.oss.cn-north-1.jcloudcs.com/releases/cli/rpm/fusion-cli-0.1.2~RELEASE.x86_64.rpm)包，安装JD Fusion CLI。目前，仅提供64位版本。
下载RPM包到你的Linux系统中，在终端中运行命令：
```bash
rpm -i fusion-cli-*.x86_64.rpm
```

在当前用户的home目录下，创建一个".fusion-cloud"文件，在文件中，以property的方式配置AK，SK等信息。
示例如下：
```bash
cd ~
cat > .fusion-cloud << EOF
x.jdcloud.fusion.ak=$AK
x.jdcloud.fusion.sk=$SK
x.jdcloud.fusion.endpoint=http://api.test.fusion.jdcloud.com/zuul
x.jdcloud.fusion.region=cn-north-1a
EOF
```
将`$AK`、`$SK`替换为你自己在JD Fusion平台上AK、SK即可。

另外，JD Fusion CLI的运行需要JRE1.8及以上版本，并且需要设置JAVA_HOME。

在配置完成后即可以使用JD Fusion CLI了。
例如：
```bash
fusion list clouds --vendor jdcloud
```

### 以Docker容器的方式安装运行

使用本文档中提供的CLI运行环境Docker镜像[jdfusion/cli](https://hub.docker.com/r/jdfusion/cli/)。这需要你本地的电脑上安装了Docker程序，关于Docker相关的文档，请参考[Docker官方文档](https://www.docker.com/)

你可以使用以下两种方式来启动Docker容器：
#### 命令行列举环境变量
命令如下：
```bash
docker run -ti \
-e X_JDCLOUD_FUSION_AK=$AK \
-e X_JDCLOUD_FUSION_SK=$SK \
-e X_JDCLOUD_FUSION_ENDPOINT=http://api.test.fusion.jdcloud.com/zuul \
-e X_JDCLOUD_FUSION_REGION=cn-north-1a \
jdfusion/cli list clouds
```
将`$AK`、`$SK`替换为你自己在JD Fusion平台上AK、SK即可。以上命令基于Linux操作系统，如需在Windows系统上执行，可将每行结尾的"\"替换为"`"。

示例中，
`-e` 为设置程序运行所需要的环境变量
`jdfusion/cli`为官方提供的jdfusion/cli运行环境镜像
`list clouds`为实际运行的指令，用于展示用户注册的云信息列表

#### 命令行使用环境变量文件
命令如下：
```bash
# docker run -ti --env-file env.list jdfusion/cli list clouds
```
env.list的文件内容格式为：
```properties
X_JDCLOUD_FUSION_AK=$AK
X_JDCLOUD_FUSION_SK=$SK
X_JDCLOUD_FUSION_ENDPOINT=http://api.test.fusion.jdcloud.com/zuul
X_JDCLOUD_FUSION_REGION=cn-north-1a
```
将`$AK`、`$SK`替换为你自己在JD Fusion平台上AK、SK即可。

示例中，
`--env-file` 为设置程序运行所需要的环境变量的文件
`jdfusion/cli`为官方提供的jdfusion/cli运行环境镜像
`list clouds`为实际运行的指令，用于展示用户注册的云信息列表

## 命令

JD Fusion CLI程序提供了尽可能多的命令，以满足用户的使用需求。

### 云信息相关命令

#### 云信息列表

用于展示用户注册的所有云信息列表

命令格式：
```bash
# list clouds [--vendor <云厂家>]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --vendor | 云厂家 |

示例：
```bash
list clouds --vendor jdcloud
```

#### 云信息详情

用于展示用户指定云的详细信息

命令格式：
```bash
# describe cloud <云实例id>
```

参数：
无

示例：
```bash
describe cloud cloud-123
```

#### 注册云信息

用于根据JSON格式的配置文件，将指定的云注册到JD Fusion平台

命令格式：
```bash
# register cloud [ -f <文件名> | -i <JSON格式的数据> ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |

JSON格式样例：
```json
{
  "cloud": {
    "name": "string",
    "vendor": "string",
    "info": {},
    "metadata": {}
  }
}
```

示例：
```bash
register cloud -i '{"cloud":{"name":"mycloud111","vendor":"jdcloud","info":{"access_key":"my_access_key","secret_key":"my_secret_key","region":"cn-north-1"},"metadata":{}}}'
```

-i 参数的内容为标准JSON格式，在Linux环境下可以使用''(单引号)括起来，而在Windows环境下则需要对JSON字符串中的""(双引号)进行转义，并且外层不加''（单引号）

#### 注销云信息

将用户指定的云信息从JD Fusion平台上注销

命令格式：
```bash
# unregister cloud <云实例ID>
```

参数：
无

示例：
```bash
unregister cloud cloud-123
```

### 虚拟机相关命令

#### 虚拟机列表

用于展示用户指定云上的所有虚拟机信息列表

命令格式：
```bash
# list vms --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list vms --cloud cloud-123

```

#### 虚拟机详细信息

用于展示用户指定虚拟机的详细信息

命令格式：
```bash
# describe vm <虚拟机实例ID> [ --cloud <云实例ID> ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 云实例ID（list clouds中的id） |

示例：
```bash
describe vm vm-123
```

#### 创建虚拟机

用于根据JSON格式的配置文件，在指定的云上创建虚拟机

命令格式：
```bash
# create vm [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "vms": {
    "az": "string",
    "name": "string",
    "hostName": "string",
    "description": "string",
    "subnetId": "string",
    "tags": [
      {
        "tagKey": "string",
        "tagValue": "string"
      }
    ],
    "privateIpAddress": "string",
    "imageId": "string",
    "instanceFlavorType": "string",
    "securityGroupId": "string",
    "internetMaxBandwidthIn": 0,
    "internetMaxBandwidthOut": 0,
    "password": "string",
    "passwordInherit": true,
    "userData": "string",
    "keyPairName": "string",
    "systemDisk": {
      "category": "string",
      "diskSize": 0,
      "description": "string"
    }
  }
}
```

示例：
```bash
create vm -f /data/json/vm.json --cloud cloud-123
```

#### 删除指定的虚拟机

用于删除用户指定的虚拟机

命令格式：
```bash
# del vm <虚拟机实例ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
del vm vm-123 --cloud cloud-123
```

#### 启动虚拟机

启动用户指定的虚拟机

命令格式：
```bash
# start vm <虚拟机实例ID>
```

参数：
无

示例：
```bash
start vm vm-123
```

#### 停止虚拟机

停止用户指定的虚拟机

命令格式：
```bash
# stop vm <虚拟机实例ID>
```

参数：
无

示例：
```bash
stop vm vm-123
```

#### 重启虚拟机

重启用户指定的虚拟机

命令格式：
```bash
# reboot vm <虚拟机实例ID>
```

参数：
无

示例：
```bash
reboot vm vm-123
```

### 云硬盘相关命令

#### 云硬盘列表

用于展示用户指定云上的所有云硬盘信息列表

命令格式：
```bash
# list disks --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list disks --cloud cloud-123
```

#### 云硬盘详细信息

用于展示用户指定云硬盘的详细信息

命令格式：
```bash
# describe disk <云硬盘实例ID> [ --cloud <云实例ID> ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 云实例ID（list clouds中的id） |

示例：
```bash
describe disk disk-123
```

#### 创建云硬盘

用于根据JSON格式的配置文件，在指定的云上创建云硬盘

命令格式：
```bash
# create disk [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "disk": {
    "name": "string",
    "description": "string",
    "diskSizeGB": 0,
    "az": "string",
    "diskType": "string",
    "snapshotId": "string",
    "tags": [
      {
        "tagKey": "string",
        "tagValue": "string"
      }
    ]
  }
}
```

示例：
```bash
create disk -f /data/json/disk.json --cloud cloud-123
```

#### 删除指定的云硬盘

用于删除用户指定的云硬盘

命令格式：
```bash
# del disk <云硬盘实例ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
del disk disk-123 --cloud cloud-123
```

#### 绑定云硬盘到虚拟机

用于将用户指定的云硬盘，绑定到用户指定的虚拟机上

命令格式：
```bash
# attach disk --from <云硬盘实例ID> --to <虚拟机ID> --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --from | 必填，指定要绑定的云硬盘ID |
| --to | 必填，指定云硬盘要绑定到的虚拟机ID |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

示例：
```bash
attach disk --from disk-123 --to vm-123 --cloud cloud-123
```

#### 将云硬盘从虚拟机上解绑

用于将用户指定的云硬盘，从用户指定的虚拟机上解绑

命令格式：
```bash
# detach disk --from <云硬盘实例ID> --to <虚拟机ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --from | 必填，指定要解绑的云硬盘ID |
| --to | 必填，指定的云硬盘要从哪台虚拟机上解绑 |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
detach disk --from disk-123 --to vm-123 --cloud cloud-123
```

### 公网IP相关命令 

#### 公网IP列表

用于展示用户指定云上的所有公网IP信息列表

命令格式：
```bash
# list eips --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list eips --cloud cloud-123
```

#### 公网IP详细信息

用于展示用户指定公网IP的详细信息

命令格式：
```bash
# describe eip <弹性公网IP实例ID> [ --cloud <云实例ID> ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 云实例ID（list clouds中的id） |

示例：
```bash
describe eip eip-123
```

#### 分配公网IP

用于根据JSON格式的配置文件，在指定的云上分配公网IP

命令格式：
```bash
# create eip [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "allocate": {
    "name": "string",
    "bandwidth": "string",
    "provider": "string"
  }
}
```

示例：
```bash
create eip -f /data/json/eip.json --cloud cloud-123
```

#### 删除指定的公网IP

用于删除用户指定的公网IP

命令格式：
```bash
# del eip <弹性公网IP实例ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
del eip eip-123 --cloud cloud-123
```

#### 绑定公网IP到虚拟机

用于将用户指定的公网IP，绑定到用户指定的虚拟机上

命令格式：
```bash
# attach eip --from <公网IP实例ID> --to <虚拟机ID> --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --from | 必填，指定要绑定的公网IP的ID |
| --to | 必填，指定公网IP的ID要绑定到的虚拟机ID |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

示例：
```bash
attach eip --from eip-123 --to vm-123 --cloud cloud-123
```

#### 将公网IP从虚拟机上解绑

用于将用户指定的公网IP，从用户指定的虚拟机上解绑

命令格式：
```bash
# detach eip --from <公网IP实例ID> --to <虚拟机ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --from | 必填，指定要解绑的公网IP的ID |
| --to | 必填，指定的公网IP要从哪台虚拟机上解绑 |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
detach eip --from eip-123 --to vm-123 --cloud cloud-123
```

### 镜像相关命令

#### 镜像列表

用于展示用户指定云上的所有镜像信息列表

命令格式：
```bash
# list images --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list images --cloud cloud-123
```

### 实例类型相关命令

#### 实例类型列表

用于展示用户指定云上的所有实例类型信息列表

命令格式：
```bash
# list instancetypes --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list instancetypes --cloud cloud-123
```

### 密钥对相关命令

#### 密钥对列表

用于展示用户指定云上的所有密钥对信息列表

命令格式：
```bash
# list kps --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list kps --cloud cloud-123
```

#### 密钥对详细信息

用于展示用户指定name的详细信息

命令格式：
```bash
# describe keypair <密钥对的name> [ --cloud <云实例ID> ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 云实例ID（list clouds中的id） |

示例：
```bash
describe keypair kp-name
```

#### 创建密钥对

用于根据JSON格式的配置文件，在指定的云上创建密钥对

命令格式：
```bash
# create keypair [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "keypair": {
    "name": "string",
    "publicKey": "string"
  }
}
```

示例：
```bash
create keypair -f /data/json/keypair.json --cloud cloud-123
```

#### 删除指定的密钥对

用于删除用户指定的name

命令格式：
```bash
# del keypair <密钥对的name> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
del keypair kp-name --cloud cloud-123
```

### 网卡相关命令

#### 网卡列表

用于展示用户指定云上的所有网卡信息列表

命令格式：
```bash
# list nis --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list nis --cloud cloud-123
```

#### 网卡详细信息

用于展示用户指定网卡的详细信息

命令格式：
```bash
# describe ni <网卡实例ID> [ --cloud <云实例ID> ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 云实例ID（list clouds中的id） |

示例：
```bash
describe ni ni-123
```

#### 创建网卡

用于根据JSON格式的配置文件，在指定的云上创建网卡

命令格式：
```bash
# create ni [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "netInterface": {
    "name": "string",
    "description": "string",
    "subnetId": "string",
    "zoneId": "string",
    "privateIpAddress": "string",
    "securityGroupId": "string"
  }
}
```

示例：
```bash
create ni -f /data/json/ni.json --cloud cloud-123
```

#### 删除指定的网卡

用于删除用户指定的网卡

命令格式：
```bash
# del ni <网卡实例ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
del ni ni-123 --cloud cloud-123
```

#### 绑定网卡到虚拟机

用于将用户指定的网卡，绑定到用户指定的虚拟机上

命令格式：
```bash
# attach ni --from <网卡实例ID> --to <虚拟机ID> --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --from | 必填，指定要绑定的网卡ID |
| --to | 必填，指定网卡要绑定到的虚拟机ID |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

示例：
```bash
attach ni --from ni-123 --to vm-123 --cloud cloud-123
```

#### 将网卡从虚拟机上解绑

用于将用户指定的网卡，从用户指定的虚拟机上解绑

命令格式：
```bash
# detach ni --from <网卡实例ID> --to <虚拟机ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --from | 必填，指定要解绑的网卡ID |
| --to | 必填，指定的网卡要从哪台虚拟机上解绑 |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
detach ni --from ni-123 --to vm-123 --cloud cloud-123
```

### 安全组相关命令

#### 安全组列表

用于展示用户指定云上的所有安全组信息列表

命令格式：
```bash
# list sgs --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list sgs --cloud cloud-123
```

#### 安全组详细信息

用于展示用户指定安全组的详细信息

命令格式：
```bash
# describe sg <安全组ID> [ --cloud <云实例ID> ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 云实例ID（list clouds中的id） |

示例：
```bash
describe sg sg-123
```

#### 创建安全组

用于根据JSON格式的配置文件，在指定的云上创建安全组

命令格式：
```bash
# create sgs [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "securityGroup": {
    "name": "string",
    "description": "string",
    "vpcId": "string"
  }
}
```

示例：
```bash
create sgs -f /data/json/sgs.json --cloud cloud-123
```

#### 删除指定的安全组

用于删除用户指定的安全组

命令格式：
```bash
# del sg <安全组实例ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
del sg sg-123 --cloud cloud-123
```

### 负载均衡相关命令

#### 负载均衡列表

用于展示用户指定云上的所有负载均衡信息列表

命令格式：
```bash
# list slbs --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list slbs --cloud cloud-123
```

#### 负载均衡详细信息

用于展示用户指定负载均衡的详细信息

命令格式：
```bash
# describe slb <负载均衡实例ID> [ --cloud <云实例ID> ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 云实例ID（list clouds中的id） |

示例：
```bash
describe slb slb-123
```

#### 创建负载均衡

用于根据JSON格式的配置文件，在指定的云上创建负载均衡

命令格式：
```bash
# create slb [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "slb": {
    "name": "string",
    "subnetId": "string",
    "loadBalancerSpec": "string"
  }
}
```

示例：
```bash
create slb -f /data/json/slb.json --cloud cloud-123
```

#### 删除指定的负载均衡

用于删除用户指定的负载均衡

命令格式：
```bash
# del slb <负载均衡实例ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
del slb slb-123 --cloud cloud-123
```

### 子网相关命令

#### 子网列表

用于展示用户指定云上的所有子网信息列表

命令格式：
```bash
# list subnets --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list subnets --cloud cloud-123
```

#### 子网详细信息

用于展示用户指定子网的详细信息

命令格式：
```bash
# describe subnet <子网实例ID> [ --cloud <云实例ID> ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 云实例ID（list clouds中的id） |

示例：
```bash
describe subnet subnet-123
```

#### 创建子网

用于根据JSON格式的配置文件，在指定的云上创建子网

命令格式：
```bash
# create subnet [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "subnet": {
    "name": "string",
    "vpcId": "string",
    "cidrBlock": "string",
    "description": "string",
    "zoneId": "string"
  }
}
```

示例：
```bash
create subnet -f /data/json/subnet.json --cloud cloud-123
```

#### 删除指定的子网

用于删除用户指定的子网

命令格式：
```bash
# del subnet <子网实例ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
del subnet subnet-123 --cloud cloud-123
```

### 专有网络相关命令

#### 专有网络列表

用于展示用户指定云上的所有专有网络信息列表

命令格式：
```bash
# list vpcs --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list vpcs --cloud cloud-123
```

#### 专有网络详细信息

用于展示用户指定专有网络的详细信息

命令格式：
```bash
# describe vpc <专有网络实例ID> [ --cloud <云实例ID> ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 云实例ID（list clouds中的id） |

示例：
```bash
describe vpc vpc-123
```

#### 创建专有网络

用于根据JSON格式的配置文件，在指定的云上创建专有网络

命令格式：
```bash
# create vpc [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "vpc": {
    "name": "string",
    "addressPrefix": "string",
    "description": "string",
    "cidrBlock": "string",
    "userCidr": "string"
  }
}
```

示例：
```bash
create vpc -f /data/json/vpc.json --cloud cloud-123
```

#### 删除指定的专有网络

用于删除用户指定的专有网络

命令格式：
```bash
# del vpc <专有网络实例ID> --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
del vpc vpc-123 --cloud cloud-123
```

### 服务器组相关命令

#### 服务器组列表

用于展示用户指定云上的所有服务器组信息列表

命令格式：
```bash
# list vservergroups --cloud <云实例ID>
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| --cloud | 必填，云实例ID（list clouds中的id） |

示例：
```bash
list vservergroups --cloud cloud-123
```

#### 创建服务器组

根据JSON格式的配置文件，创建虚拟服务器组，并添加后端服务器

命令格式：
```bash
# create vsg [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "vserverGroup": {
    "loadBalancerId": "string",
    "vserverGroupName": "string",
    "backendServers": [
      {
        "serverId": "string",
        "port": 0,
        "weight": 0
      }
    ]
  }
}
```

示例：
```bash
create vsg -f /data/json/vsg.json --cloud cloud-123
```

### HTTP监听相关命令

#### 创建HTTP监听

根据JSON格式的配置文件，创建HTTP监听

命令格式：
```bash
# create listener [ -f <文件名> | -i <JSON格式数据> ] --cloud <云实例ID> [ --tail ] [ --no-table ]
```

参数：

| 参数名称 | 参数说明 |
| ---- | ---- |
| -f/--file | -f、-i必须要有一个，文件名，内容为JSON格式的数据 |
| -i/--input | -f、-i必须要有一个，JSON格式的数据 |
| --cloud | 必填，云实例ID（list clouds中的id） |
| --tail | 跟踪任务执行过程，不可与--no-table同时出现 |
| --no-table | 只展示结果的ID，不可与--tail同时出现 |

JSON格式样例：
```json
{
  "httpListener": {
    "loadBalancerId": "string",
    "listenerPort": 0,
    "backendServerPort": 0,
    "vserverGroupId": "string",
    "bandwidth": 0,
    "healthCheckConnectPort": 0,
    "healthyThreshold": 0,
    "unhealthyThreshold": 0,
    "healthCheckTimeout": 0,
    "healthCheckInterval": 0,
    "healthCheckHttpCode": "string"
  }
}
```

示例：
```bash
create listener -f /data/json/listener.json --cloud cloud-123
```
