**苍穹外卖**

# 一.项目概述

## 1.项目结构

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

## 2.功能架构

管理端:员工管理,  分类管理,  菜品管理,  套餐管理,  订单管理,  工作台,  数据统计,  来单提醒

用户端:微信登录,  商品浏览,  购物车,  用户下单,  微信支付,  历史订单,  地址管理,  用户催单

## 3.技术选型

用户层:node.js,  VUE.js,  ElementUI,  微信小程序,  apache echarts

网关层:Nginx

应用层:Spring Boot,  Spring MVC,  Spring Task,  httpclient,  Spring Cache,  JWT,  阿里云OSS,  Swagger,  POI,  WebSocket

数据层:MySQL,  Redis,  mybatis,  pagehelper,  sping data redis

工具:Git,  maven,  Junit,  postman

## 4.问题

### 4.1.通过Swagger就可以生成接口文档, 那么我们是否就不需要Yapi, Apifox等工具?

1.Yapi, Apifox等是在设计阶段使用的工具, 管理和维护接口

2.Swagger在开发阶段使用的框架, 帮助后端开发人员做后端的接口测试

### 4.2.Swagger常用注解

| **注解**          | **说明**                                               |
| ----------------- | ------------------------------------------------------ |
| @Api              | 用在类上，例如Controller，表示对类的说明               |
| @ApiModel         | 用在类上，例如entity、DTO、VO                          |
| @ApiModelProperty | 用在属性上，描述属性信息                               |
| @ApiOperation     | 用在方法上，例如Controller的方法，说明方法的用途、作用 |

# 二.员工管理

## 1.新增员工

### 1.1.需求分析和设计

#### 1.1.1.接口设计

**基本信息:**

​	path:  /admin/employee

​	Method:  POST

**请求参数:**

​	Header

| 参数名称     | 参数值           | 是否必须 | 示例 | 备注 |
| ------------ | ---------------- | -------- | ---- | ---- |
| Content-Type | application/json | 是       |      |      |

​	Body

| 名称     | 类型    | 是否必须 | 默认值 | 备注   | 其他信息 |
| -------- | ------- | -------- | ------ | ------ | -------- |
| id       | integer | 非必须   |        | 员工id | <int64>  |
| idNumber | string  | 必须     |        | 身份证 |          |
| name     | string  | 必须     |        | 姓名   |          |
| phone    | string  | 必须     |        | 手机号 |          |
| sex      | string  | 必须     |        | 性别   |          |
| username | string  | 必须     |        | 用户名 |          |

**返回数据**

| 名称 | 类型    | 是否必须 | 默认值 | 备注 | 其他信息 |
| ---- | ------- | -------- | ------ | ---- | -------- |
| code | integer | 必须     |        |      | <int32>  |
| data | object  | 非必须   |        |      |          |
| msg  | string  | 非必须   |        |      |          |

#### 1.1.2.Employee表结构设计

| 字段名      | **数据类型** | **说明**     | **备注**    |
| ----------- | ------------ | ------------ | ----------- |
| id          | bigint       | 主键         | 自增        |
| name        | varchar(32)  | 姓名         |             |
| username    | varchar(32)  | 用户名       | 唯一        |
| password    | varchar(64)  | 密码         |             |
| phone       | varchar(11)  | 手机号       |             |
| sex         | varchar(2)   | 性别         |             |
| id_number   | varchar(18)  | 身份证号     |             |
| status      | Int          | 账号状态     | 1正常 0锁定 |
| create_time | Datetime     | 创建时间     |             |
| update_time | datetime     | 最后修改时间 |             |
| create_user | bigint       | 创建人id     |             |
| update_user | bigint       | 最后修改人id |             |

### 1.2.代码开发

注意:

​	1.当前端提交的数据和实体类中对应的属性差别比较大时, 建议使用DTO来封装数据

​	2.DTO方便封装前端传送的数据,但是对于持久层的数据传递建议使用实体类

#### 1.2.1.EmployeeDTO

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

#### 1.2.2.Employee

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

#### 1.2.3.EmployeeController

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

#### 1.2.4.EmployeeServiceImpl

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

#### 1.2.5.EmployeeMapper

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

#### 1.2.6.处理已存在账户异常

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

### 1.3.开发过程中关键问题

#### 1.3.1.如何获取的操作人id

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

## 2.员工分页查询

### 2.1.需求分析和设计

**业务规则:**

​	1.根据页码展示员工信息

​	2.每页展示10条数据

​	3.分页查询时可以根据需要,输入员工姓名进行查询

**基本信息:**

​	path:  /admin/employee/page

​	Method:  GET

**请求参数:**

​	Query 参数

| 名称     | 类型   | 是否必须 | 默认值 | 备注       | 其他信息 |
| -------- | ------ | -------- | ------ | ---------- | -------- |
| name     | string | 非必须   |        | 员工姓名   |          |
| page     | string | 必须     |        | 页码       |          |
| pageSize | string | 必须     |        | 每页记录数 |          |

**返回数据:**

{

​    "code": 0, 

   "msg": null,

​    "data": {

​        "total": 0,

​        "records": [

​            { 

​               "id": 0, 

​               "username": "string",

​                "name": "string", 

​               "password": "string",

​                "phone": "string",

​                "sex": "string", 

​               "idNumber": "string", 

​               "status": 0,

​                "createTime": "string", 

​               "updateTime": "string",

​                "createUser": 0,

​                "updateUser": 0

​            }

​        ]

​    }

 }



### 2.2.代码开发

#### 2.2.1.EmployeePageQueryDTO

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

#### 2.2.2.PageResult

```java
public class PageResult implements Serializable {

    private long total; //总记录数

    private List records; //当前页数据集合

}
```

#### 2.2.3.EmployeeController

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

#### 2.2.4.EmployeeServiceImpl

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

#### 2.2.5.EmployeeMapper

```java
/**
 * 分页查询
 * @param employeePageQueryDTO
 * @return
 */
Page<Employee> pageQuery(EmployeePageQueryDTO employeePageQueryDTO);
```

#### 2.2.6.EmployeeMapper.xml

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

### 2.3.开发过程问题解决

#### 2.3.1.如何处理日期的表示

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

## 3.启用禁用员工账号

### 3.1.需求分析和设计

**业务规则:**

​	1.可以对状态为"启用"的员工账号进行"禁用"操作

​	2.可以对状态为"禁用"的员工账号进行"启用"操作

​	3.状态为"禁用"的员工账号不能登陆系统

**基本信息:**

​	path:  /admin/employee/status/{status}

​	Method:  POST

**请求参数**

​	1.Path 参数

​		status string  状态，1为启用 0为禁用

​	2.Query 参数

​		id

### 3.2.代码开发

#### 3.2.1.EmployeeController

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

#### 3.2.2.EmployeeServiceImpl

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

#### 3.2.3.EmployeeMapper

```java
/**
 * 启用禁用员工账号
 * @param employee
 */
void update(Employee employee);
```

#### 3.2.4.EmployeeMapper.xml

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

## 4.编辑员工

### 4.1.需求分析和设计

#### 4.1.1.根据id查询员工信息

请求参数: Path 参数

​	id string  员工id

#### 4.1.2.编辑员工信息

application/json

{    

​	"id": 0, 

   "idNumber": "string",

​    "name": "string", 

   "phone": "string", 

   "sex": "string",

​    "username": "string"

 }

### 4.2.代码开发

#### 4.2.1.EmployeeController

```java
/**
 * 根据id查询员工信息
 * @param id
 * @return
 */
@GetMapping("/{id}")
@ApiOperation("根据id查询员工信息")
public Result<Employee> getById(@PathVariable Long id) {
    Employee employee = employeeService.getById(id);
    return Result.success(employee);
}

/**
 * 编辑员工信息
 * @param employeeDTO
 * @return
 */
@PutMapping
@ApiOperation("编辑员工信息")
public Result update(@RequestBody EmployeeDTO employeeDTO) {
    log.info("编辑员工信息: {}", employeeDTO);
    employeeService.update(employeeDTO);
    return Result.success();
}
```

#### 4.2.2.EmployeeServiceImpl

```java
/**
 * 根据id查询员工信息
 * @param id
 * @return
 */
@Override
public Employee getById(Long id) {
    Employee employee = employeeMapper.getById(id);
    employee.setPassword("****");
    return employee;
}

/**
 * 编辑员工信息
 * @param employeeDTO
 */
@Override
public void update(EmployeeDTO employeeDTO) {
    Employee employee = new Employee();
    BeanUtils.copyProperties(employeeDTO, employee);

    employee.setUpdateTime(LocalDateTime.now());
    employee.setUpdateUser(BaseContext.getCurrentId());

    employeeMapper.update(employee);
}
```

#### 4.2.3.EmployeeMapper

```java
/**
 * 根据id查询员工信息
 * @param id
 * @return
 */
@Select("select * from employee where id = #{id}")
Employee getById(Long id);
```

# 三.分类管理

## 1.需求分析和设计

**业务规则:**

​	1.分类名称必须是唯一的

​	2.分类按照类型可以分为菜品分类和套餐分类

​	3.新添加的分类状态为默认为"禁用"

**category表结构：**

| **字段名**  | **数据类型** | **说明**     | **备注**            |
| ----------- | ------------ | ------------ | ------------------- |
| id          | bigint       | 主键         | 自增                |
| name        | varchar(32)  | 分类名称     | 唯一                |
| type        | int          | 分类类型     | 1菜品分类 2套餐分类 |
| sort        | int          | 排序字段     | 用于分类数据的排序  |
| status      | int          | 状态         | 1启用 0禁用         |
| create_time | datetime     | 创建时间     |                     |
| update_time | datetime     | 最后修改时间 |                     |
| create_user | bigint       | 创建人id     |                     |
| update_user | bigint       | 最后修改人id |                     |

**接口设计:**

- 新增分类
- 分类分页查询
- 根据id删除分类
- 修改分类
- 启用禁用分类
- 根据类型查询分类

## 2.代码开发

### 2.1.CategoryController

```java
@RestController
@RequestMapping("/admin/category")
@Api(tags = "分类相关接口")
@Slf4j
public class CategoryController {

    @Autowired
    private CategoryService categoryService;

    /**
     * 新增分类
     * @param categoryDTO
     * @return
     */
    @PostMapping
    @ApiOperation("新增分类")
    public Result<String> save(@RequestBody CategoryDTO categoryDTO){
        log.info("新增分类：{}", categoryDTO);
        categoryService.save(categoryDTO);
        return Result.success();
    }

    /**
     * 分类分页查询
     * @param categoryPageQueryDTO
     * @return
     */
    @GetMapping("/page")
    @ApiOperation("分类分页查询")
    public Result<PageResult> page(CategoryPageQueryDTO categoryPageQueryDTO){
        log.info("分页查询：{}", categoryPageQueryDTO);
        PageResult pageResult = categoryService.pageQuery(categoryPageQueryDTO);
        return Result.success(pageResult);
    }

    /**
     * 删除分类
     * @param id
     * @return
     */
    @DeleteMapping
    @ApiOperation("删除分类")
    public Result<String> deleteById(Long id){
        log.info("删除分类：{}", id);
        categoryService.deleteById(id);
        return Result.success();
    }

    /**
     * 修改分类
     * @param categoryDTO
     * @return
     */
    @PutMapping
    @ApiOperation("修改分类")
    public Result<String> update(@RequestBody CategoryDTO categoryDTO){
        categoryService.update(categoryDTO);
        return Result.success();
    }

    /**
     * 启用、禁用分类
     * @param status
     * @param id
     * @return
     */
    @PostMapping("/status/{status}")
    @ApiOperation("启用禁用分类")
    public Result<String> startOrStop(@PathVariable("status") Integer status, Long id){
        categoryService.startOrStop(status,id);
        return Result.success();
    }

    /**
     * 根据类型查询分类
     * @param type
     * @return
     */
    @GetMapping("/list")
    @ApiOperation("根据类型查询分类")
    public Result<List<Category>> list(Integer type){
        List<Category> list = categoryService.list(type);
        return Result.success(list);
    }
}
```

### 2.2.CategoryServiceImpl

```java
/**
 * 分类业务层
 */
@Service
@Slf4j
public class CategoryServiceImpl implements CategoryService {

    @Autowired
    private CategoryMapper categoryMapper;
    @Autowired
    private DishMapper dishMapper;
    @Autowired
    private SetmealMapper setmealMapper;

    /**
     * 新增分类
     * @param categoryDTO
     */
    public void save(CategoryDTO categoryDTO) {
        Category category = new Category();
        //属性拷贝
        BeanUtils.copyProperties(categoryDTO, category);

        //分类状态默认为禁用状态0
        category.setStatus(StatusConstant.DISABLE);

        //设置创建时间、修改时间、创建人、修改人
        category.setCreateTime(LocalDateTime.now());
        category.setUpdateTime(LocalDateTime.now());
        category.setCreateUser(BaseContext.getCurrentId());
        category.setUpdateUser(BaseContext.getCurrentId());

        categoryMapper.insert(category);
    }

    /**
     * 分页查询
     * @param categoryPageQueryDTO
     * @return
     */
    public PageResult pageQuery(CategoryPageQueryDTO categoryPageQueryDTO) {
        PageHelper.startPage(categoryPageQueryDTO.getPage(),categoryPageQueryDTO.getPageSize());
        //下一条sql进行分页，自动加入limit关键字分页
        Page<Category> page = categoryMapper.pageQuery(categoryPageQueryDTO);
        return new PageResult(page.getTotal(), page.getResult());
    }

    /**
     * 根据id删除分类
     * @param id
     */
    public void deleteById(Long id) {
        //查询当前分类是否关联了菜品，如果关联了就抛出业务异常
        Integer count = dishMapper.countByCategoryId(id);
        if(count > 0){
            //当前分类下有菜品，不能删除
            throw new DeletionNotAllowedException(MessageConstant.CATEGORY_BE_RELATED_BY_DISH);
        }

        //查询当前分类是否关联了套餐，如果关联了就抛出业务异常
        count = setmealMapper.countByCategoryId(id);
        if(count > 0){
            //当前分类下有菜品，不能删除
            throw new DeletionNotAllowedException(MessageConstant.CATEGORY_BE_RELATED_BY_SETMEAL);
        }

        //删除分类数据
        categoryMapper.deleteById(id);
    }

    /**
     * 修改分类
     * @param categoryDTO
     */
    public void update(CategoryDTO categoryDTO) {
        Category category = new Category();
        BeanUtils.copyProperties(categoryDTO,category);

        //设置修改时间、修改人
        category.setUpdateTime(LocalDateTime.now());
        category.setUpdateUser(BaseContext.getCurrentId());

        categoryMapper.update(category);
    }

    /**
     * 启用、禁用分类
     * @param status
     * @param id
     */
    public void startOrStop(Integer status, Long id) {
        Category category = Category.builder()
                .id(id)
                .status(status)
                .updateTime(LocalDateTime.now())
                .updateUser(BaseContext.getCurrentId())
                .build();
        categoryMapper.update(category);
    }

    /**
     * 根据类型查询分类
     * @param type
     * @return
     */
    public List<Category> list(Integer type) {
        return categoryMapper.list(type);
    }
}
```

### 2.3.CategoryMapper

```java
@Mapper
public interface CategoryMapper {

    /**
     * 插入数据
     * @param category
     */
    @Insert("insert into category(type, name, sort, status, create_time, update_time, create_user, update_user)" +
            " VALUES" +
            " (#{type}, #{name}, #{sort}, #{status}, #{createTime}, #{updateTime}, #{createUser}, #{updateUser})")
    void insert(Category category);

    /**
     * 分页查询
     * @param categoryPageQueryDTO
     * @return
     */
    Page<Category> pageQuery(CategoryPageQueryDTO categoryPageQueryDTO);

    /**
     * 根据id删除分类
     * @param id
     */
    @Delete("delete from category where id = #{id}")
    void deleteById(Long id);

    /**
     * 根据id修改分类
     * @param category
     */
    void update(Category category);

    /**
     * 根据类型查询分类
     * @param type
     * @return
     */
    List<Category> list(Integer type);
}
```

### 2.4.DishMapper

```java
@Mapper
public interface DishMapper {

    /**
     * 根据分类id查询菜品数量
     * @param categoryId
     * @return
     */
    @Select("select count(id) from dish where category_id = #{categoryId}")
    Integer countByCategoryId(Long categoryId);

}
```

### 2.5.SetmealMapper

```java
@Mapper
public interface SetmealMapper {

    /**
     * 根据分类id查询套餐的数量
     * @param id
     * @return
     */
    @Select("select count(id) from setmeal where category_id = #{categoryId}")
    Integer countByCategoryId(Long id);

}
```

### 2.6.CategoryMapper.xml

```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.CategoryMapper">

    <select id="pageQuery" resultType="com.sky.entity.Category">
        select * from category
        <where>
            <if test="name != null and name != ''">
                and name like concat('%',#{name},'%')
            </if>
            <if test="type != null">
                and type = #{type}
            </if>
        </where>
        order by sort asc , create_time desc
    </select>

    <update id="update" parameterType="Category">
        update category
        <set>
            <if test="type != null">
                type = #{type},
            </if>
            <if test="name != null">
                name = #{name},
            </if>
            <if test="sort != null">
                sort = #{sort},
            </if>
            <if test="status != null">
                status = #{status},
            </if>
            <if test="updateTime != null">
                update_time = #{updateTime},
            </if>
            <if test="updateUser != null">
                update_user = #{updateUser}
            </if>
        </set>
        where id = #{id}
    </update>

    <select id="list" resultType="Category">
        select * from category
        where status = 1
        <if test="type != null">
            and type = #{type}
        </if>
        order by sort asc,create_time desc
    </select>
</mapper>
```

# 四.菜品管理

## 1.公共字段自动填充

### 1.1.问题分析

业务表中的公共字段：

| **序号** | **字段名**  | **含义** | **数据类型** |
| -------- | ----------- | -------- | ------------ |
| 1        | create_time | 创建时间 | datetime     |
| 2        | create_user | 创建人id | bigint       |
| 3        | update_time | 修改时间 | datetime     |
| 4        | update_user | 修改人id | bigint       |

```java
//设置当前记录的创建时间、修改时间、创建人、修改人
employee.setCreateTime(LocalDateTime.now());
employee.setUpdateTime(LocalDateTime.now());
employee.setCreateUser(BaseContext.getCurrentId());
employee.setUpdateUser(BaseContext.getCurrentId());

//设置创建时间、修改时间、创建人、修改人
category.setCreateTime(LocalDateTime.now());
category.setUpdateTime(LocalDateTime.now());
category.setCreateUser(BaseContext.getCurrentId());
category.setUpdateUser(BaseContext.getCurrentId());
```

问题：代码冗余、不便于后期维护

### 1.2.实现思路

| **序号** | **字段名**  | **含义** | **数据类型** | **操作类型**   |
| -------- | ----------- | -------- | ------------ | -------------- |
| 1        | create_time | 创建时间 | datetime     | insert         |
| 2        | create_user | 创建人id | bigint       | insert         |
| 3        | update_time | 修改时间 | datetime     | insert、update |
| 4        | update_user | 修改人id | bigint       | insert、update |

1.自定义注解 AutoFill，用于标识需要进行公共字段自动填充的方法

2.自定义切面类 AutoFillAspect，统一拦截加入了 AutoFill 注解的方法，通过反射为公共字段赋值

3.在 Mapper 的方法上加入 AutoFill 注解

技术点：枚举、注解、AOP、反射

### 1.3.代码开发

#### 1.3.1.OperationType

```java
/**
 * 数据库操作类型
 */
public enum OperationType {

    /**
     * 更新操作
     */
    UPDATE,

    /**
     * 插入操作
     */
    INSERT

}
```

#### 1.3.2.AutoFillConstant

```java
/**
 * 公共字段自动填充相关常量
 */
public class AutoFillConstant {
    /**
     * 实体类中的方法名称
     */
    public static final String SET_CREATE_TIME = "setCreateTime";
    public static final String SET_UPDATE_TIME = "setUpdateTime";
    public static final String SET_CREATE_USER = "setCreateUser";
    public static final String SET_UPDATE_USER = "setUpdateUser";
}
```

#### 1.3.3.AutoFill

```java
/**
 * 自定义注解, 用于标识某个方法需要进行功能字段自动填充处理
 */
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface AutoFill {
    // 数据库操作类型: UPDATE INSERT
    OperationType value();
}
```

#### 1.3.4.AutoFillAspect

```java
public class AutoFillAspect {
    /**
     * 切入点
     */
    @Pointcut("execution(* com.sky.mapper.*.*(..)) && @annotation(com.sky.annotation.AutoFill)")
    public void autoFillPointCut() {}

    /**
     * 前置通知
     */
    @Before("autoFillPointCut()")
    public void autoFill(JoinPoint joinPoint) {
        log.info("开始进行公共字段自动填充...");

        //获取到当前被拦截的方法上的数据库操作类型
        MethodSignature signature = (MethodSignature) joinPoint.getSignature(); // 方法签名对象
        AutoFill autoFill = signature.getMethod().getAnnotation(AutoFill.class); // 获得方法上的注解对象
        OperationType operationType = autoFill.value(); // 获得数据库操作类型

        // 获取到当前被拦截的方法的参数--实体对象
        Object[] args = joinPoint.getArgs();
        if(args == null || args.length == 0) {
            return;
        }
        Object entity = args[0];

        // 准备赋值的数据
        LocalDateTime now = LocalDateTime.now();
        Long currentId = BaseContext.getCurrentId();

        // 根据当前不同的数据操作类型, 为对应的属性通过反射来赋值
        if(operationType == OperationType.INSERT) {
            // 为4个属性赋值
            try {
                Method setCreateTime = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_CREATE_TIME, LocalDateTime.class);
                Method setCreateUser = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_CREATE_USER, Long.class);
                Method setUpdateTime = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_UPDATE_TIME, LocalDateTime.class);
                Method setUpdateUser = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_UPDATE_USER, Long.class);

                // 通过反射为对象属性赋值
                setCreateTime.invoke(entity, now);
                setCreateUser.invoke(entity, currentId);
                setUpdateTime.invoke(entity, now);
                setUpdateUser.invoke(entity, currentId);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }else if(operationType == OperationType.UPDATE) {
            // 为2个属性赋值
            try {
                Method setUpdateTime = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_UPDATE_TIME, LocalDateTime.class);
                Method setUpdateUser = entity.getClass().getDeclaredMethod(AutoFillConstant.SET_UPDATE_USER, Long.class);

                setUpdateTime.invoke(entity, now);
                setUpdateUser.invoke(entity, currentId);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

#### 1.3.5.Mapper层操作

```java
@AutoFill(value = OperationType.INSERT)
@AutoFill(value = OperationType.UPDATE)
```

## 2.新增菜品

### 2.1.需求分析和设计

**业务规则:**

​	1.菜品名称必须唯一

​	2.菜品必须属于某个分类下, 不能单独存在

​	3.新增菜品时可以根据情况选择菜品的口味

​	4.每个菜品必须对应一张图片

**接口设计:**

​	1.根据类型查询分类(分类管理模块已完成)

​	2.文件上传

​	3.新增菜品

**新增菜品请求参数:**

Body 参数 application/json

​	**categoryId** integer <int64> 分类id

​	**description** string  菜品描述

​	**flavors** array[object (DishFlavor)]  口味

​		**dishId** integer <int64> 菜品id

​		**id** integer <int64> 口味id

​		**name** string  口味名称

​		**value** string 口味值

​	**id** integer <int64> 菜品id

​	**image** string 菜品图片路径

​	**name** string 菜品名称

​	**price** number 菜品价格

​	**status** integer <int32> 菜品状态：1为起售，0为停售

示例

```json
{
    "categoryId": 0,
    "description": "string",
    "flavors": [
        {
            "dishId": 0,
            "id": 0,
            "name": "string",
            "value": "string"
        }
    ],
    "id": 0,
    "image": "string",
    "name": "string",
    "price": 0,
    "status": 0
}
```

数据库设计(dish菜品表和dish_flavor口味表):

​	**1.菜品表:dish**

| **字段名**  | **数据类型**  | **说明**     | **备注**    |
| ----------- | ------------- | ------------ | ----------- |
| id          | bigint        | 主键         | 自增        |
| name        | varchar(32)   | 菜品名称     | 唯一        |
| category_id | bigint        | 分类id       | 逻辑外键    |
| price       | decimal(10,2) | 菜品价格     |             |
| image       | varchar(255)  | 图片路径     |             |
| description | varchar(255)  | 菜品描述     |             |
| status      | int           | 售卖状态     | 1起售 0停售 |
| create_time | datetime      | 创建时间     |             |
| update_time | datetime      | 最后修改时间 |             |
| create_user | bigint        | 创建人id     |             |
| update_user | bigint        | 最后修改人id |             |

​	**2.菜品口味表:dish_flavor**

| **字段名** | **数据类型** | **说明** | **备注** |
| ---------- | ------------ | -------- | -------- |
| id         | bigint       | 主键     | 自增     |
| dish_id    | bigint       | 菜品id   | 逻辑外键 |
| name       | varchar(32)  | 口味名称 |          |
| value      | varchar(255) | 口味值   |          |

### 2.2.代码开发

#### 2.2.1.文件上传

##### 2.2.1.1.CommonController

```java
/**
 * 通用接口
 */
@RestController
@RequestMapping("/admin/common")
@Api(tags = "通用接口")
@Slf4j
public class CommonController {
    @Autowired
    private AliOssUtil aliOssUtil;

    /**
     * 文件上传
     * @param file
     * @return
     */
    @PostMapping("/upload")
    @ApiOperation("文件上传")
    public Result<String> upload(MultipartFile file) {
        log.info("文件上传: {}", file);

        try {
            // 原始文件名
            String originalFilename = file.getOriginalFilename();
            // 截取原始文件名后缀
            String extension = originalFilename.substring(originalFilename.lastIndexOf("."));
            // 构建新文件名称
            String objectName = UUID.randomUUID().toString() + extension;

            // 文件的请求路径
            String filePath = aliOssUtil.upload(file.getBytes(), objectName);
            return Result.success(filePath);
        } catch (IOException e) {
            log.info("文件上传失败: {}", e);
        }

        return Result.error(MessageConstant.UPLOAD_FAILED);
    }
}
```

##### 2.2.1.2.OssConfiguration

```java
/**
 * 配置类, 用于创建AliOssUtil对象
 */
@Configuration
@Slf4j
public class OssConfiguration {
    @Bean
    @ConditionalOnMissingBean
    public AliOssUtil aliOssUtil(AliOssProperties aliOssProperties) {
        log.info("开始创建阿里云文件上传工具类对象: {}", aliOssProperties);
        return new AliOssUtil(aliOssProperties.getEndpoint(),
                aliOssProperties.getAccessKeyId(),
                aliOssProperties.getAccessKeySecret(),
                aliOssProperties.getBucketName());
    }
}
```

##### 2.2.1.3.AliOssUtil

```java
@Data
@AllArgsConstructor
@Slf4j
public class AliOssUtil {

    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;

    /**
     * 文件上传
     *
     * @param bytes
     * @param objectName
     * @return
     */
    public String upload(byte[] bytes, String objectName) {

        // 创建OSSClient实例。
        OSS ossClient = new OSSClientBuilder().build(endpoint, accessKeyId, accessKeySecret);

        try {
            // 创建PutObject请求。
            ossClient.putObject(bucketName, objectName, new ByteArrayInputStream(bytes));
        } catch (OSSException oe) {
            System.out.println("Caught an OSSException, which means your request made it to OSS, "
                    + "but was rejected with an error response for some reason.");
            System.out.println("Error Message:" + oe.getErrorMessage());
            System.out.println("Error Code:" + oe.getErrorCode());
            System.out.println("Request ID:" + oe.getRequestId());
            System.out.println("Host ID:" + oe.getHostId());
        } catch (ClientException ce) {
            System.out.println("Caught an ClientException, which means the client encountered "
                    + "a serious internal problem while trying to communicate with OSS, "
                    + "such as not being able to access the network.");
            System.out.println("Error Message:" + ce.getMessage());
        } finally {
            if (ossClient != null) {
                ossClient.shutdown();
            }
        }

        //文件访问路径规则 https://BucketName.Endpoint/ObjectName
        StringBuilder stringBuilder = new StringBuilder("https://");
        stringBuilder
                .append(bucketName)
                .append(".")
                .append(endpoint)
                .append("/")
                .append(objectName);

        log.info("文件上传到:{}", stringBuilder.toString());

        return stringBuilder.toString();
    }
}
```

##### 2.2.1.4.AliOssProperties

```java
@Component
@ConfigurationProperties(prefix = "sky.alioss")
@Data
public class AliOssProperties {

    private String endpoint;
    private String accessKeyId;
    private String accessKeySecret;
    private String bucketName;

}
```

##### 2.2.1.5.配置文件

```yml
application.yml
alioss:
  endpoint: ${sky.alioss.endpoint}
  access-key-id: ${sky.alioss.access-key-id}
  access-key-secret: ${sky.alioss.access-key-secret}
  bucket-name: ${sky.alioss.bucket-name}

application-dev.yml
  alioss:
    endpoint: oss-cn-beijing.aliyuncs.com
    access-key-id: ${OSS_ACCESS_KEY_ID}
    access-key-secret: ${OSS_ACCESS_KEY_SECRET}
    bucket-name: java-ai-things
```

#### 2.2.2.新增菜品

##### 2.2.2.1.DishController

```java
/**
 * 菜品管理
 */
@RestController
@RequestMapping("/admin/dish")
@Api(tags = "菜品相关接口")
@Slf4j
public class DishController {
    @Autowired
    private DishService dishService;
    /**
     * 新增菜品
     * @param dishDTO
     * @return
     */
    @PostMapping
    @ApiOperation("新增菜品")
    public Result save(@RequestBody DishDTO dishDTO) {
        log.info("新增菜品: {}", dishDTO);
        dishService.saveWithFlavor(dishDTO);
        return Result.success();
    }
}
```

##### 2.2.2.2.DishServiceImpl

```java
@Service
@Slf4j
public class DishServiceImpl implements DishService {
    @Autowired
    private DishMapper dishMapper;
    @Autowired
    private DishFlavorMapper dishFlavorMapper;
    /**
     * 新增菜品和对应的口味
     * @param dishDTO
     */
    @Override
    @Transactional
    public void saveWithFlavor(DishDTO dishDTO) {
        Dish dish = new Dish();
        BeanUtils.copyProperties(dishDTO, dish);
        // 向菜品表插入一条数据
        dishMapper.insert(dish);

        // 获取insert语句生成的主键值
        Long dishId = dish.getId();

        // 向口味表插入多条数据
        List<DishFlavor> flavors = dishDTO.getFlavors();
        if(flavors != null && flavors.size() > 0) {
            flavors.forEach(dishFlavor -> {
                dishFlavor.setDishId(dishId);
            });
            dishFlavorMapper.insertBatch(flavors);
        }
    }
}
```

##### 2.2.2.3.DishMapper

```java
@Mapper
public interface DishMapper {

    /**
     * 根据分类id查询菜品数量
     * @param categoryId
     * @return
     */
    @Select("select count(id) from dish where category_id = #{categoryId}")
    Integer countByCategoryId(Long categoryId);

    /**
     * 新增菜品
     * @param dish
     */
    @AutoFill(value = OperationType.INSERT)
    void insert(Dish dish);
}
```

##### 2.2.2.4.DisMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.DishMapper">

    <insert id="insert" useGeneratedKeys="true" keyProperty="id">
        insert into dish (name, category_id, price, image, description, create_time, update_time, create_user, update_user, status) values
        (#{name}, #{categoryId}, #{price}, #{image}, #{description}, #{createTime}, #{updateTime}, #{createUser}, #{updateUser}, #{status})
    </insert>

</mapper>
```

##### 2.2.2.5.DishFlavorMapper

```java
@Mapper
public interface DishFlavorMapper {
    /**
     * 批量插入口味表数据
     * @param flavors
     */
    void insertBatch(List<DishFlavor> flavors);
}
```

##### 2.2.2.6.DishFlavorMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.DishFlavorMapper">

    <insert id="insertBatch">
        insert into dish_flavor (dish_id, name, value) values
        <foreach collection="flavors" item="df" separator=",">
            (#{df.dishId}, #{df.name}, #{df.value})
        </foreach>
    </insert>

</mapper>
```

## 3.菜品分页查询

### 3.1.需求分析和设计

**业务规则:**

​	1.根据页码展示数据

​	2.每页展示10条数据

​	3.分页查询时可以根据输入菜品名称,菜品分类,菜品状态进行查询

**基本信息:**

​	path:  /admin/dish/page

​	Method:  GET

**请求参数:**

​	Query参数:

| 名称       | 类型   | 是否必须 | 默认值 | 备注       | 其他信息 |
| ---------- | ------ | -------- | ------ | ---------- | -------- |
| categoryId | string | 非必须   |        | 分类id     |          |
| name       | string | 非必须   |        | 菜品名称   |          |
| page       | string | 必须     |        | 页码       |          |
| pageSize   | string | 必须     |        | 每页记录数 |          |
| status     | string | 非必须   |        | 分类状态   |          |

### 3.2.代码开发

#### 3.2.1.DishPageQueryDTO

```java
@Data
public class DishPageQueryDTO implements Serializable {

    private int page;

    private int pageSize;

    private String name;

    //分类id
    private Integer categoryId;

    //状态 0表示禁用 1表示启用
    private Integer status;

}
```

#### 3.2.2.DishVO

返回响应的数据

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class DishVO implements Serializable {

    private Long id;
    //菜品名称
    private String name;
    //菜品分类id
    private Long categoryId;
    //菜品价格
    private BigDecimal price;
    //图片
    private String image;
    //描述信息
    private String description;
    //0 停售 1 起售
    private Integer status;
    //更新时间
    private LocalDateTime updateTime;
    //分类名称
    private String categoryName;
    //菜品关联的口味
    private List<DishFlavor> flavors = new ArrayList<>();

    //private Integer copies;
}
```

#### 3.2.3.DishController

```java
/**
 * 菜品分页查询
 * @param dishPageQueryDTO
 * @return
 */
@GetMapping("/page")
@ApiOperation("菜品分页查询")
public Result<PageResult> page(DishPageQueryDTO dishPageQueryDTO) {
    log.info("菜品分页查询: {}", dishPageQueryDTO);
    PageResult pageResult = dishService.pageQuery(dishPageQueryDTO);
    return Result.success(pageResult);
}
```

#### 3.2.4.DishServiceImpl

```java
/**
 * 菜品分页查询
 * @param dishPageQueryDTO
 * @return
 */
@Override
public PageResult pageQuery(DishPageQueryDTO dishPageQueryDTO) {
    PageHelper.startPage(dishPageQueryDTO.getPage(), dishPageQueryDTO.getPageSize());
    Page<DishVO> page = dishMapper.pageQuery(dishPageQueryDTO);
    return new PageResult(page.getTotal(), page.getResult());
}
```

#### 3.2.5.DishMapper

```java
/**
 * 菜品分页查询
 * @param dishPageQueryDTO
 * @return
 */
Page<DishVO> pageQuery(DishPageQueryDTO dishPageQueryDTO);
```

#### 3.2.6.DishMapper.xml

```xml
<select id="pageQuery" resultType="com.sky.vo.DishVO">
    select d.*, c.name categoryName from dish d left outer join category c on d.category_id = c.id
    <where>
        <if test = "name != null">
            and d.name like concat('%', #{name}, '%')
        </if>
        <if test = "categoryId != null">
            and d.category_id = #{categoryId}
        </if>
        <if test = "status != null">
            and d.status = #{status}
        </if>
    </where>
    order by d.create_time desc
</select>
```

## 4.删除菜品

### 4.1.需求分析和设计

**业务规则:**

​	1.可以一次删除一个菜品, 也可以批量删除菜品

​	2.起售中的菜品不能删除

​	3.被套餐关联的菜品不能删除

​	4.删除菜品后, 关联的口味数据也需要删除

**基本信息:**

​	path:  /admin/dish

​	Method:  DELETE

**请求参数:**

Query 参数

| 名称 | 类型   | 是否必须 | 默认值 | 备注                   | 其他信息 |
| ---- | ------ | -------- | ------ | ---------------------- | -------- |
| ids  | string | 必须     |        | 菜品id，之间用逗号分隔 |          |

在进行删除菜品操作时，会涉及到三张表:  dish, dish_flavor, setmeal_dish

```mysql
CREATE TABLE `setmeal_dish` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
  `setmeal_id` bigint DEFAULT NULL COMMENT '套餐id',
  `dish_id` bigint DEFAULT NULL COMMENT '菜品id',
  `name` varchar(32) CHARACTER SET utf8mb3 COLLATE utf8mb3_bin DEFAULT NULL COMMENT '菜品名称 （冗余字段）',
  `price` decimal(10,2) DEFAULT NULL COMMENT '菜品单价（冗余字段）',
  `copies` int DEFAULT NULL COMMENT '菜品份数',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=47 DEFAULT CHARSET=utf8mb3 COLLATE=utf8mb3_bin COMMENT='套餐菜品关系'
```

**注意事项：**

- 在dish表中删除菜品基本数据时，同时，也要把关联在dish_flavor表中的数据一块删除。
- setmeal_dish表为菜品和套餐关联的中间表。
- 若删除的菜品数据关联着某个套餐，此时，删除失败。
- 若要删除套餐关联的菜品数据，先解除两者关联，再对菜品进行删除。

### 4.2.代码开发

#### 4.2.1.DishController

```java
/**
 * 菜品批量删除
 * @param ids
 * @return
 */
@DeleteMapping
@ApiOperation("菜品批量删除")
public Result delete(@RequestParam List<Long> ids) {
    log.info("菜品批量删除: {}", ids);
    dishService.deleteBatch(ids);
    return Result.success();
}
```

#### 4.2.2.DishServiceImpl

```java
/**
 * 菜品批量删除
 * @param ids
 */
@Transactional
@Override
public void deleteBatch(List<Long> ids) {
    // 判断当前菜品是否能够删除--是否存在起售中的菜品
    for (Long id : ids) {
        Dish dish = dishMapper.getById(id);
        if(dish.getStatus() == StatusConstant.ENABLE) {
            // 当前菜品处于起售中, 不能删除
            throw new DeletionNotAllowedException(MessageConstant.DISH_ON_SALE);
        }
    }

    // 判断当前菜品是否能够删除--是否被套餐关联
    List<Long> setmealIds = setmealDishMapper.getSetmealIdsByDishIds(ids);
    if(setmealIds != null && setmealIds.size() > 0) {
        // 当前菜品被套餐关联了, 不能删除
        throw new DeletionNotAllowedException(MessageConstant.DISH_BE_RELATED_BY_SETMEAL);
    }

    // 批量删除菜品表中的菜品数据
    dishMapper.deleteByIds(ids);

    // 批量删除菜品关联的口味数据
    dishFlavorMapper.deleteByDishIds(ids);
}
```

#### 4.2.3.DishMapper

```java
/**
 * 根据id查询菜品
 * @param id
 * @return
 */
@Select("select * from dish where id = #{id}")
Dish getById(Long id);

/**
 * 根据id批量删除菜品
 * @param ids
 */
void deleteByIds(List<Long> ids);
```

#### 4.2.4.DishMapper.xml

```xml
<delete id="deleteByIds">
    delete from dish where id in
    <foreach collection="ids" item="id" separator="," open="(" close=")">
        #{id}
    </foreach>
</delete>
```

#### 4.2.5.SetmealDishMapper

```java
/**
 * 根据菜品id查询对应的套餐id
 * @param dishIds
 * @return
 */
List<Long> getSetmealIdsByDishIds(List<Long> dishIds);
```

#### 4.2.6.SetmealDishMapper.xml

```xml
<select id="getSetmealIdsByDishIds" resultType="java.lang.Long">
    select setmeal_id from setmeal_dish where  dish_id in
    <foreach collection="dishIds" item="dishId" separator="," open="(" close=")">
        #{dishId}
    </foreach>
</select>
```

#### 4.2.7.DishFlavorMapper

```java
/**
 * 根据菜品id批量删除对应的口味表
 * @param dishIds
 */
void deleteByDishIds(List<Long> dishIds);
```

#### 4.2.8.DishFlavorMapper.xml

```xml
<delete id="deleteByDishIds">
    delete from dish_flavor where dish_id in
    <foreach collection="dishIds" item="dishId" separator="," open="(" close=")">
        #{dishId}
    </foreach>
</delete>
```

## 5.修改菜品

### 5.1.需求分析和设计

#### 5.1.1接口设计

##### 5.1.1.1.根据id查询菜品

**基本信息:**

​	path:  /admin/dish/{id}

​	Method:  GET

**请求参数:**

​	path参数:

| 名称 | 类型   | 是否必须 | 默认值 | 备注   | 其他信息 |
| ---- | ------ | -------- | ------ | ------ | -------- |
| id   | string | 必须     |        | 菜品id |          |

##### 5.1.1.2.修改菜品

**基本信息:**

​	path:  /admin/dish

​	Method:  PUT

**请求参数:** application/json

{

​    "categoryId": 0,

​    "description": "string",

​    "flavors": [

​        {

​            "dishId": 0,

​            "id": 0,

​            "name": "string", 

​           "value": "string"

​        }

​    ],

​    "id": 0,

​    "image": "string",

​    "name": "string",

​    "price": 0,    "status": 0

 }

### 5.2.代码开发

#### 5.2.1.根据id查询菜品

##### 5.2.1.1.DishController

```java
/**
 * 根据id查询菜品
 * @param id
 * @return
 */
@GetMapping("/{id}")
@ApiOperation("根据id查询菜品")
public Result<DishVO> getById(@PathVariable Long id) {
    log.info("根据id查询菜品: {}", id);
    DishVO dishVO = dishService.getByIdWithFlavor(id);
    return Result.success(dishVO);
}
```

##### 5.2.1.2.DishServiceImpl

```java
/**
 * 根据id查询菜品和对应的口味
 * @param id
 * @return
 */
@Override
public DishVO getByIdWithFlavor(Long id) {
    // 根据id查询菜品数据
    Dish dish = dishMapper.getById(id);

    // 根据菜品id查询口味数据
    List<DishFlavor> dishFlavors = dishFlavorMapper.getByDishId(id);

    // 将查询到的数据封装到VO
    DishVO dishVO = new DishVO();
    BeanUtils.copyProperties(dish, dishVO);
    dishVO.setFlavors(dishFlavors);

    return dishVO;
}
```

##### 5.2.1.3.DishMapper

```java
/**
 * 根据id查询菜品
 * @param id
 * @return
 */
@Select("select * from dish where id = #{id}")
Dish getById(Long id);
```

##### 5.2.1.4.DishFlavorMapper

```java
/**
 * 根据菜品id查询对应的口味数据
 * @param dishId
 * @return
 */
@Select("select * from dish_flavor where dish_id = #{dishId}")
List<DishFlavor> getByDishId(Long dishId);
```

#### 5.2.2.修改菜品

##### 5.2.2.1.DishController

```java
/**
 * 修改菜品
 * @param dishDTO
 * @return
 */
@PutMapping
@ApiOperation("修改菜品")
public Result update(@RequestBody DishDTO dishDTO) {
    log.info("修改菜品: {}", dishDTO);
    dishService.updateWithFlavor(dishDTO);
    return Result.success();
}
```

##### 5.2.2.2.DishServiceImpl

```java
/**
 * 修改菜品和对应的口味数据
 * @param dishDTO
 */
@Override
public void updateWithFlavor(DishDTO dishDTO) {
    Dish dish = new Dish();
    BeanUtils.copyProperties(dishDTO, dish);

    // 修改菜品基本信息
    dishMapper.update(dish);

    // 删除原有的口味数据
    dishFlavorMapper.deleteByDishId(dishDTO.getId());

    // 重新插入口味数据
    List<DishFlavor> flavors = dishDTO.getFlavors();
    if(flavors != null && flavors.size() > 0) {
        flavors.forEach(dishFlavor -> {
            dishFlavor.setDishId(dishDTO.getId());
        });
        dishFlavorMapper.insertBatch(flavors);
    }
}
```

##### 5.2.2.3.DishMapper

```java
/**
 * 修改菜品基本信息
 * @param dish
 */
@AutoFill(value = OperationType.UPDATE)
void update(Dish dish);
```

##### 5.2.2.4.DishMapper.xml

```xml
<update id="update">
    update dish
    <set>
        <if test = "name != null">name = #{name},</if>
        <if test = "categoryId != null">category_id = #{categoryId},</if>
        <if test = "price != null">price = #{price},</if>
        <if test = "image != null">image = #{image},</if>
        <if test = "description != null">description = #{description},</if>
        <if test = "status != null">status = #{status},</if>
        <if test = "updateTime != null">update_time = #{updateTime},</if>
        <if test = "updateUser != null">update_user = #{updateUser},</if>
    </set>
    where id = #{id}
</update>
```

## 6.菜品起售停售

### 6.1.需求分析和设计

**业务规则:**

​	1.菜品进行起售和停售

​	2.如果菜品停售的话, 包含该菜品的套餐也要停售

**基本信息:**

​	path:  /admin/dish/status/{status}

​	Method:  POST

**请求参数:**

​	Path 参数:  status  string  1为起售，0为停售

​	Query 参数:  id  string  菜品id

### 6.2.代码开发

#### 6.2.1.DishController

```java
/**
 * 菜品起售停售
 * @param status
 * @param id
 * @return
 */
@PostMapping ("/status/{status}")
public Result stopOrStart(@PathVariable Integer status, Long id) {
    log.info("菜品起售停售: {}, {}", status, id);
    dishService.stopOrStart(status, id);
    return Result.success();
}
```

#### 6.2.2.DishServiceImpl

```java
/**
 * 菜品起售停售
 * @param status
 * @param id
 */
@Override
public void stopOrStart(Integer status, Long id) {
    Dish dish = Dish.builder()
            .status(status)
            .id(id)
            .build();
    dishMapper.update(dish);

    if(status == StatusConstant.DISABLE) {
        // 将包含该餐品的套餐停售
        List<Long> dishIds = new ArrayList<>();
        dishIds.add(id);
        List<Long> setmealIds = setmealDishMapper.getSetmealIdsByDishIds(dishIds);
        if(setmealIds != null && setmealIds.size() > 0) {
            for (Long setmealId : setmealIds) {
                Setmeal setmeal = Setmeal.builder()
                        .status(StatusConstant.DISABLE)
                        .id(setmealId)
                        .build();
                setmealMapper.update(setmeal);
            }
        }
    }
}
```

#### 6.2.3.SetmealMapper

```java
/**
 * 根据id修改套餐
 * @param setmeal
 */
@AutoFill(value = OperationType.UPDATE)
void update(Setmeal setmeal);
```

#### 6.2.4.SetmealMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.SetmealMapper">

    <update id="update" parameterType="Setmeal">
        update setmeal
        <set>
            <if test="name != null">
                name = #{name},
            </if>
            <if test="categoryId != null">
                category_id = #{categoryId},
            </if>
            <if test="price != null">
                price = #{price},
            </if>
            <if test="status != null">
                status = #{status},
            </if>
            <if test="description != null">
                description = #{description},
            </if>
            <if test="image != null">
                image = #{image},
            </if>
            <if test="updateTime != null">
                update_time = #{updateTime},
            </if>
            <if test="updateUser != null">
                update_user = #{updateUser}
            </if>
        </set>
        where id = #{id}
    </update>

</mapper>
```

# 五.套餐管理

## 1.新增套餐

### 1.1.需求分析和设计

**业务规则：**

​	1.套餐名称唯一

​	2.套餐必须属于某个分类

​	3.套餐必须包含菜品

​	4.名称、分类、价格、图片为必填项

​	5.添加菜品窗口需要根据分类类型来展示菜品

​	6.新增的套餐默认为停售状态

**接口设计**（共涉及到4个接口）：

​	1.根据类型查询分类（已完成）

​	2.根据分类id查询菜品

​	3.图片上传（已完成）

​	4.新增套餐

**数据库设计：**

​	setmeal表为套餐表，用于存储套餐的信息。具体表结构如下：

| 字段名      | 数据类型      | 说明         | 备注        |
| ----------- | ------------- | ------------ | ----------- |
| id          | bigint        | 主键         | 自增        |
| name        | varchar(32)   | 套餐名称     | 唯一        |
| category_id | bigint        | 分类id       | 逻辑外键    |
| price       | decimal(10,2) | 套餐价格     |             |
| image       | varchar(255)  | 图片路径     |             |
| description | varchar(255)  | 套餐描述     |             |
| status      | int           | 售卖状态     | 1起售 0停售 |
| create_time | datetime      | 创建时间     |             |
| update_time | datetime      | 最后修改时间 |             |
| create_user | bigint        | 创建人id     |             |
| update_user | bigint        | 最后修改人id |             |

setmeal_dish表为套餐菜品关系表，用于存储套餐和菜品的关联关系。具体表结构如下：

| 字段名     | 数据类型      | 说明     | 备注     |
| ---------- | ------------- | -------- | -------- |
| id         | bigint        | 主键     | 自增     |
| setmeal_id | bigint        | 套餐id   | 逻辑外键 |
| dish_id    | bigint        | 菜品id   | 逻辑外键 |
| name       | varchar(32)   | 菜品名称 | 冗余字段 |
| price      | decimal(10,2) | 菜品单价 | 冗余字段 |
| copies     | int           | 菜品份数 |          |

### 1.2.代码开发

#### 1.2.1.根据分类id查询菜品

##### 1.2.1.1.DishController

```java
/**
 * 根据分类id查询菜品
 * @param categoryId
 * @return
 */
@GetMapping("/list")
@ApiOperation("根据分类id查询菜品")
public Result<List<Dish>> list(Long categoryId){
    log.info("根据分类id查询菜品: {}", categoryId);
    List<Dish> list = dishService.list(categoryId);
    return Result.success(list);
}
```

##### 1.2.1.2.DishServiceImpl

```java
/**
 * 根据分类id查询菜品
 * @param categoryId
 * @return
 */
@Override
public List<Dish> list(Long categoryId) {
    Dish dish = Dish.builder()
            .categoryId(categoryId)
            .status(StatusConstant.ENABLE)
            .build();
    return dishMapper.list(dish);
}
```

##### 1.2.1.3.DishMapper

```java
/**
 * 根据分类id查询菜品
 * @param dish
 * @return
 */
List<Dish> list(Dish dish);
```

##### 1.2.1.4.DishMapper.xml

```xml
<select id="list" resultType="com.sky.entity.Dish">
    select * from dish
    <where>
        <if test="name != null">
            and name like concat('%',#{name},'%')
        </if>
        <if test="categoryId != null">
            and category_id = #{categoryId}
        </if>
        <if test="status != null">
            and status = #{status}
        </if>
    </where>
    order by create_time desc
</select>
```

#### 1.2.2.新增套餐

##### 1.2.2.1.SetmealController

```java
/**
 * 套餐管理
 */
@RestController
@RequestMapping("/admin/setmeal")
@Api(tags = "套餐相关接口")
@Slf4j
public class SetmealController {

    @Autowired
    private SetmealService setmealService;

    /**
     * 新增套餐
     * @param setmealDTO
     * @return
     */
    @PostMapping
    @ApiOperation("新增套餐")
    public Result save(@RequestBody SetmealDTO setmealDTO) {
        log.info("新增套餐: {}", setmealDTO);
        setmealService.saveWithDish(setmealDTO);
        return Result.success();
    }
}
```

##### 1.2.2.2.SetmealServiceImpl

```java
@Service
@Slf4j
public class SetmealServiceImpl implements SetmealService {
    @Autowired
    private SetmealMapper setmealMapper;
    @Autowired
    private SetmealDishMapper setmealDishMapper;
    @Autowired
    private DishMapper dishMapper;

    /**
     * 新增套餐
     * @param setmealDTO
     */
    @Override
    @Transactional
    public void saveWithDish(SetmealDTO setmealDTO) {
        Setmeal setmeal = new Setmeal();
        BeanUtils.copyProperties(setmealDTO, setmeal);

        //向套餐表插入数据
        setmealMapper.insert(setmeal);

        //获取生成的套餐id
        Long setmealId = setmeal.getId();

        List<SetmealDish> setmealDishes = setmealDTO.getSetmealDishes();
        setmealDishes.forEach(setmealDish -> {
            setmealDish.setSetmealId(setmealId);
        });

        //保存套餐和菜品的关联关系
        setmealDishMapper.insertBatch(setmealDishes);
    }
}
```

##### 1.2.2.3.SetmealMapper

```java
/**
 * 新增套餐
 * @param setmeal
 */
@AutoFill(value = OperationType.INSERT)
void insert(Setmeal setmeal);
```

##### 1.2.2.4.SetmealMapper.xml

```xml
<insert id="insert" useGeneratedKeys="true" keyProperty="id">
    insert into setmeal
    (category_id, name, price, status, description, image, create_time, update_time, create_user, update_user)
    values (#{categoryId}, #{name}, #{price}, #{status}, #{description}, #{image}, #{createTime}, #{updateTime},
            #{createUser}, #{updateUser})
</insert>
```

##### 1.2.2.5.SetmealDishMapper

```java
/**
 * 批量保存套餐和菜品的关联关系
 * @param setmealDishes
 */
void insertBatch(List<SetmealDish> setmealDishes);
```

##### 1.2.2.6.SetmealDishMapper.xml

```java
<insert id="insertBatch">
    insert into setmeal_dish
    (setmeal_id,dish_id,name,price,copies)
    values
    <foreach collection="setmealDishes"  item="sd" separator=",">
        (#{sd.setmealId},#{sd.dishId},#{sd.name},#{sd.price},#{sd.copies})
    </foreach>
</insert>
```

## 2.套餐分页查询

### 2.1.需求分析和设计

**业务规则：**

​	1.根据页码进行分页展示

​	2.每页展示10条数据

​	3.可以根据需要，按照套餐名称、分类、售卖状态进行查询

**基本信息:**

​	path:  /admin/setmeal/page

​	Method:  GET

**请求参数:**  Query参数

| 名称       | 类型   | 是否必须 | 默认值 | 备注         | 其他信息 |
| ---------- | ------ | -------- | ------ | ------------ | -------- |
| categoryId | string | 非必须   |        | 分类id       |          |
| name       | string | 非必须   |        | 套餐名称     |          |
| page       | string | 必须     |        | 页码         |          |
| pageSize   | string | 必须     |        | 每页记录数   |          |
| status     | string | 非必须   |        | 套餐起售状态 |          |

**返回响应:**

{

​    "code": 0,

​    "msg": null,

​    "data":{

​        "total": 0,

​        "records": [

​            {

​                "id": 0,

​                "categoryId": 0,

​                "name": "string",

​                "price": 0,

​                "status": 0,

​                "description": "string",

​                "image": "string",

​                "updateTime": "string",

​                "categoryName": "string"

​            }

​        ]

​    }

 }

### 2.2.代码开发

#### 2.2.1.SetmealController

```java
/**
 * 分页查询
 * @param setmealPageQueryDTO
 * @return
 */
@GetMapping("/page")
@ApiOperation("分页查询")
public Result<PageResult> page(SetmealPageQueryDTO setmealPageQueryDTO) {
    log.info("分页查询: {}", setmealPageQueryDTO);
    PageResult pageResult = setmealService.pageQuery(setmealPageQueryDTO);
    return Result.success(pageResult);
}
```

#### 2.2.2.SetmealServiceImpl

```java
/**
 * 分页查询
 * @param setmealPageQueryDTO
 * @return
 */
@Override
public PageResult pageQuery(SetmealPageQueryDTO setmealPageQueryDTO) {
    PageHelper.startPage(setmealPageQueryDTO.getPage(), setmealPageQueryDTO.getPageSize());

    Page<SetmealVO> page = setmealMapper.pageQuery(setmealPageQueryDTO);
    long total = page.getTotal();
    List<SetmealVO> records = page.getResult();

    return new PageResult(total, records);
}
```

#### 2.2.3.SetmealMapper

```java
/**
 * 分页查询
 * @param setmealPageQueryDTO
 * @return
 */
Page<SetmealVO> pageQuery(SetmealPageQueryDTO setmealPageQueryDTO);
```

#### 2.2.4.SetmealMapper.xml

```xml
<select id="pageQuery" resultType="com.sky.vo.SetmealVO">
    select s.*, c.name categoryName from setmeal s left join category c on s.category_id = c.id
    <where>
        <if test = "name != null">
            and s.name like concat("%", #{name}, '%')
        </if>
        <if test = "status != null">
            and s.status = #{status}
        </if>
        <if test = "categoryId != null">
            and s.category_id = #{categoryId}
        </if>
    </where>
    order by s.create_time desc
</select>
```

## 3.删除套餐

### 3.1.需求分析和设计

**业务规则：**

​	1.可以一次删除一个套餐，也可以批量删除套餐

​	2.起售中的套餐不能删除

**基本信息:**

​	path:  /admin/setmeal

​	Method:  DELETE

**请求参数:**  Query参数

| 名称 | 类型   | 是否必须 | 默认值 | 备注 | 其他信息 |
| ---- | ------ | -------- | ------ | ---- | -------- |
| ids  | string | 必须     |        |      |          |

### 3.2.代码开发

#### 3.2.1.SetmealController

```java
/**
 * 批量删除套餐
 * @param ids
 * @return
 */
@DeleteMapping
@ApiOperation("批量删除套餐")
public Result delete(@RequestParam List<Long> ids) {
    log.info("批量删除套餐: {}", ids);
    setmealService.deleteBatch(ids);
    return Result.success();
}
```

#### 3.2.2.SetmealServiceImpl

```java
/**
 * 批量删除套餐
 * @param ids
 */
@Override
public void deleteBatch(List<Long> ids) {
    for (Long id : ids) {
        Setmeal setmeal = setmealMapper.getById(id);
        if(StatusConstant.ENABLE == setmeal.getStatus()) {
            // 起售中的套餐不能删除
            throw new DeletionNotAllowedException(MessageConstant.SETMEAL_ON_SALE);
        }
    }

    for (Long setmealId : ids) {
        // 删除套餐表中的数据
        setmealMapper.deleteById(setmealId);
        // 删除套餐菜品关系表中的数据
        setmealDishMapper.deleteBySetmealId(setmealId);
    }
}
```

#### 3.2.3.SetmealMapper

```java
/**
 * 根据id查询套餐
 * @param id
 * @return
 */
@Select("select * from setmeal where id = #{id}")
Setmeal getById(Long id);

/**
 * 根据id删除套餐
 * @param setmealId
 */
@Delete("delete from setmeal where id = #{id}")
void deleteById(Long setmealId);
```

#### 3.2.4.SetmealDishMapper

```java
/**
 * 根据套餐id删除套餐和菜品的关联关系
 * @param setmealId
 */
@Delete("delete from setmeal_dish where setmeal_id = #{setmealId}")
void deleteBySetmealId(Long setmealId);
```

## 4.修改套餐

### 4.1.需求分析和设计

**接口设计**（共涉及到5个接口）：

​	1.根据id查询套餐

​	2.根据类型查询分类（已完成）

​	3.根据分类id查询菜品（已完成）

​	4.图片上传（已完成）

​	5.修改套餐

**基本信息:  根据id查询套餐**

​	path:  /admin/setmeal/{id}

​	Method:  GET

**请求参数:  Path参数**

​	id  string  套餐id

**基本信息:  修改套餐**

​	path:  /admin/setmeal

​	Method:  PUT

### 4.2.代码开发

#### 4.2.1.根据id查询套餐

##### 4.2.1.1.SetmealController

```java
/**
 * 根据id查询套餐
 * @param id
 * @return
 */
@GetMapping("/{id}")
@ApiOperation("根据id查询套餐")
public Result<SetmealVO> getById(@PathVariable Long id) {
    log.info("根据id查询套餐: {}", id);
    SetmealVO setmealVO = setmealService.getByIdWithDish(id);
    return Result.success(setmealVO);
}
```

##### 4.2.1.2.SetmealServiceImpl

```java
    /**
     * 根据id查询套餐
     * @param id
     * @return
     */
    @Override
    public SetmealVO getByIdWithDish(Long id) {
        Setmeal setmeal = setmealMapper.getById(id);
        List<SetmealDish> setmealDishes = setmealDishMapper.getBySetmealId(id);

        SetmealVO setmealVO = new SetmealVO();
        BeanUtils.copyProperties(setmeal, setmealVO);
        setmealVO.setSetmealDishes(setmealDishes);

        return setmealVO;
    }
```

##### 4.2.1.3.SetmealDishMapper

```java
/**
 * 根据套餐id查询套餐和菜品的关系
 * @param setmealId
 * @return
 */
@Select("select * from setmeal_dish where setmeal_id = #{setmealId}")
List<SetmealDish> getBySetmealId(Long setmealId);
```

#### 4.2.2.修改套餐

##### 4.2.2.1.SetmealController

```java
/**
 * 修改套餐
 * @param setmealDTO
 * @return
 */
@PutMapping
@ApiOperation("修改套餐")
public Result update(@RequestBody SetmealDTO setmealDTO) {
    setmealService.update(setmealDTO);
    return Result.success();
}
```

##### 4.2.2.2.SetmealServiceImpl

```java
/**
 * 修改套餐
 * @param setmealDTO
 */
@Override
public void update(SetmealDTO setmealDTO) {
    Setmeal setmeal = new Setmeal();
    BeanUtils.copyProperties(setmealDTO, setmeal);

    // 根据id修改套餐
    setmealMapper.update(setmeal);

    Long setmealId = setmealDTO.getId();

    // 根据套餐id删除套餐和菜品的关联关系
    setmealDishMapper.deleteBySetmealId(setmealId);

    List<SetmealDish> setmealDishes = setmealDTO.getSetmealDishes();
    setmealDishes.forEach(setmealDish -> {
        setmealDish.setSetmealId(setmealId);
    });

    // 批量保存套餐和菜品的关联关系
    setmealDishMapper.insertBatch(setmealDishes);
}
```

## 5.起售停售套餐