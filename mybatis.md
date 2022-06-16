# Mybatis
1. 持久层框架。
2. 将数据存到数据库。
3. sql和代码分离
## mybatis程序
搭建环境———》导入mybatis ————》编写代码————》测试
## nybatis对应文件
1. mybatis-config.xml对应数据库配置，每个dao层的UserMapper都必须在mybatis-config.xml中注册
2. MybatisUtils工具类，从SqlSwssionFactory中获取SqlSession对象
3. dao层没有变化，是一个接口UserMapper，而接口实现类变成了xml配置文件（绑定UserMapper，写sql代码）
### 搭建环境
1. 数据库搭建
```
CREATE TABLE `user`(
`id` INT(20) NOT NULL PRIMARY KEY,
`name` VARCHAR(30) DEFAULT NULL,
`pwd` VARCHAR(30) DEFAULT NULL
)ENGINE=INNODB DEFAULT CHARSET=utf8;

INSERT INTO `user`(`id`,`name`,`pwd`) VALUES 
(1,'李永锋','123456'),
(2,'张倩','123456'),
(3,'李四','123456')
```
2. 启动数据库 net start mysql
2. 新建一个普通的maven项目
3. 删除src
4. 导入maven依赖，此时是在父工程下的pom.xml
```
<!--导入依赖-->
    <dependencies>
        <!--mysql驱动-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.49</version>
        </dependency>
        <!--mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.2</version>
        </dependency>
        <!--junit-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
```
### 创建模块
- 模块类似于一个子项目，在项目里new就行了
- 编写mybatis的核心配置文件 mybatis-config.xml
  1. 数据库连接方式
  2. 数据库驱动
  3. 用户名和密码
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<!--核心配置文件-->
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=true&amp;useUnicode=true&amp;characterEncoding=UTF-8"/>
                <property name="username" value="root"/>
                <property name="password" value="root"/>
            </dataSource>
        </environment>
    </environments>
</configuration>
```
- 编写mybatis的工具类


```
// sqlSessionFactory ---》sqlSession
public class MybatisUtils {
    
    private static SqlSessionFactory sqlSessionFactory;
    static {
        try {
            // 使用mybatis的第一步，获取sqlSessionFactory对象
            String resource = "mybatis-config.xml";
            InputStream inputStream = Resources.getResourceAsStream(resource);
            SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    //既然有了 SqlSessionFactory，顾名思义，我们可以从中获得 SqlSession 的实例。
    // SqlSession 提供了在数据库执行 SQL 命令所需的所有方法。你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。
    
    public static SqlSession getSqlSession(){
        return sqlSessionFactory.openSession();
    }
}
```
- 编写代码
1. 实体类
```
//实体类
public class User {
    private int id;
    private String name;
    private String pwd;

    public User() {
    }

    public User(int id, String name, String pwd) {
        this.id = id;
        this.name = name;
        this.pwd = pwd;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getPwd() {
        return pwd;
    }

    public void setPwd(String pwd) {
        this.pwd = pwd;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", pwd='" + pwd + '\'' +
                '}';
    }
}
```
2. Dao接口
```
public interface UserDao {
    List<User> getUserList();
}
```
3. 接口实现类由原来的Impl转为xml文件
```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--namespace绑定一个固定的mapper接口-->
<mapper namespace="com.lyf.dao.UserDao">

    <select id="getUserList" resultType="com.lyf.pojo.User">
        select * from mybatis.user;
    </select>

</mapper>
```
4. 测试
- 测试的包和开发的包一一对应
MapperRegistry，每一个Mapper.xml文件都需要在mybatis-config.xml中注册
```
//注意点：绑定异常
org.apache.ibatis.binding.BindingException: Type interface com.lyf.dao.UserDao is not known to the MapperRegistry.
```

约定大于配置
手动配置资源过滤
```
public class UserDaoTest {
    @Test
    public void test(){
        // 获取SqlSession对象
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        //方式一： 执行SQL
        UserDao mapper = sqlSession.getMapper(UserDao.class);

        List<User> userList = mapper.getUserList();

        for (User user : userList) {
            System.out.println(user);
        }

        // 关闭
        sqlSession.close();
    }
}
```
可能出现的问题：
1. 配置文件没注册
2. 绑定接口错误
3. 方法名不对，
4. 返回类型不对
5. Maven导出资源问题
### CURD
1. 查询    

    ```
    <select id="getUserList" resultType="com.lyf.pojo.User">
        select * from mybatis.user
    </select>
    ```
    id 为对应的namespace中的方法名   
    resultType为sql语句的返回值   
    parameterType 参数类型

2. 增删改需要提交事务
    ```
    <select id="getUserList" resultType="com.lyf.pojo.User">
        select * from mybatis.user
    </select>

    <select id="getUserById" resultType="com.lyf.pojo.User" parameterType="int">
        select * from mybatis.user where id = #{id}
    </select>

    <insert id="addUser" parameterType="com.lyf.pojo.User"> /*对象中的属性能直接取到*/
        insert into mybatis.user (id, name, pwd) values(#{id},#{name},#{pwd});
    </insert>

    <update id="updateUser" parameterType="com.lyf.pojo.User">
        update mybatis.user
        set name = #{name}, pwd = #{pwd}
        where id = #{id};
    </update>

    <delete id="deleteUser" parameterType="int">
        delete from mybatis.user where id = #{id};
    </delete>
    ```

    ### map和模糊查询
    1. 当实体类参数过多，更新时用Map
    ```
    //map 更新一个用户，当属性很多时
    int updateUser2(Map<String,Object> map);

     <update id="updateUser2" parameterType="map">
        update mybatis.user
        set name = #{username}
        where id = #{userid};
    </update>

     @Test
    public void testupdateUser2() {
        SqlSession sqlSession = MybatisUtils.getSqlSession();
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        HashMap<String, Object> map = new HashMap<>();
        map.put("username", "李冲");
        map.put("userid", 3);
        int res = mapper.updateUser2(map);
        if (res > 0) {
            System.out.println("修改成功");
        }
        sqlSession.commit();
        sqlSession.close();
    }
    ```
    2. 模糊查询 
    在sql中拼写时写通配符
    ```
    <select id="getUserLike" resultType="com.lyf.pojo.User">
        select * from mybatis.user where name like "%"#{value}"%"
    </select>
    ```
    3. 别名配置
    ```
    <!--引入别名，减少冗余-->
    <typeAliases>
        <!--实体类起别名-->
        <!--<typeAlias type="com.lyf.pojo.User" alias="User" />-->
        <!--扫描包-->
        <package name="com.lyf.pojo"/>
    </typeAliases>
    ```
    4. 映射器
    注册绑定mapper文件   
    方式一：使用相对于类路径的资源引用   
    ```
    <mappers>
        <mapper resource="com/lyf/dao/UserMapper.xml"/>
    </mappers>
    ```
    方式二：使用映射器接口实现类的完全限定类名,**接口和类在同一包下，且同名**  

    ```
    <mappers>
        <mapper class="com.lyf.dao.UserMapper"/>
    </mappers>
    ```
    方式三：使用包扫描  
    5. 生命周期和作用域
    生命周期和作用域是非常重要的，错误的使用会导致 ***并发问题***    
    mybatis的目的就是为了更加高效的执行数据库增删改查   
    SqlSessionFactoryBuilder
    - 用来创建SqlFactory对象，创建完不需要SqlSessionFactoryBuilder
    - 局部变量
    SqlSessionFactory
    - 相当于数据库连接池
    - SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。
    -  SqlSessionFactory 的最佳作用域是应用作用域，程序中全局唯一
    -  最简单的就是使用单例模式或者静态单例模式
    SqlSession
    - 连接到连接池的一个请求
    - 请求完需要关闭，防止资源占用
    - SqlSession 的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。
    - 每个sqlsession对应多个mapper，每个mapper对应一个sql语句  

    6. resultMap结果集映射（解决属性名和字段名（数据库中的）不一致的问题）
   
    ```
    <mapper namespace="com.lyf.dao.UserMapper">
    <!--id相当于实现了dao中的方法，resultType返回结果集-->
    
    <!--数据库里的column对应实体类的属性的resultMap-->
    <resultMap id="UserMap" type="User">
        <result column="id" property="id"/>
        <result column="name" property="name"/>
        <result column="pwd" property="password"/>
    </resultMap>

    <select id="getUserById" resultMap="UserMap" parameterType="int">
        select * from mybatis.user where id = #{id}
    </select> 
    </mapper>
    ```
## 日志
1. 日志工厂
数据库操作出现异常时使用
- SLF4J
- LOG4J
- STDOUT_LOGGING 标准日志输出 
在核心配置文件中配置
```
<settings>
        <!--标准日志工厂的实现-->
        <!--<setting name="logImpl" value="STDOUT_LOGGING"/>-->
        <setting name="logImpl" value="LOG4J2"/>
    </settings>
```
- LOG4J
  - 控制日志输出格式
  - 定义日志输出级别
  - 通过配置文件灵活配置
- LOG4J步骤
  - 导包

## 分页
为了减少数据处理量
- limit
```
语法：select * from user limit startIndex,pageSize;
select * from user limit 3; [0,3]
```
使用Mybatis实现分页，核心sql
1. 接口
   ```
   // 分页查询
    List<User> getUserByLimit(Map<String, Integer> map);
   ```
2. 实现mapper
   ```
   <!--分页-->
    <select id="getUserByLimit" parameterType="map" resultMap="UserMap">
         select * from mybatis.user limit #{startIndex}, #{pageSize}
    </select>
   ```
3. 测试
   ```
    @Test
    public void getUserByLimit(){
        SqlSession sqlSession = MybatisUtils.getSqlSession();

        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        HashMap<String,Integer> map = new HashMap<>();
        map.put("startIndex",0);
        map.put("pageSize",2);
        List<User> userList = mapper.getUserByLimit(map);
        for(User user : userList ) {
            System.out.println(user);
        }
    }
   ```

## 面向注解开发
### curd
1. 工具类创建时自动提交事务
```
public static SqlSession getSqlSession(){
        /*return sqlSession*/
        return sqlSessionFactory.openSession(true); // 自动提交
    }
```
2. 编写接口，增加注解
```
package com.lyf.dao;

import com.lyf.pojo.User;
import org.apache.ibatis.annotations.*;

import java.util.List;
import java.util.Map;

public interface UserMapper {


    @Select("select * from user where id = #{id} and name = #{name}")
    User getUserById(@Param("id") int id, @Param("name") String name);

    @Insert("insert into user (id, name, pwd) values (#{id},#{name},#{password})")
    int addUser(User user);

    @Delete("delete from user where id = #{id}")
    int deleteUserById(@Param("id") int id);

    @Update("update user set name = #{name}, pwd = #{password} where id = #{id}")
    int updateUser(User user);

}
``` 
3. 绑定接口
```
<mappers>
    <!--绑定接口,用.-->
    <mapper class="com.lyf.dao.UserMapper"/>
</mappers>
```
4. @param()注解： 基本类型参数或String需要加上，引用不需要 

## 多对一，一对多
多对一：对学生而言 ——》关联， ——》多个学生关联一个老师  association
一对多：对老师而言 ——》集合， ——》一个老师有多个学生    collection
```
CREATE TABLE IF NOT EXISTS `teacher`(
   `id` INT (10) not NULL,
   `name` VARCHAR(30) DEFAULT NULL,
   PRIMARY KEY ( `id` )
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

INSERT INTO teacher(`id`, `name`) VALUES (1, '李老师');



CREATE TABLE `student` (
	`id` INT (10) NOT NULL,
	`name` VARCHAR (30) DEFAULT NULL,
	`tid` INT (10) DEFAULT NULL,
	PRIMARY KEY (`id`),
	KEY `fktid` (`tid`),
	CONSTRAINT `fktid` FOREIGN KEY (`tid`) REFERENCES `teacher` (`id`)
)ENGINE=InnoDB DEFAULT CHARSET=utf8;

INSERT INTO student(`id`, `name`, `tid`) VALUES (1, '小明','1');
INSERT INTO student(`id`, `name`, `tid`) VALUES (2, '小红','1');
INSERT INTO student(`id`, `name`, `tid`) VALUES (3, '小里','1');
INSERT INTO student(`id`, `name`, `tid`) VALUES (4, '小白','1');
INSERT INTO student(`id`, `name`, `tid`) VALUES (5, '小东','1');
INSERT INTO student(`id`, `name`, `tid`) VALUES (6, '小青','1');
```
 ### 多对一   

 ```
 public class Student {
    private int id;
    private String name;

    // 学生要关联老师
    private Teacher teacher;
 ```
按照查询嵌套查询
```
<mapper namespace="com.lyf.dao.StudentMapper">

    <!--
    思路：
        1. 先查所有学生
        2. 根据查出的学生匹配老师
    -->
    <select id="getStudent" resultMap="StudentTeacher">
        select * from student;
    </select>

    <resultMap id="StudentTeacher" type="Student">
        <result property="id" column="id"></result>
        <result property="name" column="name"></result>
        <!--复杂属性，对象：association 集合：collection-->
        <association property="teacher" column="tid" javaType="Teacher" select="getTeacher"/>
    </resultMap>

    <select id="getTeacher" resultType="Teacher">
        select * from teacher where id = #{id}
    </select>
</mapper>
```    
按照结果嵌套查询(更简单)
```
<select id="getStudent2" resultMap="StudentTeacher2">
        select s.id sid, s.name sname, t.name tname from student s,teacher t
        where s.tid = t.id;
    </select>
    <resultMap id="StudentTeacher2" type="Student">
        <result property="id" column="sid"/>
        <result property="name" column="sname"/>
        <association property="teacher" javaType="Teacher">
            <result property="name" column="tname"/>
        </association>
    </resultMap>
```
### 一对多

```
<mapper namespace="com.lyf.dao.TeacherMapper">
    <!--按照结果嵌套查询-->
    <select id="getTeacher" resultMap="TeacherStudent">
        select s.id sid, s.name sname, t.id tid, t.name tname
        from student s, teacher t where s.tid = t.id and t.id = #{tid}
    </select>

    <resultMap id="TeacherStudent" type="Teacher">
        <result property="id" column="tid"/>
        <result property="name" column="tname"/>
        <!--
        javaType=""指定属性的类型！
        集合中的泛型信息，我们用ofType获取
        -->
        <collection property="students" ofType="Student">
            <result property="id" column="sid"/>
            <result property="name" column="sname"/>
            <result property="tid" column="tid"/>
        </collection>
    </resultMap>
</mapper>
```
JavaType 用来指定实体类中属性的类型
offType 用来指定映射到List中的pojo类型
- MySql引擎
- innoDB底层原理
- 索引
- 索引优化
## 动态sql
指根据不同的条件生成不同的sql语句
### if 
```
<select id="queryBlogIF" parameterType="map" resultType="Blog">
        select * from mybatis.blog where 1=1
        <if test="title != null"  >
            and title = #{title}
        </if>
        <if test="author != null">
            and author = #{author}
        </if>
    </select>
```
### choose(when,otherwise)
### trim(where, set)
```
<select id="queryBlogChoose" parameterType="map" resultType="Blog">
        select * from mybatis.blog
        <where>
            <choose>
                <when test="author != null">
                    author = #{author}
                </when>
                <when test="title != null">
                    and title = #{title}
                </when>
                <otherwise>
                    and views = #{views}
                </otherwise>
            </choose>
        </where>
    </select>
```

```
<update id="updateBlog" parameterType="map">
    update mybatis.blog
        <set>
            <if test="title != null">
                title = #{title},
            </if>
            <if test="author != null"></if>
                author = #{author}
        </set>
        where id = #{id}
</update>
```
### foreach
```
<!--
    select * from blog where 1=1 and (id=1 or id =2 or id = 3)
    传递的参数是map，map中是一个集合，集合中包含item
    遍历的是ids集合中的id，开头结尾指定
-->
<select id="queryBlogForeach" parameterType="map" resultType="Blog">
    select * from mybatis.blog
    <where>
        <foreach collection="ids" item="id" open="and (" close=")" separator="or">
            id = #{id}
        </foreach>
    </where>
</select>
```
## 缓存
每次查询都需要连接数据库，消耗资源   
一次查询的结果暂存在内存（下次直接取到）  
当下次查询相同数据，直接走缓存，不用走数据库   
解决高并发问题
使用缓存的条件：经常查询且不经常改变的数据   
mybatis缓存原则
    - LRU原则：最长时间不被使用的对象
    - FIFO，先进先出原则
一级缓存：sqlSession级别的
二级（全局）缓存：namespace级别的
    - setting中开启
    - 会话关闭，然后将一级缓存的结果交给二级缓存