	@Configuration
	@EnableWebSecurity
	@EnableGlobalMethodSecurity(securedEnabled = true)
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

@Configuration: 告诉spring这是一个bean。
@EnableWebSecurity：告诉spring这就是一个springSecurity的配置。
@EnableGlobalMethodSecurity(securedEnabled = true)：在Method Security一文有详解。

configre(HttpSecurity http)：这个方法过滤http请求，访问/admin/下的url需要admin权限，其他url需要认证才可访问，这里需要注意下，`/**`表示可以跨文件夹，`/*`表示只能是这个文件夹下的子文件夹。在这个方法中我们可以指定自己的loginPage,设置logout。

configureGlobal(AuthenticationManagerBuilder auth)：这个方法做认证，在这里我们允许账号为user，密码为password的具有user权限的用户和账
号为admin，密码为password的具有user、admin双重权限的用户登录。
	
