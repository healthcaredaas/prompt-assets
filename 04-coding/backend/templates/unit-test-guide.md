# 单元测试指南

> 版本: v1.0 | 最后更新: 2026-02-26

本文档定义单元测试的编写规范和最佳实践。

---

## 一、测试原则

### 1.1 FIRST原则

| 原则 | 说明 |
|------|------|
| F (Fast) | 测试要快速执行 |
| I (Independent) | 测试之间相互独立 |
| R (Repeatable) | 测试结果可重复 |
| S (Self-validating) | 测试自动验证结果 |
| T (Timely) | 测试代码及时编写 |

### 1.2 测试覆盖率

| 类型 | 目标覆盖率 |
|------|------------|
| 行覆盖率 | > 80% |
| 分支覆盖率 | > 70% |
| 方法覆盖率 | > 90% |

---

## 二、测试命名

### 2.1 测试类命名

```java
// 被测试类 + Test
public class UserServiceTest {
}

public class UserControllerTest {
}
```

### 2.2 测试方法命名

```java
// 方法名_测试场景_预期结果
@Test
public void getUserById_用户存在_返回用户信息() {
}

@Test
public void getUserById_用户不存在_抛出异常() {
}

@Test
public void createUser_用户名已存在_抛出异常() {
}
```

---

## 三、测试结构

### 3.1 AAA模式

```java
@Test
public void testCreateUser() {
    // Arrange (Given): 准备测试数据
    UserCreateDTO dto = new UserCreateDTO();
    dto.setUsername("test");
    dto.setPassword("Test@123");

    // Act (When): 执行测试方法
    Long userId = userService.create(dto);

    // Assert (Then): 验证结果
    assertNotNull(userId);
    assertTrue(userId > 0);
}
```

### 3.2 BDD模式

```java
@Test
public void testCreateUser() {
    // Given: 准备测试数据
    UserCreateDTO dto = new UserCreateDTO();
    dto.setUsername("test");
    dto.setPassword("Test@123");

    // When: 执行测试方法
    Long userId = userService.create(dto);

    // Then: 验证结果
    assertNotNull(userId);
    assertTrue(userId > 0);
}
```

---

## 四、测试框架

### 4.1 依赖配置

```xml
<dependencies>
    <!-- JUnit 5 -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- Mockito -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-core</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-junit-jupiter</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- Spring Boot Test -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- H2 Database -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### 4.2 测试配置

```yaml
# application-test.yml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
    username: sa
    password:
  jpa:
    hibernate:
      ddl-auto: create-drop
    show-sql: true
```

---

## 五、测试示例

### 5.1 Service层测试

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserMapper userMapper;

    @InjectMocks
    private UserServiceImpl userService;

    @Test
    void getUserById_用户存在_返回用户信息() {
        // Given
        Long userId = 1L;
        User user = new User();
        user.setId(userId);
        user.setUsername("test");
        when(userMapper.selectById(userId)).thenReturn(user);

        // When
        UserVO result = userService.getById(userId);

        // Then
        assertNotNull(result);
        assertEquals(userId, result.getId());
        assertEquals("test", result.getUsername());
        verify(userMapper, times(1)).selectById(userId);
    }

    @Test
    void getUserById_用户不存在_抛出异常() {
        // Given
        Long userId = 1L;
        when(userMapper.selectById(userId)).thenReturn(null);

        // When & Then
        assertThrows(BusinessException.class, () -> userService.getById(userId));
        verify(userMapper, times(1)).selectById(userId);
    }

    @Test
    void createUser_用户名已存在_抛出异常() {
        // Given
        UserCreateDTO dto = new UserCreateDTO();
        dto.setUsername("test");
        dto.setPassword("Test@123");
        when(userMapper.selectByUsername("test")).thenReturn(new User());

        // When & Then
        assertThrows(BusinessException.class, () -> userService.create(dto));
        verify(userMapper, times(1)).selectByUsername("test");
        verify(userMapper, never()).insert(any());
    }

    @Test
    void createUser_正常创建_返回用户ID() {
        // Given
        UserCreateDTO dto = new UserCreateDTO();
        dto.setUsername("test");
        dto.setPassword("Test@123");
        when(userMapper.selectByUsername("test")).thenReturn(null);
        when(userMapper.insert(any())).thenAnswer(invocation -> {
            User user = invocation.getArgument(0);
            user.setId(1L);
            return 1;
        });

        // When
        Long userId = userService.create(dto);

        // Then
        assertNotNull(userId);
        assertEquals(1L, userId);
        verify(userMapper, times(1)).selectByUsername("test");
        verify(userMapper, times(1)).insert(any());
    }
}
```

### 5.2 Controller层测试

```java
@SpringBootTest
@AutoConfigureMockMvc
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    void getById_用户存在_返回用户信息() throws Exception {
        // Given
        Long userId = 1L;
        UserVO user = new UserVO();
        user.setId(userId);
        user.setUsername("test");
        when(userService.getById(userId)).thenReturn(user);

        // When & Then
        mockMvc.perform(get("/api/v1/users/{id}", userId))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.code").value(200))
                .andExpect(jsonPath("$.data.id").value(userId))
                .andExpect(jsonPath("$.data.username").value("test"));
    }

    @Test
    void create_正常创建_返回用户ID() throws Exception {
        // Given
        Long userId = 1L;
        UserCreateDTO dto = new UserCreateDTO();
        dto.setUsername("test");
        dto.setPassword("Test@123");
        when(userService.create(any())).thenReturn(userId);

        // When & Then
        mockMvc.perform(post("/api/v1/users")
                        .contentType(MediaType.APPLICATION_JSON)
                        .content(objectMapper.writeValueAsString(dto)))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.code").value(200))
                .andExpect(jsonPath("$.data").value(userId));
    }
}
```

### 5.3 Repository层测试

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    void findByUsername_用户存在_返回用户() {
        // Given
        User user = new User();
        user.setUsername("test");
        user.setPassword("Test@123");
        userRepository.save(user);

        // When
        Optional<User> result = userRepository.findByUsername("test");

        // Then
        assertTrue(result.isPresent());
        assertEquals("test", result.get().getUsername());
    }

    @Test
    void findByUsername_用户不存在_返回空() {
        // When
        Optional<User> result = userRepository.findByUsername("notexist");

        // Then
        assertFalse(result.isPresent());
    }
}
```

---

## 六、测试技巧

### 6.1 Mock静态方法

```java
@Test
void testStaticMethod() {
    try (MockedStatic<UserUtil> mockedStatic = mockStatic(UserUtil.class)) {
        // Given
        mockedStatic.when(() -> UserUtil.getCurrentUserId()).thenReturn(1L);

        // When
        Long result = UserUtil.getCurrentUserId();

        // Then
        assertEquals(1L, result);
    }
}
```

### 6.2 测试异常

```java
@Test
void testException() {
    // 方式1：assertThrows
    BusinessException exception = assertThrows(
            BusinessException.class,
            () -> userService.delete(1L)
    );
    assertEquals("用户不存在", exception.getMessage());

    // 方式2：@Test注解
    @Test
    void testException() { ... }
}
```

### 6.3 参数化测试

```java
@ParameterizedTest
@CsvSource({
        "test, Test@123, true",
        "'', Test@123, false",
        "test, '', false"
})
void testCreateUser(String username, String password, boolean expected) {
    UserCreateDTO dto = new UserCreateDTO();
    dto.setUsername(username);
    dto.setPassword(password);

    if (expected) {
        assertDoesNotThrow(() -> userService.create(dto));
    } else {
        assertThrows(Exception.class, () -> userService.create(dto));
    }
}
```

### 6.4 测试私有方法

```java
@Test
void testPrivateMethod() throws Exception {
    // 使用反射
    Method method = UserServiceImpl.class.getDeclaredMethod("encodePassword", String.class);
    method.setAccessible(true);
    String result = (String) method.invoke(userService, "password");
    assertNotNull(result);
}
```

---

## 七、测试最佳实践

### 7.1 避免测试实现细节

```java
// 不推荐：测试实现细节
@Test
void testCreateUser() {
    userService.create(dto);
    verify(userMapper, times(1)).insert(any()); // 测试实现细节
}

// 推荐：测试行为和结果
@Test
void testCreateUser() {
    Long userId = userService.create(dto);
    assertNotNull(userId);
    UserVO user = userService.getById(userId);
    assertEquals(dto.getUsername(), user.getUsername());
}
```

### 7.2 测试边界条件

```java
@Test
void testCreateUser_用户名为空() {
    dto.setUsername(null);
    assertThrows(Exception.class, () -> userService.create(dto));
}

@Test
void testCreateUser_用户名超长() {
    dto.setUsername("a".repeat(51));
    assertThrows(Exception.class, () -> userService.create(dto));
}
```

### 7.3 测试异常场景

```java
@Test
void testCreateUser_数据库异常() {
    when(userMapper.insert(any())).thenThrow(new RuntimeException("数据库异常"));
    assertThrows(Exception.class, () -> userService.create(dto));
}
```

### 7.4 使用测试工具类

```java
public class TestUtil {

    public static UserCreateDTO createUserDTO() {
        UserCreateDTO dto = new UserCreateDTO();
        dto.setUsername("test");
        dto.setPassword("Test@123");
        return dto;
    }

    public static User createUser() {
        User user = new User();
        user.setId(1L);
        user.setUsername("test");
        return user;
    }
}
```