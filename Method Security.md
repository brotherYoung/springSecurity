# security方法级别的保护
实际开发中，我们不仅可以使用security来限制用户对视图（页面）的访问，还能用在某个方法上进行访问控制，这个方法一般是service层的方法。

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

首先，我们在SecurityConfig这个类上使用注解@EnableGlobalMethodSecurity(securedEnabled = true)，来开启security的@Secured注解。
这里还有其他两类注解，@EnableGlobalMethodSecurity(jsr250Enabled = true)开启@JSR-250注解，@EnableGlobalMethodSecurity(prePostEnabled = true)开启@PreAuthorize
注解。

    @Controller
    public class UserController {
    	
    	@RequestMapping("/index.do")
    	@Secured("ROLE_ADMIN")
    	public ModelAndView index() {
    		ModelAndView modelAndView = new ModelAndView();
    		modelAndView.setViewName("index");
    		return modelAndView;
    	}
    }
