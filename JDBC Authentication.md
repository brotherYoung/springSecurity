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
withDefaultSchema()：这个方法会自动执行spring-web-security中的一个users.dll文件，为数据库中创建两张表，users与authixxx，但是，打开这个文件你会发现建表语句有问题，create table users(varchar_ignorecase(50))，我用的是mysql数据库，根本没有varchar_ignorecase这个数据类型，修改为varchar(50)即可。
***
.withUser("user").password("password").roles("USER")：这个方法会向数据库中添加一个账号为user,密码为password，权限为ROLE_USER的用户。
我们再次在浏览器中验证就会发现，必须是数据库中没有停用的，并且在权限表中有权限的用户才可以登陆。
