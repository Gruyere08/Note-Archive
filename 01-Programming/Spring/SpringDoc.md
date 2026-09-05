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

## Step 3: See if it's working properly
Access the following endpoint to see if the site boots or not: `/swagger-ui.html`

## Setting up a simple global configuration class
To do this we first label the class as a `@configuration` class and then we use the `@OpenAPIDefinition` with the following syntax
```java
@Configuration
@OpenAPIDefinition(
        info = @Info(
                title = "Equipo API",
                version = "1.0",
                description = "REST API for managing football teams"
        )
)
public class OpenApiConfig {
}
```

## Documenting a Controller
To document a controller we use the `@Tag` annotation on the controller itself
```java
@Tag(
        name = "Equipos",
        description = "Operations related to football teams"
)
```

## Documenting a specific end point
```java
@Operation(
            summary = "Get all teams",
            description = "Returns all teams available"
    )
```
