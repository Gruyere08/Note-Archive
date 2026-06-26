# Unit Tests

## Dependency

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

## An example of a Unit test in a Service Class

### Create appropiate package structure

```
src
 ├─ main
 │   └─ java
 │       └─ com.example.api
 │            └─ service
 │                 └─ EquipoService
 │
 └─ test
     └─ java
         └─ com.example.api
              └─ service
                   └─ EquipoServiceTest
```


### Structure of a test class for a Service

```Java
@ExtendWith(MockitoExtension.class)
class EquipoServiceTest {

    @Mock
    private EquipoRepository equipoRepository;

    @InjectMocks
    private EquipoService equipoService;

    //Test Methods

}
```
#### Glossary:
`@ExtendWith(MockitoExtension.class)`: Enables mockito features for this class. Activate Mockito inside JUnit.
`@Mock`: Assings the class as a mock, in this case a mock repository
`@InjectMocks`: Injects the designated mocks into the entity that we're going to use for testing

### Naming convention for testing methods

The methods of this test class should be named after the expected behavior from the specific method they are testing.

### Testing getAll method

```Java
@Test
void shouldReturnAllEquipos() {

    //Create a lis of entities for the mock repository to return
    List<Equipo> equipos = List.of(
            new Equipo(
                    1L,
                    "Real Madrid",
                    "La Liga",
                    "España"
            ),
            new Equipo(
                    2L,
                    "Barcelona",
                    "La Liga",
                    "España"
            )
    );

    //Assign the mock repo its behavior
    when(equipoRepository.findAll())
            .thenReturn(equipos);

    //Run the tested method
    List<Equipo> result =
            equipoService.getAll();

    //Test for correct behavior
    assertEquals(2, result.size());

    //Verify that the correct method from the repo was indeed called
    verify(equipoRepository).findAll();
}
```
When you run tests, JUnit executes every method annotated with `@Test`.

### Testing getById method

```Java
@Test
void shouldReturnEquipoById() {

    Equipo equipo = new Equipo(
            1L,
            "Real Madrid",
            "La Liga",
            "España"
    );

    when(equipoRepository.findById(1L))
            .thenReturn(Optional.of(equipo));

    Equipo result =
            equipoService.getById(1L);

    assertEquals("Real Madrid",
            result.getNombre());

    verify(equipoRepository)
            .findById(1L);
}
```

