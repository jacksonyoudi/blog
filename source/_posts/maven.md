---
title: 项目管理工具maven
date: 2021-08-01 22:15:35
img: https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/xwoLGY.jpg
top: true
hide: false
cover: true
coverImg: https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/jmn7kh.jpg
toc: true
mathjax: false
summary: 介绍maven在开发中使用，以及一些专业术语相关的东西
keywords:
-  java
-  maven
-  tool

categories: 工具软件
tags:
-  java
- package manager
- maven

---


在学习的技术越来越多，写的代码越来越多，会发现代码管理越来越复杂，就自然有了包的概念。我个人前面的jar包都是手动管理的，由于，我经常使用python的pip以及golang的 govender以及 gomods，所以，会想到java的包管理工具，查了一下，发现maven很强大，可以极大的提高开发效率，故学习，下面是学习过程中整理的笔记。

## 目前在技术开发中存在的问题
1. 一个项目就是一个工程
   如果项目非常庞大，就不适合继续使用package来划分模块。最好是每一个模块对应一个项目，利于分工协作。
   借助于maven就可以将一个项目拆分成多个工程。

2. 项目中需要的jar包必须手动“复制”、”粘贴” 到WEB-INF/lib 项目下
   带来的问题：同样的jar包文件重复出现在不同的项目工程中，一方面浪费存储空间，另外也让工程比较臃肿。
   借助Maven，可以将jar包仅仅保存在“仓库”中，有需要使用的工程“引用”这个文件，并不需要重复复制。

3. jar包需要别人替我们准备好，或到官网下载
   所有知名框架或第三方工具jar包已经按照统一规范放在了Maven的中央仓库中。

4. 一个jar包依赖的其他jar包需要自己手动加到项目中
   Maven会自动将被依赖的jar包导入进来。


## maven是什么

1. Maven 是 Apache 软件基金会组织维护的一款自动化构建工具，专注服务于 Java 平台的项目构建和依赖管理 。Maven 这个单词的本意是：专家，内行。读音是[‘meɪv(ə)n]或[‘mevn]。
   构建工具的发展：Make→Ant→Maven→Gradle



2. 构建：就是以我们编写的Java代码、框架配置文件、国际化等其他资源文件、jsp页面和图片等静态资源作为“原材料”，去“生产”出一个可以运行的项目的过程。
   理想的项目构建：高度自动化，跨平台，可重用的组件，标准化的


3.构建过程中的几个主要环节
①清理：删除以前的编译结果，为重新编译做好准备。
②编译：将Java源程序编译为字节码文件。
③测试：针对项目中的关键点进行测试，确保项目在迭代开发过程中关键点的正确性。
④报告：将每一次测试后以标准的格式记录和展示测试结果。
⑤打包：将一个包含诸多文件的工程封装为一个压缩文件用于安装或部署。Java工程对应jar包，Web工程对象war包。
⑥安装：在Maven环境下特指将打包的结果——Jar包或War包安装到本地仓库中。
⑦部署：将打包的结果部署到远程仓库或将war包部署到服务器上运行。

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/PeaY9R.jpg)



日常开发中，如果不通过maven，以上步骤全部是程序员自己手动一个一个的去编译执行操作，有了maven， 只需要简单的一个命令，全部搞定，解放双手。


4. 什么是依赖？为什么要进行依赖管理？
   依赖是Maven中最关键的部分，我们之所以在工程中使用Maven，就是因为它的依赖管理功能。
   自动下载，统一依赖管理，避免jar包引起的错误。

### maven安装
```bash
brew install maven
```

安装完成后，查看安装文件
```bash

macbookpro > brew list maven
/usr/local/Cellar/maven/3.6.1/bin/mvn
/usr/local/Cellar/maven/3.6.1/bin/mvnDebug
/usr/local/Cellar/maven/3.6.1/bin/mvnyjp
/usr/local/Cellar/maven/3.6.1/libexec/bin/ (4 files)
/usr/local/Cellar/maven/3.6.1/libexec/boot/plexus-classworlds-2.6.0.jar
/usr/local/Cellar/maven/3.6.1/libexec/conf/ (3 files)
/usr/local/Cellar/maven/3.6.1/libexec/lib/ (63 files)
```
测试一下：

```bash
macbookpro> mvn --v
Apache Maven 3.6.1 (d66c9c0b3152b2e69ee9bac180bb8fcc8e6af555; 2019-04-05T03:00:29+08:00)
Maven home: /usr/local/Cellar/maven/3.6.1/libexec
Java version: 1.8.0_172, vendor: Oracle Corporation, runtime: /Library/Java/JavaVirtualMachines/jdk1.8.0_172.jdk/Contents/Home/jre
Default locale: zh_CN, platform encoding: UTF-8
OS name: "mac os x", version: "10.15", arch: "x86_64", family: "mac"
```

可以使用`mvn --help`查看帮助


### maven的核心概念
>约定的目录结构
POM
坐标
依赖
仓库
生命周期/插件/目标
继承
聚合


以下内容就是按上面进行展开的

#### 使用idea创建一个maven工程
1. idea支持创建maven项目
   ![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/NFqnYK.jpg)

   ![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/Oo9fzo.jpg)

   ![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/ZhCzDy.jpg)

   ![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/C70JLZ.jpg)


pom.xml文件为Maven工程的核心配置文件

2. 为什么要遵循约定的目录结构呢？
   我们在开发中如果需要让第三方工具或框架知道我们自己创建的资源在哪，那么基本上就是两种方式：
   ①以配置文件的方式明确告诉框架 如 < param-value>classpath:spring-context.xml < /param-value>
   ②遵循框架内部已经存在的约定 如log4j的配置文件名规定必须为 log4j.properties 或 log4j.xml ；Maven 使用约定的目录结构


#### maven常用命令
1. 注意：执行与构建过程相关的Maven命令，必须进入pom.xml 所在的目录。
2. 常用命令
> mvn clean : 清理
mvn compile : 编译主程序
mvn test-compile : 编译测试程序
mvn test : 执行测试
mvn package : 打包
mvn install ： 安装
mvn site ：生成站点


#### maven网络问题

1. Maven 的核心程序中仅仅定义了抽象的生命周期，但是具体的工作必须有特定的插件来完成。而插件本身不包含在Maven核心程序中。
2. 当我们执行的Maven命令需要用到某些插件时，Maven核心程序会首先到本地仓库中查找。
3. 本地仓库的默认位置：~/.m2/repository
4. Maven核心程序如果在本地仓库中找不到需要的插件，那么它会自动连接外网，到中央仓库下载。
5. 如果此时无法连接外网，则构建失败。
6. 修改默认本地仓库的位置可以让Maven核心程序到我们事先准备好的目录下查找插件
   ①找到Maven解压目录\conf\settings.xml
   ②在setting.xml 文件中找到 localRepository 标签
   ③将 < localRepository>/path/to/local/repo< /localRepository>从注释中取出
   ④将标签体内容修改为自定义的Maven仓库目录

如果网络慢，也可以配置阿里的国内镜像。



## POM

1. 含义：Project Object Model 项目对象模型
   DOM ：Document Object Model 文档对象模型

2. pom.xml 对于 Maven工程是核心配置文件，与构建过程相关的一切设置都在这个文件中进行配置。
   重要程度相当于web.xml 对于动态web工程

POM 中可以指定以下配置：

>项目依赖
插件
执行目标
项目构建 profile
项目版本
项目开发者列表
相关邮件列表信息

示例：
```xml
<project xmlns = "http://maven.apache.org/POM/4.0.0"
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
 
    <!-- 模型版本 -->
    <modelVersion>4.0.0</modelVersion>
    <!-- 公司或者组织的唯一标志，并且配置时生成的路径也是由此生成， 如com.companyname.project-group，maven会将该项目打成的jar包放本地路径：/com/companyname/project-group -->
    <groupId>com.companyname.project-group</groupId>
 
    <!-- 项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 -->
    <artifactId>project</artifactId>
 
    <!-- 版本号 -->
    <version>1.0</version>
</project>
```
所有 POM 文件都需要 project 元素和三个必需字段：groupId，artifactId，version。也就是下面需要说的 坐标


### 坐标
坐标就是用来定位的

Maven的坐标：
使用下面三个向量在仓库中唯一定位一个Maven工程
①groupid:公司或组织域名倒序+项目名
`<groupId>com.jackson.youdi</groupId>`
②artifactid:模块名
`<artifactId>TheOne</artifactId>`
③version：版本
`<version>1.0-SNAPSHOT</version>`


3. Maven 工程的坐标与仓库中路径的对应关系,下面给两个例子
```xml
< groupId>org.springframework< /groupId>
< artifactId>spring-core</artifactId>
< version>4.0.0.RELEASE< /version>

org/springframework/spring-core/4.0.0.RELEASE/spring-core-4.0.0.RELEASE.jar

<groupId>mysql</groupId>
<artifactId>mysql-connector-java</artifactId>
<version>8.0.18</version>

mysql/mysql-connector-java/8.0.18/mysql-connector-java-8.0.18.jar
绝对路径： ~/.m2/repository/mysql/mysql-connector-java/8.0.18/mysql-connector-java-8.0.18.jar
```

注意：我们自己的 Maven 工程必须执行安装操作才会进入仓库。安装的命令是：mvn install


### 仓库
1. 仓库的分类
   ①本地仓库：当前电脑上部署的仓库目录，为当前电脑上所有Maven工程服务
   ②远程仓库
   （1）私服：搭建在局域网环境中，为局域网范围内的所有Maven工程服务
   （2）中央仓库：假设在Internet上，为全世界所有Maven工程服务
   （3）中央仓库镜像：为了分担中央仓库流量，提升用户访问速度

2. 仓库中保存的内容：Maven工程
   ①Maven自身所需要的插件
   ②第三方框架或工具的jar包
   ③我们自己开发的Maven工程

不管是什么样的 jar 包，在仓库中都是按照坐标生成目录结构，所以可以通过统一的方式查询或依赖。

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/i512Gx.jpg)



### 依赖
1. 当 A jar 包用到了 B jar 包中的某些类时，A 就对 B 产生了依赖，这是概念上的描述。Maven解析依赖信息时会到仓库中查找被依赖的jar包。
   对于我们自己开发的Maven工程，要使用mvn install 命令安装后就可以进入仓库。

```xml
    <!--    dependecies 项目运行时的jar包-->
    <dependencies>
        <!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.18</version>
        </dependency>
    </dependencies>
```

2. 依赖的范围
   ①从项目结构角度理解compile和test的区别

```bash
.
├── main
│   ├── java  // compile范围依赖
│   └── resources
└── test
    └── java  //  test范围依赖
```

compile范围依赖
》对主程序是否有效：有效
》对测试程序是否有效：有效
》是否参与打包：参与
》是否参与部署：参与
》典型例子：spring-core

test范围依赖
》对主程序是否有效：无效
》对测试程序是否有效：有效
》是否参与打包：不参与
》是否参与部署：不参与
》典型例子：Junit

②从开发和运行这两个阶段理解compile 和 provided 的区别

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/R1yMAr.jpg)



》对主程序是否有效：有效
》对测试程序是否有效：有效
》是否参与打包：不参与
》是否参与部署：不参与
》典型例子：Servlet-api.jar

③有效性总结

||compile|test|provided|
|-|-|-|-|
|主程序|√|x|√|
|测试程序|√|√|√|
|参与部署|√|x|x|

3. 依赖的传递性
   一种相当常见的情况，比如说 A 依赖于其他库 B。如果，另外一个项目 C 想要使用 A ，那么 C 项目也需要使用库 B。
   Maven 可以避免去搜索所有所需库的需求。Maven 通过读取项目文件（pom.xml），找出它们项目之间的依赖关系。
   我们需要做的只是在每个项目的 pom 中定义好直接的依赖关系。其他的事情 Maven 会帮我们搞定。
   通过可传递性的依赖，所有被包含的库的图形会快速的增长。当有重复库时，可能出现的情形将会持续上升。Maven 提供一些功能来控制可传递的依赖的程度。



### 生命周期

![](https://raw.githubusercontent.com/jacksonyoudi/images/main/uPic/Ljs75u.jpg)


各个构建环节执行的顺序：不能打乱顺序，必须按照既定的正确顺序来执行。
Maven的核心程序中定义了抽象的生命周期，生命周期中各个阶段的具体任务是由插件来完成的。
Maven核心程序为了更好的实现自动化构建，按照这一特点执行生命周期中各个阶段：不论现在要执行生命周期中的哪一阶段，都是从这个生命周期最初的位置开始执行。
Maven有三套相互独立的生命周期，分别是：


#### 生命周期

①Clean Lifecycle 在进行真正的构建之前进行一些清理工作。
②Default Lifecycle 构建的核心部分，编译、测试、打包、安装、部署等等。
③Site Lifecycle 生成项目报告，站点，发布站点。

他们相互独立。也可以直接运行 mvn clean install site 运行所有这三套生命周期。

每套生命周期都由一组阶段(Phase)组成，我们平时在命令行输入的命令总会对应于一个特定的阶段。比如，运行 mvn clean，这个 clean 是 Clean 生命周期的一个阶段。有 Clean 生命周期，也有 clean 阶段。

#### Clean声明周期
①pre-clean 执行一些需要在clean之前完成的工作
②clean 移除所有上一次构建生成的文件
③post-clean 执行一些需要在clean 之后立刻完成的工作

#### Default声明周期
Default 生命周期是 Maven 生命周期中最重要的一个，绝大部分工作都发生在这个生命周期中。这里，只解释一些比较重要和常用的阶段：


>validate
generate-sources
process-sources
generate-resources
process-resources 复制并处理资源文件，至目标目录，准备打包。
compile 编译项目的源代码。
process-classes
generate-test-sources
process-test-sources
generate-test-resources
process-test-resources 复制并处理资源文件，至目标测试目录。
test-compile 编译测试源代码。
process-test-classes
test 使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。
prepare-package
package 接受编译好的代码，打包成可发布的格式，如 JAR。
pre-integration-test
integration-test
post-integration-test
verify
install 将包安装至本地仓库，以让其它项目依赖。
deploy 将最终的包复制到远程的仓库，以让其它开发人员与项目共享或部署到服务器上运行。

#### Site生命周期
①pre-site 执行一些需要在生成站点文档之前完成的工作
②site 生成项目的站点文档
③post-site 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备
④site-deploy 将生成的站点文档部署到特定的服务器上

这里经常用到的是 site 阶段和 site-deploy 阶段，用以生成和发布 Maven 站点，这可是 Maven 相当强大的功能，Manager 比较喜欢，文档及统计数据自动生成，很好看。


#### 插件和目标
Maven的核心仅仅定义了抽象的声明周期，具体的任务都是交由插件完成的。
每个插件都实现多个功能，每个功能就是一个插件目标
Maven的生命周期与插件目标相互绑定，以完成某个具体的构建任务。
可以将目标看做“调用插件功能的命令”

例如：compile 就是插件 maven-compiler-plugin 的一个目标；pre-clean 是插件 maven-clean-plugin 的一个目标。


以上内容来源于网络，推荐给大家比较好的资源： [https://www.runoob.com/maven/maven-tutorial.html](https://www.runoob.com/maven/maven-tutorial.html)

