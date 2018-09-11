As we have seen how to configure internatinalization by using SpringBoot technology. Here, I am placing few improvements on SpringBootInternationalization project in code wise.

Previosly I have use the SessionLocaleResolver class to load Locale details from RequestHeader. Now, I am replacing SessionLocaleResolver with AcceptHeaderLocaleResolver class which will have better features to load Header information. 
```java
package com.i18n.message.config;

import java.util.Locale;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.i18n.AcceptHeaderLocaleResolver;

@Configuration
public class I18nConfiguration {

	@Bean
	public LocaleResolver localeResolver() {
		AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
		localeResolver.setDefaultLocale(Locale.US);
		return localeResolver;
	}
}
```
If we are using AcceptHeaderLocaseResolver class we no need to explicilty load each property by using the @RequestHeader. Instead of @RequestHeader we can use LocaleContext, look at the below code
```java
package com.i18n.message.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.MessageSource;
import org.springframework.context.i18n.LocaleContextHolder;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloWorldController {

	@Autowired
	private MessageSource messageSource;

	@GetMapping(path = "/hello-world-i18n")
	public String helloWorld() {
		return messageSource.getMessage("good.morning.message", null, LocaleContextHolder.getLocale());
	}
}
```
If you have observed the above configuration class, I have completely removed the below method from the class.
```java
  @Bean
	public ResourceBundleMessageSource messageSource() {
		ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
		/* Passing basename sould match to the properties file basename */
		messageSource.setBasename("messages");
		return messageSource;
	}
```
So, you will get a doubt, how am I loading the messages files? Yes, I have configured the single property in application.properties file to set the base name of the properties files. Look at the below property,
```properties
spring.messages.basename=messages
```
