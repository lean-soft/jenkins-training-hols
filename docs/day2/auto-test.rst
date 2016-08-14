Jenkins 运行自动化测试
----------------------------------

.. attention::
    
    文档内容将与Jenkins 1.642.4保持同步，请确保你所使用的Jenkins版本与本文档的适用范围一致，再参照本文档进行Jenkins的安装和配置，以防出现联系过程中系统不对称导致的问题。
    
    本文档适用于：
    
    * Jenkins v 1.642.4

在本文中，学员们将学会如何使用Jenkins，协助开发人员完成一个开发闭环

.. figure:: images/jenkins-selenium.jenkins-dev-cycle.png

检查SecondNode是否正常运行
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

如果SecondNode没有正常运行，请按照 ::doc:`../day1/run-other-node` 重新配置


配置Selenium Grid
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. figure:: images/jenkins-selenium.png

.. figure:: images/jenkins-selenium-config.png

新建配置，并在配置Match nodes from a label expression中使用SecondNode节点的Label设置

.. figure:: images/jenkins-selenium-config-label.png

添加浏览器，IE与Chrome浏览器需要制定driver，（driver可以在讲师处获取）

.. figure:: images/jenkins-selenium-driver.png

保存配置，在节点上运行配置

.. figure:: images/jenkins-selenium-config-node.png

.. figure:: images/jenkins-selenium-node-config.png

如果配置未自动运行，在下图标记出点击开始按钮

.. figure:: images/jinekins-selenium-service-start.png

上传自动化测试项目
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
在SVN中自己团队的Repository下再次创建文件夹，文件夹名称为：用户名+AutoTest
(上传的源码可在讲师处获取)
上传后目录结构应类似于：

.. figure:: images/jenkins-selenium-sample-repo.png

打开文件/WilsonBoAutoTest/src/test/java/com/sample/AutoTest.java
使用Selenium Grid界面的代码

.. figure:: images/jenkins-selenium-main.png

替换如下代码：

.. code-block:: java

    driver = new RemoteWebDriver(new URL("http://192.168.0.36:4444/wd/hub"), capability);

在Eclipse中运行单元测试，可以在SecondNode节点服务器的远程桌面中观察到测试的自动化运行。

创建CD Job，运行自动化测试
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
创建与CI ob相同类型的Job，部署CI的生成包并运行自动化测试，项目名称为：用户名 + CD
配置SCM，路径为SVN服务器中团队Repository的 **用户名 + AutoTest** 文件夹

.. figure:: images/jenkins-selenium-svn.png

不设置触发方式， 添加Maven生成任务，和发布Junit报告任务

.. figure:: images/jenkins-selenium-job-config.png

手动触发Job， SecondNode服务器上会执行有自动化测试


修改第CI Job
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
现在要模拟CI+CD场景
配置第CI Job，添加结果发布任务

.. figure:: images/jenkins-selenium-ci-job-config.png

添加触发其他项目构建任务

.. figure:: images/jenkins-selenium-ci-job-trigger-cd.png

添加触发器，并设置要传递的参数

.. figure:: images/jenkins-selenium-ci-job-pass-param.png

在Parameters文本框中输入

.. code-block:: java

    PRE_BUILD_NUMBER=${BUILD_NUMBER}

参数PRE_BUILD_NUMBER可以将传递到触发的下游的CD Job中


创建CreateIssue Job
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
创建CreateIssue项目，运行shell脚本，如果CD Job运行失败则创建JIRA Issue。如果生成成功，并且在SVN提交的注释中有JIRA Issue Key的话，更新现有Jira Issue状态为Done。Job名称为： 用户名 + CreateIssue

.. figure:: images/jenkins-job-create-issue.png

shell脚本如下(替换**的文本)：

.. code-block:: shell

    PROJECTKEY="**JEN**"
    JENKINSSERVER="**http://192.168.0.36:8080**"
    JIRASERVER="**http://192.168.0.32:8080**"
    CIJOBNAME="**TrainingMavenProjectCI**"
    CDJOBNAME="**DeliveryAndAutoTest**"
    JR=$(curl $JENKINSSERVER/job/$CDJOBNAME/$FAILED_BUILD_NUMBER/api/xml?depth=1)
    echo $JR > rad.txt
    JOBRESULT=$(grep -oP '(?<=<result>)[^<]+' "rad.txt")
    if [ $(grep -oP '(?<=<msg>)[^<]+' "/root/.jenkins/jobs/$CIJOBNAME/builds/${FIRST_BUILD_NUMBER}/changelog.xml") != "" ]; then
    JIRAID=$(grep -oP '(?<=<msg>)[^<]+' "/root/.jenkins/jobs/$CIJOBNAME/builds/${FIRST_BUILD_NUMBER}/changelog.xml")
    curl -D- -u wilsonbo:P2ssw0rd -X POST --data "{\"update\": {\"comment\": [ {\"add\": {\"body\": \"Comment added when resolving issue\"}}]},\"transition\": {\"id\": \"31\"}}" -H "Content-Type: application/json" $JIRASERVER/rest/api/2/issue/$JIRAID/transitions?expand=transitions.fields
    elif [ $JOBRESULT != "SUCCESS" ]; then
    curl -D- -u wilsonbo:P2ssw0rd -X POST --data "{\"fields\":   {       \"project\":       {           \"key\": \"$PROJECTKEY\"       },       \"summary\": \"Auto Test Failed, jenkins job number: ${FAILED_BUILD_NUMBER}\",       \"description\": \"please see the details at: $JENKINSSERVER/job/$CDJOBNAME/${FAILED_BUILD_NUMBER}/consoleText\",       \"issuetype\": {          \"name\": \"Bug\"       }   }}" -H "Content-Type: application/json" $JIRASERVER/rest/api/2/issue/
    fi

修改CD Job
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
使用第一个项目传递的构建编号，下载war文件，并部署到staging环境

.. figure:: images/jenkins-selenium-cd-job-config.png

脚本为(替换**的文本)

.. code-block:: shell

    CIJOBNAME="**TrainingMavenProjectCI**"
    myFile="webapp.war" 
    if [ -f "$myFile" ]; then 
    rm "$myFile" 
    fi 
    wget --auth-no-challenge --http-user=wilsonbo --http-password=P2ssw0rd  http://192.168.0.36:8080/job/$CIJOBNAME/${PRE_BUILD_NUMBER}/artifact/multi-module/webapp/target/webapp.war
    cp webapp.war /opt/tomcat/webapps/staging.war

添加post-build action, **Projects to build**设置为刚才创建JIRA Issue的Job，触发条件为 **Complated(always trigger)**，并添加两个Predefined parameters参数

.. code-block:: text

    FAILED_BUILD_NUMBER=${BUILD_NUMBER}
    FIRST_BUILD_NUMBER=${PRE_BUILD_NUMBER}



修改sample项目
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
修改页面/ maven-samples-master/multi-module/webapp/src/main/webapp/index.jsp计算逻辑，签入变更，查看测试结果

.. code-block:: html

    <html>
        <head>
        </head>
        <body>
            <table>
                <tr><td><input type="text" id="val1"/></td></tr>
                <tr><td><input type="text" id="val2"/></td></tr>
                <tr><td><input type="button" id="btn" onclick="calculate()" value="Calculate"/></td></tr>
                <tr><td><input type="text"  id="res"/></td></tr> 
            </table>
            
        </body>

    </html>
    <script>  
    function calculate()
    {
        var val1 = document.getElementById("val1").value;
        var val2 = document.getElementById("val2").value;
        document.getElementById("res").value = parseInt(val1) - parseInt(val2);
    }  
    </script>

此时会先触发CI Job，当CI Job执行完成后会触发CD Job，运行结果为：

.. figure:: images/jenkins-selenium-auto-test-result.png

.. figure:: images/jenkins-selenium-auto-test-result-cd.png

检查JIRA系统，新的Bug已经被创建，点击描述中的链接，可以看到生成失败的详细信息。


修改测试用例
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
修改测试用例文件/WilsonBoAutoTest/src/test/java/com/sample/AutoTest.java，文本为

.. code-block:: java

    <html>
        <head>
        </head>
        <body>
            <table>
                <tr><td><input type="text" id="val1"/></td></tr>
                <tr><td><input type="text" id="val2"/></td></tr>
                <tr><td><input type="button" id="btn" onclick="calculate()" value="Calculate"/></td></tr>
                <tr><td><input type="text"  id="res"/></td></tr> 
            </table>
            
        </body>

    </html>
    <script>  
    function calculate()
    {
        var val1 = document.getElementById("val1").value;
        var val2 = document.getElementById("val2").value;
        document.getElementById("res").value = parseInt(val1) + parseInt(val2);
    }  
    </script>

签入源代码，并且在注释中填写Job运行失败时创建的JIRA Issue Key。

.. note::

    只填写JIRA Issue Key，不要填写其他信息

运行后，所有Job运行成功，并且Jira Issue状态为Done。


创建Delivery Pipeline视图
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
与创建仪表板视图一样，创建发布管道视图，视图名称： 用户名+CD

.. figure:: images/jenkins-delivery-veiw.png

在视图配置页面最下方，添加管道，并在initial job中设置第一个创建的Job，**final job**设置为Create Issue Job
保存修改查看视图

.. figure:: images/jenkins-delivery-veiw-config.png

.. figure:: images/jenkins-delivery-veiw-result.png

