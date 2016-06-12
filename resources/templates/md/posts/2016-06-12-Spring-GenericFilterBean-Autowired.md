{:title "Using @Autowired in a GenericFilterBean"
 :layout :post
 :tags  ["spring boot" "GenericFilterBean" "Autowired"]}
 
 Today I wanted to autowire a component into a GenericFilterBean. It did not work,
 the autowired object was null all the time. What finally helped was to skim
 through github and look how others did it. 
 This is the solution.
 
 
 GenericFilterBean:
 
 ```
 public class JwtFilter extends GenericFilterBean {
 	
 	@Autowired
 	private JwtHelper jwtHelper;
 
     @Override
     public void doFilter(final ServletRequest req,
                          final ServletResponse res,
                          final FilterChain chain) throws IOException, ServletException {         
     } 
 }
 ```
 
 First you need to create file: `context.xml` in your resources. Here
 you define the FilterBean you want to enable for autowiring.
 
 ```
 <?xml version="1.0" encoding="UTF-8"?>
 <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://www.springframework.org/schema/beans
 	   					   http://www.springframework.org/schema/beans/spring-beans.xsd">
 
     <bean id="jwtFilter" class="de.sveri.cleanercomm.filter.JwtFilter"/>
 
     <bean id="jwtFilterRegistrationBean" class="org.springframework.boot.context.embedded.FilterRegistrationBean">
         <property name="filter" ref="jwtFilter"/>
         <property name="enabled" value="true" />
     </bean>
 
 </beans>
 ```

Then you add this to your `Application` as annotation:

```
@SpringBootApplication
@ImportResource("classpath:context.xml")
public class Application {
...
```

Any finally add the `@Component` to your wired class:

```
@Component
public class JwtHelper {
...
```

Thats it. It seems that `GenericFilterBean`s are somewhat special in this
regard, at least it was not easy to find documentation for that.