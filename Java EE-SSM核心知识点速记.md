# Java EE（SSM）核心知识点速记

> 一份图看完 SSM 三大框架的核心概念。配合《整理版简答题》使用效果更佳。

---

## 🧭 SSM 全景图

```
┌─────────────────────────────────────────────────┐
│                   SSM 框架集                      │
├───────────────┬───────────────┬─────────────────┤
│    Spring     │  Spring MVC   │    MyBatis      │
│  (大管家)      │  (接待员)      │   (数据库专员)    │
├───────────────┼───────────────┼─────────────────┤
│ IoC / DI     │ 请求分发/MVC   │ SQL 映射        │
│ AOP          │ 拦截器         │ 动态 SQL        │
│ 事务管理      │ 数据绑定       │ Mapper 接口     │
└───────────────┴───────────────┴─────────────────┘
```

---

## 一、Spring（大管家——管对象、管事务）

### 🔑 IoC（控制反转）

> **谁控制谁？** 把对象创建和依赖管理的控制权从程序员交给 Spring 容器。

```
传统方式：自己 new 对象 → new Service(new Dao())    ❌ 紧耦合
IoC 方式：容器创建好给我 → @Autowired Service        ✅ 松耦合
```

- Spring 是一个**轻量级容器**，负责生产、管理、维护对象实例
- 程序中不需要 `new`，直接从 Spring 容器中获取

### 🔑 DI（依赖注入）

> IoC 的**具体实现方式**——容器把依赖"注入"给对象。

| 注入方式 | 怎么做 |
|----------|--------|
| 构造器注入 | 通过构造方法传入 `public A(B b) { this.b = b; }` |
| Setter 注入 | 通过 set 方法设值 `public void setB(B b) { this.b = b; }` |
| **注解注入** ⭐ | `@Autowired` / `@Resource` 自动装配 |

### 🔑 AOP（面向切面编程）

> 把**横切关注点**（日志、事务、权限）从业务代码中抽离。

```
业务方法执行前 → 前置通知 (Before)
业务方法执行后 → 后置通知 (AfterReturning)
业务方法环绕   → 环绕通知 (Around)   ← 最强大
业务方法抛异常 → 异常通知 (AfterThrowing)
最终执行      → 最终通知 (After)
```

| 概念 | 一句话 |
|------|--------|
| **Join Point** | 程序中的执行点（方法调用等） |
| **Pointcut**（切点）⭐ | **匹配规则**——指定哪些方法要被增强 |
| **Advice**（通知） | **增强逻辑**——在切点处干什么 |
| **Aspect**（切面） | 切点 + 通知 = 切面 |

---

## 二、Spring MVC（接待员——管请求、管路由）

### 🔑 工作流程（6 步口诀）

```
请求来 → DispatcherServlet 统一接收
       → HandlerMapping 找 Controller
       → HandlerAdapter 调方法
       → Controller 返回 ModelAndView
       → ViewResolver 解析视图
       → 渲染响应
```

### 🔑 五大核心处理器

| 处理器 | 一句话 | 类比 |
|--------|--------|------|
| **DispatcherServlet** | 总调度，所有请求入口 | 前台 |
| **HandlerMapping** | 根据 URL 找 Controller | 导航 |
| **HandlerAdapter** | 调用 Controller 方法 | 执行者 |
| **ViewResolver** | 视图名 → 真实页面 | 模板引擎 |
| **HttpMessageConverter** | 对象 → JSON（默认用 Jackson） | 翻译官 |

### 🔑 拦截器（Interceptor）

> 在 Controller **前后**插入逻辑——权限校验、日志、性能监控。

```
请求 → [拦截器 preHandle] → Controller → [拦截器 postHandle] → 视图 → [拦截器 afterCompletion]
```

| vs Filter | Interceptor | Filter |
|-----------|-------------|--------|
| 归属 | Spring MVC | Servlet 规范 |
| 能访问 Spring Bean？ | ✅ 能 | ❌ 不能 |
| 工作位置 | Controller 前后 | DispatcherServlet 之前 |

### 🔑 数据绑定注解速记

| 注解 | 绑定来源 | 示例 |
|------|----------|------|
| `@RequestParam` | URL 参数 `?name=xxx` | `(@RequestParam String name)` |
| `@PathVariable` | URL 路径 `/user/{id}` | `(@PathVariable Long id)` |
| `@ModelAttribute` | 表单多个字段 → 对象 | `(@ModelAttribute User user)` |
| `@RequestBody` | 请求体 JSON → 对象 | `(@RequestBody User user)` |

---

## 三、MyBatis（数据库专员——管 SQL、管映射）

### 🔑 核心架构

```
Mapper 接口（定义方法）
    ↕ 绑定（namespace + id）
XML 映射文件（写 SQL）
    ↕ 执行
数据库
```

### 🔑 开发流程（6 步）

```
① 导依赖（mybatis.jar + 数据库驱动）
② 写 mybatis-config.xml（数据源 + 映射文件位置）
③ 写实体类（POJO）
④ 写 Mapper 接口（方法定义）
⑤ 写 XxxMapper.xml（SQL 语句，namespace=接口全限定名）
⑥ SqlSession 执行测试
```

### 🔑 Mapper 接口原理

> MyBatis 通过 **JDK 动态代理** 为 Mapper 接口生成代理对象。
> 调用接口方法 → 自动找到 XML 中对应 SQL → 执行 → 返回结果。

### 🔑 四大 SQL 标签

| 标签 | 用途 |
|------|------|
| `<select>` | 查询 |
| `<insert>` | 插入 |
| `<update>` | 更新 |
| `<delete>` | 删除 |

### 🔑 动态 SQL（高频考点）

> 根据条件**动态拼接** SQL 语句，灵活不写死。

| 标签 | 作用 | 典型场景 |
|------|------|----------|
| `<if test="条件">` | 条件成立才拼接 | 非空判断 |
| `<where>` | 自动处理 WHERE + 去掉多余 AND/OR | 多条件查询 |
| `<set>` | 自动处理 UPDATE SET + 去掉多余逗号 | 动态更新 |
| `<foreach>` | 遍历集合 | `IN` 查询、批量插入 |
| `<choose>/<when>/<otherwise>` | 多选一 | 类似 switch-case |

**经典示例**——多条件查询：
```xml
<select id="findUsers" resultType="User">
    SELECT * FROM user
    <where>
        <if test="name != null">AND name = #{name}</if>
        <if test="age != null">AND age = #{age}</if>
    </where>
</select>
```

---

## 四、SSM 整合（三合一）

### 🔑 整合核心——Spring 如何接管 MyBatis

| 关键类 | 作用 |
|--------|------|
| `SqlSessionFactoryBean` | 替代 `SqlSessionFactoryBuilder`，在 Spring 中创建 `SqlSessionFactory` |
| `MapperScannerConfigurer` ⭐ | **批量扫描** Mapper 接口包，自动注册为 Spring Bean → 可直接 `@Autowired` |

### 🔑 整合 8 步骤

```
① 引入依赖（Spring + SpringMVC + MyBatis + 驱动 + 连接池）
② 配置 applicationContext.xml（数据源、事务、SqlSessionFactory）
③ 配置 mybatis-config.xml（或合并到 Spring 配置）
④ 配置 spring-mvc.xml（或 dispatcherServlet.xml）
⑤ 写实体类 + Mapper 接口 + XML 映射文件
⑥ 写 Service 层（接口 + 实现类，@Service + @Autowired）
⑦ 写 Controller 层（@Controller + @RequestMapping）
⑧ web.xml 配置 DispatcherServlet + 加载 Spring 配置
```

### 🔑 三层调用链

```
@Controller  →  @Service  →  @Autowired Mapper  →  XML SQL  →  DB
 (控制层)       (业务层)        (数据层)            (映射文件)
```

---

## 附录：易错点提醒

| 易错点 | 正确 |
|--------|------|
| ❌ 动态 SQL 是 Spring 的特性 | ✅ 动态 SQL 是 **MyBatis** 的特性 |
| ❌ Mapper 接口需要实现类 | ✅ Mapper 接口**不需要**实现类，MyBatis 用动态代理自动生成 |
| ❌ IoC 和 DI 是一回事 | ✅ IoC 是**思想**（控制反转），DI 是**实现方式**（依赖注入） |
| ❌ `MapperFactoryBean` 是唯一方式 | ✅ 更常用 `MapperScannerConfigurer` 批量扫描 |
| ❌ Jackson 写成 JaskSon | ✅ **Jackson**，Spring MVC 默认 JSON 转换器 |
| ❌ Thymeleaf 写成 Thyemleaf | ✅ **Thymeleaf**，现代 Spring Boot 推荐模板引擎 |

---

*本速记配合《Java EE 复习材料-简答题-整理版》使用，覆盖 SSM 全部核心考点。*
