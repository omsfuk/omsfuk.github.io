-------------------------
title: Maven问题汇总
tags: maven
-------------------------

# Maven问题汇总
## Maven 阿里云中心仓库
```
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
 xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
    <mirrors>
        <!-- 阿里云仓库 -->
        <mirror>
            <id>alimaven</id>
            <mirrorOf>central</mirrorOf>
            <name>aliyun maven</name>
            <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
        </mirror>

        <!-- 中央仓库1 -->
        <mirror>
            <id>repo1</id>
            <mirrorOf>central</mirrorOf>
            <name>Human Readable Name for this Mirror.</name>
            <url>http://repo1.maven.org/maven2/</url>
        </mirror>

        <!-- 中央仓库2 -->
        <mirror>
           <id>repo2</id>
           <mirrorOf>central</mirrorOf>
           <name>Human Readable Name for this Mirror.</name>
           <url>http://repo2.maven.org/maven2/</url>
        </mirror>     
    </mirrors>
</settings>
```

## Intellij创建Maven项目慢
创建项目时，在`Properties`中添加一个参数`archetypeCatalog=internal`。
`archetypeCatalog`表示插件使用的archetype元数据，不加这个参数时默认为`remote`，`local`，即中央仓库archetype元数据，由于中央仓库的archetype太多了，所以导致很慢，指定internal来表示仅使用内部元数据。
当然，也可以直接换个更快的源

## Maven发布到本地仓库
```
mvn install:install-file -Dfile=jar包的位置 -DgroupId=上面的groupId -DartifactId=上面的artifactId -Dversion=上面的version -Dpackaging=jar
```
