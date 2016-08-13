配置Jenkins系统
-----------------

.. attention::
    
    文档内容将与Jenkins 1.642.4保持同步，请确保你所使用的Jenkins版本与本文档的适用范围一致，再参照本文档进行Jenkins的安装和配置，以防出现联系过程中系统不对称导致的问题。
    
    本文档适用于：
    
    * Jenkins v 1.642.4

在Jenkins中配置系统及插件变量

.. figure:: config-jenkins-4system.png

配置Jenkins
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

根据服务器性能配置# of executors数量

.. note::

    不建议在Jenkins的master节点上运行Job，本次培训只是样例。实际生产环境建议Job运行在其他节点上。

.. figure:: jenkins-general-config-executor.png

配置JDK
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

添加JDK配置。
可以根据需要添加多个JDK配置，比如：不同节点如果JDK安装位置不同，就需要在Jenkins中配置不同的JDK设置。

.. figure:: jenkins-config-jdk.png

.. figure:: jenkins-config-jdk-config.png

配置Maven
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
添加Maven配置。
可以根据需要添加多个Maven配置，比如：不同节点如果Maven安装位置不同，就需要在Jenkins中配置不同的Maven设置。

.. figure:: jenkins-config-maven.png

配置SVN
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
选择正确SVN版本

.. figure:: jenkins-config-svn.png