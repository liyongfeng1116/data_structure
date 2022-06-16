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
