# Java EE 复习材料 —— 简答题（整理版）

> 共 17 题，按 **SSM 概述 → Spring → Spring MVC → Spring AOP → MyBatis → SSM 整合** 六大主题重新编排。  
> 原题中重复/相似的题目已合并，错误已修正（修正处标注 ~~删除线~~ 或 📝 说明）。

---

## 一、SSM 框架概述

### 1. SSM 框架的含义是什么？说明它与 Java EE 的关系。

> 📝 原题 9 与 17 完全重复，已合并。

**参考答案：**

SSM 框架集是 **Spring + Spring MVC + MyBatis** 三个开源框架组成的简称。它们是在 Java EE 基础上创建的、用于快速构建用户应用系统的软件框架集。SSM 常作为数据源较简单的 Web 项目的开发框架。

- **Spring**：负责对象管理（IoC）和事务、AOP 等
- **Spring MVC**：负责 Web 层的请求分发与 MVC 架构
- **MyBatis**：负责数据持久化层，简化 JDBC 操作

---

## 二、Spring 核心 —— IoC 与 DI

### 2. 对 Spring 中使用 IoC 的理解。

> 📝 原题 8。

**参考答案：**

Spring 负责 `IService`、`IDao` 等对象的实例化，并自动设置彼此的依赖关系。程序中无需手动 `new` 任何 DAO、Service 对象，而是直接从 Spring 容器中获取。Spring 是一个**轻量级容器**，起到**生产、管理、维护这些对象实例**的作用。

核心一句话：**控制反转（IoC）就是将对象创建和依赖管理的控制权交给 Spring 容器，而非程序员手动控制。**

---

### 3. Spring 框架的依赖注入（DI）是什么？举例说明其应用场景。

> 📝 原题 4。

**参考答案：**

依赖注入（Dependency Injection）是一种设计模式，它允许对象在运行时**被动接收**依赖项，而不是自己主动创建。

在 Spring 中主要有三种注入方式：

| 注入方式 | 说明 | 示例 |
|----------|------|------|
| **构造器注入** | 通过构造方法传入依赖 | `public UserService(UserDao dao) { this.dao = dao; }` |
| **Setter 注入** | 通过 setter 方法注入 | `public void setUserDao(UserDao dao) { this.dao = dao; }` |
| **注解注入** | 使用 `@Autowired` / `@Resource` | `@Autowired private UserDao userDao;` |

**应用场景**：Controller 层需要调用 Service 层，Service 层需要调用 Dao 层——所有依赖通过 Spring 容器自动注入，各层之间只依赖接口而非具体实现，实现松耦合。

---

## 三、Spring MVC

### 4. Spring MVC 的工作流程。

> 📝 原题 5。

**参考答案：**

```
用户请求 → DispatcherServlet（前端控制器）
         → HandlerMapping（处理器映射器）→ 找到对应的 Controller
         → HandlerAdapter（处理器适配器）→ 调用 Controller 方法
         → Controller 返回 ModelAndView
         → ViewResolver（视图解析器）→ 解析为具体视图
         → 渲染页面 → 响应给用户
```

| 步骤 | 组件 | 作用 |
|------|------|------|
| 1 | DispatcherServlet | 统一接收所有请求，作为中央调度器 |
| 2 | HandlerMapping | 根据请求 URL 找到对应的 Handler（Controller） |
| 3 | HandlerAdapter | 负责调用具体的 Controller 方法 |
| 4 | Controller | 处理业务逻辑，返回 ModelAndView |
| 5 | ViewResolver | 将逻辑视图名解析为实际视图（JSP/Thymeleaf 等） |
| 6 | View 渲染 | 将 Model 数据填充到视图中，生成 HTML 返回客户端 |

---

### 5. Spring MVC 核心处理器有哪些？

> 📝 原题 13。原文格式混乱且有拼写错误（JaskSon → Jackson，Thyemleaf → Thymeleaf），已修正。

**参考答案：**

| 核心处理器 | 作用 |
|-----------|------|
| **DispatcherServlet**（前端控制器） | 中央调度器，统一分发请求，如 `http://域名:port/controller路径` |
| **HandlerMapping**（处理器映射器） | 解析请求 URL，找到对应的 Controller |
| **HandlerAdapter**（处理器适配器） | 调用 Controller 中具体的方法 |
| **ViewResolver**（视图解析器） | 将 Controller 返回的视图名解析为实际视图页面（如 JSP、Thymeleaf、Freemarker） |
| **HttpMessageConverter**（数据处理器） | 将后台数据转为 JSON 等格式返回，Spring MVC 默认使用 **Jackson** |

---

### 6. Spring MVC 拦截器的作用。

> 📝 原题 2。

**参考答案：**

拦截器（Interceptor）是运行在服务端的程序，主要用于拦截用户请求进行相应处理，即实现对 Controller 请求的**预处理**和**后处理**。

**作用：**
- 在某个动作执行**前**执行特定逻辑（如权限校验、登录检查）
- 在某个动作执行**后**执行特定逻辑（如日志记录、性能监控）
- 在某个动作执行前**阻止**某些代码逻辑的执行（如拦截未登录用户）

> 💡 与 Filter（过滤器）的区别：Interceptor 属于 Spring MVC，能访问 Spring 容器中的 Bean；Filter 属于 Servlet 规范，工作在 DispatcherServlet 之前。

---

### 7. Spring 框架中的数据绑定过程。

> 📝 原题 11。

**参考答案：**

Spring 的数据绑定过程是将 **HTTP 请求参数自动绑定到 Controller 方法的参数上**。

| 注解 | 作用 |
|------|------|
| `@RequestParam` | 绑定单个请求参数到方法参数 |
| `@PathVariable` | 绑定 URL 路径变量（RESTful 风格） |
| `@ModelAttribute` | 绑定多个参数到实体对象 |
| `@RequestBody` | 绑定请求体（JSON）到对象 |

数据绑定还涉及**类型转换**：如将请求中的字符串 `"2024-01-01"` 自动转为 `Date` 类型，或将 `"123"` 转为 `int`。

---

## 四、Spring AOP

### 8. Spring AOP 中切点（Pointcut）的概念及其作用。

> 📝 原题 16。

**参考答案：**

**切点（Pointcut）** 是 Spring AOP 中用于定义**哪些连接点会被通知（Advice）影响**的表达式。

| 概念 | 说明 |
|------|------|
| **Join Point**（连接点） | 程序执行过程中的某个点，如方法调用、异常抛出 |
| **Pointcut**（切点） | 匹配连接点的表达式，精确指定哪些方法需要被增强 |
| **Advice**（通知） | 在切点处执行的增强逻辑（前置/后置/环绕/异常/返回） |

**作用**：通过切点表达式（如 `execution(* com.xxx.service.*.*(..))`），可以精确地指定哪些类的哪些方法需要被增强，实现**非侵入式**的功能扩展（如日志、事务、权限控制）。

---

## 五、MyBatis

### 9. MyBatis 映射文件 XML 开发的基本流程。

> 📝 原题 7。

**参考答案：**

| 步骤 | 操作 |
|------|------|
| ① | 创建 XML 映射文件，在 `<mapper namespace="接口全限定名">` 中绑定 Mapper 接口，编写对应的 SQL 语句 |
| ② | 创建 Mapper（数据层）接口，方法名与 XML 中的 `id` 对应 |
| ③ | 创建 Service（业务层）接口及实现类，调用 Mapper 方法 |
| ④ | 创建 Controller（控制层），调用 Service 方法处理请求 |

**核心对应关系：**
```
Controller → Service → Mapper接口 → XML映射文件中的SQL
```

---

### 10. MyBatis 框架中 Mapper 接口的作用。

> 📝 原题 15。

**参考答案：**

Mapper 接口用于**定义 SQL 映射语句**。MyBatis 会根据接口的**方法名**和**参数类型**，自动找到对应的 XML 映射文件中的 SQL 语句，并执行相应的数据库操作。

**核心机制**：MyBatis 通过 JDK 动态代理为 Mapper 接口生成代理对象，调用接口方法时实际执行 XML 中绑定的 SQL。

---

### 11. MyBatis 中动态 SQL 的概念及应用场景。

> 📝 原题 3、6、10 内容高度重复，已合并。  
> 📝 原题 6 标题误写为"Spring框架中动态SQL"，实际动态 SQL 是 MyBatis 的特性，已修正。

**参考答案：**

动态 SQL 是 MyBatis 的重要特性，允许根据**不同的条件动态生成不同的 SQL 语句**，提高 SQL 的灵活性和复用性。

**常用动态 SQL 标签：**

| 标签 | 作用 |
|------|------|
| `<if test="条件">` | 条件判断，满足条件时拼接 SQL 片段 |
| `<choose> / <when> / <otherwise>` | 多分支选择（类似 Java 的 switch） |
| `<where>` | 自动处理 WHERE 关键字，去除多余的 AND/OR |
| `<set>` | 自动处理 UPDATE 中的 SET，去除多余逗号 |
| `<trim>` | 更灵活的截取/添加前缀后缀 |
| `<foreach>` | 遍历集合，常用于 `IN` 查询 |

**应用场景：**
- 多条件组合查询（如用户可按姓名/年龄/性别任意组合搜索）
- 批量插入/更新/删除
- 根据参数有无动态决定是否添加某个查询条件

---

### 12. MyBatis 的安装/集成过程。

> 📝 原题 14。

**参考答案：**

| 步骤 | 操作 |
|------|------|
| ① 创建 Web 项目 | 建立 Java Web 项目结构 |
| ② 添加依赖 | 导入 `mybatis-3.x.x.jar` 核心包、`lib/` 下的依赖包及数据库驱动（如 `mysql-connector-java`） |
| ③ 创建核心配置 | 在 `src` 下创建 `mybatis-config.xml`，配置数据库环境（数据源）和映射文件位置 |
| ④ 创建持久化类 | 编写实体类（POJO），字段与数据库表列对应 |
| ⑤ 创建映射文件 | 编写 `XxxMapper.xml`，配置 `<select>`、`<insert>`、`<update>`、`<delete>` 等 SQL |
| ⑥ 编写测试类 | 通过 `SqlSession` 执行 SQL 验证配置是否正确 |

---

## 六、SSM 框架整合

### 13. Spring 与 MyBatis 如何进行集成？

> 📝 原题 1。原文"讲"→"将"，"SQl"→"SQL"，已修正。

**参考答案：**

Spring 与 MyBatis 的集成主要依赖 Spring 提供的两个核心类：

| 核心类 | 作用 |
|--------|------|
| **`SqlSessionFactoryBean`** | 用于创建 `SqlSessionFactory`（MyBatis 的核心工厂），替代 MyBatis 原生的 `SqlSessionFactoryBuilder` |
| **`MapperScannerConfigurer`** ⭐ | 自动扫描指定包下的 Mapper 接口，将其注册为 Spring 容器中的 Bean，从而可以直接通过 `@Autowired` 注入使用 |

> 📝 原答案写的是 `MapperFactoryBean`，实际开发中更常用 **`MapperScannerConfigurer`** 进行批量扫描，无需逐个配置。两者都可实现 Mapper 接口的 Spring 托管。

**集成效果**：MyBatis 的 Mapper 接口可以直接在 Spring 的 Service 层通过 `@Autowired` 注入使用，无需手动创建 `SqlSession`。

---

### 14. SSM 框架整合的主要步骤。

> 📝 原题 12。

**参考答案：**

| 步骤 | 操作 |
|------|------|
| ① | 引入所需依赖（Spring、Spring MVC、MyBatis、数据库驱动、连接池等） |
| ② | 配置 Spring 核心配置文件 `applicationContext.xml`（数据源、事务、SqlSessionFactory 等） |
| ③ | 配置 MyBatis 核心文件 `mybatis-config.xml`（也可合并到 Spring 配置中） |
| ④ | 配置 Spring MVC 核心文件 `dispatcherServlet.xml`（或 `spring-mvc.xml`） |
| ⑤ | 创建实体类（POJO）、Mapper 接口及对应的 XML 映射文件 |
| ⑥ | 创建 Service 层接口及实现类 |
| ⑦ | 创建 Controller 层，处理前端请求 |
| ⑧ | 在 `web.xml` 中配置 `DispatcherServlet` 前端控制器，并加载 Spring 配置文件 |

---

## 附录：原题整理对照表

| 整理后题号 | 主题 | 原题号 | 备注 |
|:---------:|------|:-----:|------|
| 1 | SSM 框架含义 | 9、17 | 两题完全重复，已合并 |
| 2 | Spring IoC | 8 | — |
| 3 | Spring DI | 4 | — |
| 4 | Spring MVC 工作流程 | 5 | — |
| 5 | Spring MVC 核心处理器 | 13 | 修正拼写错误（JaskSon→Jackson，Thyemleaf→Thymeleaf） |
| 6 | Spring MVC 拦截器 | 2 | — |
| 7 | Spring 数据绑定 | 11 | — |
| 8 | Spring AOP 切点 | 16 | — |
| 9 | MyBatis 映射文件开发 | 7 | — |
| 10 | MyBatis Mapper 接口 | 15 | — |
| 11 | MyBatis 动态 SQL | 3、6、10 | 三题内容高度重复，已合并；原题6标题"Spring框架中动态SQL"修正为"MyBatis" |
| 12 | MyBatis 安装过程 | 14 | — |
| 13 | Spring 与 MyBatis 集成 | 1 | 修正"讲"→"将"；补充 MapperScannerConfigurer 说明 |
| 14 | SSM 整合步骤 | 12 | — |

---

*整理日期：2026-07-07 | 原17题 → 整理后14题（合并3组重复/相似题）*
