运行单元测试单元测试，并收集结果
----------------------------------

.. attention::
    
    文档内容将与Jenkins 1.642.4保持同步，请确保你所使用的Jenkins版本与本文档的适用范围一致，再参照本文档进行Jenkins的安装和配置，以防出现联系过程中系统不对称导致的问题。
    
    本文档适用于：
    
    * Jenkins v 1.642.4
    
编辑Jenkins Job
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
添加测试报告发布插件

.. figure:: unit-test-result-publisher.png

使用文件通配符查找需要发布的单元测试结果，单元测试结果为xml文件

.. figure:: unit-test-result-prefix.png

保存修改

运行Job，查看结果
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. figure:: unit-test-job-result.png

安装**Test Results Analyzer Plugin**后，可以在Job页面使用单元测试结果分析插件查看图形化展示单元测试结果

.. figure:: test-result-analyzer.png

添加单元测试，查看测试结果
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
修改文件/maven-samples-master/single-module/src/main/java/com/example/Greeter.java
修改后文本

.. code-block:: java

package com.example;

/**
 * This is a class.
 */
public class Greeter {

  /**
   * This is a constructor.
   */
  public Greeter() {

  }

  //TODO: Add javadoc comment
  public String greet(String someone) {
    return String.format("Hello, %s!", someone);
  }
  
  public int calculate(String param)
  {
	  if(param == "a")
		  return 1;
	  else if(param =="b")
		  return 2;
	  else
		  return 0;
  }
}

修改文件/maven-samples-master/single-module/src/test/java/com/example/TestGreeter.java
修改后文本

.. code-block:: java

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
        assertThat(greeter.calculate("c"), is(3));
    } 
    }

签入修改文件，编写注释。
查看单元测试分析

.. figure:: new-failed-unite-test-result.png

在新加的单元测试方法中修改Bug
**Change**:

.. code-block:: java

    assertThat(greeter.calculate("c"), is(3));

**To**:

.. code-block:: java

    assertThat(greeter.calculate("c"), is(0));

保存修改，签入代码，查看生成结果

