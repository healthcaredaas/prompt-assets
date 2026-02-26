# Java代码规范

> 版本: v1.0 | 最后更新: 2026-02-26

本文档定义Java代码规范，基于《阿里巴巴Java开发手册》。

---

## 一、命名规范

### 1.1 包命名

- 使用小写字母
- 使用点分隔符
- 使用有意义的名称

```java
// 推荐
com.example.user.service
com.example.order.controller

// 不推荐
com.example.UserService
com.example.orderController
```

### 1.2 类命名

- 使用大驼峰命名法（PascalCase）
- 类名使用名词或名词短语
- 接口使用形容词或名词

| 类型 | 后缀 | 示例 |
|------|------|------|
| 实体类 | 无 | User, Order |
| 数据传输对象 | DTO | UserDTO, OrderDTO |
| 视图对象 | VO | UserVO, OrderVO |
| 服务接口 | Service | UserService |
| 服务实现 | ServiceImpl | UserServiceImpl |
| 控制器 | Controller | UserController |
| 数据访问 | Mapper | UserMapper |
| 工具类 | Utils | DateUtils |
| 异常类 | Exception | BusinessException |

### 1.3 方法命名

- 使用小驼峰命名法（camelCase）
- 方法名使用动词或动词短语

| 方法类型 | 前缀 | 示例 |
|----------|------|------|
| 查询单个 | get/getBy | getUser, getUserById |
| 查询列表 | list/listBy | listUsers, listUsersByName |
| 查询数量 | count/countBy | countUsers, countUsersByStatus |
| 新增 | save/add/create | saveUser, addUser |
| 修改 | update/modify | updateUser |
| 删除 | delete/remove | deleteUser |
| 判断 | is/has/can | isActive, hasPermission |

### 1.4 变量命名

- 使用小驼峰命名法（camelCase）
- 变量名使用名词
- 成员变量不加前缀
- 常量使用全大写，下划线分隔

```java
// 推荐
private String userName;
private Integer orderCount;
public static final int MAX_COUNT = 100;

// 不推荐
private String _userName;
private Integer mOrderCount;
public static final int maxCount = 100;
```

---

## 二、代码格式

### 2.1 缩进

- 使用4个空格缩进
- 禁止使用Tab字符

### 2.2 行宽

- 单行字符数不超过120
- 超过需要换行

### 2.3 大括号

- 左大括号前不换行
- 左大括号后换行
- 右大括号前换行
- 右大括号后换行

```java
// 推荐
if (condition) {
    // do something
}

// 不推荐
if (condition)
{
    // do something
}
```

### 2.4 空格

- 运算符两侧需要空格
- 逗号后需要空格
- 冒号后需要空格

```java
int result = a + b * c;
List<String> list = new ArrayList<>();
for (int i = 0; i < 10; i++) {
}
```

---

## 三、注释规范

### 3.1 类注释

```java
/**
 * 用户服务实现类
 *
 * @author author
 * @date 2026-02-26
 */
@Service
public class UserServiceImpl implements UserService {
}
```

### 3.2 方法注释

```java
/**
 * 根据ID查询用户
 *
 * @param id 用户ID
 * @return 用户信息
 * @throws BusinessException 用户不存在时抛出
 */
public User getUserById(Long id) {
}
```

### 3.3 行内注释

```java
// 单行注释
int count = 0; // 初始化计数器

/*
 * 多行注释
 * 第一行
 * 第二行
 */
```

---

## 四、异常处理

### 4.1 异常捕获

```java
// 推荐：明确捕获异常类型
try {
    // do something
} catch (FileNotFoundException e) {
    log.error("文件不存在: {}", fileName, e);
    throw new BusinessException("文件不存在");
}

// 不推荐：捕获所有异常
try {
    // do something
} catch (Exception e) {
    // do nothing
}
```

### 4.2 异常抛出

```java
// 推荐：抛出有意义的异常
if (user == null) {
    throw new BusinessException("用户不存在");
}

// 不推荐：抛出原始异常
if (user == null) {
    throw new RuntimeException("用户不存在");
}
```

### 4.3 异常定义

```java
/**
 * 业务异常
 */
public class BusinessException extends RuntimeException {

    private Integer code;

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

---

## 五、日志规范

### 5.1 日志级别

| 级别 | 说明 | 使用场景 |
|------|------|----------|
| ERROR | 错误日志 | 影响业务运行的错误 |
| WARN | 警告日志 | 需要关注的问题 |
| INFO | 信息日志 | 关键业务流程 |
| DEBUG | 调试日志 | 调试信息，生产环境关闭 |

### 5.2 日志格式

```java
// 推荐：使用占位符
log.info("用户登录成功, userId: {}", userId);

// 不推荐：字符串拼接
log.info("用户登录成功, userId: " + userId);
```

### 5.3 日志内容

```java
// 推荐：包含关键信息
log.info("订单创建成功, orderId: {}, orderNo: {}, userId: {}", orderId, orderNo, userId);

// 不推荐：日志信息不完整
log.info("订单创建成功");
```

---

## 六、单元测试

### 6.1 测试命名

```java
// 测试类命名：被测试类 + Test
public class UserServiceTest {
}

// 测试方法命名：方法名_测试场景_预期结果
@Test
public void getUserById_用户存在_返回用户信息() {
}

@Test
public void getUserById_用户不存在_抛出异常() {
}
```

### 6.2 测试结构

```java
@Test
public void testGetUserById() {
    // Given: 准备测试数据
    Long userId = 1L;
    User expected = new User();
    expected.setId(userId);
    expected.setName("张三");

    // When: 执行测试方法
    User actual = userService.getUserById(userId);

    // Then: 验证结果
    assertNotNull(actual);
    assertEquals(userId, actual.getId());
    assertEquals("张三", actual.getName());
}
```

---

## 七、代码示例

### 7.1 Controller示例

```java
@RestController
@RequestMapping("/api/v1/users")
@Tag(name = "用户管理", description = "用户相关接口")
@RequiredArgsConstructor
public class UserController {

    private final UserService userService;

    @GetMapping("/{id}")
    @Operation(summary = "查询用户详情")
    public Result<UserVO> getById(@PathVariable Long id) {
        return Result.success(userService.getById(id));
    }

    @PostMapping
    @Operation(summary = "创建用户")
    public Result<Long> create(@Valid @RequestBody UserCreateDTO dto) {
        return Result.success(userService.create(dto));
    }

    @PutMapping("/{id}")
    @Operation(summary = "更新用户")
    public Result<Void> update(@PathVariable Long id, @Valid @RequestBody UserUpdateDTO dto) {
        userService.update(id, dto);
        return Result.success();
    }

    @DeleteMapping("/{id}")
    @Operation(summary = "删除用户")
    public Result<Void> delete(@PathVariable Long id) {
        userService.delete(id);
        return Result.success();
    }
}
```

### 7.2 Service示例

```java
@Service
@RequiredArgsConstructor
public class UserServiceImpl implements UserService {

    private final UserMapper userMapper;

    @Override
    public UserVO getById(Long id) {
        User user = userMapper.selectById(id);
        if (user == null) {
            throw new BusinessException("用户不存在");
        }
        return BeanUtil.copyProperties(user, UserVO.class);
    }

    @Override
    @Transactional(rollbackFor = Exception.class)
    public Long create(UserCreateDTO dto) {
        // 检查用户名是否存在
        if (userMapper.selectByUsername(dto.getUsername()) != null) {
            throw new BusinessException("用户名已存在");
        }

        // 创建用户
        User user = BeanUtil.copyProperties(dto, User.class);
        user.setPassword(encodePassword(dto.getPassword()));
        userMapper.insert(user);

        return user.getId();
    }

    private String encodePassword(String password) {
        return BCrypt.hashpw(password, BCrypt.gensalt());
    }
}
```

### 7.3 Entity示例

```java
@Data
@TableName("t_user")
public class User {

    @TableId(type = IdType.ASSIGN_ID)
    private Long id;

    private String username;

    private String password;

    private String nickname;

    private String phone;

    private String email;

    private Integer status;

    private LocalDateTime createTime;

    private LocalDateTime updateTime;

    @TableLogic
    private Integer deleted;
}
```

---

## 八、代码检查工具

### 8.1 Checkstyle配置

```xml
<module name="Checker">
    <module name="LineLength">
        <property name="max" value="120"/>
    </module>
</module>
```

### 8.2 SonarQube规则

- 代码重复率 < 3%
- 代码覆盖率 > 80%
- 无严重漏洞
- 无代码异味