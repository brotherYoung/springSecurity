	@EnableWebSecurity
	public class SecurityConfig extends WebSecurityConfigurerAdapter {
		@Autowired
		public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
			auth
				.inMemoryAuthentication()
					.withUser("user").password("password").roles("USER");
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

@EnableWebSecurity：告诉spring这就是一个springSecurity的配置。

configre(HttpSecurity http)：这个方法过滤http请求，控制访问权限，这里需要注意下，`/**`表示可以跨文件夹，`/*`表示只能是这个文件夹下的子文件夹。在这个方法中我们可以指定自己的loginPage,设置logout。

configureGlobal(AuthenticationManagerBuilder auth)，必须使用@Autowired将AuthenticationManagerBuilder类的对象注入进configureGlobal方法中。

configureGlobal(AuthenticationManagerBuilder auth)：这个方法做认证，在这里我们允许账号为user，密码为password的具有user权限的用户登录。
	
