# pom.xml
	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
		<modelVersion>4.0.0</modelVersion>
		<groupId>com.ly.study</groupId>
		<artifactId>spring-security</artifactId>
		<packaging>war</packaging>
		<version>0.0.1-SNAPSHOT</version>
		<name>spring-security Maven Webapp</name>
		<url>http://maven.apache.org</url>
		<dependencies>
			<dependency>
				<groupId>junit</groupId>
				<artifactId>junit</artifactId>
				<version>3.8.1</version>
				<scope>test</scope>
			</dependency>
	
			<dependency>
				<groupId>org.springframework.security</groupId>
				<artifactId>spring-security-core</artifactId>
				<version>4.1.0.RELEASE</version>
			</dependency>
	
			<dependency>
				<groupId>org.springframework.security</groupId>
				<artifactId>spring-security-web</artifactId>
				<version>4.1.0.RELEASE</version>
			</dependency>
	
			<dependency>
				<groupId>commons-logging</groupId>
				<artifactId>commons-logging</artifactId>
				<version>1.2</version>
			</dependency>
	
			<dependency>
				<groupId>org.springframework.security</groupId>
				<artifactId>spring-security-config</artifactId>
				<version>4.1.0.RELEASE</version>
			</dependency>
	
			<dependency>
				<groupId>commons-dbcp</groupId>
				<artifactId>commons-dbcp</artifactId>
				<version>1.4</version>
			</dependency>
	
			<dependency>
				<groupId>mysql</groupId>
				<artifactId>mysql-connector-java</artifactId>
				<version>5.1.38</version>
			</dependency>
	
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-jdbc</artifactId>
				<version>4.1.6.RELEASE</version>
			</dependency>
	
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-webmvc</artifactId>
				<version>4.2.5.RELEASE</version>
			</dependency>
	
			<dependency>
				<groupId>jstl</groupId>
				<artifactId>jstl</artifactId>
				<version>1.2</version>
			</dependency>
	
		</dependencies>
	
		<build>
			<finalName>springSecurity</finalName>
		</build>
	</project>

# web.xml
	<web-app xmlns="http://java.sun.com/xml/ns/j2ee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"
		version="2.4">
		<display-name>Archetype Created Web Application</display-name>
	
	    <context-param>
	        <param-name>contextClass</param-name>
	        <param-value>
	            org.springframework.web.context.support.AnnotationConfigWebApplicationContext
	        </param-value>
	    </context-param>
	    <context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>com.*</param-value>
	    </context-param>
		
		<!-- web项目中的bean依赖spring注入 -->
		<listener>
			<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
		</listener>
		
		<filter>
			<filter-name>springSecurityFilterChain</filter-name>
			<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
		</filter>
	
		<filter-mapping>
			<filter-name>springSecurityFilterChain</filter-name>
			<url-pattern>/*</url-pattern>
		</filter-mapping>
		
		<!-- Declare a Spring MVC DispatcherServlet as usual -->
	    <servlet>
	        <servlet-name>dispatcher</servlet-name>
	        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	        <!-- Configure DispatcherServlet to use AnnotationConfigWebApplicationContext
	            instead of the default XmlWebApplicationContext -->
	        <init-param>
	            <param-name>contextClass</param-name>
	            <param-value>
	                org.springframework.web.context.support.AnnotationConfigWebApplicationContext
	            </param-value>
	        </init-param>
	        <!-- Again, config locations must consist of one or more comma- or space-delimited
	            and fully-qualified @Configuration classes -->
	        <init-param>
	            <param-name>contextConfigLocation</param-name>
	            <param-value>com.*</param-value>
	        </init-param>
	    </servlet>
	
	    <!-- map all requests for /app/* to the dispatcher servlet -->
	    <servlet-mapping>
	        <servlet-name>dispatcher</servlet-name>
	        <url-pattern>*.do</url-pattern>
	    </servlet-mapping>
	</web-app>
