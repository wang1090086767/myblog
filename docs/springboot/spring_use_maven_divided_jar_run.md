# SpringBoot 使用 Maven 打包 分离所依赖的 jar 包到 lib 目录下，并以外部依赖的方式启动 jar 包

## 1. 配置 pom 文件

~~~xml
 <build>
  <plugins>
   <plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <configuration>
     <executable>true</executable>
<!--					<mainClass>cn.it.ProducerApplication</mainClass>-->
     <layout>ZIP</layout>
     <includes>
      <include>
       <groupId>nothing</groupId>
       <artifactId>nothing</artifactId>
      </include>
     </includes>
    </configuration>
    <executions>
     <execution>
      <goals>
       <goal>repackage</goal>
      </goals>
     </execution>
    </executions>

   </plugin>

   <!-- 指定启动类，将依赖打成外部jar包 -->
   <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-jar-plugin</artifactId>
    <configuration>
     <archive>
      <!-- 生成的jar中，不要包含pom.xml和pom.properties这两个文件 -->
      <addMavenDescriptor>false</addMavenDescriptor>
      <manifest>
       <!-- 是否要把第三方jar加入到类构建路径 -->
       <addClasspath>true</addClasspath>
       <!-- 外部依赖jar包的最终位置 -->
       <classpathPrefix>lib/</classpathPrefix>
       <!-- 项目启动类 -->
<!--							<mainClass>com.mozi.mq_monitor.MqMonitorApplication</mainClass>-->
      </manifest>
     </archive>
    </configuration>
   </plugin>
   <!--拷贝依赖到jar外面的lib目录-->
   <plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <executions>
     <execution>
      <id>copy-lib</id>
      <phase>package</phase>
      <goals>
       <goal>copy-dependencies</goal>
      </goals>
      <configuration>
       <outputDirectory>target/lib</outputDirectory>
       <excludeTransitive>false</excludeTransitive>
       <stripVersion>false</stripVersion>
       <includeScope>compile</includeScope>
      </configuration>
     </execution>
    </executions>
   </plugin>
  </plugins>
 </build>
~~~

## 2. 使用命令启动时添加参数

~~~ shell
 java -Dloader.path=./lib -jar ./producer-1.0-SNAPSHOT.jar
~~~

## 3. 若以外部配置启动 springboot 可使用以下命令

~~~ shell
使用如下参数指定Springboot 配置文件
java  -Dspring.config.location=./application.yml -jar  demo-0.0.1-SNAPSHOT.jar
~~~

## 4. SpringBoot 引用外部配置文件

> 24.3 Application property files
SpringApplication will load properties from application.properties files in the following locations and add them to the Spring Environment:
A /config subdirectory of the current directory.
The current directory
A classpath /config package
The classpath root
The list is ordered by precedence (properties defined in locations higher in the list override those defined in lower locations).
  
> 这里说了四种方式可以把配置文件放到外部的。
第一种是在jar包的同一目录下建一个config文件夹，然后把配置文件放到这个文件夹下；
第二种是直接把配置文件放到jar包的同级目录；
第三种在classpath下建一个config文件夹，然后把配置文件放进去；
第四种是在classpath下直接放配置文件。

**这四种方式的优先级是从一到四一次降低的。**

所对应的 SpringBoot 源码如下：

~~~ java

#ConfigFileApplicationListener.java
        private void load(Profile profile, DocumentFilterFactory filterFactory,
                DocumentConsumer consumer) {
            //getSearchLocations()默认返回：
            //[./config/, file:./, classpath:/config/, classpath:/]
            //即搜索这些路径下的文件
            getSearchLocations().forEach((location) -> {
                boolean isFolder = location.endsWith("/");
                //getSearchNames()返回：application
                Set<String> names = (isFolder ? getSearchNames() : NO_SEARCH_NAMES);
                //跟入load(.....)
                names.forEach(
                        (name) -> load(location, name, profile, filterFactory, consumer));
            });

~~~

默认SpringBoot 会扫描 jar 包所在目录的配置文件（properties 文件或 yml 文件）和 所在目录
