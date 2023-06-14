# Spring MVC (Web)

## Model View Controller (MVC)

MVC (Model-View-Controller) is a pattern in software design commonly used to implement user interfaces, data, and controlling logic. It emphasizes a separation between the software's business logic and display. This "separation of concerns" provides for a better division of labor and improved maintenance.

![MVC Diagram](./images/web/mvc-diagram.png)

### Model

The model defines what data the app should contain. If the state of this data changes, then the model will usually notify the view (so the display can change as needed) and sometimes the controller (if different logic is needed to control the updated view).

### View

The view defines how the app's data should be displayed.

### Controller

The controller contains logic that updates the model and/or view in response to input from the users of the app.


### MVC Arond the web

what other MVC frameworks are out there?

## Spring MVC

In this section you will begin to build out the foundation for our Runnerz Application.

### Model

- Run
  - build out as a normal class first
  - then convert to a record
- Location (Enum)

```java
public record Run(
        Integer id,
        String title,
        LocalDateTime startedOn,
        LocalDateTime completedOn,
        Integer miles,
        Location location
) {
}
```

```java
public enum Location {
    INDOOR,
    OUTDOOR
}
```

### View (JSON)

```json

```

### Controller

```java
@RestController
@RequestMapping("/api/runs")
public class RunController {

    private final List<Run> runs = new ArrayList<>();

    @GetMapping
    public List<Run> findAll() {
        return runs;
    }

    @GetMapping("/{id}")
    public Optional<Run> findById(@PathVariable Integer id) {
        return runs.stream().filter(run -> run.id().equals(id)).findFirst();
    }

}
```


## Spring IoC Container / Application Context

  - Spring IoC Container / Application Context
  - Spring Beans
  - Dependency Injection


### Spring IoC Container / Application Context

The `org.springframework.context.ApplicationContext` interface represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the beans.

- Open `org.springframework.context.ApplicationContext`
- View the classes it inherits from (superclasses)`
- The configuration metadata is represented in XML, Java annotations, or Java code.

```java
@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		ConfigurableApplicationContext context = SpringApplication.run(Application.class, args);
		Arrays.stream(context.getBeanDefinitionNames()).forEach(System.out::println);
	}

}
```

You can view the beans in the `ApplicationContext` from IntelliJ's Spring Windows

### Spring Beans

A Spring IoC container manages one or more beans. These beans are created with the configuration metadata that you supply to the container.

- Please raise your had if you ever created an instance of class 🙋🏼‍♂️
- We need to tell Spring about the classes we want it to manage for us
- Your Class + Configuration Metadata = Spring Bean
  - [Class](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-class)
  - [Name](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-beanname)
  - [Scopes](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#beans-factory-scopes)
  - more ...
- @Component
  - @Controller
  - @RestController
  - @Service
  - @Repository
- @Bean
  - Create a `@Configuration` class

Now that you understand what a bean is and the container that manages them we can revisit the problem we took a look at
earlier.

- `@ComponentScan(basePackages = {"dev.danvega"})`
- `@SpringBootApplication(scanBasePackages = {"dev.danvega"})`
- `Arrays.stream(context.getBeanDefinitionNames()).forEach(System.out::println);`
- You can view the definitions of the Spring beans used in your project, and see how they are related to other beans, in the Spring tool window.

### Dependency Injection & IoC

Now that you understand what Beans and the container are we can talk about Dependency Injection (DI) and Inversion of
Control (IoC). Dependency injection (DI) is a process whereby objects define their dependencies (that is, the other objects with which they work) only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean.

- `RunController` depends on -> `RunService`
- Unit Test the controller
- `@Autowired`

```java
public record Run(Integer id, String title) {

}
```

```java
@Service
public class RunService {

    private final List<Run> runs = new ArrayList<>();

    public RunService() {
        runs.add(new Run(1,"Monday Morning Run"));
    }

    public List<Run> findAll() {
        return runs;
    }

}
```

```java
@RestController
public class RunController {

    private final RunService runService;

    public RunController() {
        runService = new RunService();
    }

    public List<Run> findAll() {
        return runService.findAll();
    }

}
```

```java
class RunControllerTest {

    RunController controller;

    @BeforeEach
    void setUp() {
        controller = new RunController();
    }

    @Test
    void shouldFindAllRuns() {
        Assertions.assertEquals(1,controller.findAll().size());
    }

}
```

There are 3 types of DI but only 2 of them are recommended:

1. Constructor-based Dependency Injection
2. Setter-based Dependency Injection
3. Field-based Dependency Injection

Now that we have correctly setup Dependency Injection we could mock out the `RunService`:

```java
@SpringBootTest
class RunControllerTest {

    RunController controller;

    @MockBean
    RunService runService;

    private List<Run> runs = new ArrayList<>();

    @BeforeEach
    void setUp() {
        controller = new RunController(runService);
        runs.add(new Run(1,"Monday Morning Run"));
    }

    @Test
    void shouldFindAllRuns() {
        Mockito.when(runService.findAll()).thenReturn(runs);
        Assertions.assertEquals(1,controller.findAll().size());
    }

}
```

[Spring Constructor Injection: Why is it the recommended approach to Dependency Injection?](https://youtu.be/aX-bgylmprA)


## Building REST APIs

  - Building REST APIs
    - What is REST API
    - Request Methods
    - Http Status Codes
    - Annotations
    - CRUD REST API
    - API testing (curl/httpie/postman)
    - CORS

## Data Validation

## Error Handling
