# Spring Boot Security Notes

Some notes took during the tutorial

## Setting Basic Authentication Configuration

```
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {

    @Override
    public void configure(AuthenticationManagerBuilder auth) throws Exception {

        User.UserBuilder users = User.withDefaultPasswordEncoder();

        auth.inMemoryAuthentication()
                .withUser(users.username("john").password("test123").roles("EMPLOYEE"))
                .withUser(users.username("mary").password("test123").roles("EMPLOYEE", "MANAGER"))
                .withUser(users.username("susan").password("test123").roles("EMPLOYEE", "ADMIN"));
    }

    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                    .anyRequest().authenticated()
                .and()
                .formLogin()
                    .loginPage("/loginPage")
                    .loginProcessingUrl("/authenticateUser")
                    .permitAll()
                .and()
                .logout().permitAll();
    }
}
```

## Setting Authorization Regex

```
...
...

    @Override
    public void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                    .antMatchers("/").hasRole("EMPLOYEE")
                    .antMatchers("/leaders/**").hasRole("MANAGER")
                    .antMatchers("/systems/**").hasRole("ADMIN")
                .and()
                .formLogin()
                ...
                ...
```

## Setting Access Denied Handler

```
...
...
.and()
.exceptionHandling()
    .accessDeniedPage("/access-denied");
...
...
```

## Showing Content Conditionally on User Roles with Thymeleaf

Dependencies for Thymeleaf security dialect used to introspect user related information:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>

<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-springsecurity5</artifactId>
</dependency>
```


In the HTML file, add the namespace for Thymeleaf security:


```
<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org"
      xmlns:sec="http://www.thymeleaf.org/extras/spring-security">
<head>
...
...
```

To visualize user information (Such as Username or Roles:

```
<div sec:authentication="name"></div>
<div sec:authentication="principal.authorities"></div>
```

To show the content based on the User Role

```
<p sec:authorize="hasRole('ROLE_MANAGER')">
    <a href="/leaders">LeaderShip Meeting</a>
</p>

<p sec:authorize="hasRole('ROLE_ADMIN')">
    <a href="/systems">IT Systems Meeting</a>
</p>
```