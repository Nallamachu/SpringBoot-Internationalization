# SpringBoot-Internationalization
POC project of Internationalization using Spring Boot technology

SpringBootInternationalization is a POC project to know how to configure and make it work of internationalization. For this project, I choose only WEB and DevTools modules. For I18n we use mainly of below two Bean configurations which I have separated from main class and placed under the package of ```com.i18n.message.config```.

```java
package com.i18n.message.config;

import java.util.Locale;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.support.ResourceBundleMessageSource;
import org.springframework.web.servlet.LocaleResolver;
import org.springframework.web.servlet.i18n.SessionLocaleResolver;

@Configuration
public class I18nConfiguration {

	@Bean
	public LocaleResolver localeResolver() {
		SessionLocaleResolver localeResolver = new SessionLocaleResolver();
		localeResolver.setDefaultLocale(Locale.US);
		return localeResolver;
	}

	@Bean
	public ResourceBundleMessageSource messageSource() {
		ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
		/* Passing basename sould match to the properties file basename */
		messageSource.setBasename("messages");
		return messageSource;
	}
}
```
Will use ```SessionLocaleResolver``` to read ```Locale(Accept-Language)``` from the ```RequestHeaders``` and ```ResourceBundleMessageSource``` used to load ```messages.properties``` files with matching of base name defined as messages.

Look at the below controller to know how we are loading messages from the respective properties files.
```java
package com.i18n.message.controller;

import java.util.Locale;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.MessageSource;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestHeader;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloWorldController {

	@Autowired
	private MessageSource messageSource;

	@GetMapping(path = "/hello-world-i18n")
	public String helloWorld(@RequestHeader(name = "Accept-Language", required = false) Locale locale) {
		return messageSource.getMessage("good.morning.message", null, locale);
	}
}
```

The below line will load the ```Accept-Language``` from the ```RequestHeaders``` and set to the ```Locale```.
```java
@RequestHeader(name = "Accept-Language", required = false)
```
```getMessage()``` method will load the property value based on the key passing ```good.morning.message``` and where locale matches to the properties file.

The below are the two properties files defined under the resource directory.
**messages.properties:**
```properties
good.morning.message=Good Morning
```
**messages_fr.properties:**
```properties
good.morning.message=Bonjour
```
Here is the main class of the SpringBootInternationalization project.
```java
package com.i18n.message;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringBootInternationalizationApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringBootInternationalizationApplication.class, args);
	}
}
```

In ```application.properties``` I have changes default ```server.port``` from 8080 to 6060 which you can also observe on application start.

While accessing the REST URL, we need to set Accept-Language as us/fr for loading respective message. If not confiure anything, will be considered US as default locale and will the value from ```messages.properties``` file.

*Note: You can also create other properties file like messages_de.properties which will point to the German language like below and try the setting Accept-language as de and look at the value is loading properly or not.*
**messages_de.properties**
```properties
good.morning.message=Guten Morgen
```
