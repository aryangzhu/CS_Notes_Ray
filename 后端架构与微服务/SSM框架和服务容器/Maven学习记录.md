maven是一个**项目管理工具**,可以对Java项目进行构建、依赖管理。
maven能够帮助开发者完成以下工作:   
构建  
文档生成   
报告  
依赖  
SCMS  
发布  
分发  
邮件列表  
首先,我们需要知道的是maven采用的是约定大于配置的原则,约定的目录可以自行搜索查看。  
## POM
POM(Project Object Model,项目对象模型)是maven工程的**基本工作单元**,是一个XML文件,包含了项目的基本信息,用于描述项目如何构建,声明项目依赖,等等。  
#### 节点介绍
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

| 节点         | 描述                                                         |
| ------------ | ------------------------------------------------------------ |
| project      | 工程的根标签                                                 |
| modelVersion | 模型版本为4.0                                                |
| groupId      | 工程组的标志,在一个组织或者项目是唯一的。                    |
| artifactId   | 工程的标识。groudId和artifactId一起定义了artifact在仓库中的位置。 |
| version      | 工程的版本号                                                 |

#### Super(父)POM
是maven默认的POM。所有的POM都继承自一个父POM(无论是否显式地定义了这个POM)。父POM中包含了一些可以被继承的默认设置。当maven发现需要下载POM中的依赖时,**它会到Super POM中配置的默认仓库http://repo1.maven.org/maven2**去下载。
## 构建生命周期
Maven构建生命周期定义了一个项目**构建和发布**的过程。
验证-》编译-》测试-》包装(打包)-》检查-》安装(安装打包到本地仓库,一共其他项目使用)-》部署(拷贝最终的工程包到远程仓库中,已共享给其他开发人员使用)
#### 三个标准声明周期
clean:项目清理的处理  
defalut或者build: 项目部署的处理  
site:项目站点文档的创建  
我们以clean生命周期为例,执行mvn post-clean时maven调用clean声明周期时,它包含以下阶段:  
pre-clean:执行一些需要在clean之前完成的工作  
clean:移除所有上一次构建生成的文件  
post-clean:执行一些需要在clean之后立刻完成的工作  
注:IDEA中有maven的快捷键,但是离不开这三个声明周期。  
下面来mvn post-clean执行之后会发生什么  
```shell
[INFO] Scanning for projects...
[INFO] ------------------------------------------------------------------
[INFO] Building Unnamed - com.companyname.projectgroup:project:jar:1.0 //构建jar包
[INFO]    task-segment: [post-clean] //任务指令
[INFO] ------------------------------------------------------------------
[INFO] [antrun:run {execution: id.pre-clean}]
[INFO] Executing tasks //执行pre-clean
     [echo] pre-clean phase
[INFO] Executed tasks //pre-clean执行完成
[INFO] [clean:clean {execution: default-clean}]
[INFO] [antrun:run {execution: id.clean}]
[INFO] Executing tasks //执行defalut
     [echo] clean phase
[INFO] Executed tasks
[INFO] [antrun:run {execution: id.post-clean}]
[INFO] Executing tasks
     [echo] post-clean phase
[INFO] Executed tasks
[INFO] ------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL //构建成功
[INFO] ------------------------------------------------------------------
[INFO] Total time: < 1 second
[INFO] Finished at: Sat Jul 07 13:38:59 IST 2012
[INFO] Final Memory: 4M/44M
[INFO] ------------------------------------------------------------------
```
## 仓库
#### 本地仓库
#### 中央仓库
社区提供
#### 远程仓库

## 引入外部依赖
在pom.xml中进行引入，使用\<dependecies>标签。
## 快照(SNAPSHOT)
#### 为什么需要快照
首先我们需要知道的是大型项目应用通常包含多个模块,并且通常的场景是多个团队开发同一个应用的不同模块。  
加入有一个service的模块需要快节奏的bug修复和更新,并且他们几乎每隔一天就要发布到远程仓库。会有下面的问题:  
1.data-service团队每次发布更新的代码都要告知app-ui团队(这里是同一个应用下的不同模块)。  
2.app-ui团队需要经常地更新他们pom.xml到最新版本。  
#### 什么是快照？
快照是一种特殊的版本,指定了某个当前的开发进度的副本。不同于常规的版本,**Maven每次构建都会在远程仓库中检查新的快照**。现在data-service团队可以每次发布更新代码的快照到仓库中。  
#### 项目快照VS版本
对于版本的话,如果指定了某个当前的开发进度的副本,例如data-service:1.0,那么就不会再次从仓库下载新的可用文件。  
而对于快照来说,app-ui团队构建项目时,maven将自动获取最新的快照(data-service:1.0-SNAPSHOT)。  
#### 项目中如何使用快照
app-ui项目中引入快照依赖  
```xml
<dependencies>
      <dependency>
      <groupId>data-service</groupId>
         <artifactId>data-service</artifactId>
         <version>1.0-SNAPSHOT</version>
         <scope>test</scope>
      </dependency>
</dependencies>
```
data-service项目中的pom.xml文件  
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>data-service</groupId>
   <artifactId>data-service</artifactId>
    <!--可以看到项目版本为SNAPSHOT-->
   <version>1.0-SNAPSHOT</version>
   <packaging>jar</packaging>
   <name>health</name>
   <url>http://maven.apache.org</url>
   <properties>
      <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
   </properties>
</project>
```



## 自动化构建

延续上面的问题,app-web-ui和app-desktop-ui项目的团队要求不管bus-core-api项目何时发生变化,他们的构建过程都应当可以启动。

在bus-core-api项目的pom文件中添加一个post-build目标操作来启动app-web-ui和app-desktop-ui项目的构建。

#### 修改bus-core-api项目文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
   xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
   http://maven.apache.org/xsd/maven-4.0.0.xsd">
   <modelVersion>4.0.0</modelVersion>
   <groupId>bus-core-api</groupId>
   <artifactId>bus-core-api</artifactId>
   <version>1.0-SNAPSHOT</version>
   <packaging>jar</packaging>
   <build>
   <plugins>
   <plugin>
      <artifactId>maven-invoker-plugin</artifactId>
      <version>1.6</version>
      <configuration>
         <debug>true</debug>
         <pomIncludes>
             <!--个人理解主要起作用的是这部分-->
            <pomInclude>app-web-ui/pom.xml</pomInclude>
            <pomInclude>app-desktop-ui/pom.xml</pomInclude> 
         </pomIncludes>
      </configuration>
      <executions>
         <execution>
            <id>build</id>
            <goals>
               <goal>run</goal>
            </goals>
         </execution>
      </executions>
   </plugin>
   </plugins>
   </build>
</project>
```



#### 

## 依赖管理

如果项目之间有依赖的话,那么是通过\<dependencies>来进行定义

而如果项目之间存在父子关系,则是通过\<parent>来定义的。

## 自动化部署



