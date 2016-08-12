.. _rst_vsalm-hoc

Jenkins动手实验
=================

.. |stub-icon| unicode:: U+1F527

.. attention::
    
    本文档使用reST格式编写，内容托管于GitHub。文档内容正在持续编写中，所有标注为 |stub-icon| 的部分表示还未编写完毕，如果您在使用中遇到问题，可以通过我们的 coding.net讨论组_ 来提交问题。
    
    
    本文档提供2个主要版本:
    
    - stable: 稳定版本，按照VSALM企业版进行标识，如：v2016.1 表示所对应的是 2016 Update 1版本
    - latest: 最新版本，持续更新。
    
    版本记录：
    
    - v2016: 当前最新稳定版本，适用于Jenkins 1.6.42.1
    - gitlab support


概述
-----

Jenkins是基于Java开发的一种持续集成及持续部署引擎，用于监控持续重复的工作，功能包括：

- 持续集成
- 持续部署


具体信息可以通过 `Jenkins <https://wiki.jenkins-ci.org/>`_ 产品主页进行了解，或者参考本文档中 关于Jenkins 部分。

本动手实验希望通过模拟一个产品从开发到部署的全流程，部署过程中包含程序生成、单元测试、静态代码扫描及自动化测试功能。

:更新日期: |today|
:作者: **薄涛** 
:主页: `DevOps Hub <http://devopshub.cn>`_


内容
-----

.. toctree::
    :titlesonly:
    
    preparation/index
    day1/index
    day2/index 
    

常见问题
---------
.. toctree::
    :titlesonly:
    
    troubleshooting/index
    
.. _coding.net讨论组: https://coding.net/u/ups216/p/vsalm-hols/topic


