Jenkins 安装和配置
--------------------

.. attention::
    
    在这个实验中你将学会怎样从无到有的安装配置Jenkins，并启动Jenkins服务。
    安装环境Ubuntu，版本16.04.1 LTS
    
    
安装操作系统， 双网卡配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

关闭防火墙： sudo ufw disable
安装sshserver： sudo apt-get install openssh-server
打开root账户权限: 
sudo vi /etc/ssh/sshd_config
将属性 PermitRootLogin 的值设置为yes
sudo passwd root 设置root密码
 

安装JDK、Tomcat、Maven
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用WinSCP将安装包上传到/home/wilsonbo
解压缩安装包并拷贝到/opt目录下，记得重命名：
sudo tar zxvf  jdk-8u101-linux-x64.tar.gz
Sudo cp -r jdk1.8.0_101/ /opt/jdk

sudo tar zxvf  apache-tomcat-9.0.0.M9.tar.gz
sudo cp -r apache-tomcat-9.0.0.M9/ /opt/tomcat

sudo tar zxvf  apache-maven-3.3.9-bin.tar.gz
sudo cp -r apache-maven-3.3.9 /opt/maven

设置环境变量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在/etc/environment文件中设置如下
.. code-block:: shell
    JAVA_HOME=/opt/jdk
    TOMCAT_HOME=/opt/tomcat
    MAVEN_HOME=/opt/maven
    PATH="$JAVA_HOME/bin:$TOMCAT_HOME/bin:$MAVEN_HOME/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
 
打开环境变量文件 sudo vi ~/.bashrc，并在文件末尾加上如下代码
.. code-block:: shell
    export JAVA_HOME=/opt/jdk
    export JRE_HOME=${JAVA_HOME}/jre
    export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
    export PATH=${JAVA_HOME}/bin:$PATH
    export TOMCAT_HOME=/opt/tomcat
    export PATH=${TOMCAT_HOME}/bin:$PATH
    export MAVEN_HOME=/opt/maven
    export PATH=${MAVEN_HOME}/bin:$PATH

将jenkins.war文件拷贝到tomcat的webapps文件夹下，并启动tomcat
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    
清楚webapps文件夹下其他文件 sudo rm -r 文件夹名
$TOMCAT_HOME/bin文件夹下运行： startup.sh

..note::
    如果Permission Denied 使用命令 sudo chown -R 用户名 文件夹名获得权限

