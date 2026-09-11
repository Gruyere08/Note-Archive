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
To document Endpoints we use the following annotation on the endpoint itself
```java
@Operation(
            summary = "Get all teams",
            description = "Returns all teams available"
    )
```
We can also document specific responses using the following syntax
```java
@ApiResponses({
        @ApiResponse(
                responseCode = "200",
                description = "Team found"
        ),
        @ApiResponse(
                responseCode = "404",
                description = "Team not found"
        )
})
```
or
```java
@ApiResponse(
            responseCode = "200",
            description = "Teams successfully retrieved"
    )
```
for a single response

#### Documenting a path parameter
We use the following syntax
```java
@Parameter(description = "Unique identifier of the team")
```
But this annotation has to be applied to the path parameter itself in the following way
```java
@GetMapping("/{id}")
public Equipo getById(
        @Parameter(description = "Unique identifier of the team")
        @PathVariable Long id
) {
    return equipoService.getById(id);
}
```

#### Documenting a request body
There are various ways to do this, but the cleaner option is using this syntax inside the `@Operation` annotation
```java
@Operation(
            summary = "Updates a team",
            description = "Updates an existing team with new information",
            requestBody = @io.swagger.v3.oas.annotations.parameters.RequestBody(
                    description = "New information to update the designated team",
                    required = true
            )
    )
```
The `@RequestBody` annotation from swagger is named the same as the annotation from Spring, that's why we have to use the full name of the annotation here.
