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

application/json

{    

​	"id": 0,

​	"idNumber": "string",    

​	"name": "string",

​    "phone": "string", 

   "sex": "string",

​    "username": "string" 

}

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

业务规则:

​	1.根据页码展示员工信息

​	2.每页展示10条数据

​	3.分页查询时可以根据需要,输入员工姓名进行查询

Query 参数:name, page, pageSize

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

业务规则:

​	1.可以对状态为"启用"的员工账号进行"禁用"操作

​	2.可以对状态为"禁用"的员工账号进行"启用"操作

​	3.状态为"禁用"的员工账号不能登陆系统

请求参数

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

业务规则:

​	1.分类名称必须是唯一的

​	2.分类按照类型可以分为菜品分类和套餐分类

​	3.新添加的分类状态为默认为"禁用"

```sql
CREATE TABLE `category` (
  `id` bigint NOT NULL AUTO_INCREMENT COMMENT '主键',
  `type` int DEFAULT NULL COMMENT '类型   1 菜品分类 2 套餐分类',
  `name` varchar(32) CHARACTER SET utf8mb3 COLLATE utf8mb3_bin NOT NULL COMMENT '分类名称',
  `sort` int NOT NULL DEFAULT '0' COMMENT '顺序',
  `status` int DEFAULT NULL COMMENT '分类状态 0:禁用，1:启用',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间',
  `update_time` datetime DEFAULT NULL COMMENT '更新时间',
  `create_user` bigint DEFAULT NULL COMMENT '创建人',
  `update_user` bigint DEFAULT NULL COMMENT '修改人',
```

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

## 3.菜品分页查询

## 4.删除菜品

## 5.修改菜品

