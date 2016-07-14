# 动态认证
实际开发中，用户数据都在数据库中，我们需要连接数据库进行动态认证。

# security的配置类，将认证改为jdbc认证。
	@EnableWebSecurity
	public class SecurityConfig extends WebSecurityConfigurerAdapter {
		
		@Autowired
		private DataSource dataSource;
		
		@Autowired
		public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
			auth
				.jdbcAuthentication()
					.dataSource(dataSource);
		}
		
		protected void configure(HttpSecurity http) throws Exception {
			http
				.authorizeRequests()                                                                
					.antMatchers("/resources/**", "/signup", "/about").permitAll()
					.antMatchers("/user/**").hasRole("USER")
					.antMatchers("/admin/**").hasRole("ADMIN")                                      
					.antMatchers("/db/**").access("hasRole('ADMIN') and hasRole('USER')")            
					.anyRequest().authenticated()                                                   
					.and()
				.formLogin();
		}
	}
# 注意！
withDefaultSchema()：这个方法会自动执行spring-security-core这个jar包中org/springframework/security/core/userdetails/jdbc/users.ddl文件，为数据库中创建两张表，但是，打开这个文件你会发现建表语句有问题，create table users(username **varchar_ignorecase(50)**)，我用的是mysql数据库，根本没有varchar_ignorecase这个数据类型，修改为varchar(50)即可，下面为修改后的建表语句。

	create table users(username varchar(50) not null primary key,password varchar(500) not null,enabled boolean not null);
	create table authorities (username varchar(50) not null,authority varchar(50) not null,constraint fk_authorities_users foreign key(username) references users(username));
	create unique index ix_auth_username on authorities (username,authority);

.withUser("user").password("password").roles("USER")：这个方法会向数据库中添加一个账号为user,密码为password，权限为ROLE_USER的用户。

我们再次在浏览器中验证就会发现，必须是数据库中没有停用的，并且在权限表中有权限的用户才可以登陆。
