与JIRA集成
----------------------------------

.. attention::
    
    文档内容将与Jenkins 1.642.4保持同步，请确保你所使用的Jenkins版本与本文档的适用范围一致，再参照本文档进行Jenkins的安装和配置，以防出现联系过程中系统不对称导致的问题。
    
    本文档适用于：
    
    * Jenkins v 1.642.4
    
在Jenkins配置中添加配置JIRA服务器配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. figure:: images/images/jenkins-config-jira.png

.. note::

    使用客户内部JIRA系统及用户账号替代上图配置

提交修改关联JIRA Issue
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在任意文件中添加注释，提交修改，并在注释中填写 JIRAProjectKey-IssueId 加文本注释。
签入后在Job页面点击Chan 

.. figure:: job-result-changes-jira-like.png

点击超链接 JEN-1可以打开JIRA Issue页面