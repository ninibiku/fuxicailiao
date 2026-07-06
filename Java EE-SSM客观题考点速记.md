# Java EE（SSM）客观题考点速记

> 覆盖填空、选择、判断等客观题型高频考点，按 **MyBatis → Spring → Spring MVC → AOP → SSM 整合** 五大模块编排。  
> 标注 ⭐ 为极高频考点，标注 📝 为易错/易混点。

---

## 一、MyBatis 标签与配置

### 1.1 四大 SQL 语句标签 ⭐

| 标签 | 用途 |
|------|------|
| `<select>` | 查询 |
| `<insert>` | 插入 |
| `<update>` | 更新 |
| `<delete>` | 删除 |

### 1.2 动态 SQL 标签 ⭐

| 标签 | 作用 |
|------|------|
| `<if test="条件">` | 条件判断，满足时拼接 SQL 片段 |
| `<choose>` / `<when>` / `<otherwise>` | 多分支选择（类似 Java switch-case） |
| `<where>` | 自动处理 WHERE 关键字，去除多余 AND/OR |
| `<set>` | 自动处理 UPDATE SET，去除多余逗号 |
| `<foreach>` | 遍历集合，通过 `collection` 属性指定集合类型 |
| `<trim>` | 灵活的字符串截取/添加前缀后缀 |

### 1.3 模糊查询 📝

- MyBatis 中模糊查询使用 **`LIKE`** 关键字
- 写法示例：`WHERE name LIKE CONCAT('%', #{keyword}, '%')`

### 1.4 核心配置标签

| 标签 | 作用 |
|------|------|
| `<environments>` | 配置数据库环境（数据源、事务管理器） |
| `<typeAliases>` | 定义类型别名，简化映射文件中全限定类名的书写 |
| `<mappers>` | 注册映射文件的位置 |

### 1.5 Mapper 命名空间 ⭐

- Mapper 映射文件的 `<mapper>` 标签通过 **`namespace`** 属性绑定对应的 Mapper 接口
- 示例：`<mapper namespace="com.xxx.dao.UserMapper">`

> 📝 `namespace` 必须与 Mapper 接口的**全限定类名**一致。

### 1.6 指定映射文件位置的 3 种方法 📝

| 方法 | 说明 |
|------|------|
| ① 本地类路径引入 | `classpath:com/xxx/mapper/UserMapper.xml` |
| ② 本地文件路径引入 | `file:///绝对路径/UserMapper.xml` |
| ③ 接口类引入 | 直接引用 Mapper 接口类，MyBatis 自动查找同名 XML |

---

## 二、Spring 核心 —— IoC 容器与 Bean

### 2.1 Spring 的核心 ⭐

> Spring 方向的核心是 **Spring IoC 容器**。

- IoC（Inversion of Control）：控制反转
- 容器负责对象的创建、装配、生命周期管理

### 2.2 Spring 配置文件关键参数 📝

| 参数 | 作用 |
|------|------|
| `SqlSessionFactory` | MyBatis 核心工厂，创建 `SqlSession` |
| `SqlSessionTemplate` | `SqlSession` 的线程安全封装，Spring 中替代原生 `SqlSession` |
| `MapperScannerConfigurer` | 自动扫描 Mapper 接口包，注册为 Spring Bean |

> 📝 注意：`SqlSessionTemplate` 是 Spring 整合 MyBatis 时使用的，它线程安全、自动管理事务和关闭 `SqlSession`。

### 2.3 Spring 实例化 Bean 的 3 种方式 ⭐

| 方式 | 说明 |
|------|------|
| ① **构造器实例化** | 通过无参构造方法创建实例（最常用） |
| ② **静态工厂方式实例化** | 调用类的静态方法返回实例 |
| ③ **实例工厂方式实例化** | 先创建工厂 Bean，再调用其方法创建目标 Bean |

### 2.4 Spring 依赖注入的 2 种方式 ⭐

| 注入方式 | 说明 |
|----------|------|
| ① **构造器注入** | 通过构造方法参数注入依赖 |
| ② **设值注入**（Setter 注入） | 通过 setter 方法注入依赖 |

> 💡 实际开发中 + 注解方式：`@Autowired` 自动装配。

### 2.5 Spring 核心注解

| 注解 | 作用 |
|------|------|
| `@Autowired` ⭐ | **自动装配**，按类型注入依赖（属性和方法上均可使用） |
| `@Component` | 将类标记为 Spring 组件（Bean） |
| `@Service` | 标记业务层组件 |
| `@Repository` | 标记数据层组件 |
| `@Controller` | 标记控制层组件 |

### 2.6 Bean 的作用域 —— `prototype` 📝

| 作用域 | 说明 |
|--------|------|
| `singleton`（默认） | 单例，整个容器只有一个实例 |
| **`prototype`** | 原型，每次获取都创建新实例。**Spring 容器只负责创建，不负责销毁** |

> 📝 `prototype` 作用域的 Bean，Spring 容器**只会负责创建**，后续生命周期由调用者管理。

---

## 三、Spring MVC

### 3.1 Spring MVC 各层角色 ⭐

| 角色 | 说明 |
|------|------|
| **前端控制器** | `DispatcherServlet`（默认类名）—— 统一接收所有请求，中央调度 |
| **后端控制器** | **Handler**（处理器/Controller）—— 处理具体业务请求 |
| **视图解析器** | `ViewResolver` —— 根据**逻辑视图名**解析为实际视图 |
| **数据转换器** | `HttpMessageConverter` —— 将返回数据转为 JSON（默认用 Jackson） |

### 3.2 Spring MVC 核心注解

| 注解 | 作用 |
|------|------|
| `@RequestMapping` ⭐ | **映射请求 URL** 到 Controller 方法（可标注在类或方法上） |
| `@RequestBody` ⭐ | 将 **HTTP 请求体**（JSON）数据绑定到方法参数 |
| `@ResponseBody` | 将方法返回值直接写入 HTTP 响应体（返回 JSON 时用） |
| `@RequestParam` | 绑定 URL 查询参数 |
| `@PathVariable` | 绑定 URL 路径变量 |

### 3.3 Spring MVC 需要用户开发的 2 个组件

| 组件 | 说明 |
|------|------|
| ① **处理器（Handler/Controller）** | 处理请求，调用业务层，返回数据和视图 |
| ② **视图（View）** | 展示数据的页面（JSP、Thymeleaf、HTML 等） |

> 📝 其他组件（DispatcherServlet、HandlerMapping、ViewResolver 等）由框架提供，无需手动开发。

### 3.4 视图解析器（ViewResolver）📝

- `ViewResolver` 根据 Controller 返回的**逻辑视图名**解析为实际视图路径
- 示例：返回 `"register"` → 解析为 `/WEB-INF/views/register.jsp`

### 3.5 Spring MVC 返回 JSON 数据的 2 种方式

| 方式 | 说明 |
|------|------|
| ① 返回 **Object 对象** | Spring MVC 自动将对象转为 JSON |
| ② 返回 **Map 集合** | Spring MVC 自动将 Map 转为 JSON |

> 💡 需配合 `@ResponseBody` 或 `@RestController` 注解使用，默认转换器为 **Jackson**。

---

## 四、AOP（面向切面编程）

### 4.1 AOP 常见功能 ⭐

| 功能 | 说明 |
|------|------|
| ① **日志记录** | 方法执行前后自动记录日志 |
| ② **权限验证** | 方法执行前检查用户权限 |
| ③ **事务管理** | 方法执行前后自动开启/提交/回滚事务 |
| ④ **性能监控** | 统计方法执行时间 |

### 4.2 AOP 关键注解（AspectJ）

| 注解 | 作用 |
|------|------|
| `@Aspect` | 标记该类为切面类 |
| `@Pointcut` ⭐ | 定义**切入点表达式**，指定哪些方法需要被增强 |
| `@Before` | 前置通知，目标方法执行前 |
| `@AfterReturning` | 返回通知，目标方法正常执行后 |
| `@AfterThrowing` | 异常通知，目标方法抛出异常后 |
| `@After` | 最终通知，目标方法执行后（无论是否异常） |
| `@Around` | 环绕通知，可控制方法是否执行及前后逻辑 |

### 4.3 AOP XML 配置

| 属性 | 作用 |
|------|------|
| `pointcut-ref` | 指定一个**已经存在的切入点名称**，引用已定义好的切点 |

---

## 五、SSM 整合与 Java EE 分层

### 5.1 SSM 框架职责分工 ⭐

```
Java EE 三层架构：
┌──────────────────────────────────────────┐
│  表示层（Presentation）  ← Spring MVC     │
│  业务层（Business）      ← Spring         │
│  持久层（Persistence）   ← MyBatis        │
└──────────────────────────────────────────┘
```

| 框架 | 实现层级 | 职责 |
|------|----------|------|
| **Spring MVC** | **表示层** | 接收请求、调用控制器、返回视图/数据 |
| **Spring** | **业务层** | IoC 容器管理、事务管理、AOP |
| **MyBatis** | **持久层** | 数据库操作、SQL 映射 |

### 5.2 SSM 整合核心配置文件

| 配置文件 | 作用 |
|----------|------|
| `db.properties` | 数据库连接参数（驱动、URL、用户名、密码） |
| `applicationContext.xml` | Spring 核心配置（数据源、事务、Bean 管理） |
| `mybatis-config.xml` | MyBatis 核心配置（也可合并到 Spring 配置中） |
| `spring-mvc.xml` | Spring MVC 配置（视图解析器、注解驱动、扫描包） |
| `web.xml` | Web 部署描述符（配置 DispatcherServlet） |

### 5.3 SSM 整合需要的 4 个组件

| 组件 | 说明 |
|------|------|
| ① Spring IoC 容器 | 管理所有 Bean 的生命周期和依赖 |
| ② Spring AOP 模块 | 提供声明式事务管理 |
| ③ MyBatis 数据源连接池 | 管理数据库连接 |
| ④ Spring MVC 视图解析器 | 解析视图名到实际视图 |

### 5.4 SSM 调用链 ⭐

```
Controller（控制层）
    ↓ 接收请求，调用 Service
Service（业务层）
    ↓ 调用 Dao/Mapper
Dao / Mapper（数据层）
    ↓ 执行 SQL
数据库
```

| 调用关系 | 说明 |
|----------|------|
| **Controller → Service** | Controller 接收请求，调用 Service 层方法 |
| **Service → Dao** | Service 层通过 `@Autowired` 注入 Dao，调用其方法实现数据库操作 |

---

## 六、代码阅读/补全考点

### 6.1 典型 Controller 代码 ⭐

```java
@Controller
public class RegisterController {
    @RequestMapping("/register")        // 映射 /register 请求
    public String register() {
        return "register";              // 返回逻辑视图名
    }
}
```

**考点提炼：**
- `@Controller`：标记为控制器 Bean
- `@RequestMapping("/register")`：将 `/register` 请求路径映射到 `register()` 方法
- `return "register"`：返回**逻辑视图名**，由 `ViewResolver` 解析为实际视图

### 6.2 典型 Service 代码 ⭐

```java
@Service
public class StudentService {
    @Autowired                          // 自动注入 Dao
    private StudentDao studentDao;

    public User deleteById(int id) {
        return studentDao.deleteById(id);   // 调用 Dao 层方法
    }
}
```

**考点提炼：**
- `@Service`：标记为业务层 Bean
- `@Autowired`：自动装配 `StudentDao`，无需 `new`
- Service 层调用 Dao 层 → 体现 SSM 分层协作

---

## 附录：极高频考点 TOP 10

| 排名 | 考点 | 题型 |
|:----:|------|:----:|
| 1 | Spring IoC 容器是 Spring 的核心 | 选择/填空 |
| 2 | `@Autowired` 自动装配 | 选择/填空 |
| 3 | `@RequestMapping` 映射请求 | 选择/填空 |
| 4 | `DispatcherServlet` 前端控制器 | 选择/填空 |
| 5 | MyBatis 四大 SQL 标签：select/insert/update/delete | 选择/填空 |
| 6 | `<if>` / `<foreach>` 动态 SQL 标签 | 选择/填空 |
| 7 | SSM 三层分工：Spring MVC(表示层)+Spring(业务层)+MyBatis(持久层) | 选择/简答 |
| 8 | Bean 实例化 3 种方式：构造器/静态工厂/实例工厂 | 选择/填空 |
| 9 | `ViewResolver` 根据逻辑视图名解析视图 | 选择/填空 |
| 10 | AOP 四大功能：日志/权限/事务/性能监控 | 多选/简答 |

---

## 附录：易错易混辨析

| 易混点 | 正确辨析 |
|--------|----------|
| ❌ 动态 SQL 是 Spring 的 | ✅ 动态 SQL 是 **MyBatis** 的特性 |
| ❌ `namespace` 写任意名 | ✅ `namespace` 必须是 Mapper 接口的**全限定类名** |
| ❌ `prototype` Bean 由容器管理销毁 | ✅ `prototype` Bean 容器**只负责创建**，不管理销毁 |
| ❌ `@RequestBody` 绑定 URL 参数 | ✅ `@RequestBody` 绑定**请求体**（JSON），`@RequestParam` 才绑定 URL 参数 |
| ❌ Controller 返回 `"register"` 是跳转到 register.jsp 文件 | ✅ 返回的是**逻辑视图名**，由 `ViewResolver` 解析后才得到真实路径 |
| ❌ `SqlSession` 可以直接在 Spring 中使用 | ✅ Spring 中使用 **`SqlSessionTemplate`**（线程安全封装） |
| ❌ `<foreach>` 用 `list` 属性指定集合 | ✅ `<foreach>` 用 **`collection`** 属性指定集合 |

---

*本速记覆盖 Java EE（SSM）客观题全部核心考点，配合简答题整理版 + 核心知识点速记使用，三份材料互为一体。*
