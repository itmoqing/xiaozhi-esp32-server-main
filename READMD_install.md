采用本地源码运行全模块的好处是，你可以体验最全的小智大模型各类工具。
声明，本文参考了：
小智源码文档
本地源码运行全模块步骤
小智硬件源码

📦 环境总览
组件	版本	用途
Docker	最新版	容器化部署数据库
MySQL	8.0+	数据存储
Redis	7.0+	缓存服务
JDK	21	后端运行环境
Python	3.10	AI模型推理
🔧 一、安装MySQL数据库
1. 先安装Docker
通过docker安转MySQL。这里因为我是没有MySQL，所以我采取了使用Docker安装。我用的Docker是在官方下载的，我并没有按照他上面给的方式去安装，感觉看的不清晰。
（1）安装Docker。我是win11系统，我选择了第3个。


（2）安装的时候记得要关闭所有杀毒软件！关闭所有防火墙！安装的时候使用管理员运行.exe文件，安装的时候按照默认选择。


（2）安装好后关闭，然后重新管理员运行打开软件。进入界面时Accept一下条款，然后Skip跳过介绍。



（3）Docker设置。这一步是在 Docker Desktop 里开启 WSL 2 集成，目的是让 Docker 可以直接在你的 WSL 2（例如 Ubuntu）环境中运行容器。点击设置->Resources->WSL integration，然后打开Ubuntu的按钮，为了让你的 Ubuntu WSL 环境可以直接用 Docker。


这里我当时安装的时候没有出现Ubuntu的按钮，是因为当时我的系统尚未安装任何WSL 2发行版。如果你没有遇到这个问题可以跳过这里。解决方法：
① 安装WSL 2发行版

wsl --install
运行本项目
powershell
1
②将发行版设置为WSL 2模式

wsl --set-version Ubuntu 2
运行本项目
powershell
1
③验证是否成功

wsl --list --verbose
运行本项目
powershell
1
确认输出中包含Ubuntu且版本为WSL 2。然后在关闭、管理员重启Docker，就可以看到这里有这个Ubuntu选项了。
（4）接下来在Docker Desktop 中设置磁盘镜像存储位置，Docker Desktop 会把所有的数据（包括镜像、容器、卷）存在 C 盘，如果不改路径，时间长了 C 盘可能爆满。因此我选择了D盘，点击Apply&restart。
等待安装好后，管理员打开Docker，这样就安装好这个大部头。

2. 通过docker安转MySQL
安装好 Docker 后，确保Docker打开的状态。你可以通过 命令行终端PowerShell，通过docker安装mysql。

docker run --name xiaozhi-esp32-server-db -e MYSQL_ROOT_PASSWORD=123456 -p 3306:3306 -e MYSQL_DATABASE=xiaozhi_esp32_server -e MYSQL_INITDB_ARGS="--character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci" -d mysql:latest
运行本项目
powershell
1

这个时候你可以看到在Docker中看到当前系统正在运行一个MySQL容器。


🔧 二、安装redis
如果还没有Redis，你可以通过docker安装redis。

docker run --name xiaozhi-esp32-server-redis -d -p 6379:6379 redis
运行本项目
powershell
1

回车之后，可以看到redis启动运行。


🔧 三、运行manager-api程序（后端服务）
1. 安装JDK21
① JDK下载网址：Java 。我是window系統行4位。解压后，可安装到D盘。例如我是安装到 D:\JDK21_0_6下的。


② 设置JDK的path环境变量。首先是Path环境变量设置。在“系统变量”区域找到并选中 Path，然后点击“编辑”。 在编辑窗口中，点击“新建”，添加以下JDK的安装路径。


③ 设置JDK的JAVA_HOME 环境变量。在“系统变量”部分点击“新建”， 变量名：JAVA_HOME，变量值：JDK 安装路径。


④ 可以打开PowerShell，验证 JDK 是否安装成功。

java -version
运行本项目
powershell
1
如果返回类似下面的结果，说明 JDK 已正确安装。

java version "21" 2025-03-21
Java(TM) SE Runtime Environment (build 21+36-2239)
Java HotSpot(TM) 64-Bit Server VM (build 21+36-2239, mixed mode)
运行本项目
powershell
1
2
3
2.安装Maven
① Maven 压缩包（下载 Maven 的二进制 zip 文件）。


②解压 Maven 到 D 盘，例如：D:\Apache\Maven。

③设置环境变量
• MAVEN_HOME：指向你解压 Maven 的目录（例如：D:\Apache\Maven）。
• Path：将 Maven 的 bin 目录添加到 Path 环境变量中（例如：D:\Apache\Maven\bin）。

3.安装Java环境相关插件
打开 VSCode，安装 Java 插件（例如：Java Extension Pack）。


4.使用Vscode编程工具加载manager-api模块
首先你需要去下载源码 。解压后按照教程你需要“把它重命名成xiaozhi-esp32-server，我把工程放在了E盘，然后使用VScode打开路径“E:\xiaozhi-esp32-server\main\manager-api”。
①按照提供的配置，将数据库连接信息填入 ：

src/main/resources/application-dev.yml中：
spring:
  datasource:
    username: root
password: 123456
运行本项目
java
运行
1
2
3
4
5
② 在src/main/resources/application-dev.yml中配置Redis连接信息。

spring:
    data:
      redis:
        host: localhost
        port: 6379
        password:
        database: 0
运行本项目
java
运行
1
2
3
4
5
6
7


5.运行manager-api程序
一定要确保你的Docker是打开的，这俩是在运行的情况下，再去运行程序。


当你看到输出日志时，说明你的manager-api启动成功了。


🔧四、运行manager-web程序（前端控制台）
1. 安装nodejs
（下面的步骤都是按照教程来的）
nodejs的安装地址：Node.js — 在任何地方运行 JavaScript

2. 使用Vscode编程工具加载manager-web模块
①终端命令进入manager-web目录下。

npm install
运行本项目
powershell
1
② 启动

npm run serve
运行本项目
powershell
1


③ 到这一步，运行成功后，打开智控台，链接：http://127.0.0.1:8001，注册第一个用户。第一个用户即是超级管理员，以后的用户都是普通用户。然后在里面配置你的LLM密钥。这一步可以去申请阿里或者智谱的密钥，网上很多，我就不描述了。


重要：注册成功后，使用超级管理员账号，登录智控台，在顶部菜单找到模型配置，然后在左侧栏点击大语言模型，找到第一条数据智谱AI，点击修改按钮， 弹出修改框后，将你注册到的智谱AI的密钥填写到API密钥中。然后点击保存。然后窗口别关闭了。
注意：采用豆包语音识别请看，没有可跳过这里。你如果使用豆包然后tts报错的话，你应该是voice_type参数出错了。别问我为啥知道…
如果你开通了豆包的语音识别，然后有这些参数


你可以按照我这个参数去修改豆包的语音合成。


然后要记得在外面这里音色管理修改参数编码，根据你开通填的音色去修改


🔧五、安装Python环境
我按照教程使用 Conda 创建 Python 环境，这一步是为本地化运行的语音交互、设备通信、AI模型推理等关键功能提供隔离、稳定的Python运行时环境。因此需要先去安装Anaconda。

1. 安装清华镜像源的Anaconda
尽量安装Anaconda3-5.3.0后面的版本，python版本3.6。安装我是安装在D盘的


2. 通过conda_prompt运行指令
(1) 使用管理员身份运行


（2）执行命令

conda remove -n xiaozhi-esp32-server --all -y
conda create -n xiaozhi-esp32-server python=3.10 -y
conda activate xiaozhi-esp32-server

# 添加清华源通道
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge

conda install libopus -y
conda install ffmpeg -y
运行本项目
powershell

1
2
3
4
5
6
7
8
9
10
11
安装libopus可能会出现如下报错：

你可以尝试：指定conda-forge安装
libopus主要在conda-forge仓库中，可以指定从conda-forge安装：

conda install -c conda-forge libopus -y
运行本项目
powershell
1
上面的命令都要一步一步执行下来。出现问题，及时问AI。


🔧六、运行源码依赖
1. 安装依赖
# 继续使用conda环境
conda activate xiaozhi-esp32-server
# 进入到你的项目根目录，再进入main/xiaozhi-server
cd main/xiaozhi-server
pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
pip install -r requirements.txt
运行本项目
powershell
1
2
3
4
5
6


🔧七、 下载语音识别模型文件
还是按照教程去下载语音识别模块，然后下载后把model.pt文件放在models/SenseVoiceSmall目录下线路 百度网盘下载SenseVoiceSmall 提取码:qvna

🔧八、 配置项目文件
1. 复制server.secret参数
使用 超级管理员账号 登录系统管理后台（智控台），在顶部菜单找到参数管理（参数编码为server.secret），找到列表中第一条数据，参数编码是server.secret,它是随机生成的，每次重新部署都会随机生成，复制它到参数值。


2. 配置.config.yaml文件
打开整一个源码文件，进入xiaozhi-server目录，检查是否存在data文件夹，如果没有data目录，请手动创建。如果 data 目录下没有 .config.yaml 文件，执行以下操作：
将 xiaozhi-server 目录下的 config_from_api.yaml 复制到 data 目录。重命名复制的文件为 .config.yaml

将复制的server.secret值粘贴到secret字段中。


🔧九、 运行xiaozhi-esp32-server项目
1.运行本地服务端
在刚刚安装依赖的环境下继续执行

# 确保在xiaozhi-server目录下执行
conda activate xiaozhi-esp32-server
python app.py
运行本项目
powershell
1
2
3


如果出现上面的结果，表示你的server端已经部署好了。接下来就是跟小智硬件的结合。注意看返回的ws://xxx.xx.xx.xx:8000/xiaozhi/v1/，其中xxx.xx.xx.xx就是你本地的地址，后面都会用到。

2.硬件接口更新
接下来你可以去在智控台更新硬件接口。
①使用超级管理员账号，登录智控台，在顶部菜单找到参数管理，找到参数编码是server.websocket，输入你的Websocket接口。
②使用超级管理员账号，登录智控台，在顶部菜单找到参数管理，找到数编码是server.ota，输入你的OTA接口。
不知道为啥我没找到，所以我通过手动新增了这两个参数


🔧十、 小智硬件烧录
打开小智的硬件源码程序。

OTA接口更新
http://你电脑局域网的ip:8002/xiaozhi/ota/
运行本项目
cpp
运行
1
Websocket接口更新
ws://你电脑局域网的ip:8000/xiaozhi/v1/
运行本项目
cpp
运行
1
这里我是写在Kconfig.projbuild文件里面，后面配置的时候会根据这个文件去更新。记得保存


你可以点击下方的设置那里，查看右边配置有没有更新到，没有问题可以下载到小智硬件了。


硬件烧录成功后，根据屏幕播报的设备码填写到控制台。点击智能体管理，还没新建智能体的话，就新建一个，然后填写设备管理，绑定播报的6位验证码。




然后点击硬件重启设备。接下来就是见证奇迹的时候。


总结：
通过上述步骤，你已经成功在本地部署了小智服务器。无论是后端服务的管理、前端控制台的操作，还是语音交互与硬件控制的集成，都可以在本地环境中完成。这种部署方式给开发者提供了极大的灵活性，同时也帮助你更深入地理解整个系统的工作流程。希望本文能帮助你少走弯路，顺利搭建起属于自己的智能硬件系统。
————————————————

                            版权声明：本文为博主原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接和本声明。
                        
原文链接：https://blog.csdn.net/lyx4949/article/details/147613994
