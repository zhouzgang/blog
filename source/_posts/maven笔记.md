---
title: maven笔记
date: 2017-03-09 21:46:26
tags:
- 笔记
---

@(笔记)

##### 概念：
- *POM*（Project Object Model）： **用通俗点的话说就是对要构建的项目进行建模，将要构建的项目看成是一个对象（Object），后文就使用PO来指代这个对象。**既然是一个对象，那么PO有哪些属性呢？在maven中一个项目都是用一个唯一的坐标（coordinate）来表示，**坐标由groupId, artifactId, version, classifier, type这五部分组成**。这样来说PO应该也要具备坐标属性。另外一方面，一个项目肯定不是孤立存在的，可能依赖于其他的一些项目，那么也就是说PO应该具备dependencies这个属性，用来表示其所依赖的外部项目。**pom.xml就是PO对象的XML描述：**PO对象也有其父对象，用parent属性来表示，并且PO对象会继承其父对象的所有属性。另外一方面，一个项目可能根据不同职责分为多个模块（module），所有模块其实也就是一个单独的项目，只不过这些项目会使用其父对象的一些属性来进行构建。
- *Lifecycle*：生命周期；Lifecycle分为多个阶段，每个阶段叫做phase。（参考后面phase附件）**phase的作用有点类似于Java语言中的接口，每一个phase就是一个契约，但并没有定义具体的动作；**具体的动作就是由goal来定义，一个goal在maven中就是一个Mojo（Maven old java object）。**Mojo抽象类中定义了一个execute()方法，一个goal的具体动作就是在execute()方法中实现**。实现的Mojo类应该放在哪里呢？答案是maven plugin里，所谓的plugin其实也就是一个maven项目，只不过这个项目会引用maven的一些API，plugin项目也具备maven坐标。

##### 参考附件
- **phase：**
```
validate： 用于验证项目的有效性和其项目所需要的内容是否具备
initialize：初始化操作，比如创建一些构建所需要的目录等。
generate-sources：用于生成一些源代码，这些源代码在compile phase中需要使用到
process-sources：对源代码进行一些操作，例如过滤一些源代码
generate-resources：生成资源文件（这些文件将被包含在最后的输入文件中）
process-resources：对资源文件进行处理
compile：对源代码进行编译
process-classes：对编译生成的文件进行处理
generate-test-sources：生成测试用的源代码
process-test-sources：对生成的测试源代码进行处理
generate-test-resources：生成测试用的资源文件
process-test-resources：对测试用的资源文件进行处理
test-compile：对测试用的源代码进行编译
process-test-classes：对测试源代码编译后的文件进行处理
test：进行单元测试
prepare-package：打包前置操作
package：打包
pre-integration-test：集成测试前置操作     
integration-test：集成测试
post-integration-test：集成测试后置操作
install：将打包产物安装到本地maven仓库
deploy：将打包产物安装到远程仓库
```

##### 参考网址
- [maven内部运行原理解析](http://www.jianshu.com/p/0fb5e3fb704d#)