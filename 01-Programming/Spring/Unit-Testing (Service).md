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

### Testing getById 

```java
@Test
    void shouldReturnEquipoWhenIdExists(){

        Long id = 1L;
        //Arrange
        Equipo equipo = new Equipo(
                id, "Barcelona", "La liga", "España");
        when(repository.findById(id)).thenReturn(Optional.of(equipo));

        //Act
        Equipo resultado = service.getById(id);

        //Assert
        assertSame(equipo, resultado);

        //Verify
        verify(repository).findById(id);


    }
```

assertSame is being used to test that the returned object is in fact the same object in memory

### Testing getById exception

The method getById from our Service has to possible paths: one where the operation succeeds, and one where it fails and throws an exception.
For this second path we will make a second test to verify that the exception is being thrown correctly.

```java
@Test
    void shouldThrowExceptionWhenIdDoesNotExist(){
        Long id = 1L;
        when(repository.findById(id)).thenReturn(Optional.empty());
        //Assert (The method is expected to throw an exception before it can return a value, so the Act is performed inside assertThrows().)
        assertThrows(EquipoNotFoundException.class, ()-> service.getById(id));
        //verify (optional in this case)
        verify(repository).findById(id);

    }
```
There's no act phase in this type of test since the method is expected to throw an exception, so doing an act phase and calling the method on its own, would make the test throw an exception and fail, so the act phase occurs inside the assertThrows.

### Testing searchByNombre

```java
@Test
    void shouldReturnEquiposMatchingName(){
        //Arrange
        String name = "Barcelona";
        List<Equipo> equipos = List.of(new Equipo(1L,name,"La liga","España"));
        when(repository.findAllByNombreContainingIgnoreCase(name)).thenReturn(equipos);
        //act
        List<Equipo> resultado = service.searchByNombre(name);
        //Assert

        assertSame(equipos, resultado);

        verify(repository).findAllByNombreContainingIgnoreCase(name);
    }
```

### Testing save

```java
@Test
    void shouldSaveEquipoAndReturnTheSavedEquipo(){
        //Arrange
        Equipo equipo = new Equipo(null, "Barcelona", "La liga", "España");
        Equipo saved = new Equipo(1L, "Barcelona", "La liga", "España");

        when(repository.save(equipo)).thenReturn(saved);

        //Act
        Equipo result = service.save(equipo);

        //Assert
        assertSame(saved, result);

        //verify
        verify(repository).save(equipo);
    }
```

The logic changes a little bit in saving methods, since we have to create a "saved" object that simulates the object we expect to receive and then a normal object that goes through the process. Once it's done we assert that we did indeed get the object we expected.

### Testing delete

```java
@Test
    void shouldDeleteEquipoWithTheSpecifiedId(){
        Long id = 1L;
        //Arrange
        Equipo equipo = new Equipo(id, "Barcelona", "La liga", "España");
        when(repository.findById(id)).thenReturn(Optional.of(equipo));
        doNothing().when(repository).delete(equipo);
        //act
        service.deleteById(id);
        //verify
        verify(repository).findById(id);
        verify(repository).delete(equipo);

    }
```

In methods that don't return anything, it is not necessary to use the doNothing method, since the mock repository will that that anyway, but it does give more clarity. Also, since the delete() method calls for another service method inside it, we have to program both mock methods with their respective return values and also verify that they were both called at the end.

### Testing delete exception

```java
@Test
    void shouldThrowExceptionWhenDeletingNonExistingEquipo(){
        Long id = 1L;
        //Arrange
        when(repository.findById(id)).thenReturn(Optional.empty());
        //Assert/Act
        assertThrows(EquipoNotFoundException.class, ()-> service.deleteById(id));
        //verify
        verify(repository).findById(id);
        verify(repository, never()).delete(any());
    }
```

This case ends up being a hybrid of previous cases. Since our delete method calls for getById before it can delete, then this method should throw an exception if it doesn't find an Equipo of that id. The important part is that the method should throw an exception before it ever calls delete, so at the end we should test that the delete method from the repository was indeed never called.
