# Spring
开源免费容器  
轻量级，非入侵式的  
控制反转（IOC），面向切面编程（AOP）  
支持事务处理   
Spring boot是起点框架
## 控制反转IOC
- 之前控制是由程序写死的，service层去调dao层
- 之前的是业务层去选择执行那种方式，现在将选择全交给用户
- 将控制权交给了用户，用户调动程序，程序被动接受
- 程序员较少关心对象的创建，系统的耦合性大大降低
- 对象由spring创建、管理、装配
**控制反转是一种通过描述（XML或注解）并通过第三方去生产或获取特定对象的方式。在Spring中实现控制反转的是IOC容器，其实现方式是注入依赖。**
## IOC创建对象的方式
- 使用无参构造对象，默认方式
```
<bean id="user" class="com.lyf.pojo.User">
    <property name="name" value="李永锋"/>
</bean>
```
- 假设要使用有参构建方法
  - 下标index赋值
    ```
    <bean id="user" class="com.lyf.pojo.User">
        <constructor-arg index="0" value="Liyongfeng"/>
        <!--<constructor-arg type="java.lang.String" value="李永锋"/>-->
    </bean>
    ```
  - 类型赋值
  - 直接通过参数命名
    ```<constructor-arg name="name" value="liyongfeng"/>```
配置文件加载（beans.xml）时，对象就被创建了
## spring配置
- 别名 ```<alias name="user" alias="userlyf"/>```
- Bean的配置
  ```
  <!--
        id 对象唯一标识符
        class bean对象的完全限定类名，包名+类型
        name： 别名，可以取多个名
    -->
    <bean id="user" class="com.lyf.pojo.User" name="userlyf，u2">
        <!--<constructor-arg index="0" value="Liyongfeng"/>-->
        <!--<constructor-arg type="java.lang.String" value="李永锋"/>-->
        <constructor-arg name="name" value="liyongfeng"/>
    </bean>
  ```
- import 多个bean合并为一个bean
## 依赖注入
- 构造器注入
- set 方式注入【重点】
  - 依赖：bean对象的创建依赖容器
  - 注入：bean对象中的属性由容器注入
  ```
  <bean id="student" class="com.lyf.pojo.Student">
        <!--1.普通值注入 value-->
        <property name="name" value="lyf"/>

        <!--2. bean注入 ref-->
        <property name="address" ref="address"/>

        <!--3. 数组注入 array-->
        <property name="books">
            <array>
                <value>罪与罚</value>
                <value>白鹿原</value>
                <value>红楼梦</value>
            </array>
        </property>

        <!--4. list注入-->
        <property name="hobbies">
            <list>
                <value>听歌</value>
                <value>看电影</value>
                <value>打篮球</value>
            </list>
        </property>

        <!--5. map-->
        <property name="card">
            <map>
                <entry key="身份证" value="62052319991199494"/>
                <entry key="护照号" value="324700475"/>
            </map>
        </property>

        <!--6. set-->
        <property name="games">
            <set>
                <value>cs</value>
                <value>lol</value>
            </set>
        </property>

        <!--7. null-->
        <property name="wife">
            <null></null>
        </property>

        <!--8. Property-->
        <property name="info">
            <props>
                <prop key="学号">2015213478</prop>
                <prop key="性别">男</prop>
            </props>
        </property>
    </bean>
  ```
- 其他方式注入
    - p命名空间
    - c命名空间
    ```
    <?xml version="1.0" encoding="GBK"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:p="http://www.springframework.org/schema/p"
            xmlns:c="http://www.springframework.org/schema/c"
            xsi:schemaLocation="http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd">

        <!--p命名空间注入，可以直接注入 property-->
        <bean id="user" class="com.lyf.pojo.User" p:name="lyf" p:age="25"/>

        <!--c命名空间注入，可以通过构造器注入 construct-args-->
        <bean id="user2" class="com.lyf.pojo.User" c:name="lyf" c:age="24" />
    </beans>
    ```
## bean的作用域
- 单例模式```<bean id="user2" class="com.lyf.pojo.User" c:name="24" c:age="24" scope="singleton" />``` 每次产生的对象相同
- 原型模式```<bean id="user2" class="com.lyf.pojo.User" c:name="24" c:age="24" scope="prototype" />``` 每次从容器中拿出来的对象都是新的
- request session application用在web开发中使用
## bean的自动装配
- 自动装配是指Spring满足bean依赖的一种方式
- Spring会在上下文中自动寻找，并自动给bean装配属性。
- 三种装配方式方式
    - 在xml中显式配置
    - 在java中显式配置
    - 隐式的自动装配【重要】
```
<bean id="cat" class="com.lyf.pojo.Cat"/>
    <bean id="dog" class="com.lyf.pojo.Dog"/>
    <!--
    byName:会自动在上下文中查找，和自己对象的set方法后面的值对应，id唯一
    byType:自动在上下文查找，和自己对象属性类型相同的bean,class唯一
    -->
    <bean id="people" class="com.lyf.pojo.People" autowire="byType">
        <property name="name" value="lyf"/>
    </bean>
```
## 使用注解自动装配
1. 导入约束
```
<?xml version="1.0" encoding="GBK"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>
</beans>
```
2. @Autowired:在属性上添加
```
public class People {
    @Autowired
    private Cat cat;
    @Autowired
    private Dog dog;
    private String name;

    public People() {
    }
```
3. @Resource    
两者的区别：
- 自动装配
- @Autowired通过byname的方式实现，而且必须要求对象存在
- @Resource默认通过byname的方式实现，如果找不到名字，则通过bytype实现。
## 使用注解开发
aop包  
使用注解需要导入context约束
```
<?xml version="1.0" encoding="GBK"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        https://www.springframework.org/schema/context/spring-context.xsd">

    <context:annotation-config/>

</beans>
```
- 属性的注入
```
@Component
public class User {
    
    public String name;
    @Value("lyf")
    public void setName(String name) {
        this.name = name;
    }
}
```
- 衍生的注解
@Component有几个衍生注解，开发中按照三层架构。
    1. dao【@Repository】
    2. service【@Service】
    3. controller【@Controller】
四个注解功能一样，都是将某个类注册到spring容器中
- 作用区间   
@Scope("") 单例模式singleton，原型模式prototype
- xml与注解
    1. xml更加万能，适用于任何场合
    2. 注解不是自己类使用不了维护复杂。
- xml用来管理bean，注解完成属性注入。
为了使注解生效：    
```
<!--指定要扫描的包，包下的注解就会生效-->
    <context:component-scan base-package="com.lyf.pojo"/>
    <context:annotation-config/>
```
## 使用java的方式配置spring
- 实体类
```
package com.lyf.pojo;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

@Component
public class User {
    private String name;

    public String getName() {
        return name;
    }
    @Value("lyf")
    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                '}';
    }
}
```
- 配置类
```
package com.lyf.config;

import com.lyf.pojo.User;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
//这个也会被spring容器托管，注册到容器中，因为本身就是一个component；
//@Configuration代表是配置类，相当于beans.xml
@Configuration
@ComponentScan("com.lyf.pojo")
public class lyfconfig {
    /*
      注册一个bean，相当于之前写的一个标签
      方法的名字相当于bean标签的id属性
      方法的返回值，相当于bean标签中的class属性
     */
    @Bean
    public User getUser() {
        return new User(); // 返回要注入的对象
    }
}
```
- 测试类
```
import com.lyf.config.lyfconfig;
import com.lyf.pojo.User;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

public class MyTest {
    public static void main(String[] args) {
        // 通过AnnotationConfigApplicationContext获取容器
        ApplicationContext context = new AnnotationConfigApplicationContext(lyfconfig.class);
        User user = (User)context.getBean("user");
        System.out.println(user.getName());
    }
}
```
## 代理模式
为什么学习代理模式？ 是SpringAOP的底层 【SpringAOP和SpringMVC】   
代理模式的分类：   
真实的人 ——> 代理角色 ——> 真实角色
租房的人 ——>   中介   ——> 房东   
代理得好处    
- 可以使真实角色得操作更加纯粹，不用关心公共得业务；
- 公共业务交给代理角色，实现业务的分工
- 公共业务发生扩展，方便集中管理    
  
代理的缺点：   
一个真实校色就会产生一个代理角色   
代理步骤：
  1. 接口
   ```
   // 租房
public interface Rent {
    public void rent();
}
   ```
  2. 真实角色
```
// 房东
public class Host implements Rent  {
    public void rent() {
        System.out.println("房东要出租房子");
    }
}
```
  3. 代理角色
```
public class Proxy {
    private Host host;
    public Proxy(Host host) {
        this.host = host;
    }

    public void rent() {
        seeHouse();
        host.rent();
        hetong();
        fare();
    }

    // 看房
    public void seeHouse() {
        System.out.println("中介带你看房");
    }
    public void hetong() {
        System.out.println("签租赁合同");
    }
    // 收中介费
    public void fare() {
        System.out.println("中介带你看房");
    }
}
```
  4. 客户端访问代理角色
```
public class Client {
    public static void main(String[] args) {
        // 房东要租房子
        Host host = new Host();
        // 代理，中介帮房东租房子，代理有附属操作
        Proxy proxy = new Proxy(host);
        // 不面对房东，直接找中介
        proxy.rent();


    }
}
```
- 静态代理
  1. 抽象角色：一般使用接口或者抽象类来解决
  2. 真实角色：被代理的角色
  3. 代理角色：代理真实角色，代理真实角色后，我们一般会做一些附属操作
  4. 客户：访问代理对象的人
- 动态代理(利用反射)
  1. 动态代理和静态代理角色一样
  2. 动态代理类是动态生成的，不是我们直接写好的！
  3. 动态代理分为两大类：基于接口的动态代理，基于类的动态代理
    - 基于接口-JDK动态代理
    - 基于类：cglib
    - java字节码实现：javasist
  需要了解两个类：Proxy 代理，InvocationHandler 调用处理程序;

```
//抽象角色
public interface UserService {
    public void add();
    public void delete();
    public void update();
    public void query();
}
```
```
//真实对象
public class UserServiceImpl implements UserService {

    @Override
    public void add() {
        System.out.println("增加了一个用户");
    }

    @Override
    public void delete() {
        System.out.println("删除了一个用户");
    }

    @Override
    public void update() {
        System.out.println("修改了一个用户");
    }

    @Override
    public void query() {
        System.out.println("查询了一个用户");
    }
}
```
```
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

// 中介类
public class ProxyInvocationHandler implements InvocationHandler {
    // 目标类的接口
    private Object target;

    public void setTarget(Object target) {
        this.target = target;
    }
    // 生成中介
    public Object getProxy() {
        return Proxy.newProxyInstance(this.getClass().getClassLoader(),
                target.getClass().getInterfaces(),this);
    }

    // 用中介调用实际的方法
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        log(method.getName());
        Object result = method.invoke(target,args);
        return result;
    }

    public void log(String msg) {
        System.out.println("执行了" + msg + "方法");
    }
}
```
```
public class Client {
    public static void main(String[] args) {
        // 创建真实对象
        UserServiceImpl userService = new UserServiceImpl();
        // 中介
        ProxyInvocationHandler pih = new ProxyInvocationHandler();

        // 中介设置要代理的对象
        pih.setTarget(userService);

        // 中介操作
        UserService proxy = (UserService) pih.getProxy();

        proxy.add();
    }
}
```
## AOP(面向切面编程)
AOP(Aspect-Oriented Programming), 即 面向切面编程, 它与 OOP( Object-Oriented Programming, 面向对象编程) 相辅相成, 提供了与 OOP 不同的抽象软件结构的视角. 在 OOP 中, 我们以类(class)作为我们的基本单元, 而 AOP 中的基本单元是 Aspect(切面)
```
//使用前导包
<dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.2</version>
        </dependency>
```
#### 方式一：使用Spring的API接口[主要SpringAPI接口实现]
```
<!--方式一：使用原生Spring API接口-->
    <!--配置aop,导入aop的约束-->
    <aop:config>
        <!--切入点：expression：表达式execution(要执行的位置)-->
        <aop:pointcut id="pointcut" expression="execution(* com.lyf.service.UserServiceImpl.*(..))"/>
        <!--执行环绕-->
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
        <aop:advisor advice-ref="log" pointcut-ref="pointcut"/>
    </aop:config>
```
#### 方式二：自定义实现AOP[主要是切面定义]]
```
<!--方式二：自定义类-->
    <bean id="diy" class="com.lyf.diy.DiyPointCut"/>
    <aop:config>
        <!--自定义切面，ref要引用的类-->
        <aop:aspect ref="diy">
            <!--切入点-->
            <aop:pointcut id="point" expression="execution(* com.lyf.service.UserServiceImpl.*(..))"/>
            <!--通知-->
            <aop:before method="before" pointcut-ref="point"/>
            <aop:after method="after" pointcut-ref="point"/>
        </aop:aspect>
    </aop:config>
```
### 方式三：使用注解实现！

## 整合MyBatis
### 步骤
1. 导入相关jar包
   - junit
   - mybatis
   - mysql数据库
   - spring相关
   - aop植入
   - mybatis-spring
2. 编写配置文件
3. 测试
#### 回忆mybatis
1. 编写实体类
2. 编写核心配置文件
3. 编写接口
4. 编写Mapper.xml
5. 测试
#### mybatis-spring
#### 申明式事务
把一组业务当成一个业务来做，要么都成功要么都失败
# SpringMvc
## SpringMvc的执行流程
### MVC
- model(dao,service)  view(JSP)  controller(servlet)
- 业务逻辑，数据，显示分离
- 
# SpringBoot
## 微服务架构

