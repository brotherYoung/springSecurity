	@EnableWebSecurity
	public class SecurityConfig extends WebSecurityConfigurerAdapter {
	
		protected void configure(HttpSecurity http) throws Exception {
			http
				.authorizeRequests()
				.antMatchers("/admin/**").hasRole("ADMIN")
					.anyRequest().authenticated()
					.and()
				.formLogin()
					.and()
				.httpBasic();
		}
		
		@Autowired
		//必须依赖注入一个AuthenticationManagerBuilder的实例，否则会报错：No AuthenticationProvider found for org.springframework.security.authentication.UsernamePasswordAuthenticationToken
		public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
			auth
				.inMemoryAuthentication()
					.withUser("user").password("password").roles("USER").and()
					.withUser("admin").password("password").roles("USER", "ADMIN");
		}
	}
@EnableWebSecurity：告诉spring这就是一个springSecurity的配置。

configre(HttpSecurity http)：这个方法过滤http请求，访问/admin/下的url需要admin权限，其他url需要认证才可访问，在这个方法中我		们可以指定自己的loginPage,设置logout。

configureGlobal(AuthenticationManagerBuilder auth)：这个方法做认证，在这里我们允许账号为user，密码为password的具有user权限的用户和账
号为admin，密码为password的具有user、admin双重权限的用户登录。
	
