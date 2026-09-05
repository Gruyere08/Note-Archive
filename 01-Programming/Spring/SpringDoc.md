# API documentation with Spring Doc and SwaggerUI

## Step 1: Dependency
The first step for documenting your API using Spring Doc and Swagger is to add the necessary dependency. The easiest way to do it is to search Spring doc in Spring initializr

## Step 2: Exclude the necessary URLs from your Security
Allow the access to the following URLs without authentication in your security configuration file
```java
.requestMatchers(
    "/swagger-ui.html",
    "/swagger-ui/**",
    "/v3/api-docs/**"
).permitAll()
```
