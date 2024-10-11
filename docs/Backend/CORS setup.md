---
sidebar_position: 2
---

# CORS setup

## Global CORS fix configuration


``` java, title="com/fontys/crowdfund/config/WebConfig.java"
package com.fontys.crowdfund.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/**") // Allow all paths
                        .allowedOrigins("http://localhost:3000")
                        .allowedMethods("GET", "POST", "PUT", "DELETE")
                        .allowedHeaders("*")
                        .allowCredentials(true);
            }
        };
    }
}

```
The code in `com/fontys/crowdfund/config/WebConfig.java` is a configuration class for a Spring Boot application. It configures CORS (Cross-Origin Resource Sharing) settings to allow the web application to interact with the backend API.
