# 虚拟机Ubuntu 16.04 server上spark集群的搭建

### 环境准备

- VMware Workstation 15 Pro
- Ubuntu 16.04 server 
- Xshell5
- Xftp5
- jdk-8u211-linux-x64.tar.gz
- scala-2.11.12.tgz
- hadoop-2.7.7.tar.gz
- spark-2.3.3-bin-hadoop2.7.tgz

### 一、Ubuntu server的安装

1、

![1557989476951](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557989476951.png)

2、

![1557989516558](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557989516558.png)

3、

![1557989524542](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557989524542.png)

4、

![1557989556982](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557989556982.png)

5、

![1557989586759](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557989586759.png)

6、一直下一步

等待安装完成即可。

![1557991883355](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557991883355.png)

ps. 因为VMware和Ubuntu配合不是很好，中间如果有的地方卡了很长时间，建议重新安装（右键移除spark-master，再删除安装目录中所有内容）。

### 二、软件安装

1、登录系统，安装ssh

​	在终端中输入命令：

```shell
sudo apt-get install openssh-server
```

2、使用xshell连接

（1）输入ifconfig查看ip：

![1557992045376](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557992045376.png)

（2）打开xshell新建会话

​	在名称和主机输入ip（便于辨别）：

![1557992156345](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557992156345.png)

​	再点击**用户身份验证**，在用户名和密码中输入对应的账号密码：

![1557992294713](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557992294713.png)

​	点击确定，再点击连接，再点击接受并保存：

![1557992353840](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557992353840.png)

​	出来这个界面表示已经连接上了：

![1557992360578](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557992360578.png)

3、传输文件

（1）点击下面这个图标，打开xftp：

![1557992422138](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557992422138.png)

（2）在右侧中右键新建文件夹software：

![1557992541995](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557992541995.png)

（3）点击进入software，将下载好的四个文件都托入右边，再关闭xftp：

![1557992604320](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557992604320.png)

4、安装jdk、scala和spark

（1）新建文件夹和解压软件

​	回到xshell中，输入以下命令：

```shell
sudo mkdir /usr/local/java
cd software
sudo tar -zxvf jdk-8u211-linux-x64.tar.gz -C /usr/local/java
sudo mkdir /usr/local/scala
sudo tar -zxvf scala-2.11.12.tgz -C /usr/local/scala
sudo mkdir /usr/local/spark
sudo tar -zxvf spark-2.3.3-bin-hadoop2.7.tgz -C /usr/local/spark
```

​	ps.第一个命令输入后需要输入密码。

（2）配置环境变量

​	个人习惯使用vim，在命令行中输入sudo apt-get install vim安装vim，中间输入y。

​	再输入命令打开环境变量文件：

```shell
sudo vim ~/.bashrc
```

​	shift+g快速到文件末尾，按o输入以下命令：

```shell
export JAVA_HOME=/usr/local/java/jdk1.8.0_211
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
export SCALA_HOME=/usr/local/scala/scala-2.11.12
export PATH=${SCALA_HOME}/bin:$PATH
export SPARK_HOME=/usr/local/spark/spark-2.3.3-bin-hadoop2.7
export PATH=${SPARK_HOME}/bin:$PATH
```

​	按Esc，再输入:wq保存退出，出来后在命令行输入命令使环境生效：

```shell
source ~/.bashrc
```

（3）测试

​	在终端中分别输入java -version、scala（退出按Ctrl+d）、spark-shell（退出按Ctrl+d）和pyspark来对jdk、scala和spark进行测试：

![1557993905785](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557993905785.png)

![1557993917200](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557993917200.png)

![1557994015283](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557994015283.png)

​	输入pyspark会报错：

![1557994096354](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557994096354.png)

​	解决方法：打开环境变量（sudo vim ~/.bashrc），在文末添加以下语句：

```shell
export PYTHONPATH=${SPARK_HOME}/python:${SPARK_HOME}/python/lib/py4j-0.10.7-src.zip:
export PYSPARK_PYTHON=python3
```

​	保存并退出，再输入source ~/.bashrc使配置生效。

​	这次在终端中输入pyspark，成功进入：

![1557994427803](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557994427803.png)

​	在主机的浏览器中输入your's ip:4040会出来页面：

​	![1557994622539](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557994622539.png)

​	终端输入exit()退出pyspark。

5、修改本机名称

（1）查看本机ip

​	在终端中输入ifconfig查看ip：

![1557994844722](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557994844722.png)

（2）修改本机名称

​	在终端中输入命令：

```shell
sudo vim /etc/hostname
```

​	将里面的ubuntu修改成master

（3）修改hosts

​	在终端输入命令：

```shell
sudo vim /etc/hosts
```

​	修改为下面这样：

![1557995086401](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557995086401.png)

​	保存退出后关闭虚拟机。

6、克隆虚拟机

（1）右键spark-master→管理→克隆

![1557995261026](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557995261026.png)

![1557995271563](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557995271563.png)

![1557995277210](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557995277210.png)

![1557995300859](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557995300859.png)

​	等待完成，第二个也这样克隆。

![1557995369276](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557995369276.png)

（2）开启三个虚拟机

​	依次启动并登录三个虚拟机，一定要按顺序（master→slave1→slave2）启动，不然ip顺序会乱。分别在三台虚拟机的终端中输入ifconfig，如果ip和hosts的一致就没问题，否则要修改成对应的。

（3）修改hostname

​	分别在spark-slave1和spark-slave2的终端输入sudo vim /etc/hostname，将里面的内容分别改为slave1和slave2，退出并保存，再输入命令sudo reboot重启两台虚拟机，名字就会变成对应的名字。

![1557995867874](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557995867874.png)

7、SHH免密登录

首先用xshell连接剩下两台虚拟机（个人觉得xshell更方便一点）。

分别在三台终端中输入以下命令，一直按回车就行：

```shell
ssh-keygen -t rsa
```

![1557996139203](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557996139203.png)

然后分别将slave1与slave2上的id_rsa.pub用scp命令发送给master:

```shell
scp ~/.ssh/id_rsa.pub spark@master:~/.ssh/id_rsa.pub.slave1
scp ~/.ssh/id_rsa.pub spark@master:~/.ssh/id_rsa.pub.slave2
```

![1557996269453](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557996269453.png)

在master上，将所有公钥加到用于认证的公钥文件authorized_keys中：

```shell
cat ~/.ssh/id_rsa.pub* >> ~/.ssh/authorized_keys
```

在master上，将公钥文件authorized_keys分发给每台slave：

```shell
scp ~/.ssh/authorized_keys spark@slave1:~/.ssh/
scp ~/.ssh/authorized_keys spark@slave2:~/.ssh/
```

![1557996419678](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557996419678.png)

现在可以在三台机器上使用以下命令测试是否可以免密登陆了：

```shell
ssh master
ssh slave1
ssh slave2
```

8、安装hadoop

（1）在master中，输入以下命令创建文件夹和解压文件：

```shell
sudo mkdir /usr/local/hadoop
cd software/
sudo tar -zxvf hadoop-2.7.7.tar.gz -C /usr/local/hadoop
```

（2）配置文件

​	首先在三台机器的/home/spark路径下创建几个文件夹：

![1557997876588](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557997876588.png)

​	进入“/usr/local/hadoop/hadoop-2.7.7/etc/hadoop”目录：

```shell
cd /usr/local/hadoop/hadoop-2.7.7/etc/hadoop/
```

​	对以下文件进行配置：hadoop-env.sh、yarn-env.sh、core-site.xml

```shell
//hadoop-env.sh
sudo vim hadoop-env.sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_211
export PATH=$PATH:/usr/local/hadoop/hadoop-2.7.7/bin
```

​	保存并退出，输入命令source hadoop-env.sh使配置生效，并输入hadoop version测试：

![1557997199620](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557997199620.png)

```shell
//yarn-env.sh
sudo vim yarn-env.sh
export JAVA_HOME=/usr/local/java/jdk1.8.0_211
```

​	同样保存并退出，输入命令source yarn-env.sh使之生效。

```shell
//slaves（把文件里的localhost删掉）
sudo vim slaves
slave1
slave2
```



```shell
//core-site.xml（file后面的路径与创建的文件夹路径一致）
sudo vim core-site.xml
<configuration>
        <property>
                <name>fs.defaultFS</name>
                <value>hdfs://master:9000/</value>
        </property>
        <property>
                 <name>hadoop.tmp.dir</name>
                <value>file:/home/spark/hadoop/tmp</value>
        </property>
</configuration>
```

```shell
//hdfs-site.xml（file后面的路径与创建的文件夹路径一致）
sudo vim hdfs-site.xml
<configuration>
        <property>
                <name>dfs.namenode.secondary.http-address</name>
                <value>master:9001</value>
        </property>
         <property>
                <name>dfs.namenode.name.dir</name>
                <value>file:/home/spark/hadoop/namenode</value>
        </property>
        <property>
                <name>dfs.datanode.data.dir</name>
                <value>file:/home/spark/hadoop/datanode</value>
        </property>
        <property>
                <name>dfs.replication</name>
                <value>3</value>
        </property>
        <property>
                <name>dfs.webhdfs.enabled</name>
                <value>true</value>
        </property>
</configuration>

```

```shell
//mapred-site.xml
sudo cp mapred-site.xml.template mapred-site.xml
sudo vim mapred-site.xml
<configuration>
        <property>
                <name>mapreduce.framework.name</name>
                <value>yarn</value>
        </property>
</configuration>
```

```shell
//yarn-site.xml
sudo vim yarn-site.xml
<configuration>
<!-- Site specific YARN configuration properties -->
        <property>
                <name>yarn.nodemanager.aux-services</name>
                <value>mapreduce_shuffle</value>
        </property>
         <property>
                <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
                <value>org.apache.hadoop.mapred.ShuffleHandler</value>
        </property>
        <property>
                <name>yarn.resourcemanager.address</name>
                <value>master:8032</value>
        </property>
        <property>
                <name>yarn.resourcemanager.scheduler.address</name>
                <value>master:8030</value>
        </property>
        <property>
                <name>yarn.resourcemanager.resource-tracker.address</name>
                <value>master:8035</value>
        </property>
        <property>
                <name>yarn.resourcemanager.admin.address</name>
                <value>master:8033</value>
        </property>
        <property>
                <name>yarn.resourcemanager.webapp.address</name>
                <value>master:8088</value>
        </property>
</configuration>
```

（3）发送配置

​	在两台slave中创建和master一致的hadoop文件夹，并在三台机器中均使用以下命令进行文件夹的权限设置：

```shell
sudo chmod -R 777 /usr/local/hadoop
```

​	此时可以将配置好的hadoop-2.7.7文件夹分发给所有slave了：

```shell
scp -r /usr/local/hadoop/hadoop-2.7.7 spark@slave1:/usr/local/hadoop/
scp -r /usr/local/hadoop/hadoop-2.7.7 spark@slave2:/usr/local/hadoop/
```

​	分发完成后，再次在两台slave中对hadoop文件夹进行权限设置：

```shell
sudo chmod -R 777 /usr/local/hadoop
```

​	在master中使用cd命令切换到hadoop主目录，格式化namenode后，启动：

```shell
cd /usr/local/hadoop/hadoop-2.7.7/
./bin/hadoop namenode -format
./sbin/start-all.sh
```

​	输入./sbin/start-all.sh会报错，解决方法：

​	将hadoop-env.sh中的export HADOOP_CONF_DIR=${HADOOP_CONF_DIR:-"/etc/hadoop"}

改为绝对路径：

```shell
export HADOOP_CONF_DIR=/usr/local/hadoop/hadoop-2.7.7/etc/hadoop
```

​	使用source hadoop-env.sh使之生效。

​	在三台机器的终端中分别输入jps命令（下图是master和slave1，slave2和slave1是一样的），看到如下图即代表启动成功，可以使用“./sbin/stop-all.sh”进行关闭：

![1557999842806](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557999842806.png)

![1557999863053](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1557999863053.png)

9、二次配置spark

​	先使用如下命令将两台slave中的spark文件删除（一会再从master传过来）：

```shell
sudo rm -rf /usr/local/spark/spark-2.3.3-bin-hadoop2.7/
```

​	然后，在master上cd到spark的根目录下的conf目录中，修改以下文件（没有该文件则新建该文件）：

```shell
cd /usr/local/spark/spark-2.3.3-bin-hadoop2.7/conf/
sudo cp spark-env.sh.template spark-env.sh
sudo vim spark-env.sh
//spark-env.sh
export SCALA_HOME=/usr/local/scala/scala-2.11.12
export JAVA_HOME=/usr/local/java/jdk1.8.0_211
export HADOOP_HOME=/usr/local/hadoop/hadoop-2.7.7
export HADOOP_CONF_DIR=/usr/local/hadoop/hadoop-2.7.7/etc/hadoop
export SPARK_MASTER_IP=master
```

```shell
sudo cp slaves.template slaves
sudo vim slaves
//slaves（把文件中自带的localhost删除）
master
slave1
slave2
```

​	同样的，在三台机器上使用chmod命令给文件夹赋予权限：

```shell
sudo chmod -R 777 /usr/local/spark
```

​	此时可以将配置好的spark-2.3.3-bin-hadoop2.7文件夹分发给所有slave了：

```shell
scp -r /usr/local/spark/spark-2.3.3-bin-hadoop2.7 spark@slave1:/usr/local/spark/
scp -r /usr/local/spark/spark-2.3.3-bin-hadoop2.7 spark@slave2:/usr/local/spark/
```

​	在两台slave中再次对文件夹赋予权限：

```shell
sudo chmod -R 777 /usr/local/spark
```

​	此时，配置已完成，在master上使用cd命令切换到spark主目录，进行启动：

```shell
cd /usr/local/spark/spark-2.3.3-bin-hadoop2.7/
./sbin/start-all.sh
```

​	在三台机器的终端中分别输入jps命令（图示是master和slave1），看到如下图即代表启动成功，可以使用“./sbin/stop-all.sh”进行关闭:

​	![1558060858886](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1558060858886.png)

​	![1558060891757](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1558060891757.png)

10、集群测试

在spark启动成功后，在master的终端中cd到spark根目录，执行以下代码（spark自带的一个例子），出现结果即为成功：

```shell
./bin/run-example SparkPi 10
```

![1558061471134](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1558061471134.png)

PS.如果嫌界面输出的信息太多可以到conf/log4j.properties中修改配置：

```shell
cd conf/
sudo cp log4j.properties.template log4j.properties
sudo vim log4j.properties
```

找到log4j.rootCategory=INFO, console，将INFO改为WARN后保存并退出，重新输入代码出来结果如下：

![1558061769049](C:\Users\jerry\AppData\Roaming\Typora\typora-user-images\1558061769049.png)

### 参考

1、https://blog.csdn.net/ms961516792/article/details/79115383#commentBox

2、https://blog.csdn.net/qq_42881421/article/details/88069211

3、https://blog.csdn.net/w3045872817/article/details/70940952

4、http://www.cnblogs.com/shishanyuan/p/4701646.html

5、https://blog.csdn.net/a602232180/article/details/74779641