# Spring Boot 单元测试

## 1、引入 Spring Boot test 依赖

~~~ XML
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
~~~

## Springboot测试类之@RunWith注解

@RunWith就是一个运行器
@RunWith(JUnit4.class)就是指用JUnit4来运行
@RunWith(SpringJUnit4ClassRunner.class),让测试运行于Spring测试环境
