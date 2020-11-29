# util-manual
spring工具使用手册

>认识Swagger:  
>Swagger 是一个规范和完整的框架，用于生成、描述、调用和可视化 RESTful 风格的 Web 服务。
>总体目标是使客户端和文件系统作为服务器以同样的速度来更新。文件的方法，参数和模型紧密集成到
>服务器端的代码，允许API来始终保持同步。
 
 作用：
 1. 接口的文档在线自动生成。
 2. 功能测试。
 
 Swagger使用的注解及其说明：
 
 ```
 @Api：用在类上，说明该类的作用。
  
  @ApiOperation：注解来给API增加方法说明。
  
  @ApiImplicitParams : 用在方法上包含一组参数说明。
  
  @ApiImplicitParam：用来注解来给方法入参增加说明。
  参数：
     ·paramType：指定参数放在哪个地方
        ··header：请求参数放置于Request Header，使用@RequestHeader获取
        ··query：请求参数放置于请求地址，使用@RequestParam获取
        ··path：（用于restful接口）-->请求参数的获取：@PathVariable
        ··body：（不常用）
        ··form（不常用）
     ·name：参数名
     ·dataType：参数类型
     ·required：参数是否必须传(true | false)
     ·value：说明参数的意思
     ·defaultValue：参数的默认值
  
  @ApiResponses：用于表示一组响应
  
  @ApiResponse：用在@ApiResponses中，一般用于表达一个错误的响应信息
        ——code：数字，例如400
        ——message：信息，例如"请求参数异常!"
        ——response：抛出异常的类   
  
  @ApiModel：描述一个Model的信息（一般用在请求参数无法使用@ApiImplicitParam注解进行描述的时候）
  
  @ApiModelProperty：描述一个model的属性
```

###第一步：导入依赖包

```xml
<dependency>
     <groupId>io.springfox</groupId>
     <artifactId>springfox-swagger2</artifactId>
     <version>2.2.2</version>
 </dependency>
 <dependency>
     <groupId>io.springfox</groupId>
     <artifactId>springfox-swagger-ui</artifactId>
     <version>2.2.2</version>
 </dependency>
 ```

###第二步：创建Swagger2配置类
```java
@Configuration
@EnableSwagger2
public class Swagger2 {
    
    /**
     * 创建API应用
     * apiInfo() 增加API相关信息
     * 通过select()函数返回一个ApiSelectorBuilder实例,用来控制哪些接口暴露给Swagger来展现，
     * 本例采用指定扫描的包路径来定义指定要建立API的目录。
     * 
     * @return
     */
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.swaggerTest.controller"))
                .paths(PathSelectors.any())
                .build();
    }
    
    /**
     * 创建该API的基本信息（这些基本信息会展现在文档页面中）
     * 访问地址：http://项目实际地址/swagger-ui.html
     * @return
     */
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("Spring Boot中使用Swagger2构建RESTful APIs")
                .description("更多请关注http://www.baidu.com")
                .termsOfServiceUrl("http://www.baidu.com")
                .contact("sunf")
                .version("1.0")
                .build();
    }
}
```
如上代码所示，通过createRestApi函数创建Docket的Bean之后，apiInfo()用来创建该Api的基本信息（这些基本信息会展现在文档页面中）

###第三步：使用Swagger提供的注解
```java

@RestController
@RequestMapping("/oss")
@Api(value = "简书-演示",description = "用来演示Swagger的一些注解")
public class TestController {


    @ApiOperation(value="修改用户密码", notes="根据用户id修改密码")
    @ApiImplicitParams({
        @ApiImplicitParam(paramType="query", name = "userId", value = "用户ID", required = true, dataType = "Integer"),
        @ApiImplicitParam(paramType="query", name = "password", value = "旧密码", required = true, dataType = "String"),
        @ApiImplicitParam(paramType="query", name = "newPassword", value = "新密码", required = true, dataType = "String")
    })
    @RequestMapping("/updatePassword")
    public String updatePassword(@RequestParam(value="userId") Integer userId, @RequestParam(value="password") String password, 
            @RequestParam(value="newPassword") String newPassword){
         if(userId <= 0 || userId > 2){
             return "未知的用户";
         }
         if(StringUtils.isEmpty(password) || StringUtils.isEmpty(newPassword)){
             return "密码不能为空";
         }
         if(password.equals(newPassword)){
             return "新旧密码不能相同";
         }
         return "密码修改成功!";
     }
}
```
启动Spring Boot程序,访问 http://locahost:8080/swagger-ui.html