# 动态认证
实际开发中，用户数据都在数据库中，我们需要连接数据库进行动态认证。
首先，我们需要修改web.xml文件
# web.xml
    <web-app>
      	<display-name>Archetype Created Web Application</display-name>
      	//springSecurity的拦截器
      	<filter>
      		<filter-name>springSecurityFilterChain</filter-name>
      		<filter-class>org.springframework.web.filter.DelegatingFilterProxy
      		</filter-class>
      	</filter>
      	//拦截任何请求
      	<filter-mapping>
      		<filter-name>springSecurityFilterChain</filter-name>
      		<url-pattern>/*</url-pattern>
      	</filter-mapping>
      	//为spring指明配置文件的位置(对了一个xml文件)
      	<context-param>
      		<param-name>contextConfigLocation</param-name>
      		<param-value>
      			classpath:securityConfig.xml
      			classpath:applicationContext.xml
      		</param-value>
      	</context-param>
      	//利用spring去管理配置文件中的bean
      	<listener>
      		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
      	</listener> 
    </web-app>
还需要一个额外的applicationContext.xml主要是配置了数据源dataSource。
# applicationContext.xml
	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
		xmlns:context="http://www.springframework.org/schema/context"
		xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:tx="http://www.springframework.org/schema/tx"
		xsi:schemaLocation="     
	           http://www.springframework.org/schema/beans     
	           http://www.springframework.org/schema/beans/spring-beans-3.0.xsd     
	           http://www.springframework.org/schema/context     
	           http://www.springframework.org/schema/context/spring-context-3.0.xsd    
	           http://www.springframework.org/schema/mvc     
	           http://www.springframework.org/schema/mvc/spring-mvc-3.0.xsd
	           http://www.springframework.org/schema/tx 
	           http://www.springframework.org/schema/tx/spring-tx-3.0.xsd">
	           
		<!-- 配置数据源，使用的是dbcp数据源 -->
		<bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource"
			destroy-method="close">
			<property name="driverClassName" value="com.mysql.jdbc.Driver" />
			<property name="url" value="jdbc:mysql://localhost:3306/security"></property>
			<property name="username" value="root" />
			<property name="password" value="root" />
			<!-- 数据库连接池保持的最小连接数 -->
			<property name="minIdle" value="5" />
			<!-- 数据库连接池保持的最大连接数 -->
			<property name="maxIdle" value="30" />
			<!-- 当数据库连接因为某种原因断掉之后，再重新从连接池中拿另外一个连接时实际上这个连接可能 已经无效，所以为了确保所拿到的连接全都有效需要在获取连接，返回连接以及连接空闲时进行 
				有效性验证 下面3个设置为ture时进行验证，默认为false -->
			<!-- 取得连接时是否进行有效性验证 -->
			<property name="testOnBorrow" value="true" />
			<!-- 返回连接时是否进行有效性验证 -->
			<property name="testOnReturn" value="true" />
			<!-- 连接空闲时是否进行有效性验证 -->
			<property name="testWhileIdle" value="true" />
			<!--removeAbandoned: 是否自动回收超时连接 -->
			<property name="removeAbandoned" value="true" />
		</bean>
	</beans>


# security的配置类，将认证改为jdbc认证。
	@EnableWebSecurity
	public class SecurityConfig extends WebSecurityConfigurerAdapter {
		@Autowired
		private DataSource dataSource;
	
		@Autowired
		public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
			auth
				.jdbcAuthentication()
				.dataSource(dataSource)
				.withDefaultSchema()
				.withUser("user").password("password").roles("USER").and()
				.withUser("admin").password("password").roles("USER", "ADMIN");
		}
	}
# 注意！
withDefaultSchema()：这个方法会自动执行spring-security-core这个jar包中org/springframework/security/core/userdetails/jdbc/users.ddl文件，为数据库中创建两张表，（下面这个ddl文件内容是我已经修改好的）	
但是，打开这个文件你会发现建表语句有问题，create table
users(username *varchar_ignorecase(50)*)，我用的是mysql数据库，根本没有varchar_ignorecase这个数据类型，修改为varchar(50)即可。

	create table users(username varchar(50) not null primary key,password varchar(500) not null,enabled boolean not null);
	create table authorities (username varchar(50) not null,authority varchar(50) not null,constraint fk_authorities_users foreign key(username) references users(username));
	create unique index ix_auth_username on authorities (username,authority);

.withUser("user").password("password").roles("USER")：这个方法会向数据库中添加一个账号为user,密码为password，权限为ROLE_USER的用户。

我们再次在浏览器中验证就会发现，必须是数据库中没有停用的，并且在权限表中有权限的用户才可以登陆。
