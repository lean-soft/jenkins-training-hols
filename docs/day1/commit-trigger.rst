配置SCM提交主动触发Jenkins构建
----------------------------------

.. attention::
    
    文档内容将与Jenkins 1.642.4保持同步，请确保你所使用的Jenkins版本与本文档的适用范围一致，再参照本文档进行Jenkins的安装和配置，以防出现联系过程中系统不对称导致的问题。
    
    本文档适用于：
    
    * Jenkins v 1.642.4
    
配置SVN服务器挂钩 - Windows服务器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

打开SVN存放Repository的根目录，并添加vbs脚本，脚本名称 post-commit-hook-jenkins.vbs
 文件位置如下：

.. figure:: svn-post-commit-vbs.png

.. code-block:: vbs

    repos   = WScript.Arguments.Item(0)
    rev     = WScript.Arguments.Item(1)
    svnlook = WScript.Arguments.Item(2)
    jenkins = WScript.Arguments.Item(3)

    Set shell = WScript.CreateObject("WScript.Shell")

    Set uuidExec = shell.Exec(svnlook & " uuid " & repos)
    Do Until uuidExec.StdOut.AtEndOfStream
    uuid = uuidExec.StdOut.ReadLine()
    Loop
    Wscript.Echo "uuid=" & uuid

    Set changedExec = shell.Exec(svnlook & " changed --revision " & rev & " " & repos)
    Do Until changedExec.StdOut.AtEndOfStream
    changed = changed + changedExec.StdOut.ReadLine() + Chr(10)
    Loop
    Wscript.Echo "changed=" & changed

    url = jenkins + "crumbIssuer/api/xml?xpath=concat(//crumbRequestField,"":"",//crumb)"
    Wscript.Echo url
    Set http = CreateObject("Microsoft.XMLHTTP")
    http.open "GET", url, False
    http.setRequestHeader "Content-Type", "text/plain;charset=UTF-8"
    http.send
    crumb = null
    if http.status = 200 then
    crumb = split(http.responseText,":")
    end if
    Wscript.Echo http.responseText
    url = jenkins + "subversion/" + uuid + "/notifyCommit?rev=" + rev
    Wscript.Echo url

    Set http = CreateObject("Microsoft.XMLHTTP")
    http.open "POST", url, False
    http.setRequestHeader "Content-Type", "text/plain;charset=UTF-8"
    if not isnull(crumb) then 
    http.setRequestHeader crumb(0),crumb(1)
    http.send changed
    Wscript.Echo " HTTP Status: " & http.status & ". Body: " & http.responseText
    end if

打开SVN中自己团队创建的Repository目录，在Repository根目录下的hooks文件夹内创建文件post-commit.bat
文件内容如下（替换下方字体加粗文本）：

.. code-block:: bat

    SET REPOS=%1
    SET REV=%2
    SET CSCRIPT=%windir%\system32\cscript.exe
    SET VBSCRIPT=**post-commit-hook-jenkins.vbs脚本路径**
    SET SVNLOOK=**svnlook.exe路径 (默认为C:\Program Files\VisualSVN Server\bin\svnlook.exe)**
    SET JENKINS=**Jenkins服务器URL地址**
    "%CSCRIPT%" "%VBSCRIPT%" "%REPOS%" %2 "%SVNLOOK%" %JENKINS%

配置SVN服务器挂钩 - Linux服务器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
将下面的文本添加到SVN服务器$REPOSITORY/hooks文件夹下的post-commit文件中（替换粗体文本）：

.. code-block:: shell

    #!/bin/sh
    REPOS="$1"
    REV="$2"

    # No environment is passed to svn hook scripts; set paths to external tools explicitly:
    WGET=**/usr/bin/wget**
    SVNLOOK=**/usr/bin/svnlook**

    # If your server requires authentication, it is recommended that you set up a .netrc file to store your username and password
    # Better yet, since Jenkins v. 1.426, use the generated API Token in place of the password
    # See https://wiki.jenkins-ci.org/display/JENKINS/Authenticating+scripted+clients
    # Since no environment is passed to hook scripts, you need to set $HOME (where your .netrc lives)
    # By convention, this should be the home dir of whichever user is running the svn process (i.e. apache)
    HOME=/var/www/

    UUID=`$SVNLOOK uuid $REPOS`
    NOTIFY_URL="subversion/${UUID}/notifyCommit?rev=${REV}"
    CRUMB_ISSUER_URL='crumbIssuer/api/xml?xpath=concat(//crumbRequestField,":",//crumb)'

    function notifyCI {
        # URL to Hudson/Jenkins server application (with protocol, hostname, port and deployment descriptor if needed)
        CISERVER=$1

        # Check if "[X] Prevent Cross Site Request Forgery exploits" is activated
        # so we can present a valid crumb or a proper header
        HEADER="Content-Type:text/plain;charset=UTF-8"
        CRUMB=`$WGET --auth-no-challenge --output-document - ${CISERVER}/${CRUMB_ISSUER_URL}`
        if [ "$CRUMB" != "" ]; then HEADER=$CRUMB; fi

        $WGET \
            --auth-no-challenge \
            --header $HEADER \
            --post-data "`$SVNLOOK changed --revision $REV $REPOS`" \
            --output-document "-"\
            --timeout=2 \
            ${CISERVER}/${NOTIFY_URL}
    }

    # The code above was placed in a function so you can easily notify multiple Jenkins/Hudson servers: 
    notifyCI "**Jenkins服务器URL地址**"

配置Jenkins Global Security
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
打开Jenkins Gloable Security中的Prevent Cross Site Request Forgery exploits设置， 并使用Default Crumb Issuer

.. figure:: scm-commit-gloable-security.png

运行Job
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
现在开始可以直接编辑SVN源代码文件，提交修改时会自动触发Jenkins构建。
修改pom.xml文件中的注释，提交修改（要写注释），查看生成结果。

.. code-block:: xml

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example.maven-samples</groupId>
    <artifactId>parent</artifactId>
    <packaging>pom</packaging>
    <version>1.0-SNAPSHOT</version>
    <name>Parent</name>
    <description>Just a pom that makes it easy to build both projects at the same time. 
        SCM trigger Jenkins build
    </description>

    <modules>
        <module>multi-module</module>
        <module>single-module</module>
    </modules>

    <prerequisites>
        <maven>3.3.9</maven>
    </prerequisites>

    </project>

提交修改后，执行结果中会显示提交的变更

.. figure:: commit-trigger-build-result.png

