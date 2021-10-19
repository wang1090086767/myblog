# SpringBoot 整合 Swagger-UI 接口文档管理工具

## 1. 引入依赖 Swagger-UI 依赖

~~~ xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>

<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>

<dependency>
    <groupId>com.github.xiaoymin</groupId>
    <artifactId>swagger-bootstrap-ui</artifactId>
    <version>1.9.6</version>
</dependency>

~~~

## 2. Java 配置类

~~~ java

import com.github.xiaoymin.swaggerbootstrapui.annotations.EnableSwaggerBootstrapUI;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@EnableSwagger2
@Configuration
@EnableSwaggerBootstrapUI
public class Swagger2Config {

    @Value("${swagger.enable:true}")
    private boolean enable;

    @Bean
    public Docket createRestApi() {

        return new Docket(DocumentationType.SWAGGER_2)
                .useDefaultResponseMessages(false)
                .forCodeGeneration(false)
                .enable(enable)
                .apiInfo(apiInfo())
                .select()
                .paths(PathSelectors.regex("(?!/error.*).*"))
                .build();

    }


    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("远程平台构建Demo")
                .version("1.0")
                .build();
    }

}

~~~