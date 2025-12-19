**苍穹外卖**

# 1.项目概述

## 1.1.项目结构

| **序号** | **名称**     | **说明**                                                     |
| -------- | ------------ | ------------------------------------------------------------ |
| 1        | sky-take-out | maven父工程，统一管理依赖版本，聚合其他子模块                |
| 2        | sky-common   | 子模块，存放公共类，例如：工具类、常量类、异常类等           |
| 3        | sky-pojo     | 子模块，存放实体类、VO、DTO等                                |
| 4        | sky-server   | 子模块，后端服务，存放配置文件、Controller、Service、Mapper等 |

1.sky-common 子模块中存放的是一些公共类，可以供其他模块使用

2.sky-pojo 子模块中存放的是一些 entity、DTO、VO

| **名称** | **说明**                                     |
| -------- | -------------------------------------------- |
| Entity   | 实体，通常和数据库中的表对应                 |
| DTO      | 数据传输对象，通常用于程序中各层之间传递数据 |
| VO       | 视图对象，为前端展示数据提供的对象           |
| POJO     | 普通Java对象，只有属性和对应的getter和setter |

3.sky-server 子模块中存放的是 配置文件、配置类、拦截器、controller、service、mapper、启动类等

## 1.2.功能架构

管理端:员工管理,  分类管理,  菜品管理,  套餐管理,  订单管理,  工作台,  数据统计,  来单提醒

用户端:微信登录,  商品浏览,  购物车,  用户下单,  微信支付,  历史订单,  地址管理,  用户催单

## 1.3.技术选型

用户层:node.js,  VUE.js,  ElementUI,  微信小程序,  apache echarts

网关层:Nginx

应用层:Spring Boot,  Spring MVC,  Spring Task,  httpclient,  Spring Cache,  JWT,  阿里云OSS,  Swagger,  POI,  WebSocket

数据层:MySQL,  Redis,  mybatis,  pagehelper,  sping data redis

工具:Git,  maven,  Junit,  postman

## 1.4.问题

### 1.4.1.通过Swagger就可以生成接口文档, 那么我们是否就不需要Yapi, Apifox等工具?

1.Yapi, Apifox等是在设计阶段使用的工具, 管理和维护接口

2.Swagger在开发阶段使用的框架, 帮助后端开发人员做后端的接口测试

### 1.4.2.Swagger常用注解

| **注解**          | **说明**                                               |
| ----------------- | ------------------------------------------------------ |
| @Api              | 用在类上，例如Controller，表示对类的说明               |
| @ApiModel         | 用在类上，例如entity、DTO、VO                          |
| @ApiModelProperty | 用在属性上，描述属性信息                               |
| @ApiOperation     | 用在方法上，例如Controller的方法，说明方法的用途、作用 |

# 2.新增员工

## 2.1.需求分析和设计

### 2.1.1.表结构设计

```sql
CREATE TABLE `employee` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
  `name` varchar(32) CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL COMMENT '姓名',
  `username` varchar(32) CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL COMMENT '用户名',
  `password` varchar(64) CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL COMMENT '密码',
  `phone` varchar(11) CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL COMMENT '手机号',
  `sex` varchar(2) CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL COMMENT '性别',
  `id_number` varchar(18) CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL COMMENT '身份证号',
  `status` int NOT NULL DEFAULT '1' COMMENT '状态 0:禁用，1:启用',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间',
```

### 2.1.2.参数传递

application/json

{    

​	"id": 0,

​	"idNumber": "string",    

​	"name": "string",

​    "phone": "string", 

   "sex": "string",

​    "username": "string" 

}

## 2.2.代码开发

注意:

​	1.当前端提交的数据和实体类中对应的属性差别比较大时, 建议使用DTO来封装数据

​	2.DTO方便封装前端传送的数据,但是对于持久层的数据传递建议使用实体类

### 2.2.1.EmployeeDTO

```java
public class EmployeeDTO implements Serializable {

    private Long id;

    private String username;

    private String name;

    private String phone;

    private String sex;

    private String idNumber;

}
```

### 2.2.2.Employee

```java
public class Employee implements Serializable {

    private static final long serialVersionUID = 1L;

    private Long id;

    private String username;

    private String name;

    private String password;

    private String phone;

    private String sex;

    private String idNumber;

    private Integer status;

    //@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime createTime;

    //@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime updateTime;

    private Long createUser;

    private Long updateUser;

}
```

### 2.2.3.EmployeeController

```java
/**
 * 新增员工
 * @param employeeDTO
 * @return
 */
@PostMapping
@ApiOperation("新增员工")
public Result save(@RequestBody EmployeeDTO employeeDTO) {
    log.info("新增员工: {}", employeeDTO);
    System.out.println("当前线程的id: " + Thread.currentThread().getId());
    employeeService.save(employeeDTO);
    return Result.success();
}
```

### 2.2.4.EmployeeServiceImpl

```java
/**
 * 新增员工
 * @param employeeDTO
 */
@Override
public void save(EmployeeDTO employeeDTO) {
    System.out.println("当前线程的id: " + Thread.currentThread().getId());
    Employee employee = new Employee();

    // 对象属性拷贝
    BeanUtils.copyProperties(employeeDTO, employee);

    // 设置账号的状态, 默认正常状态, 1表示正常, 0表示锁定
    employee.setStatus(StatusConstant.ENABLE);

    // 设置密码, 默认密码为123456
    employee.setPassword(DigestUtils.md5DigestAsHex(PasswordConstant.DEFAULT_PASSWORD.getBytes()));

    // 设置当前记录的创建时间和修改时间
    employee.setCreateTime(LocalDateTime.now());
    employee.setUpdateTime(LocalDateTime.now());

    // 记录当前记录创建人id和修改人id
    employee.setCreateUser(BaseContext.getCurrentId());
    employee.setUpdateUser(BaseContext.getCurrentId());

    employeeMapper.insert(employee);
}
```

### 2.2.5.EmployeeMapper

```java
/**
 * 插入员工数据
 * @param employee
 */
@Insert("insert into employee (name, username, password, phone, sex, id_number, create_time, update_time, create_user, update_user, status)" +
        "values" +
        "(#{name},#{username}, #{password}, #{phone},#{sex}, #{idNumber}, #{createTime}, #{updateTime}, #{createUser}, #{updateUser}, #{status})")
void insert(Employee employee);
```

### 2.2.6.处理已存在账户异常

```java
/**
 * 处理SQL异常
 * @param ex
 * @return
 */
@ExceptionHandler
public Result exceptionHandler(SQLIntegrityConstraintViolationException ex) {
    // eg: Duplicate entry 'zhangsan' for key 'employee.idx_username'
    String message = ex.getMessage();
    if (message.contains("Duplicate entry")) {
        String[] split = message.split(" ");
        String username = split[2];
        String msg = username + MessageConstant.ALREADY_EXISTS;
        return Result.error(msg);
    } else {
        return Result.error((MessageConstant.UNKNOWN_ERROR));
    }
}
```

## 2.3.开发过程中关键问题

### 2.3.1.如何获取的操作人id

每一次的运行ThreadLocal都会创建一个新的线程,线程id是共享的,通过BaseContext来获取对应的id

```java
public class BaseContext {

    public static ThreadLocal<Long> threadLocal = new ThreadLocal<>();

    public static void setCurrentId(Long id) {
        threadLocal.set(id);
    }

    public static Long getCurrentId() {
        return threadLocal.get();
    }

    public static void removeCurrentId() {
        threadLocal.remove();
    }

}
```

在校验令牌的时候进行id设置

```java
//2、校验令牌
try {
    log.info("jwt校验:{}", token);
    Claims claims = JwtUtil.parseJWT(jwtProperties.getAdminSecretKey(), token);
    Long empId = Long.valueOf(claims.get(JwtClaimsConstant.EMP_ID).toString());
    log.info("当前员工id：", empId);
    BaseContext.setCurrentId(empId);
    //3、通过，放行
    return true;
} catch (Exception ex) {
    //4、不通过，响应401状态码
    response.setStatus(401);
    return false;
}
```

最终在EmployeeServiceImpl中获取进行记录

```java
// 记录当前记录创建人id和修改人id
employee.setCreateUser(BaseContext.getCurrentId());
employee.setUpdateUser(BaseContext.getCurrentId());
```

# 3.员工分页查询

## 3.1.需求分析和设计

业务规则:

​	1.根据页码展示员工信息

​	2.每页展示10条数据

​	3.分页查询时可以根据需要,输入员工姓名进行查询

Query 参数:name, page, pageSize

## 3.2.代码开发

### 3.2.1.EmployeePageQueryDTO

```java
public class EmployeePageQueryDTO implements Serializable {

    //员工姓名
    private String name;

    //页码
    private int page;

    //每页显示记录数
    private int pageSize;

}
```

### 3.2.2.PageResult

```java
public class PageResult implements Serializable {

    private long total; //总记录数

    private List records; //当前页数据集合

}
```

### 3.2.3.EmployeeController

```java
/**
 * 员工分页查询
 * @param employeePageQueryDTO
 * @return
 */
@GetMapping("/page")
@ApiOperation("员工分页查询")
public Result<PageResult> page(EmployeePageQueryDTO employeePageQueryDTO) {
    log.info("员工分页查询, 参数为: {}", employeePageQueryDTO);
    PageResult pageResult = employeeService.pageQuery(employeePageQueryDTO);
    return Result.success(pageResult);
}
```

### 3.2.4.EmployeeServiceImpl

```java
/**
 * 员工分页查询
 * @param employeePageQueryDTO
 * @return
 */
@Override
public PageResult pageQuery(EmployeePageQueryDTO employeePageQueryDTO) {
    PageHelper.startPage(employeePageQueryDTO.getPage(), employeePageQueryDTO.getPageSize());

    Page<Employee> page = employeeMapper.pageQuery(employeePageQueryDTO);
    long total = page.getTotal();
    List<Employee> records = page.getResult();

    return new PageResult(total, records);
}
```

### 3.2.5.EmployeeMapper

```java
/**
 * 分页查询
 * @param employeePageQueryDTO
 * @return
 */
Page<Employee> pageQuery(EmployeePageQueryDTO employeePageQueryDTO);
```

### 3.2.6.EmployeeMapper.xml

```java
<select id="pageQuery" resultType="com.sky.entity.Employee">
    select * from employee
    <where>
        <if test="name != null and name != ''">
            and name like concat('%', #{name}, '%')
        </if>
    </where>
    order by create_time desc
</select>
```

## 3.3.开发过程问题解决

### 3.3.1.如何处理日期的表示

通过扩展 Spring MVC 框架的消息转换器

```java
/**
 * 扩展 Spring MVC 框架的消息转换器
 * @param converters
 */
protected void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
    log.info("扩展消息转换器...");
    //创建消息转换器对象
    MappingJackson2HttpMessageConverter converter = new MappingJackson2HttpMessageConverter();
    //设置对象转换器，底层使用Jackson将Java对象转为json
    converter.setObjectMapper(new JacksonObjectMapper());
    //将上面的消息转换器对象追加到mvc框架的转换器集合中
    converters.add(0, converter);
}
```

```java
/**
 * 对象映射器:基于jackson将Java对象转为json，或者将json转为Java对象
 * 将JSON解析为Java对象的过程称为 [从JSON反序列化Java对象]
 * 从Java对象生成JSON的过程称为 [序列化Java对象到JSON]
 */
public class JacksonObjectMapper extends ObjectMapper {

    public static final String DEFAULT_DATE_FORMAT = "yyyy-MM-dd";
    //public static final String DEFAULT_DATE_TIME_FORMAT = "yyyy-MM-dd HH:mm:ss";
    public static final String DEFAULT_DATE_TIME_FORMAT = "yyyy-MM-dd HH:mm";
    public static final String DEFAULT_TIME_FORMAT = "HH:mm:ss";

    public JacksonObjectMapper() {
        super();
        //收到未知属性时不报异常
        this.configure(FAIL_ON_UNKNOWN_PROPERTIES, false);

        //反序列化时，属性不存在的兼容处理
        this.getDeserializationConfig().withoutFeatures(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);

        SimpleModule simpleModule = new SimpleModule()
                .addDeserializer(LocalDateTime.class, new LocalDateTimeDeserializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_TIME_FORMAT)))
                .addDeserializer(LocalDate.class, new LocalDateDeserializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_FORMAT)))
                .addDeserializer(LocalTime.class, new LocalTimeDeserializer(DateTimeFormatter.ofPattern(DEFAULT_TIME_FORMAT)))
                .addSerializer(LocalDateTime.class, new LocalDateTimeSerializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_TIME_FORMAT)))
                .addSerializer(LocalDate.class, new LocalDateSerializer(DateTimeFormatter.ofPattern(DEFAULT_DATE_FORMAT)))
                .addSerializer(LocalTime.class, new LocalTimeSerializer(DateTimeFormatter.ofPattern(DEFAULT_TIME_FORMAT)));

        //注册功能模块 例如，可以添加自定义序列化器和反序列化器
        this.registerModule(simpleModule);
    }
}
```

# 4.启用禁用员工账号

## 4.1.需求分析和设计

业务规则:

​	1.可以对状态为"启用"的员工账号进行"禁用"操作

​	2.可以对状态为"禁用"的员工账号进行"启用"操作

​	3.状态为"禁用"的员工账号不能登陆系统

Query 参数:id

## 4.2.代码开发

### 4.2.1.EmployeeController

```java
/**
 * 启用禁用员工账号
 * @param status
 * @param id
 * @return
 */
@PostMapping("/status/{status}")
@ApiOperation("启用禁用员工账号")
public Result startOrStop(@PathVariable Integer status, Long id) {
    log.info("启用禁用员工账号: {}, {}", status, id);
    employeeService.startOrStop(status, id);
    return Result.success();
}
```

### 4.2.2.EmployeeServiceImpl

```java
/**
 * 启用禁用员工账号
 * @param status
 * @param id
 */
@Override
public void startOrStop(Integer status, Long id) {
    Employee employee = Employee.builder()
            .status(status)
            .id(id)
            .build();
    employeeMapper.update(employee);
}
```

### 4.2.3.EmployeeMapper

```java
/**
 * 启用禁用员工账号
 * @param employee
 */
void update(Employee employee);
```

### 4.2.4.EmployeeMapper.xml

```java
<update id="update" parameterType="Employee">
    update employee
    <set>
        <if test="name != null">name = #{name},</if>
        <if test="username != null">username = #{username},</if>
        <if test="password != null">password = #{password},</if>
        <if test="phone != null">phone = #{phone},</if>
        <if test="sex != null">sex = #{sex},</if>
        <if test="idNumber != null">id_number = #{idNumber},</if>
        <if test="updateTime != null">update_time = #{updateTime},</if>
        <if test="updateUser != null">update_user = #{updateUser},</if>
        <if test="status != null">status = #{status},</if>
    </set>
    where id = #{id}
</update>
```