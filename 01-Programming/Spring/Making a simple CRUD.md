# Making a simple CRUD

A step by step guide to creating a simple CRUD in Spring boot

## 1.Project Configuration

Start the project by going to Spring Initializr and adding the following dependencies:

- Lombok
- Spring Web
- Spring Data JPA
- Spring Boot Dev Tools
- H2 Database (optional)
- Specific database driver (MySQL, postgreSQL, etc)
- Validation

## 2. Creating a database

Create an empty database of your choosing.

## 3. Application properties

Basic configurations for the application.properties file

### MySQL example

```properties
# ===============================
# DATABASE CONFIGURATION
# ===============================

# "database_name" should be the name of the database
spring.datasource.url=jdbc:mysql://localhost:3306/database_name?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true

# Database credentials
spring.datasource.username=admin
spring.datasource.password=admin

# ===============================
# JPA / HIBERNATE
# ===============================
spring.jpa.hibernate.ddl-auto=update

# Show SQL (VERY useful while learning)
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# Optional debugging
logging.level.org.hibernate.SQL=DEBUG
logging.level.org.hibernate.orm.jdbc.bind=TRACE
```

It is possible to use system variables witht the following notation: `${VARIABLE_NAME}`

#### if using PostgreSQL

```properties
# ===============================
# DATABASE CONFIGURATION
# ===============================

spring.datasource.url=jdbc:postgresql://localhost:5432/database_name

# Everything else stays the same

# ===============================
# JPA / HIBERNATE
# ===============================
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect

# Everything else stays the same

```

## 4. Mapping the project structure

We're using a `Task` class as a placeholder for a generic entity class that will be persisted and handled

### Creating packages

We need to create the necessary packages to follow the layered architecture

#### Packages we need

- Model
- Repository
- Service
- Controller
- DTO

## 5. Mapping necessary clases

It's very useful to map the classes in advance, even if we're not going to implement any code in them yet. We're going to map the classes necessary to handle a single type of entity.

### Necessary classes

**Entity Class:** This class represents the entity itself. It should be labeled with the annotation `@Entity`. 
Example:

```java
@Entity
public class Task {
    //Empty for now
}
```

**Repository Interface:** A repository that will handle persistence for out entity. It should be an interface annotated with @Repository and extending from JpaRepository, having the entity class and the type of id as parameters. The naming convention is: I + Name of the entity class + Repository. 
Example:

```java
@Repository
public interface ITaskRepository extends JpaRepository<Task, Long> {
    //Empty for now
}
```

**Service Interface:** An interface that will define the different methods used by the service class. the purpose of this interface is being able to do dependency injection in the controller class. The naming convention is: I + Name of the entity class + Service
Example:

```java
public interface ITaskService {
    //Empty for now
}
```

**Service Class:** A class that will later manage the business logic. It has to be annotated with @Service, and it will have an attribute declared of the type belonging to the Repository interface, this attribute will be annotated with @Autowired, allowing for automatic dependency injection. The naming convention is: name of the entity class + Service

```java
@Service
public class TaskService {

    @Autowired
    private ITaskRepository taskRepo;

    //Empty for now
}
```

**Controller Class:** The class that will handle our endpoints. This class will be annotated with @RestController. It will have an attribute of the Service interface type, also labeled as @autowired for dependency injection. Naming convention: name of the entity class + Controller
Example:

```java
@RestController
public class TaskController {

    @Autowired
    private ITaskService taskService;

    //Empty for now
}
```

**DTOs:** We will not create any DTOs at this phase, they will be created later as needed.

## 6. Implementing Entity class

Define the fields that should be persisted and choose the key generation strategy

```java
@Entity
@Table(name = "tasks")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class Task {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String title;

    private String description;

    private boolean completed;

    private LocalDateTime createdAt;
}
```

The Task entity represents a database table.
Hibernate will automatically create or update the table structure based on this class.

## 7. Implementing Repository

Enable database access without writting SQL

```java
@Repository
public interface ITaskRepository extends JpaRepository<Task, Long> {
}
```

`JpaRepository` provides CRUD operations automatically, including save, find, delete, and pagination. Implement more methods as needed.

## 8. Implementing Service layer

Define business operations for the Entity

### Service Interface

```java
public interface ITaskService {

    List<Task> getAllTasks();

    Task getTaskById(Long id);

    Task saveTask(Task task);

    void deleteTask(Long id);
}
```

### Service Class

```java
@Service
@RequiredArgsConstructor
public class TaskService implements ITaskService {

    private final ITaskRepository taskRepo;

    @Override
    public List<Task> getAllTasks() {
        return taskRepo.findAll();
    }

    @Override
    public Task getTaskById(Long id) {
        return taskRepo.findById(id)
                .orElseThrow(() -> new RuntimeException("Task not found"));
    }

    @Override
    public Task saveTask(Task task) {
        return taskRepo.save(task);
    }

    @Override
    public void deleteTask(Long id) {
        taskRepo.deleteById(id);
    }
}
```

## 9. Controller class

```java
@RestController
@RequestMapping("/tasks")
@RequiredArgsConstructor
public class TaskController {

    private final ITaskService taskService;

    @GetMapping
    public List<Task> getAll() {
        return taskService.getAllTasks();
    }

    @GetMapping("/{id}")
    public Task getById(@PathVariable Long id) {
        return taskService.getTaskById(id);
    }

    @PostMapping
    public Task create(@RequestBody Task task) {
        return taskService.saveTask(task);
    }

    @PutMapping("/{id}")
    public Task update(@PathVariable Long id, @RequestBody Task task) {
        task.setId(id);
        return taskService.saveTask(task);
    }

    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) {
        taskService.deleteTask(id);
    }
}
```

