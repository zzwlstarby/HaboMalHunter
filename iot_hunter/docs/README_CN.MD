## <a name="readme_cn">Tencent IoT Hunter 中文使用帮助</a>

## 目录
一、简介

二、框架特点

三、兼容性

四、提取信息详情

五、使用方法

六、使用扩展接口

七、数据可视化

八、使用到的工具

九、未来规划


## 一、简介

Tencent IoT Hunter是为了收集IoT威胁情报而创建的框架工具，通过静态提取、动态提取、第三方网络平台获取信息的方式，全方位构建IoT病毒生命周期，为安全研究人员对IoT病毒的分析、研究、追踪提供详尽的威胁情报信息。

使用本框架工具可以非常精准的、细粒度的获取IoT样本中的相关恶意信息（CNC、Domain、function、etc.），这些恶意信息可以直接用来创建IoT恶意信息云查服务，而不需要分析人员对恶意信息进行二次确认，提升了恶意信息处理效率。

本框架提供了良好的扩展接口，使用者可编写个性化plugin来扩展信息提取的范围。通过提取出的信息，可快速搭建IoT威胁情报平台，快速进行可视化分析，挖掘IoT样本家族、变种的活跃度与分布情况。

## 二、框架特点

### （一）良好的扩展接口

此框架提供了扩展接口，使用此框架的安全人员可编写自己的plugin，来扩展信息提取的范围。为方便使用者编写自己的plugin，框架提供了基础类，新的plugin只需要继承基础类便可被直接执行，不需要编写额外的代码，让使用人专注于目标样本的信息提取。同时，框架会记录详细的log，方便使用者根据log记录来进行代码调试。

在编写plugin时，分析人员可定义自己的目标样本家族类型、传播方式、攻击的设备、攻击方式等，并可以精准获取CNC、Domain、弱口令字典、控制命令等恶意信息。

### （二）细粒度信息提取

以往的一些分析工具会将样本中的信息进行粗粒度提取，需要进行二次确认是否为恶意，才能对信息进行使用。

例如，从一个样本中粗粒度的提取到了一个IP，但这个IP是否为恶意并不知晓，因此不能直接拿来当成恶意IP来使用，需要对这个IP进一步确认是否为恶意IP才能使用。

而本框架工具在静态提取信息时，使用精准的特征匹配方式，可以精准的提取出恶意信息，无需再进行二次确认便可将恶意信息直接使用。

### （三）第三方情报汇总

本框架尝试通过公开的第三方网络情报平台，拉取更多IoT情报信息，以便为使用者提供更多有价值的参考信息。

## 三、兼容性

* Python2、Python3

* Windows、Linux、（OS X暂时未测试）

## 四、提取信息详情

本框架支持ARM、X86、X64、MIPS、Sparc、PowerPC等平台上的ELF文件静态、动态信息提取。

### （一）静态信息

**定义信息**

* 病毒名

* 恶意类型

* 家族信息

* 传播方式

* 攻击设备

* 主要攻击方式

**提取信息**

* 基本信息（文件大小、文件类型、运行平台、md5、sha1、sha256）

* 控制端地址

* 域名

* IP

* URL

* UDP

* TCP

* DNS

* 配置信息

* 弱口令字典

* 所有字符串

* 可疑字符串

* 所有函数名

* 控制命令

* 加壳信息

### （二）动态信息提取

**进程信息：**

* 进程EXECVE 执行：参数信息

* 进程clone 执行信息

**文件操作信息：**

* 文件打开：文件名

* 文件读取：读取数据

* 文件写入：写入数据

**Socket信息：**

* Connect信息：ip

* Recvfrom 信息：ip，数据

* Sendto信息：ip，数据

* Bind信息：ip

**网络通信信息：**

* 网络通信包信息：ip，协议

* HTTP信息：host，数据

* TCP信息：ip，数据

* UDP信息：ip, 数据

* IRC 信息：ip，IRC消息

**动态分析插件信息：**

* 插件名称

* 分析插件结果

### （三）输出结果

所有分析结果会以json的形式保存到结果文件中：

**静态分析结果JSON:**

![_15435692695130](https://user-images.githubusercontent.com/17021671/49279858-94613a00-f4c3-11e8-839a-07adfb7b9943.png)
![_20181130171521](https://user-images.githubusercontent.com/17021671/49279861-975c2a80-f4c3-11e8-9884-84dd8eec0b74.png)
![_20181130171541](https://user-images.githubusercontent.com/17021671/49279865-99be8480-f4c3-11e8-8c13-a04493340229.png)

**动态分析数据JSON：**
![2](https://user-images.githubusercontent.com/17021671/49278251-30d50d80-f4bf-11e8-9dc5-eafc0b25a0c1.png)
![3](https://user-images.githubusercontent.com/17021671/49278258-35012b00-f4bf-11e8-9e3e-b7731039b11d.png)
![4](https://user-images.githubusercontent.com/17021671/49278261-36caee80-f4bf-11e8-9c06-13a424b98ec6.png)
![5](https://user-images.githubusercontent.com/17021671/49278270-39c5df00-f4bf-11e8-89b2-903215ce3e5a.png)

## 五、使用方法

### （一）静态信息提取工具

此工具可使用命令行设置参数，也可以通过修改配置文件来设置参数。

```
iot_hunter.py -h
usage: iot_hunter.py [-h] [-s SAMPLE_DIR | -f SAMPLE_PATH] [-o OUTPUT_DIR]
                     [-v] [-c]

Tencent IoT Hunter

optional arguments:
  -h, --help        show this help message and exit
  -s SAMPLE_DIR   samples folder path for analyzing.
  -f SAMPLE_PATH  singal sample path for analyzing.
  -o OUTPUT_DIR  output folder path for saving analysis result and log files.
  -v, --virustotal    try to get the sample info from VirusTotal.
  -c, --clean       clean result files,save all results to 
                 result_file_detail_info.txt.
```

#### 1．使用命令行设置参数

**单样本分析：**

```
iot_hunter.py -f F:\Samples\0019c77ad7f4f97ec492726e9aa8e15e -o F:\result
```

**多样本分析：**
```
iot_hunter.py -s F:\Samples -o F:\result
Sample Dir: F:\Samples
Output Dir: F:\result
F:\Samples\0019c77ad7f4f97ec492726e9aa8e15e
F:\Samples\2983a7e5bc97996cd98dffd4f78e95b2
Packed by UPX.
F:\Samples2989b5de79e0ab4417c10b64738a10a0
```
**拉取样本VirusTotal相关信息：**
```
iot_hunter.py -v -s F:\Samples -o F:\result
```
**将分析结果导入Elasticsearch：**
```
import_data_to_es.py -r F:\result\result_ida_file_analysis.txt
```
#### 2．使用配置文件conf.py设置参数
```
MAL_SAMPLES_DIR = r"F:\Samples"
RESULT_OUTPUT_DIR = r"F:\result"
```
直接调用py脚本即可：
```
python iot_hunter.py 
```
#### 3．配置文件conf.py参数说明

**必需设置的参数：**
```
IDA分析工具路径，https://www.hex-rays.com
IDA_EXECUTABLE_FILE_PATH = r"C:\Program Files (x86)\IDA 6.5\idaq.exe"
```
**按需配置的参数：**
```
样本目录
MAL_SAMPLES_DIR = r"F:\Samples"

结果输出目录
RESULT_OUTPUT_DIR = r"F:\result"

UPX工具路径，https://github.com/upx/upx-testsuite
UPX_EXECUTABLE_FILE_PATH = r"f:\tools\upx\i386-win32.pe\upx-3.95.exe"

VirusTotal key，https://www.virustotal.com
VIRUSTOTAL_KEY = "676xxxxxxxxxxxxxxxxxxxxxxxxxxxxx7d1db"

访问VirusTotal需要代理时，设置此参数
PROXIES = {"http": "proxy.xxxx.com:8080", "https": "proxy.xxxx.com:8080"}

始终尝试接取样本的VirusTotal时，设置此参数为True
VIRUSTOTAL_ALWAYS_GET = False

样本大小限制
FILE_SIZE_LIMIT = 10 * 1024 * 1024

Elasticsearch相关参数，https://github.com/elastic/elasticsearch-py
ES_HOST = "localhost:9200"
ES_INDEX_NAME = "iot_threat"
ES_TYPE_NAME = "FileAnalysis"
```
**可保持默认的参数：**
```
IDA脚本输出的分析结果文件
IDA_FILE_ANALYSIS_RESULT = r"result_ida_file_analysis.txt"

VirusTotal拉取的样本信息文件
VIRUSTOTAL_RESULT = r"result_virustotal.txt"

汇总所有信息的结果（IDA + VirusTotal），需要使用-c参数
FILE_DETAIL_INFO = r"result_file_detail_info.txt"

Log信息文件
IDA_ANALYSIS_LOGGER_NAME = "IDA_ANALYSIS_FILE"
IDA_FILE_ANALYSIS_LOG = r"log_ida_file_analysis.log"
IOT_HUNTER_LOGGER_NAME = "IOT_HUNTER_MAIN"
IOT_HUNTER_LOG = r"log_iot_hunter.log"
OTHER_ERROR_LOG = r"log_other_error.log"
ES_LOGGER_NAME = "IMPORT_DATA_TO_ES"
ES_IMPORT_DATA_LOG = "log_import_data_to_es.log"

内部使用的参数，尽量不要修改，
如要修改，需一并修改相关文件名与目录名
IDA_PYTHON_SCRIPT = "ida_analysis_file.py"
IDA_PLUGINS_DIR_NAME = "plugins"
```
### （二）动态提取工具

#### 1．分析环境搭建

动态分析环境需要在虚拟机环境中运行IoT样本，监控其行为，IoT样本执行环境需要虚拟机，本动态分析工具基于VirtualBox开发，需要安装在Guest VM安装linux系统如（Ubuntu）。

**Guest VM工具安装：**

1.Linux 系统，VBoxGuestAdditions_5.2.22

2.QEMU虚拟机，通过QEMU User Mode 模拟支持运行ARM, MIPS, PowerPC等多平台IOT文件

3.Strace：监控样本运行的系统调用信息

4.Tcpdump: 记录样本执行的网络包，用于分析网络行为

5.纯净的系统安装上述工具后，使用root用户，保存镜像名为analysis

**Host 分析工具：**

1.Tshark：用于分析tcpdump生成的pcap包

#### 2．分析工具配置

配置文件：DynamicConfig.conf
```
[guest_vm]
# guest os configuration, username, password,vm name
name=ubuntu11.04
username=root
password=root

#sample to run path
runpath=/home/root/Desktop/

#host os path to put strace log and tcpdump pcap file
host_log_path=f:\vm_share\strace.log
host_log_tcpdump=f:\vm_share\tcpdump.pcap

#guest os path to put strace, tcpdump, guestanalyzer.py
vm_log_path=/home/justin/Desktop/strace.log
vm_log_tcpdump=/home/justin/Desktop/tcpdump.pcap
guest_analyzer_path=/home/justin/Desktop

[vbox]
virtualbox_path = D:\Program Files\Oracle\VirtualBox

[analyzer]
max_strace_lines=20000
strace_log_path=f:\vm_share\strace.log
tshark_path=c:\Program Files\Wireshark\tshark.exe
host_log_tcpdump=f:\vm_share\tcpdump.pcap
```
#### 3．动态分析工具使用

**单样本分析：** IotHunterDynamic.py –f 文件名 –d log目录

**多样本分析：** IotHunterDynamic.py –f 目录 –d log目录
```
IotHunterDynamic.py -h
usage: IotHunterDynamic.py [-h] [-f  FILENAME] [-d FILE_DIR] [-o OUT_DIR]

optional arguments:
  -h, --help            show this help message and exit
  -f  FILENAME, --filename FILENAME
                        File to Analyze
  -d FILE_DIR, --file_dir FILE_DIR
                        Files directory to Analyze
  -o OUT_DIR, --out_dir OUT_DIR
                        log output directory
```
**分析框架流程：**

1.获取待分析样本

2.启动分析虚拟机

3.发送文件到虚拟机

4.发送分析脚本到虚拟机

5.执行样本，记录行为

6.从分析机获取日志

7.解析日志，获取文件，网络，进程信息

8.调用用户自定义解析插件

9.生成样本json格式分析结果

## 六、使用扩展接口

### （一）添加静态分析plugin

使用者可根据目标样本编写自己的信息提取插件plugin，将编写好的plugin放在plugins目录中即可直接执行。

框架提供了基础类PluginParent，此类提供了基础信息：
```
class PluginParent():
    malicious_type = []
    malicious_family = []
    spread_way = []
    attack_device = []
    main_function = []
    cnc = []
    ip = []
    domain = []
    url = []
    udp = []
    tcp = []
    dns = []
    configuration = []
    weak_password = []
    suspicious_string = []
    bot_command = []
    other_info = []
    
    detect = ENUM_DETECT_RESULT["UNKNOW"]
    virus_name = ""
    
    __metaclass__ = ABCMeta
    @abstractmethod
    def analyze(self, *argv):
        return False
```
plugin需要继承PluginParent类，并实现analyze函数，在此函数中对所需字段进行填充，在代码尾部调用add_plugin(派生类名)即可：
```
import re
from util import *

class MiraiARM(PluginParent):
    def __init__(self):
        self.malicious_type = ["Botnet"]
        self.malicious_family = ["Mirai"]
        self.spread_way = ["SSH", "Telnet"]
        self.attack_device = ["Router", "Camera", "DVR", "Printer", "TV Box"]
        self.main_function = ["DDoS", "Downloader"]
        self.virus_name = "Trojan.Linux.Mirai.caa"
        self.configuration = []
        self.weak_password = []
        self.cnc = []
        self.detect = 0
        
    def analyze(self, *argv):
        self.get_configuration(key)
        self.get_cnc()
        self.get_weak_password(key)
        ...
            
    def get_cnc():
        ...

    def get_weak_password(self, key):
        ...

add_plugin(MiraiARM)
```
### （二）静态plugin调试

由于IDAPython命令行调用的方式遇到异常时会自动退出，并不会保存代码异常信息，因此，对编写plugin的使用者来说非常不方便，无法定位自己代码的问题，为此，本框架提供了log记录，使用者在编写plugin并执行后，可查看log来定位异常问题。

log_ida_file_analysis.log：

![14](https://user-images.githubusercontent.com/17021671/49281660-0e93bd80-f4c8-11e8-98e8-eadf3245fc59.png)

使用者也可以使用logger.info("xxx")方式，打印自己的log信息。

### （三）添加动态plugin

使用者可根据目标样本编写自己的动态信息提取插件plugin，将编写好的plugin放在DynamicPlugins\目录中即可直接执行。

插件开发：需实现analyze 和 get_result两个接口，分析框架会调用所有的插件，并记录插件结果，analyze函数参数behaviors记录了样本的行为数据。用户可以进行自定义分析，获取特定结果。

插件示例：获取样本connect所有目的ip列表
```
class GetConnectIP():
    """plugin to get connect ip list"""
    def __init__(self):
        self.ip_list = []
    def analyze(self, behaviors):
        hit = 0        
        for data in behaviors.socket_log['connect']:
            hit = 1
            addr = data['addr']
            if addr not in self.ip_list:
                self.ip_list.append(data['addr'])
        return hit
    def get_result(self):
        return self.ip_list
```
## 七、数据可视化

框架内提供了将分析数据导入到Elasticsearch的功能，使用者可快速搭建IoT数据挖掘平台，进行数据可视化分析。可以快速挖掘IoT家族的变种、传播广度、活跃度、新的弱口令利用等。

### （一）ES数据展示

![6](https://user-images.githubusercontent.com/17021671/49278295-4cd8af00-f4bf-11e8-83d5-49b18fd0f046.png)

### （二）弱口令的云图

通过对IoT样本中使用的弱口令进行提取，可观察到哪些弱口令使用频度最高。而对所有已知弱口令进行监控，当出现新的弱口令时，说明很有可能出现新的变种或新的弱口令漏洞利用。

![7](https://user-images.githubusercontent.com/17021671/49278298-4ea27280-f4bf-11e8-80db-5fd5e281cb7a.png)

### （三）地址位置展示

通过提取的CNC地址，可以获取到IoT家族相关地址位置。

![8](https://user-images.githubusercontent.com/17021671/49278303-519d6300-f4bf-11e8-872e-6e40a66e75f4.png)

### （四）挖掘变种家族

通过一些关键字进行挖掘，可以聚类挖掘出不同的家族变种。

![9](https://user-images.githubusercontent.com/17021671/49278312-54985380-f4bf-11e8-83c9-1f97b824231a.png)

## 八、使用到的工具

* IDA(https://www.hex-rays.com)

* IDA Python(https://github.com/idapython/src)

* UPX(https://github.com/upx/upx-testsuite)

* VirusTotal(https://www.virustotal.com)

* Elasticsearch(https://github.com/elastic/elasticsearch-py)

* Kibana(https://www.elastic.co/downloads)

* QEMU(https://www.qemu.org/)

* VirtualBox(https://www.virtualbox.org/)

* Strace(https://strace.io/)

* Tcpdump(http://www.tcpdump.org/)

* Wireshark(https://www.wireshark.org/)



## 九、未来规划

* 增加更多plugin

* 增加监控新变种的扩展功能

* 更多第三方平台信息接入

* 更多行为监控合入

* 更多真实IOT环境模拟
