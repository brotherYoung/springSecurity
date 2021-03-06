# security方法级别的保护
实际开发中，我们不仅可以使用security来限制用户对视图（页面）的访问，还能用在某个方法上进行访问控制，这个方法一般是service层的方法。

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
当我们开启了security的方法注解后，就可以为某个具体方法授权了，在这里为了简便，直接在controller上授权，允许拥有ADMIN权限的用户
访问index()方法。**必须强调：我在使用Method Security的时候犯了一个错误，因为英文文档上没写清楚@EnableGlobalMethodSecurity(securedEnabled = true)具体注解在哪里，于是我将此注解写在了controller上，想着这里需要使用@secured，结果出错了！最后也是查了几天资料才发现需要
将此注解写在带有注解@Configuration的security配置类上，从名字也可以看出来嘛，EnableGlobalMethodSecurity，global：全局的配置，当然得写在整个security的配置类上咯。**
