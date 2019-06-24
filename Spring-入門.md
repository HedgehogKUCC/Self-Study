### 環境建置

![SpringFrameWorkRuntime](https://github.com/HedgehogKUCC/Self-Study/blob/master/SpringFrameworkRuntime.png)

<br>

### Spring.jar

version: 4.2.4

Download: `https://maven.springframework.org/release/org/springframework/spring/4.2.4.RELEASE/`

SpringFramework-3.0.2.RELEASE-dependencies

Download: `https://www.cnblogs.com/CreatorKou/p/9997013.html`

<br>

加入基礎四個包：

	spring-beans-4.2.4.RELEASE.jar
	spring-context-4.2.4.RELEASE.jar
	spring-core-4.2.4.RELEASE.jar
	spring-expression-4.2.4.RELEASE.jar
	
<br>

加入： (檔案在 SpringFramework-3.0.2.RELEASE-dependencies)

`org.apache.commons\com.springsource.org.apache.commons.logging\1.1.1 日誌jar`

`org.apache.log4j\com.springsource.org.apache.log4j\1.2.15`

<br>

### 創建一個對象

`User.java`

```java
package com.spring.bean;

public class User
{
	private String name;
	private Integer age;
	
	setters/getters
}
```

<br>

### 配置註冊對象至容器

任意位置 (建議: 放到 src下)

配置文件名任意 (建議: applicationContext.xml)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    
    <!-- 將 User對象 交給 spring容器 -->
    <bean name="user" class="com.spring.bean.User"></bean>
     
</beans>
```

<br>

### 程式碼測試

`Demo.java`

```java
package com.spring.test;


import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import com.spring.bean.User;

public class Demo
{
	@Test
	public void fun1()
	{
		//1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
		
		//2.向容器 "要" user對象
		User user = (User) ac.getBean("user");
		
		//3.打印 user對象
		System.out.println(user);
	}
}
```

<br>

### Spring概念

	思想
	
		IOC
			Inverse Of Control   反轉控制

			將我們創建對象的方式反轉了
			以前對象的創建是由我們開發人員自己維護，包括依賴關係也是自己注入。

			使用spring之後，對象的創建以及依賴關係可以由spring完成創建及注入。
			反轉控制就是反轉了對象的創建方式，從我們自己創見反轉給了程序(spring)。
			
		DI
			Dependency Injection   依賴注入

			實現IOC思想需要 DI做支持

			注入方式:
				set方法注入
				構造方法注入
				字段注入

			注入類型:
				值類型注入 –  8大基本數據類型
				引用類型注入 – 將依賴對象注入

<br>

	applicationContext & BeanFactory
	
		BeanFactory接口
		
			sping 原始接口，針對原始接口的實現類功能較為單一。

			BeanFactory接口實現類的容器，特點是每次在獲得對象時才會創建對象。
			
		applicationContext
		
			每次容器啟動時就會創建容器中配置的所有對象且提供更多功能。

			從類路徑下加載配置文件: ClassPathXmlApplicationContext

			從硬盤絕對路徑下加載配置文件: FileSystemXmlApplicationContext("c:/xxx/yyy/zzz")

<br>

	結論：
	
		Web開發中，使用applicationContext

		在資源匱乏的環境可以使用BeanFactory

<br>

## spring 配置詳解

<br>

### Bean元素

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/context
    http://www.springframework.org/schema/context/spring-context.xsd">
    
    <!-- 將 User對象 交給 spring容器 -->
    <!-- 
  	Bean元素: 使用該元素描述需要 spring容器管理的對象
  		class屬性: 被管理對象的完整類名
  		name屬性: 給被管理的對象起個名字，獲得對象時根據該名稱獲得對象，可以重複，可以使用特殊字符。
  		id屬性: 與 name屬性一模一樣，名稱不可重複，不能使用特殊字符。
  		
  	結論: 盡量使用 name屬性。
   	-->
    <bean name="user" class="com.spring.bean.User"></bean>
     
</beans>
```

<br>

## Bean元素進階

<br>

### scope屬性: singleton ( 預設值 )

	單例對象，被標示為單例的對象在spring容器中只會存在一個實例
	
<br>

`applicationContext.xml`

```xml
<bean name="user" class="com.spring.bean.User"></bean>
```

```xml
<bean name="user" class="com.spring.bean.User" scope="singleton" ></bean>
```

<br>

`Demo.java`

```java
@Test
	// scope: singleton 單例 
	public void fun4()
	{
		//1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/b_create/applicationContext.xml");
		
		//2.向容器 "要" user對象
		User user = (User) ac.getBean("user");
		User user2 = (User) ac.getBean("user");
		User user3 = (User) ac.getBean("user");
		User user4 = (User) ac.getBean("user");
		
		//3.打印 user對象
		System.out.println(user);
		
		// true
		System.out.println(user2 == user4);
	}
```

<br>

### scope屬性: prototype

	多例原型
	被標識為多例的對象，每次再獲得才會創建，每次創建都是新的對象。
	整合struts2時，ActionBean必須配置為多例的。

<br>

`applicationContext.xml`

```xml
<bean name="user" class="com.spring.bean.User" scope="prototype" ></bean>
```

<br>

`Demo.java`

```java
@Test
	// scope: prototype 多例 
	public void fun4()
	{
		//1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/b_create/applicationContext.xml");
		
		//2.向容器 "要" user對象
		User user = (User) ac.getBean("user");
		User user2 = (User) ac.getBean("user");
		User user3 = (User) ac.getBean("user");
		User user4 = (User) ac.getBean("user");
		
		//3.打印 user對象
		System.out.println(user);
		
		// false
		System.out.println(user2 == user4);
	}
```

<br>

### scope屬性: request

	web環境下，對象與request生命週期一樣。
	
<br>

### scope屬性: session

	web環境下，對象與session生命週期一樣。

<br>

### 生命週期

	★	配置一個方法作為生命週期初始化方法，spring會在對象創建之後立即調用。
		init-method

	★	配置一個方法作為生命週期的銷毀方法，spring容器在關閉並銷毀所有容器中的對象之前調用。
		destroy-method

<br>

`User.java`

```java
    public void init()
	{
		System.out.println("我是初始化方法！！！");
	}
	
	private void destroy()
	{
		System.out.println("我是銷毀方法！！！");
	}
```

<br>

`applicationContext.xml`

```xml
<bean name="user" class="com.spring.bean.User" 
  		init-method="init" 
  		destroy-method="destroy">
</bean>
```

<br>

`Demo.java`

```java
@Test
	// 測試生命週期方法 init()、destroy() 
	public void fun5()
	{
		//1.創建容器對象
		ClassPathXmlApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/b_create/applicationContext.xml");
		
		//2.向容器 "要" user對象
		User user = (User) ac.getBean("user");
		
		//3.打印 user對象
		System.out.println(user);
		
		//4.關閉容器，觸發銷毀方法
		ac.close();
	}
```

<br>

## spring創建對象的方式

<br>

### 測試空參構造

`User.java`

```java
public User()
{
	System.out.println("無參數的建構式");
}
```

<br>

`applicationContext.xml`

```xml
<!-- 創建方式1: 空參構造創建 -->
<bean name="user" class="com.spring.bean.User"></bean>
```

<br>

`Demo.java`

```java
@Test
	// 創建方式1: 空參構造 
	public void fun1()
	{
		//1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/b_create/applicationContext.xml");
		
		//2.向容器 "要" user對象
		User user = (User) ac.getBean("user");
		
		//3.打印 user對象
		System.out.println(user);
	}
```

<br>

### 靜態工廠 ( 瞭解 )

`UserFactory.java`

```java
package com.spring.b_create;

import com.spring.bean.User;

public class UserFactory
{
	public static User createUser()
	{
		System.out.println("靜態工廠創建 User");
		
		return new User();
	}
}
```

<br>

`applicationContext.xml`

```xml
<!-- 
  	創建方式2: 靜態工廠創建 
  	調用 UserFactory的 createUser方法 創建名為 user2的對象，放入容器
-->
  <bean name="user2" 
  		class="com.spring.b_create.UserFactory" 
  		factory-method="createUser">
  </bean>
```

<br>

`Demo.java`

```java
@Test
	// 創建方式2: 靜態工廠 
	public void fun2()
	{
		//1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/b_create/applicationContext.xml");
		
		//2.向容器 "要" user對象
		User user = (User) ac.getBean("user2");
		
		//3.打印 user對象
		System.out.println(user);
	}
```

<br>

### 實例工廠

`UserFactory.java`

```java
public User createUser2()
{
	System.out.println("實例工廠創建 User");
		
	return new User();
}
```

<br>

`applicationContext.xml`

```xml
<!-- 
  	創建方式3: 實例工廠創建 
  	調用 UserFactory對象的 createUser2方法 創建名為 user3的對象，放入容器
-->
  <bean name="user3" 
  		factory-bean="userFactory" 
  		factory-method="createUser2">
  </bean>
  
  <bean name="userFactory" 
  		class="com.spring.b_create.UserFactory">
  </bean>
```

<br>

`Demo.java`

```java
@Test
	// 創建方式3: 實例工廠 
	public void fun3()
	{
		//1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/b_create/applicationContext.xml");
		
		//2.向容器 "要" user對象
		User user = (User) ac.getBean("user3");
		
		//3.打印 user對象
		System.out.println(user);
	}
```

<br>

### spring 的分模塊配置

`applicationContext.xml`

```xml
<!-- 導入其他 spring 配置文件 -->
<import resource="com/spring/b_create/applicationContext.xml" />
```

<br>

## spring 屬性注入

/src New Package `com.spring.c_injection`

-- Package下 --

New XML File `applicationContext.xml`

New Class `Demo.java`

<br>

### 注入方式: set方法注入

`User.java`

```java
@Override
	public String toString()
	{
		return "User [name=" + name + ", age=" + age + "]";
	}
```

<br>

`applicationContex.xml`

```xml
<!-- set方式注入: -->
  <bean name="user" class="com.spring.bean.User">
  	
  	<!-- 值類型注入: 為User對象中名為 name的屬性注入 Tom作為值 -->
  	<property name="name" value="Tom"></property>
  	<property name="age" value="18"></property>
  	
  </bean>
```

<br>

`Demo.java`

```java
package com.spring.c_injection;


import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import com.spring.bean.User;

public class Demo
{
	@Test
	public void fun1()
	{
		//1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/c_injection/applicationContext.xml");
		
		//2.向容器 "要" user對象
		User user = (User) ac.getBean("user");
		
		//3.打印 user對象
		System.out.println(user);
	}
}
```

<br>

在 `com.spring.bean` New Class

`Car.java`

```java
package com.spring.bean;

public class Car
{
	private String name;
	private String color;
	
	public String getName()
	{
		return name;
	}
	public void setName(String name)
	{
		this.name = name;
	}
	public String getColor()
	{
		return color;
	}
	public void setColor(String color)
	{
		this.color = color;
	}
	@Override
	public String toString()
	{
		return "Car [name=" + name + ", color=" + color + "]";
	}
}
```

<br>

`User.java`

```java
public class User
{
	private String name;
	private Integer age;
	private Car car;
	
	setters/getters
	
	@Override
	public String toString()
	{
		return "User [name=" + name + ", age=" + age + ", car=" + car + "]";
	}
```

<br>

`applicationContex.xml`

```xml
<!-- set方式注入: -->
  <bean name="user" class="com.spring.bean.User">
  	
  	<!-- 值類型注入: 為User對象中名為 name的屬性注入 Tom作為值 -->
  	<property name="name" value="Tom"></property>
  	<property name="age" value="18"></property>
  	
  	<!-- 引用類型注入: 為car屬性注入下方配置的car對象 -->
  	<property name="car" ref="car"></property>
  	
  </bean>
  
  <!-- 將 car對象配置到容器中 -->
  <bean name="car" class="com.spring.bean.Car">
  	<property name="name" value="藍寶基尼"></property>
  	<property name="color" value="黃色"></property>
  </bean>
```

<br>

`Demo.java`

```java
@Test
	public void fun1()
	{
		//1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/c_injection/applicationContext.xml");
		
		//2.向容器 "要" user對象
		User user = (User) ac.getBean("user");
		
		//3.打印 user對象
		System.out.println(user);
	}
```

<br>

### 注入方式: 構造函數方法注入

`User.java`

```java
    public User(String name, Car car)
	{
		this.name = name;
		this.car = car;
		System.out.println("User(String name, Car car)!!!");
	}
```

<br>

`applicationContex.xml`

```xml
<!-- 構造函數注入 -->
  <bean name="user2" class="com.spring.bean.User">
  
  	<constructor-arg name="name" value="Jerry"></constructor-arg>
  	<constructor-arg name="car" ref="car"></constructor-arg>
  	
  </bean>
```

<br>

`Demo.java`

```java
@Test
	public void fun2()
	{
		// 1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/c_injection/applicationContext.xml");

		// 2.向容器 "要" user對象
		User user = (User) ac.getBean("user2");

		// 3.打印 user對象
		System.out.println(user);
	}
```

<br>

假如： 建構式 overload ( 多載 )

`User.java`

```java
    public User(String name, Car car)
	{
		this.name = name;
		this.car = car;
		System.out.println("User(String name, Car car)!!!");
	}
	
	public User(Car car, String name)
	{
		this.car = car;
		this.name = name;
		System.out.println("User(Car car, String name)!!!");
	}
```

<br>

### index屬性

`applicationContex.xml`

```xml
<!-- 構造函數注入 -->
  <bean name="user2" class="com.spring.bean.User">

  	<constructor-arg name="name" value="Jerry" index="0"></constructor-arg>
  	<constructor-arg name="car" ref="car" index="1"></constructor-arg>
  	
  </bean>
```

<br>

`Demo.java`

```java
@Test
	public void fun2()
	{
		// 1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/c_injection/applicationContext.xml");

		// 2.向容器 "要" user對象
		User user = (User) ac.getBean("user2");

		// 3.打印 user對象
		System.out.println(user);
	}
```

<br>

又假如 類型不同

`User.java`

```java
   public User(String name, Car car)
	{
		this.name = name;
		this.car = car;
		System.out.println("User(String name, Car car)!!!");
	}
	
	public User(Integer name, Car car)
	{
		this.name = name+"";
		this.car = car;
		System.out.println("User(Integer name, Car car)!!!");
	}
```

<br>

`applicationContext.xml`

```xml
<!-- 構造函數注入 -->
  <bean name="user2" class="com.spring.bean.User">
  
  	<!-- name屬性: 構造函數的參數名 -->
  	<!-- index屬性: 構造函數的參數索引 -->
  	<!-- type屬性: 構造函數的參數類型 -->
  	<constructor-arg name="name" value="999" index="0" type="java.lang.Integer"></constructor-arg>
  	<constructor-arg name="car" ref="car" index="1"></constructor-arg>
  </bean>
```

<br>

### 注入方式: p名稱空間注入

`applicationContext.xml`

```xml
   <!-- 
		p名稱空間注入 ，走 set方法
		1.導入p名稱空間 xmlns:p="http://www.springframework.org/schema/p"
		2.使用 p:屬性 完成注入
				|-值類型: p:屬性名="值"
				|-對象類型: p:屬性名-ref="bean名稱"
	-->
	<bean name="user3" class="com.spring.bean.User" 
		  p:name="jack" p:age="20" p:car-ref="car">
	</bean>
```

<br>

`Demo.java`

```java
@Test
	public void fun3()
	{
		// 1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/c_injection/applicationContext.xml");

		// 2.向容器 "要" user對象
		User user = (User) ac.getBean("user3");

		// 3.打印 user對象
		System.out.println(user);
	}
```

<br>

### 注入方式: spel 注入

`applicationContext.xml`

```xml
   <!-- 
		spel注入: spring Expression Language spring表達式語言 
	-->
	<bean name="user4" class="com.spring.bean.User">
		<property name="name" value="#{user.name}"></property>
		<property name="age" value="#{user3.age}"></property>
		<property name="car" ref="car"></property>
	</bean>
```

<br>

## 複雜類型注入

`com.spring.c_injection` New Class `CollectionBean.java`

```java
public class CollectionBean
{
	private Object[] arr;  		// 數組類型注入
	private List list;	   		// List/set 類型注入
	private Map map;	   		// map類型注入
	private Properties prop;	// properties類型注入
	
	setters/getters
	
	@Override
	public String toString()
	{
		return "CollectionBean [arr=" + Arrays.toString(arr) + ", list=" + list + ", map=" + map + ", prop=" + prop
				+ "]";
	}
```

<br>

`applicationContext.xml`

```xml
<!-- 複雜類型注入 -->
	
	<!-- array注入 -->
	<bean name="cb" class="com.spring.c_injection.CollectionBean">
	
		<!--  如果 "數組" 中只準備注入一個值(對象)，直接使用value|ref即可 -->
		<property name="arr" value="Tom"></property>
		
	</bean>
```

<br>

`Demo.java`

```java
@Test
	public void fun5()
	{
		// 1.創建容器對象
		ApplicationContext ac = new ClassPathXmlApplicationContext("com/spring/c_injection/applicationContext.xml");

		// 2.向容器 "要" user對象
		 CollectionBean cb = (CollectionBean) ac.getBean("cb");

		// 3.打印 user對象
		System.out.println(cb);
	}
```

<br>


### 複雜類型注入: 數組

`applicationContext.xml`

```xml
<!-- 複雜類型注入 -->
	
	<!-- array注入 -->
	<bean name="cb" class="com.spring.c_injection.CollectionBean">
	
		<!-- 如果數組中只準備注入一個值(對象)，直接使用value|ref即可 
		<property name="arr" value="Tom"></property>
		-->
		
		<!-- array注入，多個元素注入 -->
		<property name="arr">
			<array>
				<value>Tom</value>
				<value>Jerry</value>
				<ref bean="user4" />
			</array>
		</property>
	
	</bean>
```

<br>

### 複雜類型注入: List

`applicationContext.xml`

```xml
      <!-- 如果 "List" 中只準備注入一個值(對象)，直接使用value|ref即可 
		<property name="list" value="Jack"></property>
		-->
		
		<property name="list">
			<list>
				<value>Jack</value>
				<value>Rose</value>
				<ref bean="user3" />
			</list>
		</property>
```

<br>

### 複雜類型注入: Map

`applicationContext.xml`

```xml
      <!-- map類型注入 -->
		<property name="map">
			<map>
				<entry key="url" value="jdbc:mysql://localhost:3306/schema"></entry>
				<entry key="user" value-ref="user4"></entry>
				<entry key-ref="user3" value-ref="user2"></entry>
			</map>
		</property>
```

<br>

### 複雜類型注入: Properties

`applicationContext.xml`

```xml
       <!-- properties類型注入 -->
		<property name="prop">
			<props>
				<prop key="driverClass">com.jdbc.mysql.Driver</prop>
				<prop key="userName">root</prop>
				<prop key="password">1234567</prop>
			</props>
		</property>
```

<br>
