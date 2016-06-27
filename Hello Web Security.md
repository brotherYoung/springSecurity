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
	除了/login以外，任何url都必须登陆才可以访问，登陆账号为user，密码为password。
	
