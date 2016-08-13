使用其他节点进行运行Jenkins Job
-------------------------------------

.. attention::
    
    文档内容将与Jenkins 1.642.4保持同步，请确保你所使用的Jenkins版本与本文档的适用范围一致，再参照本文档进行Jenkins的安装和配置，以防出现联系过程中系统不对称导致的问题。
    
    本文档适用于：
    
    * Jenkins v 1.642.4

在本联系中，你将学会如何在Jenkins中添加节点，并在配置节点上运行Job。

    
配置新的Jenkins节点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
创建节点

.. figure:: jenkins-config-create-node.png

.. figure:: create-node.png

.. figure:: node-name.png

在不同平台选择不同Launch Method，详细如下：

* 配置Windows服务器节点， Labels 设置为second
使用Launch slave agents via java Web Start

.. figure:: node-lable-windows.png

* 配置Linux服务器节点， Labels设置为m2。
使用Launch slave agents on Unix machines via SSH方式连接到Jenkins，可以使用SSH方式，**如果使用username和password方式必须使用root账户**

.. figure:: note-config-linux.png

如果是在Windows服务器运行Jenkins Slaver，请使用在浏览器中打开配置节点页面，然后运行slaver

.. figure:: note-run-slaver-windows.png

添加Jenkins配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
打开Jenkins配置页面**Jenkins -> Manage Jenkins -> Configure System** 添加JDK与Maven配置。
将JAVA_HOME与MAVEN_HOME路径设置为节点服务器上的$JAVA_HOME与$MAVEN_HOME，配置名为：JDK\Maven + 节点名

.. figure:: jenkins-config-note-jdk.png

.. figure:: jenkins-config-note-maven.png

保存修改

在second节点上运行Job
~~~~~~~~~~~~~~~~~~~~~~~~~
修改自己创建的Job

.. figure:: config-job-note.png

通过Label指定构建要在哪个节点上运行。

.. figure:: config-job-label.png

选择刚刚在**Jenkins -> Manage Jenkins -> Configure System**配置的试用于second节点的JDK与Maven配置

.. figure:: job-node-jdk.png

.. figure:: job-node-maven.png

保存修改

运行Job，并查看结果
~~~~~~~~~~~~~~~~~~~~~~~~~~~~
在Jenkins首页触发Job运行， 选择**Build Now**

.. figure:: trigger-build.png

