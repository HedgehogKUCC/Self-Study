# Mybatis 入門 (junit測試)

### MySQL

```sql
CREATE SCHEMA 'mybatis' DEFAULT CHARACTER SET utf8;
```

<br>

自行隨便填入幾筆資料

```sql
CREATE TABLE 'mybatis'.'user' (

	'id' INT NOT NULL AUTO_INCREMENT,
	'username' VARCHAR(45) NOT NULL,
	'birthday' DATE NULL,
	'sex' VARCHAR(45) NULL,
	'address' VARCHAR(45) NULL,
	
PRIMARY KEY('id') )

ENGIN = InnoDB

DEFAULT CHARACTER SET = utf8 ;
```

<br>

/src New Package

`com.mybatis.pojo` New Class `User.java`

```java
package com.mybatis.pojo;

import java.util.Date;

/**
 * 
 * <p>Title: User.java </p>
 * <p>Description: 用戶POJO </p>
 *
 * @author: kucc
 * @date: Jun 21, 2019 6:01:19 PM
 */

public class User
{
	private int id;
	private String username;
	private String sex;
	private Date birthday;
	private String address;
	
	public int getId()
	{
		return id;
	}
	public void setId(int id)
	{
		this.id = id;
	}
	public String getUsername()
	{
		return username;
	}
	public void setUsername(String username)
	{
		this.username = username;
	}
	public String getSex()
	{
		return sex;
	}
	public void setSex(String sex)
	{
		this.sex = sex;
	}
	public Date getBirthday()
	{
		return birthday;
	}
	public void setBirthday(Date birthday)
	{
		this.birthday = birthday;
	}
	public String getAddress()
	{
		return address;
	}
	public void setAddress(String address)
	{
		this.address = address;
	}
	
	@Override
	public String toString()
	{
		return "User [id=" + id + ", username=" + username + ", sex=" + sex + ", birthday=" + birthday + ", address="
				+ address + "]";
	}
}
```

<br>

### 導入 .jar

官網下載 GitHub : `https://github.com/mybatis/mybatis-3/releases`

Version : mybatis - 3.2.7

還有自行下載 `junit-4.9.jar`

<br>

### log4j.properties (公用文件)

/Java Resources New Soource Folder `config`

/config New File

`log4j.properties`

```
# Global logging configuration 開發階段請使用 DEBUG，不要使用 INFO
log4j.rootLogger=DEBUG, stdout
# Console output...
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] - %m%n
```

mybatis 默認使用 log4j 作為輸出日誌訊息

<br>

### SqlMapConfig.xml (公用文件)

通過 `SqlMapConfig.xml` 加載 mybatis 運行環境

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
	PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
	<!-- 和 spring 整合後 environments 配置將廢除 -->
	<environments default="development">
	
		<environment id="development">
		<!-- 使用 jdbc 事務管理 -->
			<transactionManager type="JDBC" />
			
			<!-- type屬性有三種取值 -->
			<!-- POOLED: 使用 Mybatis 自帶的數據庫連接池來管理數據庫連接 -->
			<!-- UNPOOLED: 不使用任何數據庫連接池來管理數據庫連接 -->
			<!-- JNDI: jndi形式使用數據庫連接、主要用於項目正常使用的時候 -->
			<dataSource type="POOLED">
				<property name="driver" value="com.mysql.cj.jdbc.Driver"/>
				<property name="url" value="jdbc:mysql://localhost:3306/mybatis?characterEncoding=utf8"/>
				<property name="username" value="root"/>
				<property name="password" value="11111111"/>
			</dataSource>
		
		</environment>
	
	</environments>
	
	<!-- 加載 mapper.xml -->
	<mappers>
		<mapper resource="sqlmap/User.xml" />
	</mappers>

</configuration>
```

<br>

### 根據 id 查詢用戶


### mapper.xml (User.xml)

建議命名規則：表名 + mapper.xml

早期 ibatis 命名規則：表名.xml

/config New Package `sqlmap` New XML

`User.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
	PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
	"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace 命名空間，為了對 sql語法進行隔離，方便管理，mapper 開發 dao 方式，使用 namespace 有特殊作用 -->
<mapper namespace="test" >
<!-- 
	在 mapper.xml 文件中配置很多的 sql語法，執行每個 sql語法時，封裝為 MappedStatement 對象 
	mapper.xml 以 statement 為單位管理 sql語法
-->	
	
	
	<!-- 根據 id 查詢用戶訊息  -->
	<!-- 
		id: 唯一標示一個 statement
		#{}: 表示一個佔位符，如果 #{ } 中傳入簡單類型的參數，#{ } 中的名稱隨意
		parameterType: 輸入參數的類型，通過 #{ } 接收 parameterType 輸入的參數
		resultType: 輸出結果類型，不管返回是多條還是單條，指定單條紀錄映射的 pojo類型
	 -->
	<select id="findUserById" parameterType="int" resultType="com.mybatis.pojo.User">
		select * from user where id= #{id}
	</select>
```

<br>

### 編碼

/src New package `com.mybatis.dao` New Class `MybatisFirst`

> 創建 SqlSessionFactory

```java
public class MybatisFirst
{
	// 會話工廠
	private SqlSessionFactory sqlSessionFactory;

	// 創建工廠
	@Before
	public void Init() throws IOException
	{
		// 配置文件 (SqlMapConfig.xml)
		String resource = "SqlMapConfig.xml";

		// 加載配置文件到輸入流
		InputStream inputStream = Resources.getResourceAsStream(resource);

		// 創建會話工廠
		sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
	}
```

<br>

> 測試

```java
// 根據 id 查詢用戶 (得到單條紀錄)
	@Test
	public void testFindUserById()
	{
		// 通過 sqlSessionFactory 創建 sqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();

		User user = null;
		try
		{
			// 通過 sqlSession 操作數據庫
			// 第一個參數: statement 的位置，等於 namespace + statement 的 id
			// 第二個參數: 傳入的參數
			user = sqlSession.selectOne("test.findUserById", 1);
		} 
		catch (Exception e)
		{
			e.printStackTrace();
		}
		finally
		{
			// 關閉 sqlSession
			sqlSession.close();
		}
		
		System.out.println(user.toString());
	}
```

<br>

### 根據用戶名稱查詢用戶訊息

根據用戶名稱***模糊***查詢用戶訊息可能返回多條紀錄

`User.xml`

```xml
	<!-- 
		根據用戶名稱查詢用戶訊息，可能返回多條。
		${ }: 表示 sql 的拼接，通過 ${ }接收參數，將參數的內容不加任何修飾拼接在 sql中。
	-->
	<select id="findUserByName" parameterType="java.lang.String" resultType="com.mybatis.pojo.User">
		select * from user where username like '%${value}%'
	</select>
```

<br>

```java
	// 根據 用戶名稱 查詢 (得到多條紀錄)
	@Test
	public void testFindUserByName()
	{
		// 通過 sqlSessionFactory 創建 sqlSession
		SqlSession sqlSession = sqlSessionFactory.openSession();

		List<User> list = null;
		try
		{
			// 通過 sqlSession 操作數據庫
			// 第一個參數: statement 的位置，等於 namespace + statement 的 id
			// 第二個參數: 傳入的參數
			list = sqlSession.selectList("test.findUserByName", "小明");
		} 
		catch (Exception e)
		{
			e.printStackTrace();
		}
		finally
		{
			// 關閉 sqlSession
			sqlSession.close();
		}
			
			System.out.println(list);
			System.out.println(list.get(0).getUsername());
		}
```

<br>

### 用戶新增

向用戶表新增一條紀錄

`User.xml`

```xml
	<!-- 新增用戶 
		parameterType: 輸入參數的類型，User對象 包括 username, birthday, sex, address
		#{ }接收 pojo數據，可以使用 OGNL 解析出 pojo的屬性值
		${username} 表示從 parameterType中獲取 pojo的屬性值
	-->
	<insert id="insertUser" parameterType="com.mybatis.pojo.User">
		insert into user(username, birthday, sex, address) values(#{username},#{birthday},#{sex},#{address})
	</insert>
```

<br>

```java
		// 新增用戶
		@Test
		public void testInsertUser()
		{
			// 通過 sqlSessionFactory 創建 sqlSession
			SqlSession sqlSession = sqlSessionFactory.openSession();

			//創建新增數據對象
			User user = new User();
			user.setUsername("浪子燕青");
			user.setBirthday(new Date());
			user.setSex("1");
			user.setAddress("河南鄭州");
					
			// 通過 sqlSession 操作數據庫
			try
			{
				sqlSession.insert("test.insertUser", user);
						
				//需要 commit event
				sqlSession.commit();
			} 
			catch (Exception e)
			{
				e.printStackTrace();
			}
			finally
			{
				// 關閉 sqlSession
				sqlSession.close();
			}
			
			System.out.println("用戶的id="+user.getId());
		}
```

<br>

### 主鍵返回

	需求：
	user對象新增到數據庫後，新紀錄的主鍵要通過user對象返回，通過user獲取主鍵。

	解決思路：
	通過 LAST_INSERT_ID() 獲取剛新增紀錄的自增主鍵值
	在insert語句執行後
	執行select LAST_INSERT_ID() 就可以獲取自增主鍵
	
`User.xml`

```xml
	<!-- 新增用戶 
		parameterType: 輸入參數的類型，User對象 包括 username, birthday, sex, address
		#{ }接收 pojo數據，可以使用 OGNL 解析出 pojo的屬性值
		${username} 表示從 parameterType中獲取 pojo的屬性值
		selectKey: 用於進行主鍵返回，定義了獲取主鍵值的 sql
		order: 設置 selectKey中 sql執行的順序，相對於 insert語句來說
		keyProperty: 將主鍵值設置到哪個屬性
		resultType: select LAST_INSERT_ID()的結果類型
	-->
	<insert id="insertUser" parameterType="com.mybatis.pojo.User">
		
		<selectKey keyProperty="id" order="AFTER" resultType="int">
			select LAST_INSERT_ID()
		</selectKey>
		
		insert into user(username, birthday, sex, address) values(#{username},#{birthday},#{sex},#{address})
	</insert>
```

<br>

### 使用 mysql 的 uuid 機制生成主鍵

	使用 uuid 生成主鍵的好處：
	可以不用考慮數據庫移植後主鍵衝突問題
	
	實現思路：
	先查詢 uuid 得到主鍵，將主鍵設置到 user對象中，將 user對象新增數據庫

`User.xml`

```xml
	<!-- mysql 的 uuid 生成主鍵 -->
	<insert id="insertUser" parameterType="com.mybatis.pojo.User">
		
		<selectKey keyProperty="id" order="BEFORE" resultType="String">
			select uuid() 
		</selectKey>
		
		insert into user(id,username, birthday, sex, address) values(#{id},#{username},#{birthday},#{sex},#{address})
	</insert>
```

<br>

### 實現 oracle 數據庫主鍵返回，如何做？？

	Oracle 沒有自增主鍵機制，使用序列完成主鍵生成
	
	實現思路：
	先查詢序列得到主鍵，將主鍵設置到 user對象中，將 user對象新增數據庫
	
`User.xml`

```xml
	<!-- oracle 
		在執行 insert 之前執行 select 序列.nextval() from dual 取出序列最大值，將值設置到 user 對象的 id 屬性
	-->
	<insert id="insertUser" parameterType="com.mybatis.pojo.User">
		
		<selectKey keyProperty="id" order="BEFORE" resultType="int">
			select 序列.nextval() from dual 
		</selectKey>
		
		insert into user(id,username, birthday, sex, address) values(#{id},#{username},#{birthday},#{sex},#{address})
	</insert>
```

<br>

### 用戶刪除和更新

`User.xml`

```xml
	<!-- 用戶刪除 -->
	<delete id="deleteUser" parameterType="int">
		delete from user where id=#{id}
	</delete>
	
	<!-- 用戶更新 
		要求: 傳入的 user 對象中包括 id 屬性值
	-->
	<update id="updateUser" parameterType="com.mybatis.pojo.User">
		update user set username=#{username}, birthday=#{birthday}, sex=#{sex}, address=#{address} where id=#{id}
	</update>
```

<br>

```java
		// 根據 id 刪除用戶
		@Test
		public void testDeleteUser()
		{
			// 通過 sqlSessionFactory 創建 sqlSession
			SqlSession sqlSession = sqlSessionFactory.openSession();
							
			// 通過 sqlSession 操作數據庫
			try
			{
				sqlSession.delete("test.deleteUser", 8);
								
				//需要 commit event
				sqlSession.commit();
			} 
			catch (Exception e)
			{
				e.printStackTrace();
			}
			finally
			{
				// 關閉 sqlSession
				sqlSession.close();
			}		
		}
```

<br>

```java
		// 根據 id 更新用戶
		@Test
		public void testUpdatetUser()
		{
			// 通過 sqlSessionFactory 創建 sqlSession
			SqlSession sqlSession = sqlSessionFactory.openSession();

			//創建更新數據對象，要求必須包括 id
			User user = new User();
			user.setId(8);
			user.setUsername("燕青");
			user.setBirthday(new Date());
			user.setSex("1");
			user.setAddress("河南鄭州");
							
			// 通過 sqlSession 操作數據庫
			try
			{
				sqlSession.update("test.updateUser", user);
								
				//需要 commit event
				sqlSession.commit();
			} 
			catch (Exception e)
			{
				e.printStackTrace();
			}
			finally
			{
				// 關閉 sqlSession
				sqlSession.close();
			}
			System.out.println(user.toString());
		}
```

<br>

# Mybatis 開發過程小結

> 編寫 `SqlMapConfig.xml`
> 
> 編寫 `mapper.xml`
>> 定義了 ***statement***
>
> 編程通過配置文件創建 `SqlSessionFactory`
> 
> 通過 `SqlSessionFactory` 獲取 `SqlSession`
> 
> 通過 `SqlSession` 操作數據庫
>> 如果執行增加、更新、刪除，需要呼叫 `SqlSession.commit()`
>
> `SqlSession` 使用完成要關閉

<br>

# Mybatis 與 Hibernate 重要區別

	企業開發進行技術選擇，考慮mybatis與hibernate適用場景。

	Mybatis : 
			入門簡單，程式人員容易上手開發，節省開發成本。
			mybatis 需要程式人員自己編寫sql語句，是一個不完全的ORM框架，對sql修改和優化非常容易實現。
			mybatis適合開發需求變更頻繁的系統，例如: 互聯網項目。

	Hibernate : 
			入門門檻高，如果用 hibernate 寫出高性能的程序不容易實現。
			hibernate不用寫sql語句，是一個ORM框架(對象關係映射)。
			hibernate適合需求固定，對象數據模型穩定，中小型項目
			例如: 企業OA系統。

	總之，企業在技術選型時根據項目實際情況，以降低成本和提高系統可維護性為出發點進行技術選型。

<br>

# 總結

`#{ }`

	表示一個佔位符，向佔位符輸入參數，mybatis 自動進行 java 類型 和 jdbc 類型的轉換。
	程式人員不需要考慮參數的類型，例如: 傳入字符串，mybatis 最終拼接好的 sql 就是參數兩邊加單引號。

	#{ }接收 pojo數據，可以使用 OGNL 解析出 pojo的屬性值

<br>

`${ }`

	表示 sql 的拼接，通過${ }接收參數，將參數的內容不加任何修飾拼接在sql中
	${ }接收 pojo數據，可以使用 OGNL 解析出 pojo的屬性值

	缺點: 不能防止 sql 注入。
	select * from mybatis.user where username = '小明' or '1=1';
	建議使用 preparedStatement 預編譯

<br>
	
`selectOne`

	用於查詢單條紀錄，不能用於查詢多條紀錄，否則拋異常
	org.apache.ibatis.exceptions.TooManyResultsException: Expected one result (or null) to be returned by selectOne(), but found: 4

<br>

# Mybatis 解決 jdbc 編程的問題

	1、數據庫連接創建、釋放頻繁造成系統資源浪費從而影響系統性能，如果使用數據庫連接池可解決此問題。
	解決: 在 SqlMapConfig.xml中配置數據連接池，使用連接池管理數據庫連接

	2、Sql語句寫在代碼中造成代碼不易維護，實際應用 sql變化的可能較大，sql變動需要改變 java代碼
	解決: 將sql語句配置在 XXXmapper.xml文件中與java代碼分開

	3、 向sql語句傳參數麻煩，因為sql語句的where條件不一定，可能多也可能少，佔位符需要和參數一一對應
	解決: Mybatis自動將java對象映射至sql語句，通過statement中的parameterType定義輸入參數的類型

	4、 對結果集解析麻煩，sql變化導致解析代碼變化，且解析前需要遍歷，如果能將數據庫紀錄封裝成pojo對象解析比較方便
	解決: Mybatis自動將sql執行結果映射至java對象，通過statement中的resultType定義輸出結果類型

<br>