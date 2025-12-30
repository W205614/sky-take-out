

**苍穹外卖**

# 一. 项目概述

## 1. 项目结构

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



## 2. 功能架构

管理端:员工管理,  分类管理,  菜品管理,  套餐管理,  订单管理,  工作台,  数据统计,  来单提醒

用户端:微信登录,  商品浏览,  购物车,  用户下单,  微信支付,  历史订单,  地址管理,  用户催单



## 3. 技术选型

用户层:node.js,  VUE.js,  ElementUI,  微信小程序,  apache echarts

网关层:Nginx

应用层:Spring Boot,  Spring MVC,  Spring Task,  httpclient,  Spring Cache,  JWT,  阿里云OSS,  Swagger,  POI,  WebSocket

数据层:MySQL,  Redis,  mybatis,  pagehelper,  sping data redis

工具:Git,  maven,  Junit,  postman



## 4. 问题

### 4.1 通过Swagger就可以生成接口文档, 那么我们是否就不需要Yapi, Apifox等工具?

1.Yapi, Apifox等是在设计阶段使用的工具, 管理和维护接口

2.Swagger在开发阶段使用的框架, 帮助后端开发人员做后端的接口测试



### 4.2 Swagger常用注解

| **注解**          | **说明**                                               |
| ----------------- | ------------------------------------------------------ |
| @Api              | 用在类上，例如Controller，表示对类的说明               |
| @ApiModel         | 用在类上，例如entity、DTO、VO                          |
| @ApiModelProperty | 用在属性上，描述属性信息                               |
| @ApiOperation     | 用在方法上，例如Controller的方法，说明方法的用途、作用 |



# 二. 员工管理

## 1. 新增员工

### 1.1 需求分析和设计

#### 1.1.1 接口设计

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



#### 1.1.2 Employee表结构设计

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



### 1.2 代码开发

注意:

​	1.当前端提交的数据和实体类中对应的属性差别比较大时, 建议使用DTO来封装数据

​	2.DTO方便封装前端传送的数据,但是对于持久层的数据传递建议使用实体类



#### 1.2.1 EmployeeDTO

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



#### 1.2.2 Employee

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



#### 1.2.3 EmployeeController

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



#### 1.2.4 EmployeeServiceImpl

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



#### 1.2.5 EmployeeMapper

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



#### 1.2.6 处理已存在账户异常

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



### 1.3 开发过程中关键问题

#### 1.3.1 如何获取的操作人id

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



## 2. 员工分页查询

### 2.1 需求分析和设计

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



### 2.2 代码开发

#### 2.2.1 EmployeePageQueryDTO

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



#### 2.2.2 PageResult

```java
public class PageResult implements Serializable {

    private long total; //总记录数

    private List records; //当前页数据集合

}
```



#### 2.2.3 EmployeeController

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



#### 2.2.4 EmployeeServiceImpl

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



#### 2.2.5 EmployeeMapper

```java
/**
 * 分页查询
 * @param employeePageQueryDTO
 * @return
 */
Page<Employee> pageQuery(EmployeePageQueryDTO employeePageQueryDTO);
```



#### 2.2.6 EmployeeMapper.xml

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

### 2.3 开发过程问题解决

#### 2.3.1 如何处理日期的表示

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



## 3. 启用禁用员工账号

### 3.1 需求分析和设计

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



### 3.2 代码开发

#### 3.2.1 EmployeeController

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



#### 3.2.2 EmployeeServiceImpl

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



#### 3.2.3 EmployeeMapper

```java
/**
 * 启用禁用员工账号
 * @param employee
 */
void update(Employee employee);
```



#### 3.2.4 EmployeeMapper.xml

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



## 4. 编辑员工

### 4.1 需求分析和设计

#### 4.1.1 根据id查询员工信息

请求参数: Path 参数

​	id string  员工id



#### 4.1.2 编辑员工信息

application/json

{    

​	"id": 0, 

   "idNumber": "string",

​    "name": "string", 

   "phone": "string", 

   "sex": "string",

​    "username": "string"

 }



### 4.2 代码开发

#### 4.2.1 EmployeeController

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



#### 4.2.2 EmployeeServiceImpl

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



#### 4.2.3 EmployeeMapper

```java
/**
 * 根据id查询员工信息
 * @param id
 * @return
 */
@Select("select * from employee where id = #{id}")
Employee getById(Long id);
```



# 三. 分类管理

## 1. 需求分析和设计

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



## 2. 代码开发

### 2.1 CategoryController

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



### 2.2 CategoryServiceImpl

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



### 2.3 CategoryMapper

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



### 2.4 DishMapper

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



### 2.5 SetmealMapper

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



### 2.6 CategoryMapper.xml

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



# 四. 菜品管理

## 1. 公共字段自动填充

### 1.1 问题分析

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



### 1.2 实现思路

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



### 1.3 代码开发

#### 1.3.1 OperationType

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



#### 1.3.2 AutoFillConstant

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



#### 1.3.3 AutoFill

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



#### 1.3.4 AutoFillAspect

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



#### 1.3.5 Mapper层操作

```java
@AutoFill(value = OperationType.INSERT)
@AutoFill(value = OperationType.UPDATE)
```



## 2. 新增菜品

### 2.1 需求分析和设计

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



### 2.2 代码开发

#### 2.2.1 文件上传

##### 2.2.1.1 CommonController

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



##### 2.2.1.2 OssConfiguration

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



##### 2.2.1.3 AliOssUtil

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



##### 2.2.1.4 AliOssProperties

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



##### 2.2.1.5 配置文件

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



#### 2.2.2 新增菜品

##### 2.2.2.1 DishController

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



##### 2.2.2.2 DishServiceImpl

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



##### 2.2.2.3 DishMapper

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



##### 2.2.2.4 DisMapper.xml

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



##### 2.2.2.5 DishFlavorMapper

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



##### 2.2.2.6 DishFlavorMapper.xml

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



## 3. 菜品分页查询

### 3.1 需求分析和设计

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



### 3.2 代码开发

#### 3.2.1 DishPageQueryDTO

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



#### 3.2.2 DishVO

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



#### 3.2.3 DishController

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



#### 3.2.4 DishServiceImpl

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



#### 3.2.5 DishMapper

```java
/**
 * 菜品分页查询
 * @param dishPageQueryDTO
 * @return
 */
Page<DishVO> pageQuery(DishPageQueryDTO dishPageQueryDTO);
```



#### 3.2.6 DishMapper.xml

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



## 4. 删除菜品

### 4.1 需求分析和设计

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



### 4.2 代码开发

#### 4.2.1 DishController

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



#### 4.2.2 DishServiceImpl

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



#### 4.2.3 DishMapper

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



#### 4.2.4 DishMapper.xml

```xml
<delete id="deleteByIds">
    delete from dish where id in
    <foreach collection="ids" item="id" separator="," open="(" close=")">
        #{id}
    </foreach>
</delete>
```



#### 4.2.5 SetmealDishMapper

```java
/**
 * 根据菜品id查询对应的套餐id
 * @param dishIds
 * @return
 */
List<Long> getSetmealIdsByDishIds(List<Long> dishIds);
```



#### 4.2.6 SetmealDishMapper.xml

```xml
<select id="getSetmealIdsByDishIds" resultType="java.lang.Long">
    select setmeal_id from setmeal_dish where  dish_id in
    <foreach collection="dishIds" item="dishId" separator="," open="(" close=")">
        #{dishId}
    </foreach>
</select>
```



#### 4.2.7 DishFlavorMapper

```java
/**
 * 根据菜品id批量删除对应的口味表
 * @param dishIds
 */
void deleteByDishIds(List<Long> dishIds);
```



#### 4.2.8 DishFlavorMapper.xml

```xml
<delete id="deleteByDishIds">
    delete from dish_flavor where dish_id in
    <foreach collection="dishIds" item="dishId" separator="," open="(" close=")">
        #{dishId}
    </foreach>
</delete>
```



## 5. 修改菜品

### 5.1 需求分析和设计

#### 5.1.1 接口设计

##### 5.1.1.1 根据id查询菜品

**基本信息:**

​	path:  /admin/dish/{id}

​	Method:  GET

**请求参数:**

​	path参数:

| 名称 | 类型   | 是否必须 | 默认值 | 备注   | 其他信息 |
| ---- | ------ | -------- | ------ | ------ | -------- |
| id   | string | 必须     |        | 菜品id |          |



##### 5.1.1.2 修改菜品

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



### 5.2 代码开发

#### 5.2.1 根据id查询菜品

##### 5.2.1.1 DishController

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



##### 5.2.1.2 DishServiceImpl

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



##### 5.2.1.3 DishMapper

```java
/**
 * 根据id查询菜品
 * @param id
 * @return
 */
@Select("select * from dish where id = #{id}")
Dish getById(Long id);
```



##### 5.2.1.4 DishFlavorMapper

```java
/**
 * 根据菜品id查询对应的口味数据
 * @param dishId
 * @return
 */
@Select("select * from dish_flavor where dish_id = #{dishId}")
List<DishFlavor> getByDishId(Long dishId);
```



#### 5.2.2 修改菜品

##### 5.2.2.1 DishController

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



##### 5.2.2.2 DishServiceImpl

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



##### 5.2.2.3 DishMapper

```java
/**
 * 修改菜品基本信息
 * @param dish
 */
@AutoFill(value = OperationType.UPDATE)
void update(Dish dish);
```



##### 5.2.2.4 DishMapper.xml

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



## 6. 菜品起售停售

### 6.1 需求分析和设计

**业务规则:**

​	1.菜品进行起售和停售

​	2.如果菜品停售的话, 包含该菜品的套餐也要停售

**基本信息:**

​	path:  /admin/dish/status/{status}

​	Method:  POST

**请求参数:**

​	Path 参数:  status  string  1为起售，0为停售

​	Query 参数:  id  string  菜品id



### 6.2 代码开发

#### 6.2.1 DishController

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



#### 6.2.2 DishServiceImpl

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



#### 6.2.3 SetmealMapper

```java
/**
 * 根据id修改套餐
 * @param setmeal
 */
@AutoFill(value = OperationType.UPDATE)
void update(Setmeal setmeal);
```



#### 6.2.4 SetmealMapper.xml

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



# 五. 套餐管理

## 1. 新增套餐

### 1.1 需求分析和设计

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



### 1.2 代码开发

#### 1.2.1 根据分类id查询菜品

##### 1.2.1.1 DishController

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



##### 1.2.1.2 DishServiceImpl

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



##### 1.2.1.3 DishMapper

```java
/**
 * 根据分类id查询菜品
 * @param dish
 * @return
 */
List<Dish> list(Dish dish);
```



##### 1.2.1.4 DishMapper.xml

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



#### 1.2.2 新增套餐

##### 1.2.2.1 SetmealController

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



##### 1.2.2.2 SetmealServiceImpl

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



##### 1.2.2.3 SetmealMapper

```java
/**
 * 新增套餐
 * @param setmeal
 */
@AutoFill(value = OperationType.INSERT)
void insert(Setmeal setmeal);
```



##### 1.2.2.4 SetmealMapper.xml

```xml
<insert id="insert" useGeneratedKeys="true" keyProperty="id">
    insert into setmeal
    (category_id, name, price, status, description, image, create_time, update_time, create_user, update_user)
    values (#{categoryId}, #{name}, #{price}, #{status}, #{description}, #{image}, #{createTime}, #{updateTime},
            #{createUser}, #{updateUser})
</insert>
```



##### 1.2.2.5 SetmealDishMapper

```java
/**
 * 批量保存套餐和菜品的关联关系
 * @param setmealDishes
 */
void insertBatch(List<SetmealDish> setmealDishes);
```



##### 1.2.2.6 SetmealDishMapper.xml

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



## 2. 套餐分页查询

### 2.1 需求分析和设计

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



### 2.2 代码开发

#### 2.2.1 SetmealController

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



#### 2.2.2 SetmealServiceImpl

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



#### 2.2.3 SetmealMapper

```java
/**
 * 分页查询
 * @param setmealPageQueryDTO
 * @return
 */
Page<SetmealVO> pageQuery(SetmealPageQueryDTO setmealPageQueryDTO);
```



#### 2.2.4 SetmealMapper.xml

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



## 3. 删除套餐

### 3.1 需求分析和设计

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



### 3.2 代码开发

#### 3.2.1 SetmealController

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



#### 3.2.2 SetmealServiceImpl

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



#### 3.2.3 SetmealMapper

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



#### 3.2.4 SetmealDishMapper

```java
/**
 * 根据套餐id删除套餐和菜品的关联关系
 * @param setmealId
 */
@Delete("delete from setmeal_dish where setmeal_id = #{setmealId}")
void deleteBySetmealId(Long setmealId);
```



## 4. 修改套餐

### 4.1 需求分析和设计

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



### 4.2 代码开发

#### 4.2.1 根据id查询套餐

##### 4.2.1.1 SetmealController

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



##### 4.2.1.2 SetmealServiceImpl

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



##### 4.2.1.3 SetmealDishMapper

```java
/**
 * 根据套餐id查询套餐和菜品的关系
 * @param setmealId
 * @return
 */
@Select("select * from setmeal_dish where setmeal_id = #{setmealId}")
List<SetmealDish> getBySetmealId(Long setmealId);
```



#### 4.2.2 修改套餐

##### 4.2.2.1 SetmealController

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



##### 4.2.2.2 SetmealServiceImpl

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



## 5. 起售停售套餐

### 5.1 需求分析和设计

**基本信息:**

​	path:  /admin/setmeal/status/{status}

  Method:  POST

**业务规则：**

​	1.可以对状态为起售的套餐进行停售操作，可以对状态为停售的套餐进行起售操作

​	2.起售的套餐可以展示在用户端，停售的套餐不能展示在用户端

​	3.起售套餐时，如果套餐内包含停售的菜品，则不能起售



### 5.2 代码开发

#### 5.2.1 SetmealController

```java
/**
 * 套餐起售停售
 * @param status
 * @param id
 * @return
 */
@PostMapping("/status/{status}")
@ApiOperation("套餐起售停售")
public Result startOrStop(@PathVariable Integer status, Long id) {
    setmealService.startOrStop(status, id);
    return Result.success();
}
```



#### 5.2.2 SetmealServiceImpl

```java
/**
 * 套餐起售停售
 * @param status
 * @param id
 */
@Override
public void startOrStop(Integer status, Long id) {
    // 套餐起售时, 判断套餐内是否有停售餐品, 如果有不能起售
    if(status == StatusConstant.ENABLE) {
        List<Dish> dishList = dishMapper.getBySetmealId(id);
        if(dishList != null && dishList.size() > 0) {
            dishList.forEach(dish -> {
                if(StatusConstant.DISABLE == dish.getStatus()) {
                    throw new SetmealEnableFailedException(MessageConstant.SETMEAL_ENABLE_FAILED);
                }
            });
        }
    }

    Setmeal setmeal = Setmeal.builder()
            .id(id)
            .status(status)
            .build();
    setmealMapper.update(setmeal);
}
```



#### 5.2.3 DishMapper

```java
/**
 * 根据套餐id查询菜品
 * @param setmealId
 * @return
 */
@Select("select a.* from dish a left join setmeal_dish b on a.id = b.dish_id where b.setmeal_id = #{setmealId}")
List<Dish> getBySetmealId(Long setmealId);
```

# 六. 店铺营业状态设置

## 1. Redis入门

### 1.1 Redis简介

Redis是一个基于**内存**的key-value结构数据库。Redis 是互联网技术领域使用最为广泛的**存储中间件**。

**官网：**https://redis.io
**中文网：**https://www.redis.net.cn/

**key-value结构存储：** 

**主要特点：**

- 基于内存存储，读写性能高  
- 适合存储热点数据（热点商品、资讯、新闻）
- 企业应用广泛

Redis是用C语言开发的一个开源的高性能键值对(key-value)数据库，官方提供的数据是可以达到100000+的QPS（每秒内查询次数）。它存储的value类型比较丰富，也被称为结构化的NoSql数据库。

NoSql（Not Only SQL），不仅仅是SQL，泛指**非关系型数据库**。NoSql数据库并不是要取代关系型数据库，而是关系型数据库的补充。

**关系型数据库(RDBMS)：**

- Mysql
- Oracle
- DB2
- SQLServer

**非关系型数据库(NoSql)：**

- Redis
- Mongo db
- MemCached

### 1.2 Redis服务启动与停止

以window版Redis进行演示：

#### 1.2.1 服务启动命令

**redis-server.exe redis.windows.conf** 

Redis服务默认端口号为 **6379** ，通过快捷键**Ctrl + C** 即可停止Redis服务

当Redis服务启动成功后，可通过客户端进行连接。

#### 1.2.2 客户端连接命令

**redis-cli.exe** 

通过redis-cli.exe命令默认连接的是本地的redis服务，并且使用默认6379端口。也可以通过指定如下参数连接：

- -h ip地址
- -p 端口号
- -a 密码（如果需要）

#### 1.2.3 修改Redis配置文件

设置Redis服务密码，修改redis.windows.conf

```
requirepass 123456
```

**注意：**

- 修改密码后需要重启Redis服务才能生效
- Redis配置文件中 # 表示注释

重启Redis后，再次连接Redis时，需加上密码，否则连接失败。

```
redis-cli.exe -h localhost -p 6379 -a 123456 
```

此时，-h 和 -p 参数可省略不写。

## 2. Redis数据类型

### 2.1 五种常用数据类型介绍

Redis存储的是key-value结构的数据，其中key是字符串类型，value有5种常用的数据类型：

- 字符串 string
- 哈希 hash
- 列表 list
- 集合 set
- 有序集合 sorted set / zset



### 2.2 各种数据类型特点 

**解释说明：**

- 字符串(string)：普通字符串，Redis中最简单的数据类型
- 哈希(hash)：也叫散列，类似于Java中的HashMap结构
- 列表(list)：按照插入顺序排序，可以有重复元素，类似于Java中的LinkedList
- 集合(set)：无序集合，没有重复元素，类似于Java中的HashSet
- 有序集合(sorted set/zset)：集合中每个元素关联一个分数(score)，根据分数升序排序，没有重复元素



## 3. Redis常用命令

### 3.1 字符串操作命令

Redis 中字符串类型常用命令：

- **SET** key value 					         设置指定key的值
- **GET** key                                        获取指定key的值
- **SETEX** key seconds value         设置指定key的值，并将 key 的过期时间设为 seconds 秒
- **SETNX** key value                        只有在 key    不存在时设置 key 的值

更多命令可以参考Redis中文网：https://www.redis.net.cn



### 3.2 哈希操作命令

Redis hash 是一个string类型的 field 和 value 的映射表，hash特别适合用于存储对象，常用命令：

- **HSET** key field value             将哈希表 key 中的字段 field 的值设为 value
- **HGET** key field                       获取存储在哈希表中指定字段的值
- **HDEL** key field                       删除存储在哈希表中的指定字段
- **HKEYS** key                              获取哈希表中所有字段
- **HVALS** key                              获取哈希表中所有值 



### 3.3 列表操作命令

Redis 列表是简单的字符串列表，按照插入顺序排序，常用命令：

- **LPUSH** key value1 [value2]         将一个或多个值插入到列表头部
- **LRANGE** key start stop                获取列表指定范围内的元素
- **RPOP** key                                       移除并获取列表最后一个元素
- **LLEN** key                                        获取列表长度
- **BRPOP** key1 [key2 ] timeout       移出并获取列表的最后一个元素， 如果列表没有元素会阻塞列表直到等待超    时或发现可弹出元素为止 



### 3.4 集合操作命令

Redis set 是string类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据，常用命令：

- **SADD** key member1 [member2]            向集合添加一个或多个成员
- **SMEMBERS** key                                         返回集合中的所有成员
- **SCARD** key                                                  获取集合的成员数
- **SINTER** key1 [key2]                                   返回给定所有集合的交集
- **SUNION** key1 [key2]                                 返回所有给定集合的并集
- **SREM** key member1 [member2]            移除集合中一个或多个成员 



### 3.5 有序集合操作命令

Redis有序集合是string类型元素的集合，且不允许有重复成员。每个元素都会关联一个double类型的分数。常用命令：

常用命令：

- **ZADD** key score1 member1 [score2 member2]     向有序集合添加一个或多个成员
- **ZRANGE** key start stop [WITHSCORES]                     通过索引区间返回有序集合中指定区间内的成员
- **ZINCRBY** key increment member                              有序集合中对指定成员的分数加上增量 increment
- **ZREM** key member [member ...]                                移除有序集合中的一个或多个成员 



### 3.6 通用命令

Redis的通用命令是不分数据类型的，都可以使用的命令：

- KEYS pattern 		查找所有符合给定模式( pattern)的 key 
- EXISTS key 		检查给定 key 是否存在
- TYPE key 		返回 key 所储存的值的类型
- DEL key 		该命令用于在 key 存在是删除 key



## 4. 在Java中操作Redis

### 4.1 Redis的Java客户端

前面我们讲解了Redis的常用命令，这些命令是我们操作Redis的基础，那么我们在java程序中应该如何操作Redis呢？这就需要使用Redis的Java客户端，就如同我们使用JDBC操作MySQL数据库一样。

Redis 的 Java 客户端很多，常用的几种：

- Jedis
- Lettuce
- Spring Data Redis

Spring 对 Redis 客户端进行了整合，提供了 Spring Data Redis，在Spring Boot项目中还提供了对应的Starter，即 spring-boot-starter-data-redis。

我们重点学习**Spring Data Redis**。



### 4.2 Spring Data Redis使用方式

#### 4.2.1 介绍

Spring Data Redis 是 Spring 的一部分，提供了在 Spring 应用中通过简单的配置就可以访问 Redis 服务，对 Redis 底层开发包进行了高度封装。在 Spring 项目中，可以使用Spring Data Redis来简化 Redis 操作。

网址：https://spring.io/projects/spring-data-redis 

Spring Boot提供了对应的Starter，maven坐标：

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

Spring Data Redis中提供了一个高度封装的类：**RedisTemplate**，对相关api进行了归类封装,将同一类型操作封装为operation接口，具体分类如下：

- ValueOperations：string数据操作
- SetOperations：set类型数据操作
- ZSetOperations：zset类型数据操作
- HashOperations：hash类型的数据操作
- ListOperations：list类型的数据操作



#### 4.2.2 环境搭建

进入到sky-server模块

**1). 导入Spring Data Redis的maven坐标(已完成)**

```xml
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```



**2). 配置Redis数据源**

在application-dev.yml中添加

```yaml
sky:
  redis:
    host: localhost
    port: 6379
    password: 123456
    database: 10
```

**解释说明：**

database:指定使用Redis的哪个数据库，Redis服务启动后默认有16个数据库，编号分别是从0到15。

可以通过修改Redis配置文件来指定数据库的数量。

在application.yml中添加读取application-dev.yml中的相关Redis配置

```yaml
spring:
  profiles:
    active: dev
  redis:
    host: ${sky.redis.host}
    port: ${sky.redis.port}
    password: ${sky.redis.password}
    database: ${sky.redis.database}
```



**3). 编写配置类，创建RedisTemplate对象**

```java
package com.sky.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.StringRedisSerializer;

@Configuration
@Slf4j
public class RedisConfiguration {

    @Bean
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory){
        log.info("开始创建redis模板对象...");
        RedisTemplate redisTemplate = new RedisTemplate();
        //设置redis的连接工厂对象
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        //设置redis key的序列化器
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        return redisTemplate;
    }
}
```

**解释说明：**

当前配置类不是必须的，因为 Spring Boot 框架会自动装配 RedisTemplate 对象，但是默认的key序列化器为

JdkSerializationRedisSerializer，导致我们存到Redis中后的数据和原始数据有差别，故设置为

StringRedisSerializer序列化器。



**4). 通过RedisTemplate对象操作Redis**

在test下新建测试类

```java
package com.sky.test;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.*;

@SpringBootTest
public class SpringDataRedisTest {
    @Autowired
    private RedisTemplate redisTemplate;

    @Test
    public void testRedisTemplate(){
        System.out.println(redisTemplate);
        //string数据操作
        ValueOperations valueOperations = redisTemplate.opsForValue();
        //hash类型的数据操作
        HashOperations hashOperations = redisTemplate.opsForHash();
        //list类型的数据操作
        ListOperations listOperations = redisTemplate.opsForList();
        //set类型数据操作
        SetOperations setOperations = redisTemplate.opsForSet();
        //zset类型数据操作
        ZSetOperations zSetOperations = redisTemplate.opsForZSet();
    }
}
```

上述环境搭建完毕后，接下来，我们就来具体对常见5种数据类型进行操作。



#### 4.2.3 操作常见类型数据

**1). 操作字符串类型数据**

```java
	/**
     * 操作字符串类型的数据
     */
    @Test
    public void testString(){
        // set get setex setnx
        redisTemplate.opsForValue().set("name","小明");
        String city = (String) redisTemplate.opsForValue().get("name");
        System.out.println(city);
        redisTemplate.opsForValue().set("code","1234",3, TimeUnit.MINUTES);
        redisTemplate.opsForValue().setIfAbsent("lock","1");
        redisTemplate.opsForValue().setIfAbsent("lock","2");
    }
```



**2). 操作哈希类型数据**

```java
	/**
     * 操作哈希类型的数据
     */
    @Test
    public void testHash(){
        //hset hget hdel hkeys hvals
        HashOperations hashOperations = redisTemplate.opsForHash();

        hashOperations.put("100","name","tom");
        hashOperations.put("100","age","20");

        String name = (String) hashOperations.get("100", "name");
        System.out.println(name);

        Set keys = hashOperations.keys("100");
        System.out.println(keys);

        List values = hashOperations.values("100");
        System.out.println(values);

        hashOperations.delete("100","age");
    }
```



**3). 操作列表类型数据**

```java
	/**
     * 操作列表类型的数据
     */
    @Test
    public void testList(){
        //lpush lrange rpop llen
        ListOperations listOperations = redisTemplate.opsForList();

        listOperations.leftPushAll("mylist","a","b","c");
        listOperations.leftPush("mylist","d");

        List mylist = listOperations.range("mylist", 0, -1);
        System.out.println(mylist);

        listOperations.rightPop("mylist");

        Long size = listOperations.size("mylist");
        System.out.println(size);
    }
```



**4). 操作集合类型数据**

```java
	/**
     * 操作集合类型的数据
     */
    @Test
    public void testSet(){
        //sadd smembers scard sinter sunion srem
        SetOperations setOperations = redisTemplate.opsForSet();

        setOperations.add("set1","a","b","c","d");
        setOperations.add("set2","a","b","x","y");

        Set members = setOperations.members("set1");
        System.out.println(members);

        Long size = setOperations.size("set1");
        System.out.println(size);

        Set intersect = setOperations.intersect("set1", "set2");
        System.out.println(intersect);

        Set union = setOperations.union("set1", "set2");
        System.out.println(union);

        setOperations.remove("set1","a","b");
    }
```



**5). 操作有序集合类型数据**

```java
	/**
     * 操作有序集合类型的数据
     */
    @Test
    public void testZset(){
        //zadd zrange zincrby zrem
        ZSetOperations zSetOperations = redisTemplate.opsForZSet();

        zSetOperations.add("zset1","a",10);
        zSetOperations.add("zset1","b",12);
        zSetOperations.add("zset1","c",9);

        Set zset1 = zSetOperations.range("zset1", 0, -1);
        System.out.println(zset1);

        zSetOperations.incrementScore("zset1","c",10);

        zSetOperations.remove("zset1","a","b");
    }
```



**6). 通用命令操作**

```java
	/**
     * 通用命令操作
     */
    @Test
    public void testCommon(){
        //keys exists type del
        Set keys = redisTemplate.keys("*");
        System.out.println(keys);

        Boolean name = redisTemplate.hasKey("name");
        Boolean set1 = redisTemplate.hasKey("set1");

        for (Object key : keys) {
            DataType type = redisTemplate.type(key);
            System.out.println(type.name());
        }

        redisTemplate.delete("mylist");
    }
```



## 5. 店铺营业状态设置

### 5.1 需求分析和设计

**接口设计:**

​	1.设置营业状态

​	2.管理端查询营业状态

​	3.用户端查询营业状态

**基本信息:**

​	1.设置营业状态:

​		path:  /admin/shop/{status}

​		Method:  PUT

​		请求参数:  path参数

​			status  string  必需  店铺营业状态：1为营业，0为打烊

​	2.管理端查询营业状态

​		path:  /admin/shop/status

​		Method:  GET

​		请求参数:  无

​	3.用户端查询营业状态

​		path:  /user/shop/status

​		Method:  GET

​		请求参数:  无



### 5.2 代码开发

#### 5.2.1 管理端

```java
@RestController("adminShopController")
@RequestMapping("/admin/shop")
@Api(tags = "店铺相关接口")
@Slf4j
public class ShopController {
    public static final String KEY = "SHOP_STATUS";

    @Autowired
    private RedisTemplate redisTemplate;

    /**
     * 设置店铺的营业状态
     * @param status
     * @return
     */
    @PutMapping("/{status}")
    @ApiOperation("设置店铺的营业状态")
    public Result setStatus(@PathVariable Integer status) {
        log.info("设置店铺的营业状态为: {}", status == 1 ? "营业中" : "打烊中");
        redisTemplate.opsForValue().set(KEY, status);
        return Result.success();
    }

    /**
     * 获取店铺的营业状态
     * @return
     */
    @GetMapping("/status")
    @ApiOperation("获取店铺的营业状态")
    public Result<Integer> getStatus() {
        Integer status = (Integer) redisTemplate.opsForValue().get(KEY);
        log.info("获取到店铺的营业状态为: {}", status == 1 ? "营业中" : "打烊中");
        return Result.success(status);
    }
}
```



#### 5.2.2 用户端

```java
@RestController("userShopController")
@RequestMapping("/user/shop")
@Api(tags = "店铺相关接口")
@Slf4j
public class ShopController {
    public static final String KEY = "SHOP_STATUS";

    @Autowired
    private RedisTemplate redisTemplate;

    /**
     * 获取店铺的营业状态
     * @return
     */
    @GetMapping("/status")
    @ApiOperation("获取店铺的营业状态")
    public Result<Integer> getStatus() {
        Integer status = (Integer) redisTemplate.opsForValue().get(KEY);
        log.info("获取到店铺的营业状态为: {}", status == 1 ? "营业中" : "打烊中");
        return Result.success(status);
    }
}
```



# 七. 微信登陆及商品浏览

## 1. HttpClient

### 1.1 介绍

HttpClient 是Apache Jakarta Common 下的子项目，可以用来提供高效的、最新的、功能丰富的支持 HTTP 协议的客户端编程工具包，并且它支持 HTTP 协议最新的版本和建议。

**HttpClient作用：**

- 发送HTTP请求
- 接收响应数据

  为什么要在Java程序中发送Http请求？有哪些应用场景呢？

**HttpClient应用场景：**

当我们在使用扫描支付、查看地图、获取验证码、查看天气等功能时    

其实，应用程序本身并未实现这些功能，都是在应用程序里访问提供这些功能的服务，访问这些服务需要发送HTTP请求，并且接收响应数据，可通过HttpClient来实现。 

**HttpClient的maven坐标：**

```xml
<dependency>
	<groupId>org.apache.httpcomponents</groupId>
	<artifactId>httpclient</artifactId>
	<version>4.5.13</version>
</dependency>
```

**HttpClient的核心API：**

- HttpClient：Http客户端对象类型，使用该类型对象可发起Http请求。
- HttpClients：可认为是构建器，可创建HttpClient对象。
- CloseableHttpClient：实现类，实现了HttpClient接口。
- HttpGet：Get方式请求类型。
- HttpPost：Post方式请求类型。

**HttpClient发送请求步骤：**

- 创建HttpClient对象
- 创建Http请求对象
- 调用HttpClient的execute方法发送请求



### 1.2 入门案例

对HttpClient编程工具包有了一定了解后，那么，我们使用HttpClient在Java程序当中来构造Http的请求，并且把请求发送出去，接下来，就通过入门案例分别发送**GET请求**和**POST请求**，具体来学习一下它的使用方法。

#### 1.2.1 GET方式请求

正常来说，首先，应该导入HttpClient相关的坐标，但在项目中，就算不导入，也可以使用相关的API。

因为在项目中已经引入了aliyun-sdk-oss坐标：

```xml
<dependency>
    <groupId>com.aliyun.oss</groupId>
    <artifactId>aliyun-sdk-oss</artifactId>
</dependency>
```

上述依赖的底层已经包含了HttpClient相关依赖。 

故选择导入或者不导入均可。

进入到sky-server模块，编写测试代码，发送GET请求。

**实现步骤：**

1. 创建HttpClient对象
2. 创建请求对象
3. 发送请求，接受响应结果
4. 解析结果
5. 关闭资源

```java
package com.sky.test;

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
public class HttpClientTest {

    /**
     * 测试通过httpclient发送GET方式的请求
     */
    @Test
    public void testGET() throws Exception{
        //创建httpclient对象
        CloseableHttpClient httpClient = HttpClients.createDefault();

        //创建请求对象
        HttpGet httpGet = new HttpGet("http://localhost:8080/user/shop/status");

        //发送请求，接受响应结果
        CloseableHttpResponse response = httpClient.execute(httpGet);

        //获取服务端返回的状态码
        int statusCode = response.getStatusLine().getStatusCode();
        System.out.println("服务端返回的状态码为：" + statusCode);

        HttpEntity entity = response.getEntity();
        String body = EntityUtils.toString(entity);
        System.out.println("服务端返回的数据为：" + body);

        //关闭资源
        response.close();
        httpClient.close();
    }
}
```

在访问http://localhost:8080/user/shop/status请求时，需要提前启动项目。



#### 1.2.2 POST方式请求

在HttpClientTest中添加POST方式请求方法，相比GET请求来说，POST请求若携带参数需要封装请求体对象，并将该对象设置在请求对象中。

**实现步骤：**

1. 创建HttpClient对象
2. 创建请求对象
3. 发送请求，接收响应结果
4. 解析响应结果
5. 关闭资源

```java
	/**
     * 测试通过httpclient发送POST方式的请求
     */
    @Test
    public void testPOST() throws Exception{
        // 创建httpclient对象
        CloseableHttpClient httpClient = HttpClients.createDefault();

        //创建请求对象
        HttpPost httpPost = new HttpPost("http://localhost:8080/admin/employee/login");

        JSONObject jsonObject = new JSONObject();
        jsonObject.put("username","admin");
        jsonObject.put("password","123456");

        StringEntity entity = new StringEntity(jsonObject.toString());
        //指定请求编码方式
        entity.setContentEncoding("utf-8");
        //数据格式
        entity.setContentType("application/json");
        httpPost.setEntity(entity);

        //发送请求
        CloseableHttpResponse response = httpClient.execute(httpPost);

        //解析返回结果
        int statusCode = response.getStatusLine().getStatusCode();
        System.out.println("响应码为：" + statusCode);

        HttpEntity entity1 = response.getEntity();
        String body = EntityUtils.toString(entity1);
        System.out.println("响应数据为：" + body);

        //关闭资源
        response.close();
        httpClient.close();
    }
```



## 2. 微信小程序开发

### 2.1 介绍

小程序是一种新的开放能力，开发者可以快速地开发一个小程序。可以在微信内被便捷地获取和传播，同时具有出色的使用体验。 

**官方网址：**https://mp.weixin.qq.com/cgi-bin/wx?token=&lang=zh_CN 

小程序主要运行微信内部，可通过上述网站来整体了解微信小程序的开发。

**首先，**在进行小程序开发时，需要先去注册一个小程序，在注册的时候，它实际上又分成了不同的注册的主体。我们可以以个人的身份来注册一个小程序，当然，也可以以企业政府、媒体或者其他组织的方式来注册小程序。那么，不同的主体注册小程序，最终开放的权限也是不一样的。比如以个人身份来注册小程序，是无法开通支付权限的。若要提供支付功能，必须是企业、政府或者其它组织等。所以，不同的主体注册小程序后，可开发的功能是不一样的。 

**然后，**微信小程序我们提供的一些开发的支持，实际上微信的官方是提供了一系列的工具来帮助开发者快速的接入
并且完成小程序的开发，提供了完善的开发文档，并且专门提供了一个开发者工具，还提供了相应的设计指南，同时也提供了一些小程序体验DEMO，可以快速的体验小程序实现的功能。

**最后，**开发完一个小程序要上线，也给我们提供了详细地接入流程。 



### 2.2 准备工作

开发微信小程序之前需要做如下准备工作：

- 注册小程序
- 完善小程序信息
- 下载开发者工具

**1). 注册小程序**

注册地址：https://mp.weixin.qq.com/wxopen/waregister?action=step1 



**2). 完善小程序信息**

登录小程序后台：https://mp.weixin.qq.com/

两种登录方式选其一即可 

完善小程序信息、小程序类目 

查看小程序的 AppID 



**3). 下载开发者工具**

下载地址： https://developers.weixin.qq.com/miniprogram/dev/devtools/stable.html

扫描登录开发者工具 

创建小程序项目 

熟悉开发者工具布局 

设置不校验合法域名 

**注：**开发阶段，小程序发出请求到后端的Tomcat服务器，若不勾选，请求发送失败。



### 2.3 入门案例

实际上，小程序的开发本质上属于前端开发，主要使用JavaScript开发，咱们现在的定位主要还是在后端，所以，对于小程序开发简单了解即可。

#### 2.3.1 小程序目录结构

小程序包含一个描述整体程序的 app 和多个描述各自页面的 page。一个小程序主体部分由三个文件组成，必须放在项目的根目录，如下： 

**文件说明：** 

**app.js：**必须存在，主要存放小程序的逻辑代码

**app.json：**必须存在，小程序配置文件，主要存放小程序的公共配置

**app.wxss:**  非必须存在，主要存放小程序公共样式表，类似于前端的CSS样式

对小程序主体三个文件了解后，其实一个小程序又有多个页面。比如说，有商品浏览页面、购物车的页面、订单支付的页面、商品的详情页面等等。那这些页面会放在哪呢？
会存放在pages目录。

每个小程序页面主要由四个文件组成： 

**文件说明：** 

**js文件：**必须存在，存放页面业务逻辑代码，编写的js代码。

**wxml文件：**必须存在，存放页面结构，主要是做页面布局，页面效果展示的，类似于HTML页面。

**json文件：**非必须，存放页面相关的配置。

**wxss文件：**非必须，存放页面样式表，相当于CSS文件。



#### 2.3.2 编写和编译小程序

**1). 编写**

进入到index.wxml，编写页面布局

```xml
<view class="container">
  <view>{{msg}}</view>
   <view>
    <button type="default" bindtap="getUserInfo">获取用户信息</button>
    <image style="width: 100px;height: 100px;" src="{{avatarUrl}}"></image>
    {{nickName}}
  </view>
   <view>
    <button type="primary" bindtap="wxlogin">微信登录</button>
    授权码：{{code}}
  </view>
   <view>
    <button type="warn" bindtap="sendRequest">发送请求</button>
    响应结果：{{result}}
  </view>
</view>
```

进入到index.js，编写业务逻辑代码

```javascript
Page({
  data:{
    msg:'hello world',
    avatarUrl:'',
    nickName:'',
    code:'',
    result:''
  },
  getUserInfo:function(){
    wx.getUserProfile({
      desc: '获取用户信息',
      success:(res) => {
        console.log(res)
        this.setData({
          avatarUrl:res.userInfo.avatarUrl,
          nickName:res.userInfo.nickName
        })
      }
    })
  },
  wxlogin:function(){
    wx.login({
      success: (res) => {
        console.log("授权码："+res.code)
        this.setData({
          code:res.code
        })
      }
    })
  },
  sendRequest:function(){
    wx.request({
      url: 'http://localhost:8080/user/shop/status',
      method:'GET',
      success:(res) => {
        console.log("响应结果：" + res.data.data)
        this.setData({
          result:res.data.data
        })
      }
    })
  }}) 
```



#### 2.3.3 发布小程序

小程序的代码都已经开发完毕，要将小程序发布上线，让所有的用户都能使用到这个小程序。

点击上传按钮： 

指定版本号： 

上传成功： 

把代码上传到微信服务器就表示小程序已经发布了吗？
**其实并不是。**当前小程序版本只是一个开发版本。

进到微信公众平台，打开版本管理页面。 

需提交审核，变成审核版本，审核通过后，进行发布，变成线上版本。

一旦成为线上版本，这就说明小程序就已经发布上线了，微信用户就可以在微信里面去搜索和使用这个小程序了。



## 3. 微信登录

### 3.1 导入小程序代码

开发微信小程序，本质上是属于前端的开发，我们的重点其实还是后端代码开发。所以，小程序的代码已经提供好了，直接导入到微信开发者工具当中，直接来使用就可以了。

**1). 导入代码** 

AppID：使用自己的AppID 

**2). 查看项目结构**

主体的文件:app.js app.json app.wxss
项目的页面比较多，主要存放在pages目录。 

**3). 修改配置**

因为小程序要请求后端服务，需要修改为自己后端服务的ip地址和端口号(默认不需要修改)

common-->vendor.js-->搜索(ctrl+f)-->baseUri 



### 3.2 微信登录流程

微信登录：https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/login.html

**流程图：** 

**步骤分析：**

1. 小程序端，调用wx.login()获取code，就是授权码。
2. 小程序端，调用wx.request()发送请求并携带code，请求开发者服务器(自己编写的后端服务)。
3. 开发者服务端，通过HttpClient向微信接口服务发送请求，并携带appId+appsecret+code三个参数。
4. 开发者服务端，接收微信接口服务返回的数据，session_key+opendId等。opendId是微信用户的唯一标识。
5. 开发者服务端，自定义登录态，生成令牌(token)和openid等数据返回给小程序端，方便后绪请求身份校验。
6. 小程序端，收到自定义登录态，存储storage。
7. 小程序端，后绪通过wx.request()发起业务请求时，携带token。
8. 开发者服务端，收到请求后，通过携带的token，解析当前登录用户的id。
9. 开发者服务端，身份校验通过后，继续相关的业务逻辑处理，最终返回业务数据。



接下来，我们使用Postman进行测试。

**说明：**

1. 调用 [wx.login()](https://developers.weixin.qq.com/miniprogram/dev/api/open-api/login/wx.login.html) 获取 **临时登录凭证code** ，并回传到开发者服务器。
2. 调用 [auth.code2Session](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/login/auth.code2Session.html) 接口，换取 **用户唯一标识 OpenID** 、 用户在微信开放平台帐号下的**唯一标识UnionID**（若当前小程序已绑定到微信开放平台帐号） 和 **会话密钥 session_key**。

之后开发者服务器可以根据用户标识来生成自定义登录态，用于后续业务逻辑中前后端交互时识别用户身份。

**实现步骤：**

**1). 获取授权码**

点击确定按钮，获取授权码，每个授权码只能使用一次，每次测试，需重新获取。 



**2). 明确请求接口**

请求方式、请求路径、请求参数 



**3). 发送请求**

获取session_key和openid 

若出现code been used错误提示，说明授权码已被使用过，请重新获取 

 

### 3.3 需求分析和设计

#### 3.3.1 产品原型

用户进入到小程序的时候，微信授权登录之后才能点餐。需要获取当前微信用户的相关信息，比如昵称、头像等，这样才能够进入到小程序进行下单操作。是基于微信登录来实现小程序的登录功能，没有采用传统账户密码登录的方式。若第一次使用小程序来点餐，就是一个新用户，需要把这个新的用户保存到数据库当中完成自动注册。

**业务规则：**

- 基于微信登录实现小程序的登录功能
- 如果是新用户需要自动完成注册



#### 3.3.2 接口设计

通过微信登录的流程，如果要完成微信登录的话，最终就要获得微信用户的openid。在小程序端获取授权码后，向后端服务发送请求，并携带授权码，这样后端服务在收到授权码后，就可以去请求微信接口服务。最终，后端向小程序返回openid和token等数据。

基于上述的登录流程，就可以设计出该接口的**请求参数**和**返回数据**。

**说明：**请求路径/user/user/login,第一个user代表用户端，第二个user代表用户模块。



#### 3.3.3 表设计

当用户第一次使用小程序时，会完成自动注册，把用户信息存储到**user**表中。

| **字段名**  | **数据类型** | **说明**           | **备注** |
| ----------- | ------------ | ------------------ | -------- |
| id          | bigint       | 主键               | 自增     |
| openid      | varchar(45)  | 微信用户的唯一标识 |          |
| name        | varchar(32)  | 用户姓名           |          |
| phone       | varchar(11)  | 手机号             |          |
| sex         | varchar(2)   | 性别               |          |
| id_number   | varchar(18)  | 身份证号           |          |
| avatar      | varchar(500) | 微信用户头像路径   |          |
| create_time | datetime     | 注册时间           |          |

**说明：**手机号字段比较特殊，个人身份注册的小程序没有权限获取到微信用户的手机号。如果是以企业的资质
注册的小程序就能够拿到微信用户的手机号。



### 3.4 代码开发

#### 3.4.1 定义相关配置

**配置微信登录所需配置项：**

application-dev.yml

```yaml
sky:
  wechat:
    appid: wxffb3637a228223b8
    secret: 84311df9199ecacdf4f12d27b6b9522d
```

application.yml

```yaml
sky:
  wechat:
    appid: ${sky.wechat.appid}
    secret: ${sky.wechat.secret}
```

**配置为微信用户生成jwt令牌时使用的配置项：**

application.yml

```yaml
sky:
  jwt:
    # 设置jwt签名加密时使用的秘钥
    admin-secret-key: itcast
    # 设置jwt过期时间
    admin-ttl: 7200000
    # 设置前端传递过来的令牌名称
    admin-token-name: token
    user-secret-key: itheima
    user-ttl: 7200000
    user-token-name: authentication
```



#### 3.4.2 DTO设计

**根据传入参数设计DTO类：** 

在sky-pojo模块，UserLoginDTO.java已定义

```java
package com.sky.dto;

import lombok.Data;

import java.io.Serializable;

/**
 * C端用户登录
 */
@Data
public class UserLoginDTO implements Serializable {

    private String code;

}
```



#### 3.4.3 VO设计

**根据返回数据设计VO类：** 

在sky-pojo模块，UserLoginVO.java已定义

```java
package com.sky.vo;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class UserLoginVO implements Serializable {

    private Long id;
    private String openid;
    private String token;

}
```



#### 3.4.4 Controller层

**根据接口定义创建UserController的login方法：**

```java
package com.sky.controller.user;

import com.sky.constant.JwtClaimsConstant;
import com.sky.dto.UserLoginDTO;
import com.sky.entity.User;
import com.sky.properties.JwtProperties;
import com.sky.result.Result;
import com.sky.service.UserService;
import com.sky.utils.JwtUtil;
import com.sky.vo.UserLoginVO;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.HashMap;
import java.util.Map;

@RestController
@RequestMapping("/user/user")
@Api(tags = "C端用户相关接口")
@Slf4j
public class UserController {

    @Autowired
    private UserService userService;
    @Autowired
    private JwtProperties jwtProperties;

    /**
     * 微信登录
     * @param userLoginDTO
     * @return
     */
    @PostMapping("/login")
    @ApiOperation("微信登录")
    public Result<UserLoginVO> login(@RequestBody UserLoginDTO userLoginDTO){
        log.info("微信用户登录：{}",userLoginDTO.getCode());

        //微信登录
        User user = userService.wxLogin(userLoginDTO);//后绪步骤实现

        //为微信用户生成jwt令牌
        Map<String, Object> claims = new HashMap<>();
        claims.put(JwtClaimsConstant.USER_ID,user.getId());
        String token = JwtUtil.createJWT(jwtProperties.getUserSecretKey(), jwtProperties.getUserTtl(), claims);

        UserLoginVO userLoginVO = UserLoginVO.builder()
                .id(user.getId())
                .openid(user.getOpenid())
                .token(token)
                .build();
        return Result.success(userLoginVO);
    }
}
```

其中，JwtClaimsConstant.USER_ID常量已定义。



#### 3.4.5 Service层接口

**创建UserService接口：**

```java
package com.sky.service;

import com.sky.dto.UserLoginDTO;
import com.sky.entity.User;

public interface UserService {

    /**
     * 微信登录
     * @param userLoginDTO
     * @return
     */
    User wxLogin(UserLoginDTO userLoginDTO);
}
```



#### 3.4.6 Service层实现类

**创建UserServiceImpl实现类：**实现获取微信用户的openid和微信登录功能

```java
package com.sky.service.impl;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONObject;
import com.sky.constant.MessageConstant;
import com.sky.dto.UserLoginDTO;
import com.sky.entity.User;
import com.sky.exception.LoginFailedException;
import com.sky.mapper.UserMapper;
import com.sky.properties.WeChatProperties;
import com.sky.service.UserService;
import com.sky.utils.HttpClientUtil;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.HashMap;
import java.util.Map;

@Service
@Slf4j
public class UserServiceImpl implements UserService {

    //微信服务接口地址
    public static final String WX_LOGIN = "https://api.weixin.qq.com/sns/jscode2session";

    @Autowired
    private WeChatProperties weChatProperties;
    @Autowired
    private UserMapper userMapper;

    /**
     * 微信登录
     * @param userLoginDTO
     * @return
     */
    public User wxLogin(UserLoginDTO userLoginDTO) {
        String openid = getOpenid(userLoginDTO.getCode());

        //判断openid是否为空，如果为空表示登录失败，抛出业务异常
        if(openid == null){
            throw new LoginFailedException(MessageConstant.LOGIN_FAILED);
        }

        //判断当前用户是否为新用户
        User user = userMapper.getByOpenid(openid);

        //如果是新用户，自动完成注册
        if(user == null){
            user = User.builder()
                    .openid(openid)
                    .createTime(LocalDateTime.now())
                    .build();
            userMapper.insert(user);//后绪步骤实现
        }

        //返回这个用户对象
        return user;
    }

    /**
     * 调用微信接口服务，获取微信用户的openid
     * @param code
     * @return
     */
    private String getOpenid(String code){
        //调用微信接口服务，获得当前微信用户的openid
        Map<String, String> map = new HashMap<>();
        map.put("appid",weChatProperties.getAppid());
        map.put("secret",weChatProperties.getSecret());
        map.put("js_code",code);
        map.put("grant_type","authorization_code");
        String json = HttpClientUtil.doGet(WX_LOGIN, map);

        JSONObject jsonObject = JSON.parseObject(json);
        String openid = jsonObject.getString("openid");
        return openid;
    }
}
```



#### 3.4.7 Mapper层

**创建UserMapper接口：**

```java
package com.sky.mapper;

import com.sky.entity.User;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

@Mapper
public interface UserMapper {

    /**
     * 根据openid查询用户
     * @param openid
     * @return
     */
    @Select("select * from user where openid = #{openid}")
    User getByOpenid(String openid);

    /**
     * 插入数据
     * @param user
     */
    void insert(User user);
}
```

**创建UserMapper.xml映射文件：**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.UserMapper">

    <insert id="insert" useGeneratedKeys="true" keyProperty="id">
        insert into user (openid, name, phone, sex, id_number, avatar, create_time)
        values (#{openid}, #{name}, #{phone}, #{sex}, #{idNumber}, #{avatar}, #{createTime})
    </insert>

</mapper>
```



#### 3.4.8 编写拦截器

**编写拦截器JwtTokenUserInterceptor：**统一拦截用户端发送的请求并进行jwt校验

```java
package com.sky.interceptor;

import com.sky.constant.JwtClaimsConstant;
import com.sky.context.BaseContext;
import com.sky.properties.JwtProperties;
import com.sky.utils.JwtUtil;
import io.jsonwebtoken.Claims;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * jwt令牌校验的拦截器
 */
@Component
@Slf4j
public class JwtTokenUserInterceptor implements HandlerInterceptor {

    @Autowired
    private JwtProperties jwtProperties;

    /**
     * 校验jwt
     *
     * @param request
     * @param response
     * @param handler
     * @return
     * @throws Exception
     */
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //判断当前拦截到的是Controller的方法还是其他资源
        if (!(handler instanceof HandlerMethod)) {
            //当前拦截到的不是动态方法，直接放行
            return true;
        }

        //1、从请求头中获取令牌
        String token = request.getHeader(jwtProperties.getUserTokenName());

        //2、校验令牌
        try {
            log.info("jwt校验:{}", token);
            Claims claims = JwtUtil.parseJWT(jwtProperties.getUserSecretKey(), token);
            Long userId = Long.valueOf(claims.get(JwtClaimsConstant.USER_ID).toString());
            log.info("当前用户的id：", userId);
            BaseContext.setCurrentId(userId);
            //3、通过，放行
            return true;
        } catch (Exception ex) {
            //4、不通过，响应401状态码
            response.setStatus(401);
            return false;
        }
    }
}
```

**在WebMvcConfiguration配置类中注册拦截器：**

```java
	@Autowired
    private JwtTokenUserInterceptor jwtTokenUserInterceptor;
	/**
     * 注册自定义拦截器
     * @param registry
     */
    protected void addInterceptors(InterceptorRegistry registry) {
        log.info("开始注册自定义拦截器...");
        //.........

        registry.addInterceptor(jwtTokenUserInterceptor)
                .addPathPatterns("/user/**")
                .excludePathPatterns("/user/user/login")
                .excludePathPatterns("/user/shop/status");
    }  
```



## 4. 导入商品浏览功能代码

### 4.1 需求分析和设计

#### 4.1.1 产品原型

用户登录成功后跳转到系统首页，在首页需要根据分类来展示菜品和套餐。如果菜品设置了口味信息，需要展示**选择规格**按钮，否则显示**+**按钮。                                                                



#### 4.1.2 接口设计

根据上述原型图先**粗粒度**设计接口，共包含4个接口。

**接口设计：**

- 查询分类
- 根据分类id查询菜品
- 根据分类id查询套餐
- 根据套餐id查询包含的菜品

 

### 4.2 代码导入

可按照mapper-->service-->controller依次导入，这样代码不会显示相应的报错。

进入到sky-server模块中

#### 4.2.1 Mapper层

**在SetmealMapper.java中添加list和getDishItemBySetmealId两个方法**

```java
	/**
     * 动态条件查询套餐
     * @param setmeal
     * @return
     */
    List<Setmeal> list(Setmeal setmeal);

    /**
     * 根据套餐id查询菜品选项
     * @param setmealId
     * @return
     */
    @Select("select sd.name, sd.copies, d.image, d.description " +
            "from setmeal_dish sd left join dish d on sd.dish_id = d.id " +
            "where sd.setmeal_id = #{setmealId}")
    List<DishItemVO> getDishItemBySetmealId(Long setmealId);
```

**创建SetmealMapper.xml文件**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.SetmealMapper">

    <select id="list" parameterType="Setmeal" resultType="Setmeal">
        select * from setmeal
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
    </select>
</mapper>
```



#### 4.2.2 Service层

**创建SetmealService.java**

```java
package com.sky.service;

import com.sky.dto.SetmealDTO;
import com.sky.dto.SetmealPageQueryDTO;
import com.sky.entity.Setmeal;
import com.sky.result.PageResult;
import com.sky.vo.DishItemVO;
import com.sky.vo.SetmealVO;
import java.util.List;

public interface SetmealService {

    /**
     * 条件查询
     * @param setmeal
     * @return
     */
    List<Setmeal> list(Setmeal setmeal);

    /**
     * 根据id查询菜品选项
     * @param id
     * @return
     */
    List<DishItemVO> getDishItemById(Long id);

}
```

**创建SetmealServiceImpl.java**

```java
package com.sky.service.impl;

import com.sky.entity.Setmeal;
import com.sky.mapper.DishMapper;
import com.sky.mapper.SetmealDishMapper;
import com.sky.mapper.SetmealMapper;
import com.sky.service.SetmealService;
import com.sky.vo.DishItemVO;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * 套餐业务实现
 */
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
     * 条件查询
     * @param setmeal
     * @return
     */
    public List<Setmeal> list(Setmeal setmeal) {
        List<Setmeal> list = setmealMapper.list(setmeal);
        return list;
    }

    /**
     * 根据id查询菜品选项
     * @param id
     * @return
     */
    public List<DishItemVO> getDishItemById(Long id) {
        return setmealMapper.getDishItemBySetmealId(id);
    }
}
```

**在DishService.java中添加listWithFlavor方法定义**

```java
	/**
     * 条件查询菜品和口味
     * @param dish
     * @return
     */
    List<DishVO> listWithFlavor(Dish dish);
```

**在DishServiceImpl.java中实现listWithFlavor方法**

```java
	/**
     * 条件查询菜品和口味
     * @param dish
     * @return
     */
    public List<DishVO> listWithFlavor(Dish dish) {
        List<Dish> dishList = dishMapper.list(dish);

        List<DishVO> dishVOList = new ArrayList<>();

        for (Dish d : dishList) {
            DishVO dishVO = new DishVO();
            BeanUtils.copyProperties(d,dishVO);

            //根据菜品id查询对应的口味
            List<DishFlavor> flavors = dishFlavorMapper.getByDishId(d.getId());

            dishVO.setFlavors(flavors);
            dishVOList.add(dishVO);
        }

        return dishVOList;
    }
```



#### 4.2.3 Controller层

**创建DishController.java**

```java
package com.sky.controller.user;

import com.sky.constant.StatusConstant;
import com.sky.entity.Dish;
import com.sky.result.Result;
import com.sky.service.DishService;
import com.sky.vo.DishVO;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.List;

@RestController("userDishController")
@RequestMapping("/user/dish")
@Slf4j
@Api(tags = "C端-菜品浏览接口")
public class DishController {
    @Autowired
    private DishService dishService;

    /**
     * 根据分类id查询菜品
     *
     * @param categoryId
     * @return
     */
    @GetMapping("/list")
    @ApiOperation("根据分类id查询菜品")
    public Result<List<DishVO>> list(Long categoryId) {
        Dish dish = new Dish();
        dish.setCategoryId(categoryId);
        dish.setStatus(StatusConstant.ENABLE);//查询起售中的菜品

        List<DishVO> list = dishService.listWithFlavor(dish);

        return Result.success(list);
    }

}
```

**创建CategoryController.java**

```java
package com.sky.controller.user;

import com.sky.entity.Category;
import com.sky.result.Result;
import com.sky.service.CategoryService;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.List;

@RestController("userCategoryController")
@RequestMapping("/user/category")
@Api(tags = "C端-分类接口")
public class CategoryController {

    @Autowired
    private CategoryService categoryService;

    /**
     * 查询分类
     * @param type
     * @return
     */
    @GetMapping("/list")
    @ApiOperation("查询分类")
    public Result<List<Category>> list(Integer type) {
        List<Category> list = categoryService.list(type);
        return Result.success(list);
    }
}
```

**创建SetmealController.java**

```java
package com.sky.controller.user;

import com.sky.constant.StatusConstant;
import com.sky.entity.Setmeal;
import com.sky.result.Result;
import com.sky.service.SetmealService;
import com.sky.vo.DishItemVO;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.util.List;

@RestController("userSetmealController")
@RequestMapping("/user/setmeal")
@Api(tags = "C端-套餐浏览接口")
public class SetmealController {
    @Autowired
    private SetmealService setmealService;

    /**
     * 条件查询
     *
     * @param categoryId
     * @return
     */
    @GetMapping("/list")
    @ApiOperation("根据分类id查询套餐")
    public Result<List<Setmeal>> list(Long categoryId) {
        Setmeal setmeal = new Setmeal();
        setmeal.setCategoryId(categoryId);
        setmeal.setStatus(StatusConstant.ENABLE);

        List<Setmeal> list = setmealService.list(setmeal);
        return Result.success(list);
    }

    /**
     * 根据套餐id查询包含的菜品列表
     *
     * @param id
     * @return
     */
    @GetMapping("/dish/{id}")
    @ApiOperation("根据套餐id查询包含的菜品列表")
    public Result<List<DishItemVO>> dishList(@PathVariable("id") Long id) {
        List<DishItemVO> list = setmealService.getDishItemById(id);
        return Result.success(list);
    }
}
```



# 八. 缓存商品及购物车

## 1. 缓存菜品

### 1.1 实现思路

缓存逻辑分析:

​	1.每个分类下的菜品保存一份缓存数据

​	2.数据库中的菜品数据有变更时清理缓存数据

### 1.2 代码开发

#### 1.2.1 管理端

```java
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

        // 清理缓存数据
        String key = "dish_" + dishDTO.getCategoryId();
        cleanCache(key);
        return Result.success();
    }


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

        // 将所有的菜品缓存数据清理掉, 所有以dish_开头的key
        cleanCache("dis_*");
        return Result.success();
    }


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

        // 将所有的菜品缓存数据清理掉, 所有以dish_开头的key
        cleanCache("dis_*");
        return Result.success();
    }

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

        // 将所有的菜品缓存数据清理掉, 所有以dish_开头的key
        cleanCache("dis_*");
        return Result.success();
    }


    private void cleanCache(String pattern) {
        Set keys = redisTemplate.keys(pattern);
        redisTemplate.delete(keys);
    }
```



#### 1.2.2 用户端

```java
public Result<List<DishVO>> list(Long categoryId) {
    // 构建redis中的key, 规则: dish_分类id
    String key = "dish_" + categoryId;

    // 查询redis中是否存在菜品数据
    List<DishVO> list = (List<DishVO>) redisTemplate.opsForValue().get(key);
    if(list != null && list.size() > 0) {
        // 如果存在, 直接返回, 无须查询数据库
        return Result.success(list);
    }

    Dish dish = new Dish();
    dish.setCategoryId(categoryId);
    dish.setStatus(StatusConstant.ENABLE);//查询起售中的菜品

    // 如果不存在, 查询数据库, 并将查询到的数据放入redis中
    list = dishService.listWithFlavor(dish);
    redisTemplate.opsForValue().set(key, list);

    return Result.success(list);
}
```



## 2. 缓存套餐

### 2.1 Spring Cache

#### 2.1.1 介绍

Spring Cache 是一个框架，实现了基于注解的缓存功能，只需要简单地加一个注解，就能实现缓存功能。

Spring Cache 提供了一层抽象，底层可以切换不同的缓存实现，例如：

- EHCache
- Caffeine
- Redis(常用)

**起步依赖：**

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-cache</artifactId>  		            		       	 <version>2.7.3</version> 
</dependency>
```



#### 2.1.2 常用注解

在SpringCache中提供了很多缓存操作的注解，常见的是以下的几个：

| **注解**       | **说明**                                                     |
| -------------- | ------------------------------------------------------------ |
| @EnableCaching | 开启缓存注解功能，通常加在启动类上                           |
| @Cacheable     | 在方法执行前先查询缓存中是否有数据，如果有数据，则直接返回缓存数据；如果没有缓存数据，调用方法并将方法返回值放到缓存中 |
| @CachePut      | 将方法的返回值放到缓存中                                     |
| @CacheEvict    | 将一条或多条数据从缓存中删除                                 |

在spring boot项目中，使用缓存技术只需在项目中导入相关缓存技术的依赖包，并在启动类上使用@EnableCaching开启缓存支持即可。

例如，使用Redis作为缓存技术，只需要导入Spring data Redis的maven坐标即可。



#### 2.1.3 入门案例

**1). 环境准备**

**导入基础工程:**底层已使用Redis缓存实现 

 

**数据库准备:**

创建名为spring_cache_demo数据库，将springcachedemo.sql脚本直接导入数据库中。 



**引导类上加@EnableCaching:**

```java
package com.itheima;

import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;

@Slf4j
@SpringBootApplication
@EnableCaching//开启缓存注解功能
public class CacheDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(CacheDemoApplication.class,args);
        log.info("项目启动成功...");
    }
}
```



**2). @CachePut注解**

**@CachePut 说明：** 

​	作用: 将方法返回值，放入缓存

​	value: 缓存的名称, 每个缓存名称下面可以有很多key

​	key: 缓存的key  ----------> 支持Spring的表达式语言SPEL语法



**在save方法上加注解@CachePut**

当前UserController的save方法是用来保存用户信息的，我们希望在该用户信息保存到数据库的同时，也往缓存中缓存一份数据，我们可以在save方法上加上注解 @CachePut，用法如下：

```java
	/**
	* CachePut：将方法返回值放入缓存
	* value：缓存的名称，每个缓存名称下面可以有多个key
	* key：缓存的key
	*/
	@PostMapping
    @CachePut(value = "userCache", key = "#user.id")//key的生成：userCache::1
    public User save(@RequestBody User user){
        userMapper.insert(user);
        return user;
    }
```

**说明：**key的写法如下

#user.id : #user指的是方法形参的名称, id指的是user的id属性 , 也就是使用user的id属性作为key ;

#result.id : #result代表方法返回值，该表达式 代表以返回对象的id属性作为key ；

#p0.id：#p0指的是方法中的第一个参数，id指的是第一个参数的id属性,也就是使用第一个参数的id属性作为key ;

#a0.id：#a0指的是方法中的第一个参数，id指的是第一个参数的id属性,也就是使用第一个参数的id属性作为key ;

#root.args[0].id:#root.args[0]指的是方法中的第一个参数，id指的是第一个参数的id属性,也就是使用第一个参数

的id属性作为key ;

**启动服务,通过swagger接口文档测试，访问UserController的save()方法**

因为id是自增，所以不需要设置id属性

**查看user表中的数据**

**查看Redis中的数据**



**3). @Cacheable注解**

**@Cacheable 说明:**

​	作用: 在方法执行前，spring先查看缓存中是否有数据，如果有数据，则直接返回缓存数据；若没有数据，调用方法并将方法返回值放到缓存中

​	value: 缓存的名称，每个缓存名称下面可以有多个key

​	key: 缓存的key  ----------> 支持Spring的表达式语言SPEL语法



 **在getById上加注解@Cacheable**

```java
	/**
	* Cacheable：在方法执行前spring先查看缓存中是否有数据，如果有数据，则直接返回缓存数据；若没有数据，	  *调用方法并将方法返回值放到缓存中
	* value：缓存的名称，每个缓存名称下面可以有多个key
	* key：缓存的key
	*/
	@GetMapping
    @Cacheable(cacheNames = "userCache",key="#id")
    public User getById(Long id){
        User user = userMapper.getById(id);
        return user;
    }
```

**重启服务,通过swagger接口文档测试，访问UserController的getById()方法**

第一次访问，会请求我们controller的方法，查询数据库。后面再查询相同的id，就直接从Redis中查询数据，不用再查询数据库了，就说明缓存生效了。

提前在redis中手动删除掉id=1的用户数据 

**查看控制台sql语句：**说明从数据库查询的用户数据 

**查看Redis中的缓存数据：**说明已成功缓存 

再次查询相同id的数据时，直接从redis中直接获取，不再查询数据库。



**4). @CacheEvict注解**

**@CacheEvict 说明：** 

​	作用: 清理指定缓存

​	value: 缓存的名称，每个缓存名称下面可以有多个key

​	key: 缓存的key  ----------> 支持Spring的表达式语言SPEL语法



**在 delete 方法上加注解@CacheEvict**

```java
	@DeleteMapping
    @CacheEvict(cacheNames = "userCache",key = "#id")//删除某个key对应的缓存数据
    public void deleteById(Long id){
        userMapper.deleteById(id);
    }

	@DeleteMapping("/delAll")
    @CacheEvict(cacheNames = "userCache",allEntries = true)//删除userCache下所有的缓存数据
    public void deleteAll(){
        userMapper.deleteAll();
    }
```

**重启服务,通过swagger接口文档测试，访问UserController的deleteAll()方法**

**查看user表：**数据清空 

**查询Redis缓存数据**

 



### 2.2 实现思路

**实现步骤：**

1). 导入Spring Cache和Redis相关maven坐标

2). 在启动类上加入@EnableCaching注解，开启缓存注解功能

3). 在用户端接口SetmealController的 list 方法上加入@Cacheable注解

4). 在管理端接口SetmealController的 save、delete、update、startOrStop等方法上加入CacheEvict注解



### 2.3 代码开发

按照上述实现步骤：

**1). 导入Spring Cache和Redis相关maven坐标(已实现)**

```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>

<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```



**2). 在启动类上加入@EnableCaching注解，开启缓存注解功能**

```java
package com.sky;

import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@SpringBootApplication
@EnableTransactionManagement //开启注解方式的事务管理
@Slf4j
@EnableCaching
public class SkyApplication {
    public static void main(String[] args) {
        SpringApplication.run(SkyApplication.class, args);
        log.info("server started");
    }
}
```



**3). 在用户端接口SetmealController的 list 方法上加入@Cacheable注解**

```java
	/**
     * 条件查询
     *
     * @param categoryId
     * @return
     */
    @GetMapping("/list")
    @ApiOperation("根据分类id查询套餐")
    @Cacheable(cacheNames = "setmealCache",key = "#categoryId") //key: setmealCache::100
    public Result<List<Setmeal>> list(Long categoryId) {
        Setmeal setmeal = new Setmeal();
        setmeal.setCategoryId(categoryId);
        setmeal.setStatus(StatusConstant.ENABLE);

        List<Setmeal> list = setmealService.list(setmeal);
        return Result.success(list);
    }
```



**4). 在管理端接口SetmealController的 save、delete、update、startOrStop等方法上加入CacheEvict注解**

```java
	/**
     * 新增套餐
     *
     * @param setmealDTO
     * @return
     */
    @PostMapping
    @ApiOperation("新增套餐")
    @CacheEvict(cacheNames = "setmealCache",key = "#setmealDTO.categoryId")//key: setmealCache::100
    public Result save(@RequestBody SetmealDTO setmealDTO) {
        setmealService.saveWithDish(setmealDTO);
        return Result.success();
    }
	/**
     * 批量删除套餐
     *
     * @param ids
     * @return
     */
    @DeleteMapping
    @ApiOperation("批量删除套餐")
    @CacheEvict(cacheNames = "setmealCache",allEntries = true)
    public Result delete(@RequestParam List<Long> ids) {
        setmealService.deleteBatch(ids);
        return Result.success();
    }
	/**
     * 修改套餐
     *
     * @param setmealDTO
     * @return
     */
    @PutMapping
    @ApiOperation("修改套餐")
    @CacheEvict(cacheNames = "setmealCache",allEntries = true)
    public Result update(@RequestBody SetmealDTO setmealDTO) {
        setmealService.update(setmealDTO);
        return Result.success();
    }

    /**
     * 套餐起售停售
     *
     * @param status
     * @param id
     * @return
     */
    @PostMapping("/status/{status}")
    @ApiOperation("套餐起售停售")
    @CacheEvict(cacheNames = "setmealCache",allEntries = true)
    public Result startOrStop(@PathVariable Integer status, Long id) {
        setmealService.startOrStop(status, id);
        return Result.success();
    }
```



## 3. 添加购物车

### 3.1 需求分析和设计

#### 3.1.1 产品原型

用户可以将菜品或者套餐添加到购物车。对于菜品来说，如果设置了口味信息，则需要选择规格后才能加入购物车;对于套餐来说，可以直接点击+将当前套餐加入购物车。在购物车中可以修改菜品和套餐的数量，也可以清空购物车。

 

#### 3.1.2 接口设计

**基本信息:**

​	path:  /user/shoppingCart/add

​	Method:  POST

**请求参数:**

​	Body 参数  application/json

​	dishFlavor  string  口味 可选

​	dishId  integer <int64>  菜品id  可选

​	setmealId  integer <int64>  套餐id  可选

**说明：**添加购物车时，有可能添加菜品，也有可能添加套餐。故传入参数要么是菜品id，要么是套餐id。



#### 3.1.3 表设计

用户的购物车数据，也是需要保存在数据库中的，购物车对应的数据表为shopping_cart表，具体表结构如下：

| **字段名**  | **数据类型**  | **说明**     | **备注** |
| ----------- | ------------- | ------------ | -------- |
| id          | bigint        | 主键         | 自增     |
| name        | varchar(32)   | 商品名称     | 冗余字段 |
| image       | varchar(255)  | 商品图片路径 | 冗余字段 |
| user_id     | bigint        | 用户id       | 逻辑外键 |
| dish_id     | bigint        | 菜品id       | 逻辑外键 |
| setmeal_id  | bigint        | 套餐id       | 逻辑外键 |
| dish_flavor | varchar(50)   | 菜品口味     |          |
| number      | int           | 商品数量     |          |
| amount      | decimal(10,2) | 商品单价     | 冗余字段 |
| create_time | datetime      | 创建时间     |          |

**说明：** 

- 购物车数据是关联用户的，在表结构中，我们需要记录，每一个用户的购物车数据是哪些
- 菜品列表展示出来的既有套餐，又有菜品，如果用户选择的是套餐，就保存套餐ID(setmeal_id)，如果用户选择的是菜品，就保存菜品ID(dish_id)
- 对同一个菜品/套餐，如果选择多份不需要添加多条记录，增加数量number即可



### 3.2 代码开发

#### 3.2.1 ShoppingCartDTO

```java
@Data
public class ShoppingCartDTO implements Serializable {

    private Long dishId;
    private Long setmealId;
    private String dishFlavor;

}
```



#### 3.2.2 ShoppingCartController

```java
@RestController
@RequestMapping("/user/shoppingCart")
@Slf4j
@Api(tags = "C端购物车相关接口")
public class ShoppingCartController {
    @Autowired
    private ShoppingCartService shoppingCartService;

    /**
     * 添加购物车
     * @param shoppingCartDTO
     * @return
     */
    @PostMapping("/add")
    @ApiOperation("添加购物车")
    public Result add(@RequestBody ShoppingCartDTO shoppingCartDTO) {
        log.info("添加购物车, 商品信息为: {}", shoppingCartDTO);
        shoppingCartService.addShoppingCart(shoppingCartDTO);
        return Result.success();
    }
}
```



#### 3.2.3 ShoppingCartServiceImpl

```java
@Service
@Slf4j
public class ShoppingCartServiceImpl implements ShoppingCartService {
    @Autowired
    private ShoppingCartMapper shoppingCartMapper;
    @Autowired
    private DishMapper dishMapper;
    @Autowired
    private SetmealMapper setmealMapper;
    /**
     * 添加购物车
     * @param shoppingCartDTO
     */
    @Override
    public void addShoppingCart(ShoppingCartDTO shoppingCartDTO) {
        //判断当前加入购物车的商品是否存在
        ShoppingCart shoppingCart = new ShoppingCart();
        BeanUtils.copyProperties(shoppingCartDTO,shoppingCart); //属性拷贝
        Long userId = BaseContext.getCurrentId();  //拦截器获取到的用户id
        shoppingCart.setUserId(userId);
        List<ShoppingCart> list = shoppingCartMapper.list(shoppingCart);

        //如果已经存在了，只需要将数量加一
        if(list!=null&&list.size()>0){
            //这里list要么没有数据，要么只有一条数据
            ShoppingCart cart = list.get(0);
            cart.setNumber(cart.getNumber()+1); //update shopping_cart set number=?where id=?
            shoppingCartMapper.updateNumberById(cart);
        }else {
            //如果不存在，需要插入一条购物车数据
            /**
             * 判断这次添加到购物车的是菜品还是套餐
             */
            Long dishId = shoppingCartDTO.getDishId();
            if(dishId!=null){
                //本次添加是菜品
                Dish dish = dishMapper.getById(dishId);
                shoppingCart.setName(dish.getName());
                shoppingCart.setImage(dish.getImage());
                shoppingCart.setAmount(dish.getPrice());
            }else{
                //本次添加的是套餐
                Long setmealId = shoppingCartDTO.getSetmealId();
                Setmeal setmeal = setmealMapper.getById(setmealId);
                shoppingCart.setName(setmeal.getName());
                shoppingCart.setImage(setmeal.getImage());
                shoppingCart.setAmount(setmeal.getPrice());
            }
            shoppingCart.setNumber(1);
            shoppingCart.setCreateTime(LocalDateTime.now());
            //统一插入数据
            shoppingCartMapper.insert(shoppingCart);
        }
    }
}
```



#### 3.2.4 ShoppingCartMapper

```java
@Mapper
public interface ShoppingCartMapper {

    /**
     * 动态条件查询
     * @param shoppingCart
     * @return
     */
    List<ShoppingCart> list(ShoppingCart shoppingCart);


    /**
     * 根据id修改商品数量
     * @param shoppingCart
     */
    @Update("update shopping_cart set number = #{number} where id = #{id}")
    void updateNumberById(ShoppingCart shoppingCart);

    /**
     * 插入购物车数据
     * @param shoppingCart
     */
    @Insert("insert into shopping_cart(name , user_id, dish_id, setmeal_id, dish_flavor, number, amount,image, create_time)" +
            "values(#{name},#{userId},#{dishId},#{setmealId},#{dishFlavor},#{number},#{amount},#{image},#{createTime})")
    void insert(ShoppingCart shoppingCart);
}
```



#### 3.2.5 ShoppingCartMapper.xml

```xml
<select id="list" resultType="com.sky.entity.ShoppingCart">
    select * from shopping_cart
    <where>
        <if test="userId != null">
            and user_id = #{userId}
        </if>
        <if test="setmealId != null">
            and setmeal_id = #{setmealId}
        </if>
        <if test="dishId != null">
            and dish_id = #{dishId}
        </if>
        <if test="dishFlavor != null">
            and dish_flavor = #{dishFlavor}
        </if>
    </where>
</select>
```



## 4. 查看购物车

### 4.1 需求分析和设计

**基本信息:**

​	path:  /user/shoppingCart/list

​	Method:  GET



### 4.2 代码开发

#### 4.2.1 ShoppingCartController

```java
/**
 * 查看购物车
 * @return
 */
@GetMapping("/list")
@ApiOperation("查看购物车")
public Result<List<ShoppingCart>> list() {
    List<ShoppingCart> list = shoppingCartService.showShoppingCart();
    return Result.success(list);
}
```



#### 4.2.2 ShoppingCartServiceImpl

```java
/**
 * 查看购物车
 * @return
 */
@Override
public List<ShoppingCart> showShoppingCart() {
    // 获取到当前微信用户的id
    Long userId = BaseContext.getCurrentId();
    ShoppingCart shoppingCart = ShoppingCart.builder()
            .userId(userId)
            .build();
    List<ShoppingCart> list = shoppingCartMapper.list(shoppingCart);
    return list;
}
```



##  5. 清空购物车

### 5.1 需求分析和设计

**基本信息:**

​	path:  /user/shoppingCart/clean

​	Method:  DELETE



### 5.2 代码开发

#### 5.2.1 ShoppingCartController

```java
/**
 * 清空购物车
 * @return
 */
@DeleteMapping("/clean")
@ApiOperation("清空购物车")
public Result clean() {
    shoppingCartService.cleanShoppingCart();
    return Result.success();
}
```



#### 5.2.2 ShoppingCartServiceImpl

```java
/**
 * 清空购物车
 */
@Override
public void cleanShoppingCart() {
    // 获取到当前微信用户的id
    Long userId = BaseContext.getCurrentId();
    shoppingCartMapper.deleteByUserId(userId);
}
```



#### 5.2.3 ShoppingCartMapper

```java
/**
 * 根据userId清空购物车
 * @param userId
 */
@Delete("delete from shopping_cart where user_id = #{userId}")
void deleteByUserId(Long userId);
```



## 6. 删除购物车内一个商品

### 6.1 需求分析和设计

**基本信息:**

​	path:  /user/shoppingCart/sub

​	Method:  POST



### 6.2 代码开发

#### 6.2.1 ShoppingCartController

```java
/**
 * 删除购物车中一个商品
 * @param shoppingCartDTO
 * @return
 */
@PostMapping("/sub")
@ApiOperation("删除购物车中一个商品")
public Result sub(@RequestBody ShoppingCartDTO shoppingCartDTO){
    log.info("删除购物车中一个商品，商品：{}", shoppingCartDTO);
    shoppingCartService.subShoppingCart(shoppingCartDTO);
    return Result.success();
}
```



#### 6.2.2 ShoppingCartServiceImpl

```java
/**
 * 删除购物车中一个商品
 * @param shoppingCartDTO
 */
public void subShoppingCart(ShoppingCartDTO shoppingCartDTO) {
    ShoppingCart shoppingCart = new ShoppingCart();
    BeanUtils.copyProperties(shoppingCartDTO,shoppingCart);
    //设置查询条件，查询当前登录用户的购物车数据
    shoppingCart.setUserId(BaseContext.getCurrentId());

    List<ShoppingCart> list = shoppingCartMapper.list(shoppingCart);

    if(list != null && list.size() > 0){
        shoppingCart = list.get(0);

        Integer number = shoppingCart.getNumber();
        if(number == 1){
            //当前商品在购物车中的份数为1，直接删除当前记录
            shoppingCartMapper.deleteById(shoppingCart.getId());
        }else {
            //当前商品在购物车中的份数不为1，修改份数即可
            shoppingCart.setNumber(shoppingCart.getNumber() - 1);
            shoppingCartMapper.updateNumberById(shoppingCart);
        }
    }
}
```



#### 6.2.3 ShoppingCartMapper

```java
/**
 * 根据id删除购物车数据
 * @param id
 */
@Delete("delete from shopping_cart where id = #{id}")
void deleteById(Long id);
```



# 九. 地址簿, 用户下单及订单支付

## 1. 地址簿模块

### 1.1 需求分析和设计

#### 1.1.1 产品原型

地址簿，指的是消费者用户的地址信息，用户登录成功后可以维护自己的地址信息。同一个用户可以有多个地址信息，但是只能有一个**默认地址**。

​             

对于地址簿管理，我们需要实现以下几个功能： 

- 查询地址列表
- 新增地址
- 修改地址
- 删除地址
- 设置默认地址
- 查询默认地址



#### 1.1.2 表设计

用户的地址信息会存储在address_book表，即地址簿表中。具体表结构如下：

| **字段名**    | **数据类型** | **说明**     | **备注**       |
| ------------- | ------------ | ------------ | -------------- |
| id            | bigint       | 主键         | 自增           |
| user_id       | bigint       | 用户id       | 逻辑外键       |
| consignee     | varchar(50)  | 收货人       |                |
| sex           | varchar(2)   | 性别         |                |
| phone         | varchar(11)  | 手机号       |                |
| province_code | varchar(12)  | 省份编码     |                |
| province_name | varchar(32)  | 省份名称     |                |
| city_code     | varchar(12)  | 城市编码     |                |
| city_name     | varchar(32)  | 城市名称     |                |
| district_code | varchar(12)  | 区县编码     |                |
| district_name | varchar(32)  | 区县名称     |                |
| detail        | varchar(200) | 详细地址信息 | 具体到门牌号   |
| label         | varchar(100) | 标签         | 公司、家、学校 |
| is_default    | tinyint(1)   | 是否默认地址 | 1是 0否        |

这里面有一个字段is_default，实际上我们在设置默认地址时，只需要更新这个字段就可以了。



### 1.2 代码开发

#### 1.2.1 AddressBookController

```java
@RestController
@RequestMapping("/user/addressBook")
@Api(tags = "C端地址簿接口")
public class AddressBookController {

    @Autowired
    private AddressBookService addressBookService;

    /**
     * 查询当前登录用户的所有地址信息
     *
     * @return
     */
    @GetMapping("/list")
    @ApiOperation("查询当前登录用户的所有地址信息")
    public Result<List<AddressBook>> list() {
        AddressBook addressBook = new AddressBook();
        addressBook.setUserId(BaseContext.getCurrentId());
        List<AddressBook> list = addressBookService.list(addressBook);
        return Result.success(list);
    }

    /**
     * 新增地址
     *
     * @param addressBook
     * @return
     */
    @PostMapping
    @ApiOperation("新增地址")
    public Result save(@RequestBody AddressBook addressBook) {
        addressBookService.save(addressBook);
        return Result.success();
    }

    @GetMapping("/{id}")
    @ApiOperation("根据id查询地址")
    public Result<AddressBook> getById(@PathVariable Long id) {
        AddressBook addressBook = addressBookService.getById(id);
        return Result.success(addressBook);
    }

    /**
     * 根据id修改地址
     *
     * @param addressBook
     * @return
     */
    @PutMapping
    @ApiOperation("根据id修改地址")
    public Result update(@RequestBody AddressBook addressBook) {
        addressBookService.update(addressBook);
        return Result.success();
    }

    /**
     * 设置默认地址
     *
     * @param addressBook
     * @return
     */
    @PutMapping("/default")
    @ApiOperation("设置默认地址")
    public Result setDefault(@RequestBody AddressBook addressBook) {
        addressBookService.setDefault(addressBook);
        return Result.success();
    }

    /**
     * 根据id删除地址
     *
     * @param id
     * @return
     */
    @DeleteMapping
    @ApiOperation("根据id删除地址")
    public Result deleteById(Long id) {
        addressBookService.deleteById(id);
        return Result.success();
    }

    /**
     * 查询默认地址
     */
    @GetMapping("default")
    @ApiOperation("查询默认地址")
    public Result<AddressBook> getDefault() {
        //SQL:select * from address_book where user_id = ? and is_default = 1
        AddressBook addressBook = new AddressBook();
        addressBook.setIsDefault(1);
        addressBook.setUserId(BaseContext.getCurrentId());
        List<AddressBook> list = addressBookService.list(addressBook);

        if (list != null && list.size() == 1) {
            return Result.success(list.get(0));
        }

        return Result.error("没有查询到默认地址");
    }

}
```



#### 1.2.2 AddressBookServiceImpl

```java
@Service
@Slf4j
public class AddressBookServiceImpl implements AddressBookService {
    @Autowired
    private AddressBookMapper addressBookMapper;

    /**
     * 条件查询
     *
     * @param addressBook
     * @return
     */
    public List<AddressBook> list(AddressBook addressBook) {
        return addressBookMapper.list(addressBook);
    }

    /**
     * 新增地址
     *
     * @param addressBook
     */
    public void save(AddressBook addressBook) {
        addressBook.setUserId(BaseContext.getCurrentId());
        addressBook.setIsDefault(0);
        addressBookMapper.insert(addressBook);
    }

    /**
     * 根据id查询
     *
     * @param id
     * @return
     */
    public AddressBook getById(Long id) {
        AddressBook addressBook = addressBookMapper.getById(id);
        return addressBook;
    }

    /**
     * 根据id修改地址
     *
     * @param addressBook
     */
    public void update(AddressBook addressBook) {
        addressBookMapper.update(addressBook);
    }

    /**
     * 设置默认地址
     *
     * @param addressBook
     */
    @Transactional
    public void setDefault(AddressBook addressBook) {
        //1、将当前用户的所有地址修改为非默认地址 update address_book set is_default = ? where user_id = ?
        addressBook.setIsDefault(0);
        addressBook.setUserId(BaseContext.getCurrentId());
        addressBookMapper.updateIsDefaultByUserId(addressBook);

        //2、将当前地址改为默认地址 update address_book set is_default = ? where id = ?
        addressBook.setIsDefault(1);
        addressBookMapper.update(addressBook);
    }

    /**
     * 根据id删除地址
     *
     * @param id
     */
    public void deleteById(Long id) {
        addressBookMapper.deleteById(id);
    }

}
```



#### 1.2.3 AddressBookMapper

```java
@Mapper
public interface AddressBookMapper {

    /**
     * 条件查询
     * @param addressBook
     * @return
     */
    List<AddressBook> list(AddressBook addressBook);

    /**
     * 新增
     * @param addressBook
     */
    @Insert("insert into address_book" +
            "        (user_id, consignee, phone, sex, province_code, province_name, city_code, city_name, district_code," +
            "         district_name, detail, label, is_default)" +
            "        values (#{userId}, #{consignee}, #{phone}, #{sex}, #{provinceCode}, #{provinceName}, #{cityCode}, #{cityName}," +
            "                #{districtCode}, #{districtName}, #{detail}, #{label}, #{isDefault})")
    void insert(AddressBook addressBook);

    /**
     * 根据id查询
     * @param id
     * @return
     */
    @Select("select * from address_book where id = #{id}")
    AddressBook getById(Long id);

    /**
     * 根据id修改
     * @param addressBook
     */
    void update(AddressBook addressBook);

    /**
     * 根据 用户id修改 是否默认地址
     * @param addressBook
     */
    @Update("update address_book set is_default = #{isDefault} where user_id = #{userId}")
    void updateIsDefaultByUserId(AddressBook addressBook);

    /**
     * 根据id删除地址
     * @param id
     */
    @Delete("delete from address_book where id = #{id}")
    void deleteById(Long id);

}
```



#### 1.2.4 AddressBookMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.AddressBookMapper">

    <select id="list" parameterType="AddressBook" resultType="AddressBook">
        select * from address_book
        <where>
            <if test="userId != null">
                and user_id = #{userId}
            </if>
            <if test="phone != null">
                and phone = #{phone}
            </if>
            <if test="isDefault != null">
                and is_default = #{isDefault}
            </if>
        </where>
    </select>

    <update id="update" parameterType="addressBook">
        update address_book
        <set>
            <if test="consignee != null">
                consignee = #{consignee},
            </if>
            <if test="sex != null">
                sex = #{sex},
            </if>
            <if test="phone != null">
                phone = #{phone},
            </if>
            <if test="detail != null">
                detail = #{detail},
            </if>
            <if test="label != null">
                label = #{label},
            </if>
            <if test="isDefault != null">
                is_default = #{isDefault},
            </if>
        </set>
        where id = #{id}
    </update>

</mapper>
```



## 2. 用户下单

### 2.1 需求分析和设计

#### 2.1.1 产品原型

**用户下单业务说明：**
在电商系统中，用户是通过下单的方式通知商家，用户已经购买了商品，需要商家进行备货和发货。
用户下单后会产生订单相关数据，订单数据需要能够体现如下信息：

买的哪些商品?商品数量?订单总金额?哪个用户?收货地址?用户手机号? 

用户将菜品或者套餐加入购物车后，可以点击购物车中的 "去结算" 按钮，页面跳转到订单确认页面，点击 "去支付" 按钮则完成下单操作



#### 2.1.2 接口设计

**基本信息:**

​	path:  /user/order/submit

​	Method:  POST

**请求参数**:  application/json

| 名称                  | 类型    | 是否必须 | 默认值 | 备注                                    | 其他信息 |
| --------------------- | ------- | -------- | ------ | --------------------------------------- | -------- |
| addressBookId         | integer | 必须     |        | 地址簿id                                | <int64>  |
| amount                | number  | 必须     |        | 总金额                                  |          |
| deliveryStatus        | integer | 必须     |        | 配送状态  1立即送出 0选择具体送出时间   | <int32>  |
| estimatedDeliveryTime | string  | 必须     |        | 预计送达时间                            |          |
| packAmount            | integer | 必须     |        | 打包费                                  | <int32>  |
| payMethod             | integer | 必须     |        | 付款方式                                |          |
| remark                | string  | 必须     |        | 备注                                    |          |
| tablewareNumber       | integer | 必须     |        | 餐具数量                                | <int32>  |
| tablewareStatus       | integer | 必须     |        | 餐具数量状态  1按餐量提供 0选择具体数量 | <int32>  |



#### 2.1.3 表设计

用户下单业务对应的数据表为orders表和order_detail表(一对多关系,一个订单关联多个订单明细)：

| 表名         | 含义       | 说明                                                         |
| ------------ | ---------- | ------------------------------------------------------------ |
| orders       | 订单表     | 主要存储订单的基本信息(如: 订单号、状态、金额、支付方式、下单用户、收件地址等) |
| order_detail | 订单明细表 | 主要存储订单详情信息(如: 该订单关联的套餐及菜品的信息)       |

具体的表结构如下: 

**1). orders订单表**

| **字段名**              | **数据类型**  | **说明**     | **备注**                                        |
| ----------------------- | ------------- | ------------ | ----------------------------------------------- |
| id                      | bigint        | 主键         | 自增                                            |
| number                  | varchar(50)   | 订单号       |                                                 |
| status                  | int           | 订单状态     | 1待付款 2待接单 3已接单 4派送中 5已完成 6已取消 |
| user_id                 | bigint        | 用户id       | 逻辑外键                                        |
| address_book_id         | bigint        | 地址id       | 逻辑外键                                        |
| order_time              | datetime      | 下单时间     |                                                 |
| checkout_time           | datetime      | 付款时间     |                                                 |
| pay_method              | int           | 支付方式     | 1微信支付 2支付宝支付                           |
| pay_status              | tinyint       | 支付状态     | 0未支付 1已支付 2退款                           |
| amount                  | decimal(10,2) | 订单金额     |                                                 |
| remark                  | varchar(100)  | 备注信息     |                                                 |
| phone                   | varchar(11)   | 手机号       | 冗余字段                                        |
| address                 | varchar(255)  | 详细地址信息 | 冗余字段                                        |
| consignee               | varchar(32)   | 收货人       | 冗余字段                                        |
| cancel_reason           | varchar(255)  | 订单取消原因 |                                                 |
| rejection_reason        | varchar(255)  | 拒单原因     |                                                 |
| cancel_time             | datetime      | 订单取消时间 |                                                 |
| estimated_delivery_time | datetime      | 预计送达时间 |                                                 |
| delivery_status         | tinyint       | 配送状态     | 1立即送出 0选择具体时间                         |
| delivery_time           | datetime      | 送达时间     |                                                 |
| pack_amount             | int           | 打包费       |                                                 |
| tableware_number        | int           | 餐具数量     |                                                 |
| tableware_status        | tinyint       | 餐具数量状态 | 1按餐量提供 0选择具体数量                       |



**2). order_detail订单明细表**

| **字段名**  | **数据类型**  | **说明**     | **备注** |
| ----------- | ------------- | ------------ | -------- |
| id          | bigint        | 主键         | 自增     |
| name        | varchar(32)   | 商品名称     | 冗余字段 |
| image       | varchar(255)  | 商品图片路径 | 冗余字段 |
| order_id    | bigint        | 订单id       | 逻辑外键 |
| dish_id     | bigint        | 菜品id       | 逻辑外键 |
| setmeal_id  | bigint        | 套餐id       | 逻辑外键 |
| dish_flavor | varchar(50)   | 菜品口味     |          |
| number      | int           | 商品数量     |          |
| amount      | decimal(10,2) | 商品单价     |          |

**说明：**用户提交订单时，需要往订单表orders中插入一条记录，并且需要往order_detail中插入一条或多条记录。



### 2.2 代码开发

#### 2.2.1 DTO设计

**根据用户下单接口的参数设计DTO：** 

在sky-pojo模块，OrdersSubmitDTO.java已定义

```java
package com.sky.dto;

import com.fasterxml.jackson.annotation.JsonFormat;
import lombok.Data;
import java.io.Serializable;
import java.math.BigDecimal;
import java.time.LocalDateTime;

@Data
public class OrdersSubmitDTO implements Serializable {
    //地址簿id
    private Long addressBookId;
    //付款方式
    private int payMethod;
    //备注
    private String remark;
    //预计送达时间
    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd HH:mm:ss")
    private LocalDateTime estimatedDeliveryTime;
    //配送状态  1立即送出  0选择具体时间
    private Integer deliveryStatus;
    //餐具数量
    private Integer tablewareNumber;
    //餐具数量状态  1按餐量提供  0选择具体数量
    private Integer tablewareStatus;
    //打包费
    private Integer packAmount;
    //总金额
    private BigDecimal amount;
}
```



#### 2.2.2 VO设计

**根据用户下单接口的返回结果设计VO：** 

在sky-pojo模块，OrderSubmitVO.java已定义

```java
package com.sky.vo;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;
import java.io.Serializable;
import java.math.BigDecimal;
import java.time.LocalDateTime;

@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class OrderSubmitVO implements Serializable {
    //订单id
    private Long id;
    //订单号
    private String orderNumber;
    //订单金额
    private BigDecimal orderAmount;
    //下单时间
    private LocalDateTime orderTime;
}
```



#### 2.2.3 OrderController

```java
@RestController("userOrderController")
@RequestMapping("/user/order")
@Api(tags = "用户端订单相关接口")
@Slf4j
public class OrderController {
    @Autowired
    private OrderService orderService;

    /**
     * 用户下单
     * @param ordersSubmitDTO
     * @return
     */
    @PostMapping("/submit")
    @ApiOperation("用户下单")
    public Result<OrderSubmitVO> submit(@RequestBody OrdersSubmitDTO ordersSubmitDTO) {
        log.info("用户下单: 参数为: {}", ordersSubmitDTO);
        OrderSubmitVO orderSubmitVO = orderService.submitOrder(ordersSubmitDTO);
        return Result.success(orderSubmitVO);
    }
}
```



#### 2.2.4 OrderServiceImpl

```java
@Service
@Slf4j
public class OrderServiceImpl implements OrderService {
    @Autowired
    private OrderMapper orderMapper;
    @Autowired
    private OrderDetailMapper orderDetailMapper;
    @Autowired
    private AddressBookMapper addressBookMapper;
    @Autowired
    private ShoppingCartMapper shoppingCartMapper;

    /**
     * 用户下单
     * @param ordersSubmitDTO
     * @return
     */
    @Transactional
    @Override
    public OrderSubmitVO submitOrder(OrdersSubmitDTO ordersSubmitDTO) {
        // 处理各种业务异常(地址簿为空, 购物车数据为空)
        AddressBook addressBook = addressBookMapper.getById(ordersSubmitDTO.getAddressBookId());
        if(addressBook == null) {
            throw new AddressBookBusinessException(MessageConstant.ADDRESS_BOOK_IS_NULL);
        }

        // 查询当前用户的购物车数据
        Long userId = BaseContext.getCurrentId();
        ShoppingCart shoppingCart = new ShoppingCart();
        shoppingCart.setUserId(userId);
        List<ShoppingCart> shoppingCartList = shoppingCartMapper.list(shoppingCart);

        if(shoppingCartList == null || shoppingCartList.size() == 0) {
            throw new ShoppingCartBusinessException(MessageConstant.SHOPPING_CART_IS_NULL);
        }

        // 向订单表插入1条数据
        Orders orders = new Orders();
        BeanUtils.copyProperties(ordersSubmitDTO, orders);
        orders.setOrderTime(LocalDateTime.now());
        orders.setPayStatus(Orders.UN_PAID);
        orders.setStatus(Orders.PENDING_PAYMENT);
        orders.setNumber(String.valueOf(System.currentTimeMillis()));
        orders.setPhone(addressBook.getPhone());
        orders.setConsignee(addressBook.getConsignee());
        orders.setUserId(userId);

        orderMapper.insert(orders);

        // 向订单明细表插入n条数据
        List<OrderDetail> orderDetailList = new ArrayList<>();
        for(ShoppingCart cart : shoppingCartList) {
            OrderDetail orderDetail = new OrderDetail(); // 订单明细
            BeanUtils.copyProperties(cart, orderDetail);
            orderDetail.setOrderId(orders.getId()); // 设置当前订单明细关联的订单id
            orderDetailList.add(orderDetail);
        }

        orderDetailMapper.insertBatch(orderDetailList);

        // 清空当前用户的购物车数据
        shoppingCartMapper.deleteByUserId(userId);

        // 封装VO返回结果
        OrderSubmitVO orderSubmitVO = OrderSubmitVO.builder()
                .id(orders.getId())
                .orderTime(orders.getOrderTime())
                .orderNumber(orders.getNumber())
                .orderAmount(orders.getAmount())
                .build();
        return orderSubmitVO;
    }
}
```



#### 2.2.5 OrderMapper

```java
@Mapper
public interface OrderMapper {
    /**
     * 向订单表插入数据
     * @param orders
     */
    void insert(Orders orders);
}
```



#### 2.2.6 OrderMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.OrderMapper">

    <insert id="insert" parameterType="Orders" useGeneratedKeys="true" keyProperty="id">
        insert into orders(number, status, user_id, address_book_id, order_time, checkout_time, pay_method, pay_status,
                           amount, remark, phone, address, consignee, estimated_delivery_time, delivery_status,
                           pack_amount, tableware_number, tableware_status)
        values
                (#{number}, #{status}, #{userId}, #{addressBookId}, #{orderTime}, #{checkoutTime}, #{payMethod},
                #{payStatus}, #{amount}, #{remark}, #{phone}, #{address}, #{consignee}, #{estimatedDeliveryTime},
                #{deliveryStatus}, #{packAmount}, #{tablewareNumber}, #{tablewareStatus})
    </insert>

</mapper>
```



#### 2.2.7 OrderDetailMapper

```java
@Mapper
public interface OrderDetailMapper {
    /**
     * 批量插入订单明细表数据
     * @param orderDetailList
     */
    void insertBatch(List<OrderDetail> orderDetailList);
}
```



#### 2.2.8 OrderDetailMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.sky.mapper.OrderDetailMapper">

    <insert id="insertBatch" parameterType="list">
        insert into order_detail
            (name, order_id, dish_id, setmeal_id, dish_flavor, number, amount, image)
        values
        <foreach collection="orderDetailList" item="od" separator=",">
            (#{od.name},#{od.orderId},#{od.dishId},#{od.setmealId},#{od.dishFlavor},
            #{od.number},#{od.amount},#{od.image})
        </foreach>
    </insert>

</mapper>
```



## 3. 订单支付

### 3.1 代码开发

#### 3.1.1 OrderController

```java
/**
 * 订单支付
 *
 * @param ordersPaymentDTO
 * @return
 */
@PutMapping("/payment")
@ApiOperation("订单支付")
public Result<OrderPaymentVO> payment(@RequestBody OrdersPaymentDTO ordersPaymentDTO) throws Exception {
    log.info("订单支付：{}", ordersPaymentDTO);
    OrderPaymentVO orderPaymentVO = orderService.payment(ordersPaymentDTO);
    log.info("生成预支付交易单：{}", orderPaymentVO);
    return Result.success(orderPaymentVO);
}
```



#### 3.1.2 OrderServiceImpl

```java
 /**
     * 订单支付
     * @param ordersPaymentDTO
     * @return
     */
    public OrderPaymentVO payment(OrdersPaymentDTO ordersPaymentDTO) throws Exception {
        // 当前登录用户id
        Long userId = BaseContext.getCurrentId();
        User user = userMapper.getById(userId);
/*        //调用微信支付接口，生成预支付交易单
        JSONObject jsonObject = weChatPayUtil.pay(
                ordersPaymentDTO.getOrderNumber(), //商户订单号
                new BigDecimal(0.01), //支付金额，单位 元
                "苍穹外卖订单", //商品描述
                user.getOpenid() //微信用户的openid
        );

        if (jsonObject.getString("code") != null && jsonObject.getString("code").equals("ORDERPAID")) {
            throw new OrderBusinessException("该订单已支付");
        }
*/
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("code","ORDERPAID");
        OrderPaymentVO vo = jsonObject.toJavaObject(OrderPaymentVO.class);
        vo.setPackageStr(jsonObject.getString("package"));
        Integer OrderPaidStatus = Orders.PAID;//支付状态，已支付
        Integer OrderStatus = Orders.TO_BE_CONFIRMED;  //订单状态，待接单
        LocalDateTime check_out_time = LocalDateTime.now();//更新支付时间
        orderMapper.updateStatus(OrderStatus, OrderPaidStatus, check_out_time, this.orders.getId());
        return vo;
    }

    /**
     * 支付成功，修改订单状态
     *
     * @param outTradeNo
     */
    public void paySuccess(String outTradeNo) {

        // 根据订单号查询订单
        Orders ordersDB = orderMapper.getByNumber(outTradeNo);

        // 根据订单id更新订单的状态、支付方式、支付状态、结账时间
        Orders orders = Orders.builder()
                .id(ordersDB.getId())
                .status(Orders.TO_BE_CONFIRMED)
                .payStatus(Orders.PAID)
                .checkoutTime(LocalDateTime.now())
                .build();

        orderMapper.update(orders);
    }
```



#### 3.1.3 OrderMapper

```java
/**
 * 根据订单号查询订单
 * @param orderNumber
 */
@Select("select * from orders where number = #{orderNumber}")
Orders getByNumber(String orderNumber);

/**
 * 修改订单信息
 * @param orders
 */
void update(Orders orders);

/**
 * 修改订单状态
 * @param orderStatus
 * @param orderPaidStatus
 * @param check_out_time
 * @param id
 */
@Update("update orders set status = #{orderStatus},pay_status = #{orderPaidStatus} ,checkout_time = #{check_out_time} where id = #{id}")
void updateStatus(Integer orderStatus, Integer orderPaidStatus, LocalDateTime check_out_time, Long id);
```



#### 3.1.4 OrderMapper.xml

```xml
<update id="update" parameterType="com.sky.entity.Orders">
    update orders
    <set>
        <if test="cancelReason != null and cancelReason!='' ">
            cancel_reason=#{cancelReason},
        </if>
        <if test="rejectionReason != null and rejectionReason!='' ">
            rejection_reason=#{rejectionReason},
        </if>
        <if test="cancelTime != null">
            cancel_time=#{cancelTime},
        </if>
        <if test="payStatus != null">
            pay_status=#{payStatus},
        </if>
        <if test="payMethod != null">
            pay_method=#{payMethod},
        </if>
        <if test="checkoutTime != null">
            checkout_time=#{checkoutTime},
        </if>
        <if test="status != null">
            status = #{status},
        </if>
        <if test="deliveryTime != null">
            delivery_time = #{deliveryTime}
        </if>
    </set>
    where id = #{id}
</update>
```



#### 3.1.5 PayNotifyController

```java
/**
 * 支付回调相关接口
 */
@RestController
@RequestMapping("/notify")
@Slf4j
public class PayNotifyController {
    @Autowired
    private OrderService orderService;
    @Autowired
    private WeChatProperties weChatProperties;

    /**
     * 支付成功回调
     *
     * @param request
     */
    @RequestMapping("/paySuccess")
    public void paySuccessNotify(HttpServletRequest request, HttpServletResponse response) throws Exception {
        //读取数据
        String body = readData(request);
        log.info("支付成功回调：{}", body);

        //数据解密
        String plainText = decryptData(body);
        log.info("解密后的文本：{}", plainText);

        JSONObject jsonObject = JSON.parseObject(plainText);
        String outTradeNo = jsonObject.getString("out_trade_no");//商户平台订单号
        String transactionId = jsonObject.getString("transaction_id");//微信支付交易号

        log.info("商户平台订单号：{}", outTradeNo);
        log.info("微信支付交易号：{}", transactionId);

        //业务处理，修改订单状态、来单提醒
        orderService.paySuccess(outTradeNo);

        //给微信响应
        responseToWeixin(response);
    }

    /**
     * 读取数据
     *
     * @param request
     * @return
     * @throws Exception
     */
    private String readData(HttpServletRequest request) throws Exception {
        BufferedReader reader = request.getReader();
        StringBuilder result = new StringBuilder();
        String line = null;
        while ((line = reader.readLine()) != null) {
            if (result.length() > 0) {
                result.append("\n");
            }
            result.append(line);
        }
        return result.toString();
    }

    /**
     * 数据解密
     *
     * @param body
     * @return
     * @throws Exception
     */
    private String decryptData(String body) throws Exception {
        JSONObject resultObject = JSON.parseObject(body);
        JSONObject resource = resultObject.getJSONObject("resource");
        String ciphertext = resource.getString("ciphertext");
        String nonce = resource.getString("nonce");
        String associatedData = resource.getString("associated_data");

        AesUtil aesUtil = new AesUtil(weChatProperties.getApiV3Key().getBytes(StandardCharsets.UTF_8));
        //密文解密
        String plainText = aesUtil.decryptToString(associatedData.getBytes(StandardCharsets.UTF_8),
                nonce.getBytes(StandardCharsets.UTF_8),
                ciphertext);

        return plainText;
    }

    /**
     * 给微信响应
     * @param response
     */
    private void responseToWeixin(HttpServletResponse response) throws Exception{
        response.setStatus(200);
        HashMap<Object, Object> map = new HashMap<>();
        map.put("code", "SUCCESS");
        map.put("message", "SUCCESS");
        response.setHeader("Content-type", ContentType.APPLICATION_JSON.toString());
        response.getOutputStream().write(JSONUtils.toJSONString(map).getBytes(StandardCharsets.UTF_8));
        response.flushBuffer();
    }
}
```





# 十. 用户端订单模块

## 1. 查询历史订单

### 1.1 需求分析和设计

**业务规则:**

​	1.分页查询历史订单

​	2.可以根据订单状态查询

​	3.展示订单数据时，需要展示的数据包括：下单时间、订单状态、订单金额、订单明细（商品名称、图片）

**基本信息:**

​	path:  /user/order/historyOrders

​	Method:  GET

**请求参数:** Query 参数

​	page  string  页面  必需

​	pageSize  string  每页记录数  必需

​	status  string  订单状态  可选



### 1.2 代码开发

#### 1.2.1 OrderController

```java
/**
 * 历史订单查询
 * @param page
 * @param pageSize
 * @param status 订单状态 1待付款 2待接单 3已接单 4派送中 5已完成 6已取消
 * @return
 */
@GetMapping("/historyOrders")
@ApiOperation("历史订单查询")
public Result<PageResult> page(int page, int pageSize, Integer status) {
    log.info("查询历史订单, 参数为: {}, {}, {}", page, pageSize, status);
    PageResult pageResult = orderService.pageQueryUser(page, pageSize, status);
    return Result.success(pageResult);
}
```



#### 1.2.2 OrderServiceImpl

```java
/**
 * 用户端历史订单查询
 * @param pageNum
 * @param pageSize
 * @param status
 * @return
 */
@Override
public PageResult pageQueryUser(int pageNum, int pageSize, Integer status) {
    PageHelper.startPage(pageNum, pageSize);

    OrdersPageQueryDTO ordersPageQueryDTO = new OrdersPageQueryDTO();
    ordersPageQueryDTO.setUserId(BaseContext.getCurrentId());
    ordersPageQueryDTO.setStatus(status);

    // 分页条件查询
    Page<Orders> page = orderMapper.pageQuery(ordersPageQueryDTO);
    List<OrderVO> list = new ArrayList<>();

    // 查询出订单明细, 封装到OrderVO响应
    if(page != null && page.getTotal() > 0) {
        for (Orders orders : page) {
            Long orderId = orders.getId(); // 获取订单id

            // 查询订单明细
            List<OrderDetail> orderDetailList = orderDetailMapper.getByOrderId(orderId);

            OrderVO orderVO = new OrderVO();
            BeanUtils.copyProperties(orders, orderVO);
            orderVO.setOrderDetailList(orderDetailList);

            list.add(orderVO);
        }
    }
    return new PageResult(page.getTotal(),list);
}
```



#### 1.2.3 OrderMapper

```java
/**
 * 分页条件查询并按下单时间排序
 * @param ordersPageQueryDTO
 * @return
 */
Page<Orders> pageQuery(OrdersPageQueryDTO ordersPageQueryDTO);
```



#### 1.2.4 OrderMapper.xml

```xml
<select id="pageQuery" resultType="com.sky.entity.Orders">
    select * from orders
    <where>
        <if test = "number != null and number != ''">
            and number like concat('%', #{number}, '%')
        </if>
        <if test = "phone != null and phone != ''">
            and phone like concat('%', #{phone}, '%')
        </if>
        <if test = "userId != null">
            and user_id = #{userId}
        </if>
        <if test = "status != null">
            and status = #{status}
        </if>
        <if test = "beginTime != null">
            and order_time &gt;= #{beginTime}
        </if>
        <if test = "endTime != null">
            and order_time &lt;= #{endTime}
        </if>
    </where>
    order by order_time desc
</select>
```



#### 1.2.5 OrderDetailMapper

```java
/**
 * 根据订单id查询订单明细
 * @param orderId
 * @return
 */
@Select("select * from order_detail where order_id = #{orderId}")
List<OrderDetail> getByOrderId(Long orderId);
```



## 2. 查询订单详情

### 2.1 需求分析和设计

**基本信息:**

​	path:  /user/order/orderDetail/{id}

​	Method:  GET

**请求参数**:  Path参数

​	id  String  订单id  必需



### 2.2 代码开发

#### 2.2.1 OrderController

```java
/**
 * 查询订单详情
 * @param id
 * @return
 */
@GetMapping("/orderDetail/{id}")
@ApiOperation("查询订单详情")
public Result<OrderVO> details(@PathVariable("id") Long id) {
    log.info("查询订单详情, 订单id为: {}", id);
    OrderVO orderVO = orderService.details(id);
    return Result.success(orderVO);
}
```



#### 2.2.2 OrderServiceImpl

```java
/**
 * 查询订单详情
 * @param id
 * @return
 */
@Override
public OrderVO details(Long id) {
    // 根据id查询订单
    Orders orders = orderMapper.getById(id);

    // 查询订单对应的菜品或套餐明细
    List<OrderDetail> orderDetailList = orderDetailMapper.getByOrderId(orders.getId());

    // 将订单和详细信息封装到OrderVO返回
    OrderVO orderVO = new OrderVO();
    BeanUtils.copyProperties(orders, orderVO);
    orderVO.setOrderDetailList(orderDetailList);

    return orderVO;
}
```



#### 2.2.3 OrderMapper

```java
/**
 * 根据id查询订单
 * @param id
 * @return
 */
@Select("select * from orders where id = #{id}")
Orders getById(Long id);
```



## 3. 取消订单

### 3.1 需求分析和设计

**业务规则：**

​	1.待支付和待接单状态下，用户可直接取消订单

​	2.商家已接单状态下，用户取消订单需电话沟通商家

​	3.派送中状态下，用户取消订单需电话沟通商家

​	4.如果在待接单状态下取消订单，需要给用户退款

​	5.取消订单后需要将订单状态修改为“已取消”

**基本信息:**

​	path:  /user/order/cancel/{id}

​	Method:  PUT

**请求参数**:  Path参数

​	id  String  订单id  必需



### 3.2 代码开发

#### 3.2.1 OrderController

```java
/**
 * 取消订单
 * @param id
 * @return
 */
@PutMapping("/cancel/{id}")
@ApiOperation("取消订单")
public Result cancel(@PathVariable("id") Long id) throws Exception{
    log.info("取消订单, 订单id为: {}", id);
    orderService.userCancelById(id);
    return Result.success();
}
```



#### 3.2.2 OrderServiceImpl

```java
/**
 * 取消订单
 * @param id
 */
@Override
public void userCancelById(Long id) throws Exception{
    // 根据id查询订单
    Orders ordersDB = orderMapper.getById(id);

    // 校验订单是否存在
    if(ordersDB == null) {
        throw new OrderBusinessException(MessageConstant.ORDER_NOT_FOUND);
    }

    // 订单状态 1待付款 2待接单 3已接单 4派送中 5已完成 6已取消
    if(ordersDB.getStatus() > 2) {
        throw new OrderBusinessException(MessageConstant.ORDER_STATUS_ERROR);
    }

    Orders orders = new Orders();
    orders.setId(ordersDB.getId());

    // 订单处于待接单状态下取消, 需要进行退款
    if(ordersDB.getStatus().equals(Orders.TO_BE_CONFIRMED)) {
        // 调用微信支付退款接口
        /*weChatPayUtil.refund(
                ordersDB.getNumber(), // 商户订单号
                ordersDB.getNumber(), // 商户退款单号
                new BigDecimal(0.01), // 退款金额, 单位 元
                new BigDecimal(0.01)); // 原订单金额*/

        orders.setPayStatus(Orders.REFUND);
    }

    // 更新订单状态, 取消原因, 取消时间
    orders.setStatus(Orders.CANCELLED);
    orders.setCancelReason("用户取消");
    orders.setCancelTime(LocalDateTime.now());
    orderMapper.update(orders);
}
```



## 4. 再来一单

### 4.1 需求分析和设计

**业务规则：**

​	1.再来一单就是将原订单中的商品重新加入到购物车中

**基本信息:**

​	path:  /user/order/repetition/{id}

​	Method:  POST

**请求参数**:  Path参数

​	id  string  订单id 



### 4.2 代码开发

#### 4.2.1 OrderController

```java
/**
 * 再来一单
 * @param id
 * @return
 */
@PostMapping("/repetition/{id}")
@ApiOperation("再来一单")
public Result repetition(@PathVariable("id") Long id) {
    log.info("再来一单, 订单id为: {}", id);
    orderService.repetition(id);
    return Result.success();
}
```



#### 4.2.2 OrderServiceImpl

```java
/**
 * 再来一单
 * @param id
 */
@Override
public void repetition(Long id) {
    Long userId = BaseContext.getCurrentId();

    // 根据id查询当前订单详情
    List<OrderDetail> orderDetailList = orderDetailMapper.getByOrderId(id);

    // 将订单详情对象转换为购物车对象
    List<ShoppingCart> shoppingCartList = orderDetailList.stream().map(x -> {
        ShoppingCart shoppingCart = new ShoppingCart();

        // 将原订单详情里的菜品信息重新复制到购物车对象中
        BeanUtils.copyProperties(x, shoppingCart, "id");
        shoppingCart.setUserId(userId);
        shoppingCart.setCreateTime(LocalDateTime.now());

        return shoppingCart;
    }).collect(Collectors.toList());

    // 将购物车对象批量添加到数据库
    shoppingCartMapper.insertBatch(shoppingCartList);
}
```



#### 4.2.3 ShoppingCartMapper

```java
/**
 * 批量添加购物车数据
 * @param shoppingCartList
 */
void insertBatch(List<ShoppingCart> shoppingCartList);
```



#### 4.2.4 ShoppingCartMapper.xml

```xml
<insert id="insertBatch" parameterType="list">
    insert into shopping_cart
        (name, image, user_id, dish_id, setmeal_id, dish_flavor, number, amount, create_time)
    values
    <foreach collection="shoppingCartList" item="sc" separator=",">
        (#{sc.name}, #{sc.image}, #{sc.userId}, #{sc.dishId}, #{sc.setmealId}, #{sc.dishFlavor},
         #{sc.number}, #{sc.amount}, #{sc.createTime})
    </foreach>
</insert>
```



# 十一. 商家端订单管理模块

## 1. 订单搜索

### 1.1 需求分析和设计

**业务规则：**

​	1.输入订单号/手机号进行搜索，支持模糊搜索

​	2.根据订单状态进行筛选

​	3.下单时间进行时间筛选

​	4.搜索内容为空，提示未找到相关订单

​	5.搜索结果页，展示包含搜索关键词的内容

​	6.分页展示搜索到的订单数据

**基本信息:**

​	path:  /admin/order/conditionSearch

​	Method:  GET

**请求参数:**  Query参数

​	beginTime  string

​	endTime  string

​	number  string

​	page  string

​	pageSize  string

​	phone  string

​	status  string



### 1.2 代码开发

#### 1.2.1 OrderController

```java
/**
 * 订单管理
 */
@RestController("adminOrderController")
@RequestMapping("/admin/order")
@Slf4j
@Api(tags = "订单管理接口")
public class OrderController {
    @Autowired
    private OrderService orderService;

    /**
     * 订单搜索
     * @param ordersPageQueryDTO
     * @return
     */
    @GetMapping("/conditionSearch")
    @ApiOperation("订单搜索")
    public Result<PageResult> conditionSearch(OrdersPageQueryDTO ordersPageQueryDTO) {
        log.info("订单搜索: {}", ordersPageQueryDTO);
        PageResult pageResult = orderService.conditionSearch(ordersPageQueryDTO);
        return Result.success(pageResult);
    }
}
```



#### 1.2.2 OrderServiceImpl

```java
/**
 * 订单搜索
 * @param ordersPageQueryDTO
 * @return
 */
@Override
public PageResult conditionSearch(OrdersPageQueryDTO ordersPageQueryDTO) {
    PageHelper.startPage(ordersPageQueryDTO.getPage(), ordersPageQueryDTO.getPageSize());

    Page<Orders> page = orderMapper.pageQuery(ordersPageQueryDTO);

    // 部分订单状态, 需要额外返回订单菜品信息, 将Orders转化为OrderVO
    List<OrderVO> orderVOList = getOrderVOList(page);

    return new PageResult(page.getTotal(), orderVOList);
}

private List<OrderVO> getOrderVOList(Page<Orders> page) {
    // 需要返回订单菜品信息, 自定义OrderVO响应结果
    List<OrderVO> orderVOList = new ArrayList<>();

    List<Orders> ordersList = page.getResult();
    if(!CollectionUtils.isEmpty(ordersList)) {
        for (Orders orders : ordersList) {
            // 将共同字段复制到OrderVO
            OrderVO orderVO = new OrderVO();
            BeanUtils.copyProperties(orders, orderVO);
            String orderDishes = getOrderDishesStr(orders);

            // 手动添加地址
            orderVO.setAddress(getAddress(orders.getAddressBookId()));

            // 将订单菜品信息封装到orderVO中, 并添加到orderVOList
            orderVO.setOrderDishes(orderDishes);
            orderVOList.add(orderVO);
        }
    }
    return orderVOList;
}

/**
 * 根据订单id获取菜品信息字符串
 * @param orders
 * @return
 */
private String getOrderDishesStr(Orders orders) {
    // 查询订单菜品详情信息
    List<OrderDetail> orderDetailList = orderDetailMapper.getByOrderId(orders.getId());

    // 将每一条订单菜品信息拼接为字符串
    List<String> orderDishList = orderDetailList.stream().map(x -> {
        String orderDish = x.getName() + "*" + x.getNumber() + ";";
        return orderDish;
    }).collect(Collectors.toList());

    // 将订单对应的所有菜品信息拼接在一起
    return String.join("", orderDishList);
}

private String getAddress(Long addressBookId){
    AddressBook addressBook = addressBookMapper.getById(addressBookId);
    if (addressBook == null) {
        return "";
    }
    String address = addressBook.getProvinceName() + addressBook.getCityName() +
            addressBook.getDistrictName() + addressBook.getDetail();
    return address;
}
```



## 2. 各个状态的订单数量统计

### 2.1 需求分析和设计

**基本信息:**

​	path:  /admin/order/statistics

​	Method:  GET



### 2.2 代码开发

#### 2.2.1 OrderController

```java
/**
 * 各个状态的订单数量查询
 * @return
 */
@GetMapping("/statistics")
@ApiOperation("各个状态的订单数量查询")
public Result<OrderStatisticsVO> statistics() {
    OrderStatisticsVO orderStatisticsVO = orderService.statistics();
    return Result.success(orderStatisticsVO);
}
```



#### 2.2.2 OrderServiceImpl

```java
/**
 * 各个状态的订单查询
 * @return
 */
@Override
public OrderStatisticsVO statistics() {
    // 根据状态, 分别查询待接单, 待派送, 派送中的订单数量
    // 订单状态 1待付款 2待接单 3已接单 4派送中 5已完成 6已取消
    Integer toBeConfirmed = orderMapper.countStatus(Orders.TO_BE_CONFIRMED);
    Integer confirmed = orderMapper.countStatus(Orders.CONFIRMED);
    Integer deliveryInProgress = orderMapper.countStatus(Orders.DELIVERY_IN_PROGRESS);

    // 将查询出的数据封装到orderStatisticsVO中响应
    OrderStatisticsVO orderStatisticsVO = new OrderStatisticsVO();
    orderStatisticsVO.setToBeConfirmed(toBeConfirmed);
    orderStatisticsVO.setConfirmed(confirmed);
    orderStatisticsVO.setDeliveryInProgress(deliveryInProgress);
    return orderStatisticsVO;
}
```



#### 2.2.3 OrderMapper

```java
/**
 * 根据状态统计订单状态
 * @param status
 * @return
 */
@Select("select count(id) from orders where status = #{status}")
Integer countStatus(Integer status);
```



## 3. 查询订单情况

### 3.1 需求分析和设计

**业务规则**：

​	1.订单详情页面需要展示订单基本信息（状态、订单号、下单时间、收货人、电话、收货地址、金额等）

​	2.订单详情页面需要展示订单明细数据（商品名称、数量、单价）

**基本信息:**

​	path:  /admin/order/details/{id}

​	Method:  GET

**请求参数**:  Path参数

​	id  string  订单id



### 3.2 代码开发

#### 3.2.1 OrderController

```java
/**
 * 订单详情
 * @param id
 * @return
 */
@GetMapping("/details/{id}")
@ApiOperation("查询订单详情")
public Result<OrderVO> details(@PathVariable("id") Long id) {
    log.info("查询订单详情, 订单id为: {}", id);
    OrderVO orderVO = orderService.details(id);
    return Result.success(orderVO);
}
```



## 4. 接单

### 4.1 需求分析和设计

**业务规则：**

​	商家接单其实就是将订单的状态修改为“已接单”

**基本信息:**

​	path:  /admin/order/confirm

​	Method:  PUT

**请求参数:**  Body参数 application/json

​	id  integer  订单id



### 4.2 代码开发

#### 4.2.1 OrderController

```java
/**
 * 接单
 * @param ordersConfirmDTO
 * @return
 */
@PutMapping("/confirm")
@ApiOperation("接单")
public Result confirm(@RequestBody OrdersConfirmDTO ordersConfirmDTO) {
    orderService.confirm(ordersConfirmDTO);
    return Result.success();
}
```



#### 4.2.2 OrderServiceImpl

```java
/**
 * 接单
 * @param ordersConfirmDTO
 */
@Override
public void confirm(OrdersConfirmDTO ordersConfirmDTO) {
    Orders orders = Orders.builder()
            .id(ordersConfirmDTO.getId())
            .status(Orders.CONFIRMED)
            .build();
    orderMapper.update(orders);
}
```



## 5. 拒单

### 5.1 需求分析和设计

**业务规则：**

​	1.商家拒单其实就是将订单状态修改为“已取消”

​	2.只有订单处于“待接单”状态时可以执行拒单操作

​	3.商家拒单时需要指定拒单原因

​	4.商家拒单时，如果用户已经完成了支付，需要为用户退款

**基本信息:**

​	path:  /admin/order/rejection

​	Method:  PUT

**请求参数:**  Body参数  application/json

​	id  integer  订单id

​	rejectionReason  string  拒单原因



### 5.2 代码开发

#### 5.2.1 OrderController

```java
/**
 * 拒单
 * @param ordersRejectionDTO
 * @return
 * @throws Exception
 */
@PutMapping("/rejection")
@ApiOperation("拒单")
public Result rejection(@RequestBody OrdersRejectionDTO ordersRejectionDTO) throws Exception {
    orderService.rejection(ordersRejectionDTO);
    return Result.success();
}
```



#### 5.2.2 OrderServiceImpl

```java
/**
     * 拒单
     * @param ordersRejectionDTO
     */
    @Override
    public void rejection(OrdersRejectionDTO ordersRejectionDTO) throws Exception {
        // 根据id查询订单
        Orders ordersDB = orderMapper.getById(ordersRejectionDTO.getId());

        // 订单只有存在且状态为2（待接单）才可以拒单
        if (ordersDB == null || !ordersDB.getStatus().equals(Orders.TO_BE_CONFIRMED)) {
            throw new OrderBusinessException(MessageConstant.ORDER_STATUS_ERROR);
        }

        //支付状态
        Integer payStatus = ordersDB.getPayStatus();
        Orders orders = new Orders();
        if (payStatus == Orders.PAID) {
//            //用户已支付，需要退款
//            String refund = weChatPayUtil.refund(
//                    ordersDB.getNumber(),
//                    ordersDB.getNumber(),
//                    new BigDecimal(0.01),
//                    new BigDecimal(0.01));
//            log.info("申请退款：{}", refund);

            // 拒单需要退款，根据订单id更新订单状态、拒单原因、取消时间
            orders.setId(ordersDB.getId());
            orders.setStatus(Orders.CANCELLED);
            orders.setPayStatus(Orders.REFUND);
            orders.setRejectionReason(ordersRejectionDTO.getRejectionReason());
            orders.setCancelTime(LocalDateTime.now());
        }

        orderMapper.update(orders);
    }
```



## 6. 取消订单

### 6.1 需求分析和设计

**业务规则：**

​	1.取消订单其实就是将订单状态修改为“已取消”

​	2.商家取消订单时需要指定取消原因

​	3.商家取消订单时，如果用户已经完成了支付，需要为用户退款

**基本信息:**

​	path:  /admin/order/cancel

​	Method:  PUT

**请求参数:**  application/json

​	cancel  string  订单取消原因

​	id  integer  订单id



### 6.2 代码开发

#### 6.2.1 OrderController

```java
/**
 * 取消订单
 * @param ordersCancelDTO
 * @return
 * @throws Exception
 */
@PutMapping("/cancel")
@ApiOperation("取消订单")
public Result cancel(@RequestBody OrdersCancelDTO ordersCancelDTO) throws Exception{
    orderService.cancel(ordersCancelDTO);
    return Result.success();
}
```



#### 6.2.2 OrderServiceImpl

```java
/**
     * 商家取消订单
     *
     * @param ordersCancelDTO
     */
    public void cancel(OrdersCancelDTO ordersCancelDTO) throws Exception {
        // 根据id查询订单
        Orders ordersDB = orderMapper.getById(ordersCancelDTO.getId());

        //支付状态
        Integer payStatus = ordersDB.getPayStatus();
        Orders orders = new Orders();
        if (payStatus == 1) {
//            //用户已支付，需要退款
//            String refund = weChatPayUtil.refund(
//                    ordersDB.getNumber(),
//                    ordersDB.getNumber(),
//                    new BigDecimal(0.01),
//                    new BigDecimal(0.01));
//            log.info("申请退款：{}", refund);


            // 管理端取消订单需要退款，根据订单id更新订单状态、取消原因、取消时间
            orders.setId(ordersCancelDTO.getId());
            orders.setStatus(Orders.CANCELLED);
            orders.setPayStatus(Orders.REFUND);
            orders.setCancelReason(ordersCancelDTO.getCancelReason());
            orders.setCancelTime(LocalDateTime.now());
        }
        orderMapper.update(orders);
    }
```



## 7. 派送订单

### 7.1 需求分析和设计

**业务规则：**

​	1.派送订单其实就是将订单状态修改为“派送中”

​	2.只有状态为“待派送”的订单可以执行派送订单操作

**基本信息:**

​	path:  /admin/order/delivery/{id}

​	Method:  PUT

**请求参数**:  Path参数

​	id  string  订单id



### 7.2 代码开发

#### 7.2.1 OrderController

```java
/**
 * 派送订单
 * @param id
 * @return
 */
@PutMapping("/delivery/{id}")
@ApiOperation("派送订单")
public Result delivery(@PathVariable("id") Long id) {
    orderService.delivery(id);
    return Result.success();
}
```



#### 7.2.2 OrderServiceImpl

```java
/**
 * 派送订单
 * @param id
 */
@Override
public void delivery(Long id) {
    Orders ordersDB = orderMapper.getById(id);

    // 校验订单是否存在, 状态是否为3
    if(ordersDB == null || !ordersDB.getStatus().equals(Orders.CONFIRMED)) {
        throw new OrderBusinessException(MessageConstant.ORDER_STATUS_ERROR);
    }

    Orders orders = new Orders();
    orders.setId(ordersDB.getId());
    // 更新订单状态, 改为派送中
    orders.setStatus(Orders.DELIVERY_IN_PROGRESS);

    orderMapper.update(orders);
}
```



## 8. 完成订单

### 8.1 需求分析和设计

**业务规则**：

​	1.完成订单其实就是将订单状态修改为“已完成”

​	2.只有状态为“派送中”的订单可以执行订单完成操作

**基本信息:**

​	path:  /admin/order/complete/{id}

​	Method:  PUT

**请求参数**:  Path参数

​	id  string  订单id



### 8.2 代码开发

#### 8.2.1 OrderController

```java
/**
 * 完成订单
 * @param id
 * @return
 */
@PutMapping("/complete/{id}")
@ApiOperation("完成订单")
public Result complete(@PathVariable("id") Long id) {
    orderService.complete(id);
    return Result.success();
}
```



#### 8.2.2 OrderServiceImpl

```java
/**
 * 完成订单
 * @param id
 */
@Override
public void complete(Long id) {
    Orders ordersDB = orderMapper.getById(id);

    // 校验订单是否存在, 状态是否为4
    if(ordersDB == null || !ordersDB.getStatus().equals(Orders.DELIVERY_IN_PROGRESS)) {
        throw new OrderBusinessException(MessageConstant.ORDER_STATUS_ERROR);
    }

    Orders orders = new Orders();
    orders.setId(ordersDB.getId());
    // 更新订单状态, 改为完成
    orders.setStatus(Orders.COMPLETED);
    orders.setDeliveryTime(LocalDateTime.now());

    orderMapper.update(orders);
}
```



# 十二. 校验收货地址是否超出配送范围

## 1. 环境准备

注册账号：https://passport.baidu.com/v2/?reg&tt=1671699340600&overseas=&gid=CF954C2-A3D2-417F-9FE6-B0F249ED7E33&tpl=pp&u=https%3A%2F%2Flbsyun.baidu.com%2Findex.php%3Ftitle%3D%E9%A6%96%E9%A1%B5

登录百度地图开放平台：https://lbsyun.baidu.com/

进入控制台，创建应用，获取AK：

相关接口:

https://lbsyun.baidu.com/index.php?title=webapi/guide/webservice-geocoding

https://lbsyun.baidu.com/index.php?title=webapi/directionlite-v1



## 2. 代码开发

### 2.1 application.yml

配置外卖商家店铺地址和百度地图的AK：

```yml
shop:
  address: ${sky.shop.address}
baidu:
  ak: ${sky.baidu.ak}
```



### 2.2 OrderServiceImpl

改造OrderServiceImpl，注入上面的配置项：

~~~java
    @Value("${sky.shop.address}")
    private String shopAddress;

    @Value("${sky.baidu.ak}")
    private String ak;
~~~

在OrderServiceImpl中提供校验方法：

~~~java
/**
     * 检查客户的收货地址是否超出配送范围
     * @param address
     */
    private void checkOutOfRange(String address) {
        Map map = new HashMap();
        map.put("address",shopAddress);
        map.put("output","json");
        map.put("ak",ak);

        //获取店铺的经纬度坐标
        String shopCoordinate = HttpClientUtil.doGet("https://api.map.baidu.com/geocoding/v3", map);

        JSONObject jsonObject = JSON.parseObject(shopCoordinate);
        if(!jsonObject.getString("status").equals("0")){
            throw new OrderBusinessException("店铺地址解析失败");
        }

        //数据解析
        JSONObject location = jsonObject.getJSONObject("result").getJSONObject("location");
        String lat = location.getString("lat");
        String lng = location.getString("lng");
        //店铺经纬度坐标
        String shopLngLat = lat + "," + lng;

        map.put("address",address);
        //获取用户收货地址的经纬度坐标
        String userCoordinate = HttpClientUtil.doGet("https://api.map.baidu.com/geocoding/v3", map);

        jsonObject = JSON.parseObject(userCoordinate);
        if(!jsonObject.getString("status").equals("0")){
            throw new OrderBusinessException("收货地址解析失败");
        }

        //数据解析
        location = jsonObject.getJSONObject("result").getJSONObject("location");
        lat = location.getString("lat");
        lng = location.getString("lng");
        //用户收货地址经纬度坐标
        String userLngLat = lat + "," + lng;

        map.put("origin",shopLngLat);
        map.put("destination",userLngLat);
        map.put("steps_info","0");

        //路线规划
        String json = HttpClientUtil.doGet("https://api.map.baidu.com/directionlite/v1/driving", map);

        jsonObject = JSON.parseObject(json);
        if(!jsonObject.getString("status").equals("0")){
            throw new OrderBusinessException("配送路线规划失败");
        }

        //数据解析
        JSONObject result = jsonObject.getJSONObject("result");
        JSONArray jsonArray = (JSONArray) result.get("routes");
        Integer distance = (Integer) ((JSONObject) jsonArray.get(0)).get("distance");

        if(distance > 100000){
            //配送距离超过100000米
            throw new OrderBusinessException("超出配送范围");
        }
    }
~~~

在OrderServiceImpl的submitOrder方法中调用上面的校验方法：

```java
// 检查用户的收货地址是否超出配送范围
checkOutOfRange(addressBook.getCityName() + addressBook.getDistrictName() + addressBook.getDetail());
```



# 十三. 订单状态定时处理, 来单提醒和客户催单

## 1. Spring Task

### 1.1 介绍

**Spring Task** 是Spring框架提供的任务调度工具，可以按照约定的时间自动执行某个代码逻辑。

**定位：**定时任务框架

**作用：**定时自动执行某段Java代码

  为什么要在Java程序中使用Spring Task？

**应用场景：**

1). 信用卡每月还款提醒 

2). 银行贷款每月还款提醒

3). 火车票售票系统处理未支付订单 

4). 入职纪念日为用户发送通知

**强调：**只要是需要定时处理的场景都可以使用Spring Task



### 1.2 cron表达式

**cron表达式**其实就是一个字符串，通过cron表达式可以**定义任务触发的时间**

**构成规则：**分为6或7个域，由空格分隔开，每个域代表一个含义

每个域的含义分别为：秒、分钟、小时、日、月、周、年(可选)

**举例：**

2022年10月12日上午9点整 对应的cron表达式为：**0 0 9 12 10 ? 2022** 

**说明：**一般**日**和**周**的值不同时设置，其中一个设置，另一个用？表示。



**比如：**描述2月份的最后一天，最后一天具体是几号呢？可能是28号，也有可能是29号，所以就不能写具体数字。

为了描述这些信息，提供一些特殊的字符。这些具体的细节，我们就不用自己去手写，因为这个cron表达式，它其实有在线生成器。

cron表达式在线生成器：https://cron.qqe2.com/ 

可以直接在这个网站上面，只要根据自己的要求去生成corn表达式即可。所以一般就不用自己去编写这个表达式。

**通配符：**

\* 表示所有值； 

? 表示未说明的值，即不关心它为何值； 

\- 表示一个指定的范围； 

, 表示附加一个可能值； 

/ 符号前表示开始时间，符号后表示每次递增的值；

**cron表达式案例：**

*/5 * * * * ? 每隔5秒执行一次

0 */1 * * * ? 每隔1分钟执行一次

0 0 5-15 * * ? 每天5-15点整点触发

0 0/3 * * * ? 每三分钟触发一次

0 0-5 14 * * ? 在每天下午2点到下午2:05期间的每1分钟触发 

0 0/5 14 * * ? 在每天下午2点到下午2:55期间的每5分钟触发

0 0/5 14,18 * * ? 在每天下午2点到2:55期间和下午6点到6:55期间的每5分钟触发

0 0/30 9-17 * * ? 朝九晚五工作时间内每半小时

0 0 10,14,16 * * ? 每天上午10点，下午2点，4点 



### 1.3 入门案例

#### 1.3.1 Spring Task使用步骤

1). 导入maven坐标 spring-context（已存在） 

2). 启动类添加注解 @EnableScheduling 开启任务调度

3). 自定义定时任务类



#### 1.3.2 代码开发

**编写定时任务类：**

进入sky-server模块中

```java
package com.sky.task;

import lombok.extern.slf4j.Slf4j;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

import java.util.Date;

/**
 * 自定义定时任务类
 */
@Component
@Slf4j
public class MyTask {

    /**
     * 定时任务 每隔5秒触发一次
     */
    @Scheduled(cron = "0/5 * * * * ?")
    public void executeTask(){
        log.info("定时任务开始执行：{}",new Date());
    }
}
```

**开启任务调度：**

启动类添加注解 @EnableScheduling

```java
package com.sky;

import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.scheduling.annotation.EnableScheduling;
import org.springframework.transaction.annotation.EnableTransactionManagement;

@SpringBootApplication
@EnableTransactionManagement //开启注解方式的事务管理
@Slf4j
@EnableCaching
@EnableScheduling
public class SkyApplication {
    public static void main(String[] args) {
        SpringApplication.run(SkyApplication.class, args);
        log.info("server started");
    }
}
```



#### 1.3.3 功能测试

启动服务，查看日志 ,每隔5秒执行一次。



## 2. 订单状态定时处理

### 2.1 需求分析

用户下单后可能存在的情况：

- 下单后未支付，订单一直处于**“待支付”**状态
- 用户收货后管理端未点击完成按钮，订单一直处于**“派送中”**状态

​    

支付超时的订单如何处理？                                                       

派送中的订单一直不点击完成如何处理？



对于上面两种情况需要通过**定时任务**来修改订单状态，具体逻辑为：

- 通过定时任务每分钟检查一次是否存在支付超时订单（下单后超过15分钟仍未支付则判定为支付超时订单），如果存在则修改订单状态为“已取消”
- 通过定时任务每天凌晨1点检查一次是否存在“派送中”的订单，如果存在则修改订单状态为“已完成”



### 2.2 代码开发

#### 2.2.1 OrderTask

```java
/**
 * 定时任务类, 定时处理订单状态
 */
@Component
@Slf4j
public class OrderTask {
    @Autowired
    private OrderMapper orderMapper;

    /**
     * 处理超时订单的方法
     */
    @Scheduled(cron = "0 * * * * ?") // 每分钟触发一次
    public void processTimeoutOrder() {
        log.info("定时处理超时订单: {}", LocalDateTime.now());

        LocalDateTime time = LocalDateTime.now().plusMinutes(-15);

        List<Orders> ordersList = orderMapper.getByStatusAndOrderTimeLT(Orders.PENDING_PAYMENT, time);

        if(ordersList != null && ordersList.size() > 0) {
            for (Orders orders : ordersList) {
                orders.setStatus(Orders.CANCELLED);
                orders.setCancelReason("订单超时, 自动取消");
                orders.setCancelTime(LocalDateTime.now());
                orderMapper.update(orders);
            }
        }
    }

    /**
     * 处理一直处于派送中状态的方法
     */
    @Scheduled(cron = "0 0 1 * * ?") // 每分钟触发一次
    public void processDeliveryOrder() {
        log.info("定时处理派送中订单: {}", LocalDateTime.now());

        LocalDateTime time = LocalDateTime.now().plusMinutes(-60);

        List<Orders> ordersList = orderMapper.getByStatusAndOrderTimeLT(Orders.DELIVERY_IN_PROGRESS, time);

        if(ordersList != null && ordersList.size() > 0) {
            for (Orders orders : ordersList) {
                orders.setStatus(Orders.COMPLETED);
                orderMapper.update(orders);
            }
        }
    }
}
```



#### 2.2.2 OrderMapper

```java
/**
 * 根据订单状态和下单时间查询订单
 * @param status
 * @param orderTime
 * @return
 */
@Select("select * from orders where status = #{status} and order_time < #{orderTime}")
List<Orders> getByStatusAndOrderTimeLT(Integer status, LocalDateTime orderTime);
```



## 3. WebSocket

### 3.1 介绍

WebSocket 是基于 TCP 的一种新的**网络协议**。它实现了浏览器与服务器全双工通信——浏览器和服务器只需要完成一次握手，两者之间就可以创建**持久性**的连接， 并进行**双向**数据传输。

**HTTP协议和WebSocket协议对比：**

- HTTP是**短连接**
- WebSocket是**长连接**
- HTTP通信是**单向**的，基于请求响应模式
- WebSocket支持**双向**通信
- HTTP和WebSocket底层都是TCP连接

​            

**思考：**既然WebSocket支持双向通信，功能看似比HTTP强大，那么我们是不是可以基于WebSocket开发所有的业务功能？

**WebSocket缺点：**

服务器长期维护长连接需要一定的成本
各个浏览器支持程度不一
WebSocket 是长连接，受网络限制比较大，需要处理好重连

**结论：**WebSocket并不能完全取代HTTP，它只适合在特定的场景下使用



**WebSocket应用场景：**

1). 视频弹幕

2). 网页聊天

3). 体育实况更新

4). 股票基金报价实时更新 



### 3.2 入门案例

#### 3.2.1 案例分析

**需求：**实现浏览器与服务器全双工通信。浏览器既可以向服务器发送消息，服务器也可主动向浏览器推送消息。 

**实现步骤：**

1). 直接使用websocket.html页面作为WebSocket客户端

2). 导入WebSocket的maven坐标

3). 导入WebSocket服务端组件WebSocketServer，用于和客户端通信

4). 导入配置类WebSocketConfiguration，注册WebSocket的服务端组件

5). 导入定时任务类WebSocketTask，定时向客户端推送数据



#### 3.2.2 代码开发

1). 定义websocket.html页面(资料中已提供)

```html
<!DOCTYPE HTML>
<html>
<head>
    <meta charset="UTF-8">
    <title>WebSocket Demo</title>
</head>
<body>
    <input id="text" type="text" />
    <button onclick="send()">发送消息</button>
    <button onclick="closeWebSocket()">关闭连接</button>
    <div id="message">
    </div>
</body>
<script type="text/javascript">
    var websocket = null;
    var clientId = Math.random().toString(36).substr(2);

    //判断当前浏览器是否支持WebSocket
    if('WebSocket' in window){
        //连接WebSocket节点
        websocket = new WebSocket("ws://localhost:8080/ws/"+clientId);
    }
    else{
        alert('Not support websocket')
    }

    //连接发生错误的回调方法
    websocket.onerror = function(){
        setMessageInnerHTML("error");
    };

    //连接成功建立的回调方法
    websocket.onopen = function(){
        setMessageInnerHTML("连接成功");
    }

    //接收到消息的回调方法
    websocket.onmessage = function(event){
        setMessageInnerHTML(event.data);
    }

    //连接关闭的回调方法
    websocket.onclose = function(){
        setMessageInnerHTML("close");
    }

    //监听窗口关闭事件，当窗口关闭时，主动去关闭websocket连接，防止连接还没断开就关闭窗口，server端会抛异常。
    window.onbeforeunload = function(){
        websocket.close();
    }

    //将消息显示在网页上
    function setMessageInnerHTML(innerHTML){
        document.getElementById('message').innerHTML += innerHTML + '<br/>';
    }

    //发送消息
    function send(){
        var message = document.getElementById('text').value;
        websocket.send(message);
    }
	
	//关闭连接
    function closeWebSocket() {
        websocket.close();
    }
</script>
</html>
```



2). 导入maven坐标

在sky-server模块pom.xml中已定义

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```



3). 定义WebSocket服务端组件(资料中已提供)

直接导入到sky-server模块即可

```java
package com.sky.websocket;

import org.springframework.stereotype.Component;
import javax.websocket.OnClose;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.server.PathParam;
import javax.websocket.server.ServerEndpoint;
import java.util.Collection;
import java.util.HashMap;
import java.util.Map;

/**
 * WebSocket服务
 */
@Component
@ServerEndpoint("/ws/{sid}")
public class WebSocketServer {

    //存放会话对象
    private static Map<String, Session> sessionMap = new HashMap();

    /**
     * 连接建立成功调用的方法
     */
    @OnOpen
    public void onOpen(Session session, @PathParam("sid") String sid) {
        System.out.println("客户端：" + sid + "建立连接");
        sessionMap.put(sid, session);
    }

    /**
     * 收到客户端消息后调用的方法
     *
     * @param message 客户端发送过来的消息
     */
    @OnMessage
    public void onMessage(String message, @PathParam("sid") String sid) {
        System.out.println("收到来自客户端：" + sid + "的信息:" + message);
    }

    /**
     * 连接关闭调用的方法
     *
     * @param sid
     */
    @OnClose
    public void onClose(@PathParam("sid") String sid) {
        System.out.println("连接断开:" + sid);
        sessionMap.remove(sid);
    }

    /**
     * 群发
     *
     * @param message
     */
    public void sendToAllClient(String message) {
        Collection<Session> sessions = sessionMap.values();
        for (Session session : sessions) {
            try {
                //服务器向客户端发送消息
                session.getBasicRemote().sendText(message);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }

}
```



4). 定义配置类，注册WebSocket的服务端组件(从资料中直接导入即可)

```java
package com.sky.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;

/**
 * WebSocket配置类，用于注册WebSocket的Bean
 */
@Configuration
public class WebSocketConfiguration {

    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }

}
```



5). 定义定时任务类，定时向客户端推送数据(从资料中直接导入即可)

```java
package com.sky.task;

import com.sky.websocket.WebSocketServer;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

@Component
public class WebSocketTask {
    @Autowired
    private WebSocketServer webSocketServer;

    /**
     * 通过WebSocket每隔10秒向客户端发送消息
     */
    // @Scheduled(cron = "0/10 * * * * ?")
    public void sendMessageToClient() {
        webSocketServer.sendToAllClient("这是来自服务端的消息：" + DateTimeFormatter.ofPattern("HH:mm:ss").format(LocalDateTime.now()));
    }
}
```



## 4. 来单提醒

### 4.1 需求分析和设计

用户下单并且支付成功后，需要第一时间通知外卖商家。通知的形式有如下两种：

- 语音播报  
- 弹出提示框



**设计思路：**

- 通过WebSocket实现管理端页面和服务端保持长连接状态
- 当客户支付后，调用WebSocket的相关API实现服务端向客户端推送消息
- 客户端浏览器解析服务端推送的消息，判断是来单提醒还是客户催单，进行相应的消息提示和语音播报
- 约定服务端发送给客户端浏览器的数据格式为JSON，字段包括：type，orderId，content
    - type 为消息类型，1为来单提醒 2为客户催单
    - orderId 为订单id
    - content 为消息内容



### 4.2 代码开发

#### 4.2.1 OrderServiceImpl

**在OrderServiceImpl中注入WebSocketServer对象，修改payment方法，加入如下代码：**

```java
/**
     * 订单支付
     * @param ordersPaymentDTO
     * @return
     */
    public OrderPaymentVO payment(OrdersPaymentDTO ordersPaymentDTO) throws Exception {
        // 当前登录用户id
        Long userId = BaseContext.getCurrentId();
        User user = userMapper.getById(userId);
/*        //调用微信支付接口，生成预支付交易单
        JSONObject jsonObject = weChatPayUtil.pay(
                ordersPaymentDTO.getOrderNumber(), //商户订单号
                new BigDecimal(0.01), //支付金额，单位 元
                "苍穹外卖订单", //商品描述
                user.getOpenid() //微信用户的openid
        );

        if (jsonObject.getString("code") != null && jsonObject.getString("code").equals("ORDERPAID")) {
            throw new OrderBusinessException("该订单已支付");
        }
*/
        JSONObject jsonObject = new JSONObject();
        jsonObject.put("code","ORDERPAID");
        OrderPaymentVO vo = jsonObject.toJavaObject(OrderPaymentVO.class);
        vo.setPackageStr(jsonObject.getString("package"));
        Integer OrderPaidStatus = Orders.PAID;//支付状态，已支付
        Integer OrderStatus = Orders.TO_BE_CONFIRMED;  //订单状态，待接单
        LocalDateTime check_out_time = LocalDateTime.now();//更新支付时间
        orderMapper.updateStatus(OrderStatus, OrderPaidStatus, check_out_time, this.orders.getId());

        Map map = new HashMap();
        map.put("type", 1);// 消息类型，1表示来单提醒
        //获取订单id
        Orders orders=orderMapper.getByNumberAndUserId(ordersPaymentDTO.getOrderNumber(), userId);
        map.put("orderId", orders.getId());
        map.put("content", "订单号：" + ordersPaymentDTO.getOrderNumber());

        // 通过WebSocket实现来单提醒，向客户端浏览器推送消息
        webSocketServer.sendToAllClient(JSON.toJSONString(map));
        log.info("来单提醒：{}", JSON.toJSONString(map));

        return vo;
    }
```



#### 4.2.2 orderMapper

```java
/**
 * 根据订单号查询当前用户的id
 * @param orderNumber
 * @param userId
 * @return
 */
@Select("select * from orders where number = #{orderNumber} and user_id = #{userId};")
Orders getByNumberAndUserId(String orderNumber, Long userId);
```



## 5. 客户催单

### 5.1 需求分析和设计

用户在小程序中点击催单按钮后，需要第一时间通知外卖商家。通知的形式有如下两种：

- 语音播报 
- 弹出提示框 



**设计思路：**

- 通过WebSocket实现管理端页面和服务端保持长连接状态
- 当用户点击催单按钮后，调用WebSocket的相关API实现服务端向客户端推送消息
- 客户端浏览器解析服务端推送的消息，判断是来单提醒还是客户催单，进行相应的消息提示和语音播报
    约定服务端发送给客户端浏览器的数据格式为JSON，字段包括：type，orderId，content
    - type 为消息类型，1为来单提醒 2为客户催单
    - orderId 为订单id
    - content 为消息内容

当用户点击催单按钮时，向服务端发送请求。

**基本信息:**

​	path:  /user/order/reminder/{id}

​	Method:  GET

**请求参数:**  Path参数

​	id  string  订单id



### 5.2 代码开发

#### 5.2.1 OrderController

```java
/**
 * 用户催单
 * @param id
 * @return
 */
@GetMapping("/reminder/{id}")
@ApiOperation("用户催单")
public Result reminder(@PathVariable("id") Long id) {
    orderService.reminder(id);
    return Result.success();
}
```



#### 5.2.2 OrderServiceImpl

```java
/**
 * 用户催单
 * @param id
 */
@Override
public void reminder(Long id) {
    Orders ordersDB = orderMapper.getById(id);

    // 校验订单是否存在
    if(ordersDB == null) {
        throw new OrderBusinessException(MessageConstant.ORDER_STATUS_ERROR);
    }

    Map map = new HashMap();
    map.put("type", 2);
    map.put("orderId", id);
    map.put("content", "订单号: " + ordersDB.getNumber());

    // 通过websocket向客户端浏览器推送消息
    webSocketServer.sendToAllClient(JSON.toJSONString(map));
}
```



# 十四. 数据统计-图形报表

## 1. 营业额统计

### 1.1 需求分析和设计

#### 1.1.1 产品原型

营业额统计是基于折现图来展现，并且按照天来展示的。实际上，就是某一个时间范围之内的每一天的营业额。同时，不管光标放在哪个点上，那么它就会把具体的数值展示出来。并且还需要注意日期并不是固定写死的，是由上边时间选择器来决定。比如选择是近7天、或者是近30日，或者是本周，就会把相应这个时间段之内的每一天日期通过横坐标展示。

**业务规则：**

- 营业额指订单状态为已完成的订单金额合计
- 基于可视化报表的折线图展示营业额数据，X轴为日期，Y轴为营业额
- 根据时间选择区间，展示每天的营业额数据



#### 1.1.2 接口设计

**基本信息:**

​	path:   /admin/report/turnoverStatistics

​	Method:  GET

**请求参数**:  Query参数

​	begin  string  开始日期

​	end  string  结束日期

**注意：**具体返回数据一般由前端来决定，前端展示图表，具体折现图对应数据是什么格式，是有固定的要求的。
所以说，后端需要去适应前端，它需要什么格式的数据，我们就给它返回什么格式的数据。



### 1.2 代码开发

#### 1.2.1 TurnoverReportVO

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class TurnoverReportVO implements Serializable {

    //日期，以逗号分隔，例如：2022-10-01,2022-10-02,2022-10-03
    private String dateList;

    //营业额，以逗号分隔，例如：406.0,1520.0,75.0
    private String turnoverList;

}
```



#### 1.2.2 ReportController

```java
/**
 * 数据统计相关接口
 */
@RestController
@RequestMapping("/admin/report")
@Api(tags = "数据统计相关接口")
@Slf4j
public class ReportController {
    @Autowired
    private ReportService reportService;

    /**
     * 营业额统计
     * @param begin
     * @param end
     * @return
     */
    @GetMapping("/turnoverStatistics")
    @ApiOperation("营业额统计")
    public Result<TurnoverReportVO> turnoverStatistics(
            @DateTimeFormat(pattern = "yyyy-MM-dd") LocalDate begin,
            @DateTimeFormat(pattern = "yyyy-MM-dd") LocalDate end) {
        log.info("营业额数据统计: {}, {}", begin, end);
        return Result.success(reportService.getTurnoverStatistics(begin, end));
    }
}
```



#### 1.2.3 ReportServiceImpl

```java
@Service
@Slf4j
public class ReportServiceImpl implements ReportService {
    @Autowired
    private OrderMapper orderMapper;

    /**
     * 统计指定区间内的营业额
     * @param begin
     * @param end
     * @return
     */
    @Override
    public TurnoverReportVO getTurnoverStatistics(LocalDate begin, LocalDate end) {
        // 当前集合用于存放从begin到end范围内的每天的日期
        List<LocalDate> dateList = new ArrayList<>();

        dateList.add(begin);

        while(!begin.equals(end)) {
            // 日期计算, 计算指定日期的后一天对应的日期
            begin = begin.plusDays(1);
            dateList.add(begin);
        }

        // 存放每天的营业额
        List<Double> turnoverList = new ArrayList<>();
        for (LocalDate date : dateList) {
            // 查询date日期对应的营业额数据, 指的是状态的已完成的订单金额总计
            LocalDateTime beginTime = LocalDateTime.of(date, LocalTime.MIN);
            LocalDateTime endTime = LocalDateTime.of(date, LocalTime.MAX);

            Map map = new HashMap();
            map.put("begin", beginTime);
            map.put("end", endTime);
            map.put("status", Orders.COMPLETED);
            Double turnover = orderMapper.sumByMap(map);
            turnover = turnover == null ? 0.0 : turnover;
            turnoverList.add(turnover);
        }

        return TurnoverReportVO
                .builder()
                .dateList(StringUtils.join(dateList, ","))
                .turnoverList(StringUtils.join(turnoverList, ","))
                .build();
    }
}
```



#### 1.2.4 OrderMapper

```java
/**
 * 查询指定日期的营业额
 * @param map
 * @return
 */
Double sumByMap(Map map);
```



#### 1.2.5 OrderMapper.xml

```java
<select id="sumByMap" resultType="java.lang.Double">
    select sum(amount) from orders
    <where>
        <if test = "begin != null">
            and order_time &gt; #{begin}
        </if>
        <if test = "end != null">
            and order_time &lt; #{end}
        </if>
        <if test = "status != null">
            and status = #{status}
        </if>
    </where>
</select>
```



## 2. 用户统计

### 2.1 需求分析和设计

#### 2.1.1 产品原型

所谓用户统计，实际上统计的是用户的数量。通过折线图来展示，上面这根蓝色线代表的是用户总量，下边这根绿色线代表的是新增用户数量，是具体到每一天。所以说用户统计主要统计**两个数据**，一个是**总的用户数量**，另外一个是**新增用户数量**。



**业务规则：**

- 基于可视化报表的折线图展示用户数据，X轴为日期，Y轴为用户数
- 根据时间选择区间，展示每天的用户总量和新增用户量数据



#### 2.1.2 接口设计

**基本信息:**

​	path:  /admin/report/userStatistics

​	Method:  GET

**请求参数**:  Query参数

​	begin  string  开始日期

​	end  string  结束日期



### 2.2 代码开发

#### 2.2.1 UserReportVO

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class UserReportVO implements Serializable {

    //日期，以逗号分隔，例如：2022-10-01,2022-10-02,2022-10-03
    private String dateList;

    //用户总量，以逗号分隔，例如：200,210,220
    private String totalUserList;

    //新增用户，以逗号分隔，例如：20,21,10
    private String newUserList;

}
```



#### 2.2.2 ReportController

```java
/**
 * 用户统计
 * @param begin
 * @param end
 * @return
 */
@GetMapping("/userStatistics")
@ApiOperation("用户统计")
public Result<UserReportVO> userStatistics(
        @DateTimeFormat(pattern = "yyyy-MM-dd") LocalDate begin,
        @DateTimeFormat(pattern = "yyyy-MM-dd") LocalDate end) {
    log.info("用户统计: {}, {}", begin, end);
    return Result.success(reportService.getUserStatistics(begin, end));
}
```

  

#### 2.2.3 ReportServiceImpl

```java
/**
 * 用户统计
 * @param begin
 * @param end
 * @return
 */
@Override
public UserReportVO getUserStatistics(LocalDate begin, LocalDate end) {
    // 当前集合用于存放从begin到end范围内的每天的日期
    List<LocalDate> dateList = new ArrayList<>();

    dateList.add(begin);

    while(!begin.equals(end)) {
        // 日期计算, 计算指定日期的后一天对应的日期
        begin = begin.plusDays(1);
        dateList.add(begin);
    }

    // 存放每天的新增用户数量
    List<Integer> newUserList = new ArrayList<>();
    // 存放每天的总用户数量
    List<Integer> totalUserList = new ArrayList<>();

    for (LocalDate date : dateList) {
        LocalDateTime beginTime = LocalDateTime.of(date, LocalTime.MIN);
        LocalDateTime endTime = LocalDateTime.of(date, LocalTime.MAX);

        Map map = new HashMap();
        map.put("end", endTime);

        // 总用户数量
        Integer totalUser = userMapper.countByMap(map);

        map.put("begin", beginTime);
        // 新增用户
        Integer newUser = userMapper.countByMap(map);

        totalUserList.add(totalUser);
        newUserList.add(newUser);
    }

    return UserReportVO
            .builder()
            .dateList(StringUtils.join(dateList, ","))
            .totalUserList(StringUtils.join(totalUserList, ","))
            .newUserList(StringUtils.join(newUserList, ","))
            .build();
}
```



#### 2.2.4 UserMapper

```java
/**
 * 根据动态条件统计用户数据
 * @param map
 * @return
 */
Integer countByMap(Map map);
```



#### 2.2.5 UserMapper.xml

```xml
<select id="countByMap" resultType="java.lang.Integer">
    select count(id) from user
    <where>
        <if test = "begin != null">
            and create_time &gt; #{begin}
        </if>
        <if test = "end != null">
            and create_time &lt; #{end}
        </if>
    </where>
</select>
```



## 3. 订单统计

### 3.1 需求分析和设计

#### 3.1.1 产品原型

订单统计通过一个折现图来展现，折线图上有两根线，这根蓝色的线代表的是订单总数，而下边这根绿色的线代表的是有效订单数，指的就是状态是已完成的订单就属于有效订单，分别反映的是每一天的数据。上面还有3个数字，分别是订单总数、有效订单、订单完成率，它指的是整个时间区间之内总的数据。



**业务规则：**

- 有效订单指状态为 “已完成” 的订单
- 基于可视化报表的折线图展示订单数据，X轴为日期，Y轴为订单数量
- 根据时间选择区间，展示每天的订单总数和有效订单数
- 展示所选时间区间内的有效订单数、总订单数、订单完成率，订单完成率 = 有效订单数 / 总订单数 * 100%



#### 3.1.2 接口设计

**基本信息:**

​	path:  /admin/report/ordersStatistics

​	Method:  GET

**请求参数**:  Query参数

​	begin  string  开始日期

​	end  string  结束日期



### 3.2 代码开发

#### 3.2.1 OrderReportVO

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class OrderReportVO implements Serializable {

    //日期，以逗号分隔，例如：2022-10-01,2022-10-02,2022-10-03
    private String dateList;

    //每日订单数，以逗号分隔，例如：260,210,215
    private String orderCountList;

    //每日有效订单数，以逗号分隔，例如：20,21,10
    private String validOrderCountList;

    //订单总数
    private Integer totalOrderCount;

    //有效订单数
    private Integer validOrderCount;

    //订单完成率
    private Double orderCompletionRate;

}
```

 

#### 3.2.2 ReportController

```java
/**
 * 订单统计
 * @param begin
 * @param end
 * @return
 */
@GetMapping("/ordersStatistics")
@ApiOperation("订单统计")
public Result<OrderReportVO> orderStatistics(
        @DateTimeFormat(pattern = "yyyy-MM-dd") LocalDate begin,
        @DateTimeFormat(pattern = "yyyy-MM-dd") LocalDate end) {
    log.info("订单数据统计: {}, {}", begin, end);
    return Result.success(reportService.getOrderStatistics(begin, end));
}
```



#### 3.2.3 ReportServiceImpl

```java
/**
 * 统计指定区间内的订单数据
 * @param begin
 * @param end
 * @return
 */
@Override
public OrderReportVO getOrderStatistics(LocalDate begin, LocalDate end) {
    // 当前集合用于存放从begin到end范围内的每天的日期
    List<LocalDate> dateList = new ArrayList<>();

    dateList.add(begin);

    while(!begin.equals(end)) {
        // 日期计算, 计算指定日期的后一天对应的日期
        begin = begin.plusDays(1);
        dateList.add(begin);
    }

    // 存放每日订单数
    List<Integer> orderCountList = new ArrayList<>();
    // 存放每日有效订单数
    List<Integer> validOrderCountList = new ArrayList<>();

    for (LocalDate date : dateList) {
        LocalDateTime beginTime = LocalDateTime.of(date, LocalTime.MIN);
        LocalDateTime endTime = LocalDateTime.of(date, LocalTime.MAX);

        // 查询每天的订单总数
        Integer orderCount = getOrderCount(beginTime, endTime, null);
        // 查询每天的有效订单数
        Integer validOrderCount = getOrderCount(beginTime, endTime, Orders.COMPLETED);

        orderCountList.add(orderCount);
        validOrderCountList.add(validOrderCount);
    }

    // 计算时间区间内的订单总数量
    Integer totalOrderCount = orderCountList.stream().reduce(Integer::sum).get();
    // 计算时间区间内的有效订单数量
    Integer validOrderCount = validOrderCountList.stream().reduce(Integer::sum).get();
    // 计算订单完成率
    Double orderCompletionRate = 0.0;
    if(totalOrderCount != 0) {
        orderCompletionRate = validOrderCount.doubleValue() / totalOrderCount;
    }
    return OrderReportVO
            .builder()
            .dateList(StringUtils.join(dateList, ","))
            .orderCountList(StringUtils.join(orderCountList, ","))
            .validOrderCountList(StringUtils.join(validOrderCountList, ","))
            .totalOrderCount(totalOrderCount)
            .validOrderCount(validOrderCount)
            .orderCompletionRate(orderCompletionRate)
            .build();
}

private Integer getOrderCount(LocalDateTime begin, LocalDateTime end, Integer status) {
    Map map = new HashMap();
    map.put("begin", begin);
    map.put("end", end);
    map.put("status", status);

    return orderMapper.countByMap(map);
}
```



#### 3.2.4 OrderMapper

```java
/**
 * 根据动态条件统计订单数量
 * @param map
 * @return
 */
Integer countByMap(Map map);
```



#### 3.2.5 OrderMapper.xml

```java
<select id="countByMap" resultType="java.lang.Integer">
    select count(id) from orders
    <where>
        <if test = "begin != null">
            and order_time &gt; #{begin}
        </if>
        <if test = "end != null">
            and order_time &lt; #{end}
        </if>
        <if test = "status != null">
            and status = #{status}
        </if>
    </where>
</select>
```



## 4. 销量排名Top10

### 4.1 需求分析和设计

#### 4.1.1 产品原型

所谓销量排名，销量指的是商品销售的数量。项目当中的商品主要包含两类：一个是**套餐**，一个是**菜品**，所以销量排名其实指的就是菜品和套餐销售的数量排名。通过柱形图来展示销量排名，这些销量是按照降序来排列，并且只需要统计销量排名前十的商品。

 

**业务规则：**

- 根据时间选择区间，展示销量前10的商品（包括菜品和套餐）
- 基于可视化报表的柱状图降序展示商品销量
- 此处的销量为商品销售的份数



#### 4.1.2 接口设计

**基本信息:**

​	path:  /admin/report/top10

​	Method:  GET

**请求参数**:  Query参数

​	begin  string  开始日期

​	end  string  结束日期



### 4.2 代码开发

#### 4.2.1 SalesTop10ReportVO

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class SalesTop10ReportVO implements Serializable {

    //商品名称列表，以逗号分隔，例如：鱼香肉丝,宫保鸡丁,水煮鱼
    private String nameList;

    //销量列表，以逗号分隔，例如：260,215,200
    private String numberList;

}
```



#### 4.2.2 GoodsSalesDTO

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
public class GoodsSalesDTO implements Serializable {
    //商品名称
    private String name;

    //销量
    private Integer number;
}
```



#### 4.2.3 ReportController

```java
/**
 * 销量排名top10
 * @param begin
 * @param end
 * @return
 */
@GetMapping("/top10")
@ApiOperation("销量排名top10")
public Result<SalesTop10ReportVO> top10(
        @DateTimeFormat(pattern = "yyyy-MM-dd") LocalDate begin,
        @DateTimeFormat(pattern = "yyyy-MM-dd") LocalDate end) {
    log.info("销量排名top10: {}, {}", begin, end);
    return Result.success(reportService.getSalesTop10(begin, end));
}
```

 

#### 4.2.4 ReportServiceImpl

```java
/**
 * 指定区间内销量排名top10
 * @param begin
 * @param end
 * @return
 */
@Override
public SalesTop10ReportVO getSalesTop10(LocalDate begin, LocalDate end) {
    LocalDateTime beginTime = LocalDateTime.of(begin, LocalTime.MIN);
    LocalDateTime endTime = LocalDateTime.of(end, LocalTime.MAX);

    List<GoodsSalesDTO> salesTop10 = orderMapper.getSalesTop10(beginTime, endTime);
    List<String> names = salesTop10.stream().map(GoodsSalesDTO::getName).collect(Collectors.toList());
    String nameList = StringUtils.join(names, ",");

    List<Integer> numbers = salesTop10.stream().map(GoodsSalesDTO::getNumber).collect(Collectors.toList());
    String numberList = StringUtils.join(numbers, ",");

    return SalesTop10ReportVO
            .builder()
            .nameList(nameList)
            .numberList(numberList)
            .build();
}
```



#### 4.2.5 OrderMapper

```java
/**
 * 统计指定区间内的销量排名top10
 * @param begin
 * @param end
 * @return
 */
List<GoodsSalesDTO> getSalesTop10(LocalDateTime begin, LocalDateTime end);
```



#### 4.2.6 OrderMapper.xml

```java
<select id="getSalesTop10" resultType="com.sky.dto.GoodsSalesDTO">
    select od.name, sum(od.number) number
    from order_detail od, orders o
    where od.order_id = o.id and o.status = 5
    <if test = "begin != null">
        and o.order_time &gt; #{begin}
    </if>
    <if test = "end != null">
        and o.order_time &lt; #{end}
    </if>
    group by od.name
    order by number desc
    limit 0,10
</select>
```



# 十五. 工作台及数据统计-Excel报表

## 1. 工作台

### 1.1 需求分析和设计

#### 1.1.1 产品原型

工作台是系统运营的数据看板，并提供快捷操作入口，可以有效提高商家的工作效率。

**工作台展示的数据：**

- 今日数据
- 订单管理
- 菜品总览
- 套餐总览
- 订单信息



**名词解释：**

- 营业额：已完成订单的总金额
- 有效订单：已完成订单的数量
- 订单完成率：有效订单数 / 总订单数 * 100%
- 平均客单价：营业额 / 有效订单数
- 新增用户：新增用户的数量



#### 1.1.2 接口设计

通过上述原型图分析，共包含6个接口。

**接口设计：**

- 今日数据接口
- 订单管理接口
- 菜品总览接口
- 套餐总览接口
- 订单搜索（已完成）
- 各个状态的订单数量统计（已完成）

 

### 1.2 代码导入

直接导入课程资料中的工作台模块功能代码即可： 

#### 1.2.1 Controller层

**添加WorkSpaceController.java**

```java
package com.sky.controller.admin;

import com.sky.result.Result;
import com.sky.service.WorkspaceService;
import com.sky.vo.BusinessDataVO;
import com.sky.vo.DishOverViewVO;
import com.sky.vo.OrderOverViewVO;
import com.sky.vo.SetmealOverViewVO;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import java.time.LocalDateTime;
import java.time.LocalTime;

/**
 * 工作台
 */
@RestController
@RequestMapping("/admin/workspace")
@Slf4j
@Api(tags = "工作台相关接口")
public class WorkSpaceController {

    @Autowired
    private WorkspaceService workspaceService;

    /**
     * 工作台今日数据查询
     * @return
     */
    @GetMapping("/businessData")
    @ApiOperation("工作台今日数据查询")
    public Result<BusinessDataVO> businessData(){
        //获得当天的开始时间
        LocalDateTime begin = LocalDateTime.now().with(LocalTime.MIN);
        //获得当天的结束时间
        LocalDateTime end = LocalDateTime.now().with(LocalTime.MAX);

        BusinessDataVO businessDataVO = workspaceService.getBusinessData(begin, end);
        return Result.success(businessDataVO);
    }

    /**
     * 查询订单管理数据
     * @return
     */
    @GetMapping("/overviewOrders")
    @ApiOperation("查询订单管理数据")
    public Result<OrderOverViewVO> orderOverView(){
        return Result.success(workspaceService.getOrderOverView());
    }

    /**
     * 查询菜品总览
     * @return
     */
    @GetMapping("/overviewDishes")
    @ApiOperation("查询菜品总览")
    public Result<DishOverViewVO> dishOverView(){
        return Result.success(workspaceService.getDishOverView());
    }

    /**
     * 查询套餐总览
     * @return
     */
    @GetMapping("/overviewSetmeals")
    @ApiOperation("查询套餐总览")
    public Result<SetmealOverViewVO> setmealOverView(){
        return Result.success(workspaceService.getSetmealOverView());
    }
}
```



#### 1.2.2 Service层接口

**添加WorkspaceService.java**

```java
package com.sky.service;

import com.sky.vo.BusinessDataVO;
import com.sky.vo.DishOverViewVO;
import com.sky.vo.OrderOverViewVO;
import com.sky.vo.SetmealOverViewVO;
import java.time.LocalDateTime;

public interface WorkspaceService {

    /**
     * 根据时间段统计营业数据
     * @param begin
     * @param end
     * @return
     */
    BusinessDataVO getBusinessData(LocalDateTime begin, LocalDateTime end);

    /**
     * 查询订单管理数据
     * @return
     */
    OrderOverViewVO getOrderOverView();

    /**
     * 查询菜品总览
     * @return
     */
    DishOverViewVO getDishOverView();

    /**
     * 查询套餐总览
     * @return
     */
    SetmealOverViewVO getSetmealOverView();

}
```



#### 1.2.3 Service层实现类

**添加WorkspaceServiceImpl.java**

```java
package com.sky.service.impl;

import com.sky.constant.StatusConstant;
import com.sky.entity.Orders;
import com.sky.mapper.DishMapper;
import com.sky.mapper.OrderMapper;
import com.sky.mapper.SetmealMapper;
import com.sky.mapper.UserMapper;
import com.sky.service.WorkspaceService;
import com.sky.vo.BusinessDataVO;
import com.sky.vo.DishOverViewVO;
import com.sky.vo.OrderOverViewVO;
import com.sky.vo.SetmealOverViewVO;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.util.HashMap;
import java.util.Map;

@Service
@Slf4j
public class WorkspaceServiceImpl implements WorkspaceService {

    @Autowired
    private OrderMapper orderMapper;
    @Autowired
    private UserMapper userMapper;
    @Autowired
    private DishMapper dishMapper;
    @Autowired
    private SetmealMapper setmealMapper;

    /**
     * 根据时间段统计营业数据
     * @param begin
     * @param end
     * @return
     */
    public BusinessDataVO getBusinessData(LocalDateTime begin, LocalDateTime end) {
        /**
         * 营业额：当日已完成订单的总金额
         * 有效订单：当日已完成订单的数量
         * 订单完成率：有效订单数 / 总订单数
         * 平均客单价：营业额 / 有效订单数
         * 新增用户：当日新增用户的数量
         */

        Map map = new HashMap();
        map.put("begin",begin);
        map.put("end",end);

        //查询总订单数
        Integer totalOrderCount = orderMapper.countByMap(map);

        map.put("status", Orders.COMPLETED);
        //营业额
        Double turnover = orderMapper.sumByMap(map);
        turnover = turnover == null? 0.0 : turnover;

        //有效订单数
        Integer validOrderCount = orderMapper.countByMap(map);

        Double unitPrice = 0.0;

        Double orderCompletionRate = 0.0;
        if(totalOrderCount != 0 && validOrderCount != 0){
            //订单完成率
            orderCompletionRate = validOrderCount.doubleValue() / totalOrderCount;
            //平均客单价
            unitPrice = turnover / validOrderCount;
        }

        //新增用户数
        Integer newUsers = userMapper.countByMap(map);

        return BusinessDataVO.builder()
                .turnover(turnover)
                .validOrderCount(validOrderCount)
                .orderCompletionRate(orderCompletionRate)
                .unitPrice(unitPrice)
                .newUsers(newUsers)
                .build();
    }


    /**
     * 查询订单管理数据
     *
     * @return
     */
    public OrderOverViewVO getOrderOverView() {
        Map map = new HashMap();
        map.put("begin", LocalDateTime.now().with(LocalTime.MIN));
        map.put("status", Orders.TO_BE_CONFIRMED);

        //待接单
        Integer waitingOrders = orderMapper.countByMap(map);

        //待派送
        map.put("status", Orders.CONFIRMED);
        Integer deliveredOrders = orderMapper.countByMap(map);

        //已完成
        map.put("status", Orders.COMPLETED);
        Integer completedOrders = orderMapper.countByMap(map);

        //已取消
        map.put("status", Orders.CANCELLED);
        Integer cancelledOrders = orderMapper.countByMap(map);

        //全部订单
        map.put("status", null);
        Integer allOrders = orderMapper.countByMap(map);

        return OrderOverViewVO.builder()
                .waitingOrders(waitingOrders)
                .deliveredOrders(deliveredOrders)
                .completedOrders(completedOrders)
                .cancelledOrders(cancelledOrders)
                .allOrders(allOrders)
                .build();
    }

    /**
     * 查询菜品总览
     *
     * @return
     */
    public DishOverViewVO getDishOverView() {
        Map map = new HashMap();
        map.put("status", StatusConstant.ENABLE);
        Integer sold = dishMapper.countByMap(map);

        map.put("status", StatusConstant.DISABLE);
        Integer discontinued = dishMapper.countByMap(map);

        return DishOverViewVO.builder()
                .sold(sold)
                .discontinued(discontinued)
                .build();
    }

    /**
     * 查询套餐总览
     *
     * @return
     */
    public SetmealOverViewVO getSetmealOverView() {
        Map map = new HashMap();
        map.put("status", StatusConstant.ENABLE);
        Integer sold = setmealMapper.countByMap(map);

        map.put("status", StatusConstant.DISABLE);
        Integer discontinued = setmealMapper.countByMap(map);

        return SetmealOverViewVO.builder()
                .sold(sold)
                .discontinued(discontinued)
                .build();
    }
}
```



#### 1.2.4 Mapper层

**在SetmealMapper中添加countByMap方法定义**

```java
	/**
     * 根据条件统计套餐数量
     * @param map
     * @return
     */
    Integer countByMap(Map map);
```

**在SetmealMapper.xml中添加对应SQL实现**

```xml
<select id="countByMap" resultType="java.lang.Integer">
        select count(id) from setmeal
        <where>
            <if test="status != null">
                and status = #{status}
            </if>
            <if test="categoryId != null">
                and category_id = #{categoryId}
            </if>
        </where>
</select>
```

**在DishMapper中添加countByMap方法定义**

```java
	/**
     * 根据条件统计菜品数量
     * @param map
     * @return
     */
    Integer countByMap(Map map);
```

**在DishMapper.xml中添加对应SQL实现**

```xml
<select id="countByMap" resultType="java.lang.Integer">
        select count(id) from dish
        <where>
            <if test="status != null">
                and status = #{status}
            </if>
            <if test="categoryId != null">
                and category_id = #{categoryId}
            </if>
        </where>
</select>
```



## 2. Apache POI

### 2.1 介绍

Apache POI 是一个处理Miscrosoft Office各种文件格式的开源项目。简单来说就是，我们可以使用 POI 在 Java 程序中对Miscrosoft Office各种文件进行读写操作。
一般情况下，POI 都是用于操作 Excel 文件。

 

**Apache POI 的应用场景：**

- 银行网银系统导出交易明细

- 各种业务系统导出Excel报表

- 批量导入业务数据 




### 2.2 入门案例

Apache POI既可以将数据写入Excel文件，也可以读取Excel文件中的数据，接下来分别进行实现。

**Apache POI的maven坐标：**(项目中已导入)

```xml
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>3.16</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>3.16</version>
</dependency>
```



#### 2.2.1 将数据写入Excel文件

```java
package com.sky.test;

import org.apache.poi.xssf.usermodel.XSSFCell;
import org.apache.poi.xssf.usermodel.XSSFRow;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;

public class POITest {

    /**
     * 基于POI向Excel文件写入数据
     * @throws Exception
     */
    public static void write() throws Exception{
        //在内存中创建一个Excel文件对象
        XSSFWorkbook excel = new XSSFWorkbook();
        //创建Sheet页
        XSSFSheet sheet = excel.createSheet("itcast");

        //在Sheet页中创建行，0表示第1行
        XSSFRow row1 = sheet.createRow(0);
        //创建单元格并在单元格中设置值，单元格编号也是从0开始，1表示第2个单元格
        row1.createCell(1).setCellValue("姓名");
        row1.createCell(2).setCellValue("城市");

        XSSFRow row2 = sheet.createRow(1);
        row2.createCell(1).setCellValue("张三");
        row2.createCell(2).setCellValue("北京");

        XSSFRow row3 = sheet.createRow(2);
        row3.createCell(1).setCellValue("李四");
        row3.createCell(2).setCellValue("上海");

        FileOutputStream out = new FileOutputStream(new File("D:\\itcast.xlsx"));
        //通过输出流将内存中的Excel文件写入到磁盘上
        excel.write(out);

        //关闭资源
        out.flush();
        out.close();
        excel.close();
    }
    public static void main(String[] args) throws Exception {
        write();
    }
}
```



#### 2.2.2 读取Excel文件中的数据

```java
package com.sky.test;

import org.apache.poi.xssf.usermodel.XSSFCell;
import org.apache.poi.xssf.usermodel.XSSFRow;
import org.apache.poi.xssf.usermodel.XSSFSheet;
import org.apache.poi.xssf.usermodel.XSSFWorkbook;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileOutputStream;

public class POITest {
    /**
     * 基于POI读取Excel文件
     * @throws Exception
     */
    public static void read() throws Exception{
        FileInputStream in = new FileInputStream(new File("D:\\itcast.xlsx"));
        //通过输入流读取指定的Excel文件
        XSSFWorkbook excel = new XSSFWorkbook(in);
        //获取Excel文件的第1个Sheet页
        XSSFSheet sheet = excel.getSheetAt(0);

        //获取Sheet页中的最后一行的行号
        int lastRowNum = sheet.getLastRowNum();

        for (int i = 0; i <= lastRowNum; i++) {
            //获取Sheet页中的行
            XSSFRow titleRow = sheet.getRow(i);
            //获取行的第2个单元格
            XSSFCell cell1 = titleRow.getCell(1);
            //获取单元格中的文本内容
            String cellValue1 = cell1.getStringCellValue();
            //获取行的第3个单元格
            XSSFCell cell2 = titleRow.getCell(2);
            //获取单元格中的文本内容
            String cellValue2 = cell2.getStringCellValue();

            System.out.println(cellValue1 + " " +cellValue2);
        }

        //关闭资源
        in.close();
        excel.close();
    }

    public static void main(String[] args) throws Exception {
        read();
    }
}

```



## 3. 导出运营数据Excel报表

### 3.1 需求分析和设计

#### 3.1.1 产品原型

在数据统计页面，有一个数据导出的按钮，点击该按钮时，其实就会下载一个文件。这个文件实际上是一个Excel形式的文件，文件中主要包含最近30日运营相关的数据。表格的形式已经固定，主要由概览数据和明细数据两部分组成。真正导出这个报表之后，相对应的数字就会填充在表格中，就可以进行存档。



**业务规则：**

- 导出Excel形式的报表文件
- 导出最近30天的运营数据



#### 3.1.2 接口设计

通过上述原型图设计对应的接口。

基本信息:

​	path:   /admin/report/export

​	Method:  GET

**注意：**

- 当前接口没有传递参数，因为导出的是最近30天的运营数据，后端计算即可，所以不需要任何参数

- 当前接口没有返回数据，因为报表导出功能本质上是文件下载，服务端会通过输出流将Excel文件下载到客户端浏览器



### 3.2 代码开发

####  3.2.1 实现步骤

1). 设计Excel模板文件

2). 查询近30天的运营数据

3). 将查询到的运营数据写入模板文件

4). 通过输出流将Excel文件下载到客户端浏览器



#### 3.2.2 ReportController

```java
/**
 * 导出运营数据报表
 * @param response
 */
@GetMapping("/export")
@ApiOperation("导出运营数据报表")
public void export(HttpServletResponse response) {
    reportService.exportBusinessData(response);
}
```



#### 3.2.3 ReportServiceImpl

```java
/**
 * 导出运营数据报表
 * @param response
 */
@Override
public void exportBusinessData(HttpServletResponse response) {
    // 1. 查询数据库, 获取营业数据 -- 查询最近30天的营业数据
    LocalDate dateBegin = LocalDate.now().minusDays(30);
    LocalDate dateEnd = LocalDate.now().minusDays(1);

    // 查询概览数据
    BusinessDataVO businessDataVO = workspaceService
            .getBusinessData(LocalDateTime.of(dateBegin, LocalTime.MIN), LocalDateTime.of(dateEnd, LocalTime.MAX));

    // 2. 通过POI将数据写入到Excel文件中
    InputStream in = this.getClass().getClassLoader().getResourceAsStream("template/运营数据报表模板.xlsx");

    try {
        // 基于模板文件创建一个新的Excel文件
        XSSFWorkbook excel = new XSSFWorkbook(in);

        // 获取表格文件的Sheet页
        XSSFSheet sheet = excel.getSheet("Sheet1");

        // 填充数据--时间
        sheet.getRow(1).getCell(1).setCellValue("时间: " + dateBegin + "至" + dateEnd);

        // 获得第四行
        XSSFRow row = sheet.getRow(3);
        row.getCell(2).setCellValue(businessDataVO.getTurnover());
        row.getCell(4).setCellValue(businessDataVO.getOrderCompletionRate());
        row.getCell(6).setCellValue(businessDataVO.getNewUsers());

        // 获得第五行
        row = sheet.getRow(4);
        row.getCell(2).setCellValue(businessDataVO.getValidOrderCount());
        row.getCell(4).setCellValue(businessDataVO.getUnitPrice());

        // 填充明细数据
        for (int i = 0; i < 30; i++) {
            LocalDate date = dateBegin.plusDays(i);
            // 查询某一天的营业数据
            BusinessDataVO businessData = workspaceService
                    .getBusinessData(LocalDateTime.of(date, LocalTime.MIN), LocalDateTime.of(date, LocalTime.MAX));

            // 获得某一行
            row = sheet.getRow(7 + i);
            row.getCell(1).setCellValue(date.toString());
            row.getCell(2).setCellValue(businessData.getTurnover());
            row.getCell(3).setCellValue(businessData.getValidOrderCount());
            row.getCell(4).setCellValue(businessData.getOrderCompletionRate());
            row.getCell(5).setCellValue(businessData.getUnitPrice());
            row.getCell(6).setCellValue(businessData.getNewUsers());
        }

        // 3. 通过输出流将Excel文件下载到客户端浏览器
        ServletOutputStream out = response.getOutputStream();
        excel.write(out);

        // 关闭资源
        out.close();
        excel.close();
    } catch (IOException e) {
        e.printStackTrace();
    }

}
```