# Unit Testing with J-Unit and Mockito

Unit testing is a type of automatic test to verify that a single isolated class works as intended. 
For this reason, the class is tested in isolation from all the others, using programmable mocks, which are fake implementations of dependencies.

## Example with Equipos_API:

This is an example using a service class from the project "Equipos_API"

### Basic structure of a test class

```Java
@ExtendWith(MockitoExtension.class)
class EquipoServiceTest {

    // Create mock
    @Mock
    private EquipoRepository repository;

    // Inject mocks into the class to be tested
    @InjectMocks
    private EquipoService service;

}
```

### Testing the getAll

Test methods are annotated with @Test and are named after the expected behavior, usually starting with the word "should"

```Java
    @Test
    void shouldReturnAllEquipos() {

        // Arrange
        List<Equipo> equipos = List.of(
                new Equipo(1L, "Barcelona", "La Liga", "España"),
                new Equipo(2L, "Liverpool", "Premier League", "Inglaterra")
        );

        when(repository.findAll()).thenReturn(equipos);

        // Act
        List<Equipo> resultado = service.getAll();

        // Assert
        assertEquals(2, resultado.size());
        assertEquals("Barcelona", resultado.get(0).getNombre());
        assertEquals("Liverpool", resultado.get(1).getNombre());

        // Verifies the repository was called correctly
        verify(repository).findAll();
    }
```

This test is divided in four sections:

- Arrange: In this phase you prepare the object or collection of objects that your mock repository will return
- Act: The main action is executed
- Assert: We assert that the result given by the class is the same we expected
- Verify: verifies that the respository class was called correctly

### Important distinction between AssertSame and AssertEquals

These are two methods used in the assert portion of a test, but they behave differently.
**AssertSame**: It only passes if the two given objects are the exact same in memory. It's used to test identity.
**AssertEquals**: It doesn't care about identity, it's used to test equality between values.
