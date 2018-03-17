---
title: maven学习笔记
date: 2018-03-17 22:58:52
tags:
- 笔记
---

@(笔记)

Maven 是一个项目管理和构建自动化工具。
理解：一直不能很好的使用Maven，只把Maven叫做项目管理工具，其实更应该是构建自动化工具；那么再结合前端，npm，yarn是包管理工具，webpack便是打包构建工具，也是有打包流程，和插件之说。

核心概念：
POM (Project Object Model)
Maven 插件
Maven 生命周期
Maven 依赖管理
       Maven 库

Maven 使用约定优于配置的原则，有约定的项目结构。

标准目录结构：
![B0003.JPG](/images/B0003.JPG)

插件：一个目标是一个工作单元，而插件则是一个或者多个目标的集合。比如说Jar插件，Compiler插件，Surefire插件等。从看名字就能知道，Jar 插件包含建立Jar文件的目标， Compiler 插件包含编译源代码和单元测试代码的目标。Surefire 插件的话，则是运行单元测试。
看到这里，估计你能明白了，mvn 本身不会做太多的事情，它不知道怎么样编译或者怎么样打包。它把构建的任务交给插件去做。插件定义了常用的构建逻辑，能够被重复利用。这样做的好处是，一旦插件有了更新，那么所有的 maven 用户都能得到更新。

有序生命周期:
* validate： 用于验证项目的有效性和其项目所需要的内容是否具备
* initialize：初始化操作，比如创建一些构建所需要的目录等。
* generate-sources：用于生成一些源代码，这些源代码在compile phase中需要使用到
* process-sources：对源代码进行一些操作，例如过滤一些源代码
* generate-resources：生成资源文件（这些文件将被包含在最后的输入文件中）
* process-resources：对资源文件进行处理
* compile：对源代码进行编译
* process-classes：对编译生成的文件进行处理
* generate-test-sources：生成测试用的源代码
* process-test-sources：对生成的测试源代码进行处理
* generate-test-resources：生成测试用的资源文件
* process-test-resources：对测试用的资源文件进行处理
* test-compile：对测试用的源代码进行编译
* process-test-classes：对测试源代码编译后的文件进行处理
* test：进行单元测试
* prepare-package：打包前置操作
* package：打包
* pre-integration-test：集成测试前置操作     
* integration-test：集成测试
* post-integration-test：集成测试后置操作
* install：将打包产物安装到本地maven仓库
* deploy：将打包产物安装到远程仓库

Maven内置变量说明：
* ${basedir} 项目根目录
* ${project.build.directory} 构建目录，缺省为target
* ${project.build.outputDirectory} 构建过程输出目录，缺省为target/classes
* ${project.build.finalName} 产出物名称，缺省为${project.artifactId}-${project.version}
* ${project.packaging} 打包类型，缺省为jar
* ${project.xxx} 当前pom文件的任意节点的内容



踩坑列表：
1. setting.xml 文件里的内容报错，导致问题一直无法被定位。积累maven配置有用的优先级，进行问题排查。
2. Jar 包下载失败解决办法，可能是因为下载jar包中断导致本地仓库有不完整jar包。先删除jar包，重新下载。

学习网址：
1. (orcale的maven教程) http://www.oracle.com/technetwork/cn/community/java/apache-maven-getting-started-1-406235-zhs.html
2. (Maven 资源配置文件) http://www.cnblogs.com/pixy/p/4798089.html
3. (Maven 常用插件) http://www.cnblogs.com/pixy/p/4977550.html
4. (springBoot Maven插件的作用) http://www.cnblogs.com/acm-bingzi/p/mavenSpringBootPlugin.html http://www.voidcn.com/article/p-krttmics-brm.html
5. （Jar下载失败解决办法）http://blog.csdn.net/captian_900331/article/details/50897831
参考知识点：
1. Artifact 的词源是拉丁语 arte(艺术) + factum(做、造)，翻译为「（手）工艺品」

