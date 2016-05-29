---
title: PowerDesigner Flyway 数据库迁移
date: 2016-04-23 21:08
permalink: powerdesigner-flyway-db-migration
categories:
  - DB
tags:
  - PowerDesigner
  - Flyway
  - 数据库迁移
---

这篇文章主要介绍通过`PowerDesigner`、`Flyway`这两个工具来制作和执行数据库升级脚本。

<!-- more -->

## 要解决什么问题？
假设这样一个场景：我们最新数据库模型版本为`V1`，现在我们要为两个客户部署我们的系统（数据库使用`V1`版本）。两天后开发人员A修改了数据库模型版本为`V1.1`，这时又有另外一个客户想部署我们的系统（数据库使用`V1.1`版本）。又过了几天后开发人员B修改了数据库模型版本为`V1.2`，这时要求为前面几个已经部署了我们系统的客户升级系统，如果没有一个好的方式来对**数据库脚本进行版本管理**的话这种升级就会是一个比较「头大」的问题。这里将介绍一种流程和方式来对描述的问题进行解决。

## 数据库脚本版本定义
要进行**数据库脚本版本管理**就要对数据库脚本的版本进行明确的定义和约束。

**版本定义**

完整示例：`V3.0.0_1__create_user_table.sql`。说明：

* `V3.0.0`表示项目的开发版本号（必须）；
* `_1`表示升级脚本次数，每一次生成新的升级脚本次数`+1`（必须），注：`_`必须要有；
* `__create_user_table`表示升级脚本描述信息（必选），注：`__`为分隔符必须要有；

`PowerDesigner`生成的`模型归档`文件名规则也遵照此方式命名，其中`__create_user_table`这部分可以不要。

## 升级脚本生成
### 第一步：
![PowerDesigner 数据库升级脚本生成][]

**重要属性说明**

* `File name`：升级脚本名称按 [数据库脚本版本定义](#数据库脚本版本定义) 规范取名；
* `Using an archive model`：已归档模型文件，主要作用是于现在的数据模型进行对比最终生成增量的数据库升级脚本；
* `Automatic archive`：自动归档当前的数据模型；

### 第二步，对比差异：
![PowerDesigner 升级脚本差异对比][]

确认没有问题后点击`OK`生成最终的升级脚本。

### 第三步，归档新的数据模型：
![PowerDesigner 模型归档][]

归档新的数据模型用于下一次数据模型再变更时对比生成新的升级脚本。

## 注意事项
* 生成的升级脚本一定要经过验证确定能够从低版本升级到当前版本；
* 模型归档文件不需要保留太多，一般 1~3 个版本就好了；

## 刷库脚本生成
生成完升级脚本后也应该生成一份最新的刷库脚本，用`PowerDesigner`生成刷库的脚本比较简单选择菜单`Databse->Generate Databse`按操作执行即可。

## Flyway 工具
现在我们已经有了一个或多个升级脚本，要如何升级我们已经部署的多个不同版本系统数据库呢？举个简单的例子：

现在我们部署了三个系统（A1、A2、A3）分别使用的数据库`刷库脚本`版本为`V1.0`、`V1.1`、`V1.2`。最新的升级脚本已经为`V1.6`，那么当升级时我们希望：

A1：V1.1->V1.2->V1.3->V1.4->V1.5->V1.6

A2：V1.2->V1.3->V1.4->V1.5->V1.6

A3：V1.3->V1.4->V1.5->V1.6

如何让这个过程更自动化更方便就是 Flyway 这个数据库版本控制工具要做的事情，[Flyway 官网]。

Flyway 集成的可选方式较多，官网也有详细的介绍，现在项目中集成的是 Flyway Maven 插件的方式。

### 设置 Baseline（数据库版本基线）
当系统部署第一次执行刷库脚本后，就应该先创建数据库版本基线，执行`mvn flyway:baseline`，这是以后数据库升级的基础。**注**：基线的设置只需要第一次初始化数据库时执行一次就好了，以后都不需要再执行。

### 执行数据库升级脚本
当需要升级数据库时只需要执行`mvn flyway:migrate` Flyway 将自动扫描指定目录下的升级脚本（`.sql`文件），根据文件名的命名规则依次执行相关`sql`文件。

### 修复元数据表（`schema_version`）
当某个升级脚本执行失败时在`schema_version`表中会产生一条失败数据，如果想修改升级脚本后重新执行`mvn flyway:migrate`迁移命令。需要先将失败的元数据修复（其实就是删除掉），可以先执行`mvn flyway:repair`然后再执行`mvn flyway:migrate`。**注**：升级脚本已经执行的 sql 语句（如：创建表、修改表等）不能回滚需要手动清理。

更多的一些用法可参考 [Flyway 官网]。

## 项目示例
通过`PowerDesigner`生成的`升级脚本`和`刷库脚本`都会统一放到我们项目的`db-migrate`模块，其中：`create`目录存放刷库脚本，`migration`目录存放升级脚本。

![项目示例][]

pom 配置：

```xml
<?xml version="1.0"?>
<project
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"
    xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.xxx</groupId>
        <artifactId>xxx</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>
    <artifactId>xxx-db-migrate</artifactId>
    <packaging>pom</packaging>
    <name>xxx-db-migrate</name>
    <description>数据库迁移/升级模块</description>

    <build>
        <plugins>
            <!-- flyway 数据库升级/迁移插件 -->
            <plugin>
                <groupId>org.flywaydb</groupId>
                <artifactId>flyway-maven-plugin</artifactId>
                <version>4.0</version>
                <configuration>
                    <driver>com.mysql.jdbc.Driver</driver>
                    <url>jdbc:mysql://127.0.0.1:3306/dc?&amp;useUnicode=true&amp;characterEncoding=utf-8&amp;autoReconnect=true</url>
                    <user>root</user>
                    <password>123456</password>
                    <locations>
                        <location>filesystem:${project.basedir}/db/migration</location>
                    </locations>
                    <baselineVersion>3.0.0_7</baselineVersion>
                    <baselineDescription>Base Migration</baselineDescription>
                </configuration>
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>${mysql.version}</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
</project>
```

**操作步骤**：

- 如果是第一次刷库我们会依次执行`create`目录下的相关 sql 脚本完成后，执行`mvn flyway:baseline`命令建立数据库版本基线（第一次刷库不需要执行`mvn flyway:migrate`命令）。
- 在执行完上一步后，如果`migration`目录下的升级脚本有增加，如：`V3.0.0_8__xxxxx.sql`、`V3.0.0_9__xxxxx.sql`，则只需要执行`mvn flyway:migrate`命令即可。


[PowerDesigner 数据库升级脚本生成]: http://7xqn9b.com1.z0.glb.clouddn.com/PowerDesigner/PowerDesigner-01.png
[PowerDesigner 升级脚本差异对比]: http://7xqn9b.com1.z0.glb.clouddn.com/PowerDesigner/PowerDesigner-02.png
[PowerDesigner 模型归档]: http://7xqn9b.com1.z0.glb.clouddn.com/PowerDesigner/PowerDesigner-03.png
[Flyway 官网]: https://flywaydb.org
[项目示例]: http://7xqn9b.com1.z0.glb.clouddn.com/PowerDesigner/%E9%A1%B9%E7%9B%AE%E7%A4%BA%E4%BE%8B.png
