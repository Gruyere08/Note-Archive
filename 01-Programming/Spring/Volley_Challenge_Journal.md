# Volley Challenge Development Journal

This is a development journal for the construction of the Volley Team API, to be used later as study reference.

## Chapter 1: Setting up an order

The first thing I did was plan a strategy to construct this API in most efficient way possible. I identified tasks to be done and I gave them an order to keep the work structured and clean. This is the order that will be used in the construction of this project:

1. **Understand and model the domain:** Map out necessary classes for the domain. Identify necessary entities, their attributes and validations.
2. **Create the Spring boot project, adding the necessary dependencies:** Spring Web, Spring Data JPA, H2 Database, Lombok, Spring Security.
3. **Configure Database:** Configure the H2 In-memory database with the initial data provided by the challenge.
4. **Creating Entity + Repository Layers:** Create the `Equipo` entity with its JPA annotations. Create `EquipoRepository` with the necessary custom queries like `findByNombreContainingIgnoreCase`.
5. **Create the Service layer (Business Logic):** Create the necessary business logic in `EquipoService`, making sure to use the correct method of injection of dependencies. Important methods: `getAll`, `getById`, `create`, `update`, `delete`, `search`. Use custom exceptions.
6. **Create Controller layer:** Create the necessary endpoints:
   - GET `/equipos`
   - GET `/equipos/{id}`
   - GET `/equipos/buscar?nombre=`
   - POST `/equipos`
   - PUT `/equipos/{id}`
   - DELETE `/equipos/{id}`
7. **Global exception handling:** Create custom exceptions and handle them acordingly by matching the response format requested by the challenge.
8. **Validations and DTO layer:** Implement the necessary validations and apply the DTO pattern.
9. **Unit Testing (JUnit + Mockito):** Implement tests following the requirements. Focus on: `getById` success & fail, `create`, `delete`. Keep it simple but clean.
10. **Spring security:** Implement JWT authentication. Create the `/auth/login` endpoint and configure security.
11. **Swagger documentation:** Create the corresponding documentation using Swagger.
12. **Dockerize the application:** Create a docker file. Build JAR. Run app inside container
13. **Final polish:** Clean package structure. Consistent naming. Remove unused code. Add README.

