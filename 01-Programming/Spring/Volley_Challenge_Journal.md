# Volley Challenge Development Journal

This is a development journal for the construction of the Volley Team API, to be used later as study reference.

## Chapter 1: Setting up an order

The first thing I did was plan a strategy to construct this API in most efficient way possible. I identified tasks to be done and I gave them an order to keep the work structured and clean. This is the order that will be used in the construction of this project:

1. **Understand and model the domain:** Map out necessary classes for the domain. Identify necessary entities, their attributes and validations.
2. **Create the Spring boot project, adding the necessary dependencies:** Spring Web, Spring Data JPA, H2 Database, Lombok, Spring Security.
3. **Configure Database:** Configure the H2 In-memory database with the initial data provided by the challenge.
4. **Creating Entity + Repository Layers:** Create the `Equipo` entity with its JPA annotations. Create `EquipoRepository` with the necessary custom queries like `findByNombreContainingIgnoreCase`.
5. **Create the Service layer (Business Logic):**