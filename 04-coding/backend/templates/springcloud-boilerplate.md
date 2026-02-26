# Spring Cloud项目模板

> 版本: v1.0 | 最后更新: 2026-02-26

本文档定义Spring Cloud微服务项目的标准结构和模板。

---

## 一、项目结构

```
{{service_name}}/
├── src/main/java/com/example/{{service}}/
│   ├── controller/         # 控制器层
│   │   └── UserController.java
│   ├── service/           # 服务层
│   │   ├── UserService.java
│   │   └── impl/
│   │       └── UserServiceImpl.java
│   ├── mapper/            # 数据访问层
│   │   └── UserMapper.java
│   ├── entity/            # 实体类
│   │   └── User.java
│   ├── dto/               # 数据传输对象
│   │   ├── UserCreateDTO.java
│   │   └── UserUpdateDTO.java
│   ├── vo/                # 视图对象
│   │   └── UserVO.java
│   ├── config/            # 配置类
│   │   ├── MybatisConfig.java
│   │   └── SwaggerConfig.java
│   ├── constant/          # 常量
│   │   └── UserConstant.java
│   ├── enums/             # 枚举
│   │   └── UserStatus.java
│   ├── exception/         # 异常
│   │   ├── BusinessException.java
│   │   └── GlobalExceptionHandler.java
│   ├── util/              # 工具类
│   │   └── UserUtil.java
│   ├── feign/             # Feign客户端
│   │   └── OrderFeignClient.java
│   ├── mq/                # 消息队列
│   │   ├── producer/
│   │   │   └── UserProducer.java
│   │   └── consumer/
│   │       └── UserConsumer.java
│   └── Application.java   # 启动类
├── src/main/resources/
│   ├── mapper/            # MyBatis映射文件
│   │   └── UserMapper.xml
│   ├── application.yml    # 配置文件
│   ├── application-dev.yml
│   ├── application-test.yml
│   └── application-prod.yml
├── src/test/java/         # 测试代码
│   └── com/example/{{service}}/
│       ├── controller/
│       │   └── UserControllerTest.java
│       └── service/
│           └── UserServiceTest.java
├── Dockerfile             # Docker构建文件
├── pom.xml                # Maven配置
└── README.md              # 项目文档
```

---

## 二、POM配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.example</groupId>
        <artifactId>parent</artifactId>
        <version>1.0.0</version>
    </parent>

    <artifactId>{{service_name}}</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>
    <name>{{service_name}}</name>
    <description>{{service_description}}</description>

    <dependencies>
        <!-- Spring Boot -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Spring Cloud Alibaba -->
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
        </dependency>

        <!-- MyBatis-Plus -->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus-boot-starter</artifactId>
        </dependency>

        <!-- Redis -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>

        <!-- RocketMQ -->
        <dependency>
            <groupId>org.apache.rocketmq</groupId>
            <artifactId>rocketmq-spring-boot-starter</artifactId>
        </dependency>

        <!-- OpenFeign -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-loadbalancer</artifactId>
        </dependency>

        <!-- Swagger -->
        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <artifactId>knife4j-spring-boot-starter</artifactId>
        </dependency>

        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <scope>provided</scope>
        </dependency>

        <!-- Test -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

---

## 三、配置文件

### 3.1 application.yml

```yaml
server:
  port: 8080

spring:
  application:
    name: {{service_name}}
  profiles:
    active: dev
  cloud:
    nacos:
      discovery:
        server-addr: ${NACOS_SERVER_ADDR:localhost:8848}
      config:
        server-addr: ${NACOS_SERVER_ADDR:localhost:8848}
        file-extension: yaml
        shared-configs:
          - data-id: common.yaml
            group: DEFAULT_GROUP
            refresh: true

mybatis-plus:
  mapper-locations: classpath:mapper/**/*.xml
  type-aliases-package: com.example.{{service}}.entity
  configuration:
    map-underscore-to-camel-case: true
    log-impl: org.apache.ibatis.logging.slf4j.Slf4jImpl

logging:
  level:
    com.example.{{service}}: debug
  pattern:
    console: "%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n"
```

### 3.2 application-dev.yml

```yaml
server:
  port: 8080

spring:
  datasource:
    url: jdbc:mysql://localhost:3306/{{database}}?useUnicode=true&characterEncoding=utf8&useSSL=false&serverTimezone=Asia/Shanghai
    username: root
    password: root
    driver-class-name: com.mysql.cj.jdbc.Driver
  redis:
    host: localhost
    port: 6379
    password:
    database: 0

mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

---

## 四、启动类

```java
@SpringBootApplication
@EnableDiscoveryClient
@EnableFeignClients
@MapperScan("com.example.{{service}}.mapper")
public class {{ServiceName}}Application {

    public static void main(String[] args) {
        SpringApplication.run({{ServiceName}}Application.class, args);
    }
}
```

---

## 五、通用类模板

### 5.1 统一返回结果

```java
@Data
@NoArgsConstructor
@AllArgsConstructor
public class Result<T> {

    private Integer code;
    private String message;
    private T data;
    private Long timestamp;

    public static <T> Result<T> success() {
        return success(null);
    }

    public static <T> Result<T> success(T data) {
        return new Result<>(200, "success", data, System.currentTimeMillis());
    }

    public static <T> Result<T> error(Integer code, String message) {
        return new Result<>(code, message, null, System.currentTimeMillis());
    }

    public static <T> Result<T> error(String message) {
        return error(500, message);
    }
}
```

### 5.2 分页结果

```java
@Data
public class PageResult<T> {

    private List<T> list;
    private Long total;
    private Integer page;
    private Integer size;
    private Integer pages;

    public static <T> PageResult<T> of(List<T> list, Long total, Integer page, Integer size) {
        PageResult<T> result = new PageResult<>();
        result.setList(list);
        result.setTotal(total);
        result.setPage(page);
        result.setSize(size);
        result.setPages((int) Math.ceil((double) total / size));
        return result;
    }
}
```

### 5.3 业务异常

```java
@Getter
public class BusinessException extends RuntimeException {

    private final Integer code;

    public BusinessException(String message) {
        super(message);
        this.code = 500;
    }

    public BusinessException(Integer code, String message) {
        super(message);
        this.code = code;
    }
}
```

### 5.4 全局异常处理

```java
@Slf4j
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(BusinessException.class)
    public Result<Void> handleBusinessException(BusinessException e) {
        log.error("业务异常: {}", e.getMessage());
        return Result.error(e.getCode(), e.getMessage());
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public Result<Void> handleValidationException(MethodArgumentNotValidException e) {
        String message = e.getBindingResult().getFieldErrors().stream()
                .map(FieldError::getDefaultMessage)
                .collect(Collectors.joining(", "));
        log.error("参数校验异常: {}", message);
        return Result.error(400, message);
    }

    @ExceptionHandler(Exception.class)
    public Result<Void> handleException(Exception e) {
        log.error("系统异常: ", e);
        return Result.error(500, "系统异常，请稍后重试");
    }
}
```

---

## 六、代码模板

### 6.1 Entity模板

```java
@Data
@TableName("{{table_name}}")
public class {{EntityName}} {

    @TableId(type = IdType.ASSIGN_ID)
    private Long id;

    // 字段定义

    private LocalDateTime createTime;

    private LocalDateTime updateTime;

    @TableLogic
    private Integer deleted;
}
```

### 6.2 DTO模板

```java
@Data
public class {{EntityName}}CreateDTO {

    @NotBlank(message = "名称不能为空")
    @Length(max = 50, message = "名称长度不能超过50")
    private String name;

    @NotNull(message = "状态不能为空")
    private Integer status;
}
```

### 6.3 VO模板

```java
@Data
public class {{EntityName}}VO {

    private Long id;

    private String name;

    private Integer status;

    private LocalDateTime createTime;
}
```

### 6.4 Mapper模板

```java
@Mapper
public interface {{EntityName}}Mapper extends BaseMapper<{{EntityName}}> {

    // 自定义查询方法
}
```

### 6.5 Service接口模板

```java
public interface {{EntityName}}Service {

    /**
     * 根据ID查询
     */
    {{EntityName}}VO getById(Long id);

    /**
     * 分页查询
     */
    PageResult<{{EntityName}}VO> list({{EntityName}}QueryDTO query);

    /**
     * 创建
     */
    Long create({{EntityName}}CreateDTO dto);

    /**
     * 更新
     */
    void update(Long id, {{EntityName}}UpdateDTO dto);

    /**
     * 删除
     */
    void delete(Long id);
}
```

### 6.6 Service实现模板

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class {{EntityName}}ServiceImpl implements {{EntityName}}Service {

    private final {{EntityName}}Mapper {{entityName}}Mapper;

    @Override
    public {{EntityName}}VO getById(Long id) {
        {{EntityName}} entity = {{entityName}}Mapper.selectById(id);
        if (entity == null) {
            throw new BusinessException("数据不存在");
        }
        return BeanUtil.copyProperties(entity, {{EntityName}}VO.class);
    }

    @Override
    public PageResult<{{EntityName}}VO> list({{EntityName}}QueryDTO query) {
        Page<{{EntityName}}> page = new Page<>(query.getPage(), query.getSize());
        LambdaQueryWrapper<{{EntityName}}> wrapper = new LambdaQueryWrapper<>();
        // 添加查询条件
        Page<{{EntityName}}> result = {{entityName}}Mapper.selectPage(page, wrapper);
        List<{{EntityName}}VO> list = result.getRecords().stream()
                .map(e -> BeanUtil.copyProperties(e, {{EntityName}}VO.class))
                .collect(Collectors.toList());
        return PageResult.of(list, result.getTotal(), query.getPage(), query.getSize());
    }

    @Override
    @Transactional(rollbackFor = Exception.class)
    public Long create({{EntityName}}CreateDTO dto) {
        {{EntityName}} entity = BeanUtil.copyProperties(dto, {{EntityName}}.class);
        {{entityName}}Mapper.insert(entity);
        return entity.getId();
    }

    @Override
    @Transactional(rollbackFor = Exception.class)
    public void update(Long id, {{EntityName}}UpdateDTO dto) {
        {{EntityName}} entity = {{entityName}}Mapper.selectById(id);
        if (entity == null) {
            throw new BusinessException("数据不存在");
        }
        BeanUtil.copyProperties(dto, entity);
        {{entityName}}Mapper.updateById(entity);
    }

    @Override
    @Transactional(rollbackFor = Exception.class)
    public void delete(Long id) {
        {{entityName}}Mapper.deleteById(id);
    }
}
```

### 6.7 Controller模板

```java
@Slf4j
@RestController
@RequestMapping("/api/v1/{{entity_name}}s")
@Tag(name = "{{entity_name}}管理", description = "{{entity_name}}相关接口")
@RequiredArgsConstructor
public class {{EntityName}}Controller {

    private final {{EntityName}}Service {{entityName}}Service;

    @GetMapping("/{id}")
    @Operation(summary = "查询{{entity_name}}详情")
    public Result<{{EntityName}}VO> getById(@PathVariable Long id) {
        return Result.success({{entityName}}Service.getById(id));
    }

    @GetMapping
    @Operation(summary = "分页查询{{entity_name}}列表")
    public Result<PageResult<{{EntityName}}VO>> list({{EntityName}}QueryDTO query) {
        return Result.success({{entityName}}Service.list(query));
    }

    @PostMapping
    @Operation(summary = "创建{{entity_name}}")
    public Result<Long> create(@Valid @RequestBody {{EntityName}}CreateDTO dto) {
        return Result.success({{entityName}}Service.create(dto));
    }

    @PutMapping("/{id}")
    @Operation(summary = "更新{{entity_name}}")
    public Result<Void> update(@PathVariable Long id, @Valid @RequestBody {{EntityName}}UpdateDTO dto) {
        {{entityName}}Service.update(id, dto);
        return Result.success();
    }

    @DeleteMapping("/{id}")
    @Operation(summary = "删除{{entity_name}}")
    public Result<Void> delete(@PathVariable Long id) {
        {{entityName}}Service.delete(id);
        return Result.success();
    }
}
```

---

## 七、Dockerfile

```dockerfile
FROM openjdk:17-jdk-slim

LABEL maintainer="example@example.com"

WORKDIR /app

COPY target/{{service_name}}.jar app.jar

ENV TZ=Asia/Shanghai
ENV JAVA_OPTS="-Xms512m -Xmx1024m -XX:+UseG1GC"

EXPOSE 8080

ENTRYPOINT ["sh", "-c", "java $JAVA_OPTS -jar app.jar"]
```