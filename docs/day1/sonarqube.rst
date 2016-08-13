SonarQube 静态代码检查
----------------------------------

.. attention::
    
    文档内容将与Jenkins 1.642.4保持同步，请确保你所使用的Jenkins版本与本文档的适用范围一致，再参照本文档进行Jenkins的安装和配置，以防出现联系过程中系统不对称导致的问题。
    
    本文档适用于：
    
    * Jenkins v 1.642.4

通过本次联系，使学员掌握如何使用SonarQube插件在Jenkins持续集成过程中进行静态代码扫描

配置SonarQube服务器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
在SonarQube中创建项目，项目名称同SVN中的Repository名称
并在Sonar的Administration -> General Settings -> Java -> Cobertura中配置报告相对路径

.. code-block:: xml

    target/site/cobertura/coverage.xml

.. note::

    如果Sonar没有安装Cobertura插件，请在讲师处获取

在Jenkins系统配置中添加SonarQube配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
添加SonarQube服务器

.. figure:: jenkins-config-sonar.png

按照Job运行的节点操作系统类型，添加SonarQube Scanner。名称为：SonarScanner + 节点名

.. figure:: sonar-scanner-node.png

修改Job配置， 添加Sonar插件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
按照Job运行的节点操作系统类型选择SonarQube Scanner

.. figure:: job-config-sonar-scanner.png

修改**Analysis properties**属性，输入如下配置脚本：

.. code-block:: text

    # Root project information
    sonar.projectKey=TP
    sonar.projectName=TrainingProject
    sonar.projectVersion=1.${SVN_REVISION}
    
    # Some properties that will be inherited by the modules
    sonar.sources=src
    
    # List of the module identifiers
    #sonar.modules=single-module,multi-module
    sonar.modules=single-module
    
    # Properties can obviously be overriden for
    # each module - just prefix them with the module ID
    module1.sonar.projectName=Single Module
    #module2.sonar.projectName=MultiModule

    sonar.core.codeCoveragePlugin=cobertura

运行查看扫描结果
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. figure:: job-result-sonar.png

.. figure:: sonar-result.png

在Job的SonarQube配置中使用全局变量设置版本
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
可以在SonarQube Scanner运行配置中使用SVN提交版本来设置SonarQube扫描结果版本

.. figure:: job-sonar-version.png

.. code-block:: text

    sonar.projectVersion=1.${SVN_REVISION}

在文件/maven-samples-master/single-module/src/test/java/com/example/TestGreeter.java中添加单元测试，脚本如下：

.. code-block:: text

    package com.example;

    import org.junit.Before;
    import org.junit.Test;

    import static org.hamcrest.CoreMatchers.is;
    import static org.hamcrest.Matchers.greaterThan;
    import static org.junit.Assert.assertThat;
    import static org.junit.matchers.JUnitMatchers.containsString;

    public class TestGreeter {

    private Greeter greeter;

    @Before
    public void setup() {
        greeter = new Greeter();
    }

    @Test
    public void greetShouldIncludeTheOneBeingGreeted() {
        String someone = "World";

        assertThat(greeter.greet(someone), containsString(someone));
    }

    @Test
    public void greetShouldIncludeGreetingPhrase() {
        String someone = "World";

        assertThat(greeter.greet(someone).length(), is(greaterThan(someone.length())));
    }
    
    @Test
    public void greetCalculateNumber() { 
        assertThat(greeter.calculate("a"), is(1));
        assertThat(greeter.calculate("b"), is(2));
        assertThat(greeter.calculate("c"), is(0));
    } 
    
    **@Test**
    **public void greetShouldIncludeTheOneBeingGreeted() {**
      **  String someone = "World";**

        **assertThat(greeter.NotCoveredFunction(someone), containsString(someone));**
    **}**
    }

提交修改，并在SonarQube中查看结果

