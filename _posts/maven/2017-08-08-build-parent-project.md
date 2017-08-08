---
layout: post
title: build parent project
category: maven
tags: [maven,java]
---

# 通过父项目来管理多个模块的子项目

通过父项目来管理子模块，同时可以把静态资源统一放在一个单独的WebProject模块，在打包时进行合并。

## 父项目pom配置
```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.parent</groupId>
    <artifactId>ParentProjectBuild</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>
    
    <modules>
        <module>OhterProject</module>
		    <module>WebProject</module>
    </modules>
</project>
```

## WebProject的pom配置

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	
	<groupId>cn.web</groupId>
	<artifactId>SNXYWebProject</artifactId>
	<version>1.0.0</version>
	<name>SNXYWebProject</name>
	<packaging>war</packaging>
	 
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<build.env>DEV</build.env>
		<maven.build.timestamp.format>yyyyMMddHHmmss</maven.build.timestamp.format>
	</properties>
  <dependencies>
		<!-- dependencies -->
		
  </dependencies>
  <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>2.1.1</version>
                <configuration>
                  <warName>war_${project.version}_${build.env}_${maven.build.timestamp}</warName>
                  <archive>
                      <addMavenDescriptor>false</addMavenDescriptor>
                  </archive>
                  <!--
                  过滤掉暂时不需要的jar
                  -->
                  <packagingExcludes>
                      WEB-INF/lib/activation-*.jar,
                      WEB-INF/lib/dx-*.jar
                  </packagingExcludes>
				  
				          <webResources>
                      <!-- 
                        静态资源复制
                        note the order of the following resource elements are important. 
                        if there are duplicate files, the first file copied will win
                       -->
                      <resource>
                        <directory>D:\other\web</directory>
                      </resource>
                   </webResources>
                </configuration>
          </plugin>
          <plugin>
                <!--deploy war to jboss server-->
                <groupId>org.jboss.as.plugins</groupId>
                <artifactId>jboss-as-maven-plugin</artifactId>
                <configuration>
                  <timeout>60000</timeout>
                  <jbossHome>/opt/jboss-eap-6.4</jbossHome>
                  <username>name</username>
                  <password>pass</password>
                  <hostname>192.168.0.1</hostname> <!-- put your IP or hostname -->
                  <port>9999</port>
                </configuration>
                <executions>
                  <execution>
                    <id>upload-war</id>
                    <phase>install</phase>
                    <goals>
                      <goal>deploy</goal>
                    </goals>
                    <configuration>
                      <filename>war_${project.version}_${build.env}_${maven.build.timestamp}.war</filename>  
                      <name>test.war</name>
                      <deployEnabled>true</deployEnabled>
                      <force>true</force>
                      <domain>
                      <!-- This configuration is need only if you running JBoss in domain mode -->
                        <server-groups>
                          <server-group>test</server-group>
                        </server-groups>
                      </domain>
                    </configuration>
                  </execution>
                </executions>
             </plugin>
       		</plugins>	
    </build>
</project>
```
