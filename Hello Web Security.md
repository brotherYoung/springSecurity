	@EnableWebSecurity
	public class SecurityConfig extends WebSecurityConfigurerAdapter {
	
		@Autowired
		//必须注入一个AuthenticationManagerBuilder的实例，否则会报错：No AuthenticationProvider found for org.springframework.security.authentication.UsernamePasswordAuthenticationToken
		public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
			auth
				.inMemoryAuthentication()
					.withUser("user").password("password").roles("USER");
		}
	}
	第一个springSecurity的程序就搞定了
	
