# Spring MVC (Web)

  - RunnerzUI Introduction
  - Model View Controller (MVC)
    - What is MVC (Model/View/Controller)
  - Spring MVC
    - Model
    - Controller
  - Spring Core
    - Spring IoC Container / Application Context
    - Spring Beans
    - Dependency Injection
  - Building REST APIs
    - What is an API
    - What is REST API
      - HTTP Request Methods
      - HTTP Response Status Codes
    - Annotations
    - CRUD REST API
      - GET/POST/PUT/DELETE/
      - Filter to show off `@RequestParam`
    - API testing (curl/httpie/postman)
      - validate that the postman api collection works
      - validate that the intellij api collection works
    - CORS
  - Data Validation
  - Error Handling
  - RunnerzUI (UI/REST API/CORS/etc...)


## RunnerzUI Introduction

In this section you will be introduced to the RunnerzUI application. This application will be used throughout the course to demonstrate the concepts we are learning.

## Model View Controller (MVC)

MVC (Model-View-Controller) is a pattern in software design commonly used to implement user interfaces, data, and controlling logic. It emphasizes a separation between the software's business logic and display. This "separation of concerns" provides for a better division of labor and improved maintenance.

![MVC Diagram](./images/web/mvc-diagram.png)

### Model

The model defines what data the app should contain. If the state of this data changes, then the model will usually notify the view (so the display can change as needed) and sometimes the controller (if different logic is needed to control the updated view).

### View

The view defines how the app's data should be displayed.

### Controller

The controller contains logic that updates the model and/or view in response to input from the users of the app.

## Spring MVC

In this section you will begin to build out the foundation for our Runnerz Application.

### Model

- Run
  - Create `Run.java` as a class
  - Convert to a record
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

JavaScript Object Notation (JSON) is a standard text-based format for representing structured data based on JavaScript object syntax. It is commonly used for transmitting data in web applications (e.g., sending some data from the server to the client, so it can be displayed on a web page, or vice versa).

```json
{
"id": 1,
"title": "Monday Morning Run",
"startedOn": "2023-06-15T09:29:43.013931",
"completedOn": "2023-06-15T09:59:43.013945",
"miles": 3,
"location": "INDOOR",
"duration": "PT30M0.000014S",
"avgPace": 10
}
```

```json
[
  {
    "id": 1,
    "title": "Monday Morning Run",
    "startedOn": "2023-06-15T09:29:43.013931",
    "completedOn": "2023-06-15T09:59:43.013945",
    "miles": 3,
    "location": "INDOOR",
    "duration": "PT30M0.000014S",
    "avgPace": 10
  },
  {
    "id": 2,
    "title": "Wednesday Evening Run",
    "startedOn": "2023-06-15T09:29:43.014008",
    "completedOn": "2023-06-15T10:29:43.014011",
    "miles": 6,
    "location": "INDOOR",
    "duration": "PT1H0.000003S",
    "avgPace": 10
  }
]
```

### Controller

- RunController
  - Create `RunController.java` as a class
  - Convert to a `@RestController`
  - Add `@RequestMapping("/api/runs")`
  - Add `@GetMapping`
  - Add `@GetMapping("/{id}")`
  - Add `@PostConstruct` to initialize some data

```java
@RestController
@RequestMapping("/api/runs")
public class RunController {

    private final List<Run> runs = new ArrayList<>();

    public RunController() {
        runs.add(new Run(1,
                "Monday Morning Run",
                LocalDateTime.now(),
                LocalDateTime.now().plus(30, ChronoUnit.MINUTES),
                3,
                Location.INDOOR));

        runs.add(new Run(2,
                "Wednesday Evening Run",
                LocalDateTime.now(),
                LocalDateTime.now().plus(60, ChronoUnit.MINUTES),
                6,
                Location.INDOOR));
    }

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


### PostConstruct

[Using @PostConstruct and @PreDestroy](https://docs.spring.io/spring-framework/reference/core/beans/annotation-config/postconstruct-and-predestroy-annotations.html)

- When the constructor is called, the bean is not yet initialized - i.e. no dependencies are injected. In the @PostConstruct method the bean is fully initialized and you can use the dependencies.

- This is the contract that guarantees that this method will be invoked only once in the bean lifecycle. It may happen (though unlikely) that a bean is instantiated multiple times by the container in its internal working, but it guarantees that @PostConstruct will be invoked only once.

```java
    @PostConstruct
    private void init() {
        runs.add(new Run(1,
                "Monday Morning Run",
                LocalDateTime.now(),
                LocalDateTime.now().plus(30, ChronoUnit.MINUTES),
                3,
                Location.INDOOR));

        runs.add(new Run(2,
                "Wednesday Evening Run",
                LocalDateTime.now(),
                LocalDateTime.now().plus(60, ChronoUnit.MINUTES),
                6,
                Location.INDOOR));
    }
```


## Building REST APIs

### APIs

API stands for Application Programming Interface. An API is a software intermediary that allows two applications to talk to each other. In other words, an API is the messenger that delivers your request to the provider that you're requesting it from and then delivers the response back to you.

### REST APIs

REST (Representational State Transfer) refers to a group of software architecture design constraints that bring about efficient, reliable and scalable distributed systems.

The basic idea of REST is that a resource, e.g. a document, is transferred via well-recognized, language-agnostic, and reliably standardized client/server interactions. Services are deemed RESTful when they adhere to these constraints.

HTTP APIs in general are sometimes colloquially referred to as RESTful APIs, RESTful services, or REST services, although they don't necessarily adhere to all REST constraints. Beginners can assume a REST API means an HTTP service that can be called using standard web libraries and tools.

#### HTTP Request Methods

HTTP defines a set of request methods to indicate the desired action to be performed for a given resource. Although they can also be nouns, these request methods are sometimes referred to as HTTP verbs. Each of them implements a different semantic, but some common features are shared by a group of them: e.g. a request method can be safe, idempotent, or cacheable.

- **GET**: The GET method requests a representation of the specified resource. Requests using GET should only retrieve data.
- **HEAD**: The HEAD method asks for a response identical to a GET request, but without the response body.
- **POST**: The POST method submits an entity to the specified resource, often causing a change in state or side effects on the server.
- **PUT**: The PUT method replaces all current representations of the target resource with the request payload.
- **DELETE**: The DELETE method deletes the specified resource.

#### HTTP Response Status Codes

HTTP response status codes indicate whether a specific HTTP request has been successfully completed. Responses are grouped in five classes:

- Informational responses (100 – 199)
- Successful responses (200 – 299)
- Redirection messages (300 – 399)
- Client error responses (400 – 499)
- Server error responses (500 – 599)

### Annotations

- [@Component](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Component.html) - Indicates that an annotated class is a "component". Such classes are considered as candidates for auto-detection when using annotation-based configuration and classpath scanning.
  - [@Controller](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Controller.html) - Indicates that an annotated class is a "Controller" (e.g. a web controller).
  - [@RestController](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RestController.html) - A convenience annotation that is itself annotated with @Controller and @ResponseBody.
  - [@Service](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Service.html) - Indicates that an annotated class is a "Service", originally defined by Domain-Driven Design (Evans, 2003) as "an operation offered as an interface that stands alone in the model, with no encapsulated state."
  - [@Repository](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/stereotype/Repository.html) - Indicates that an annotated class is a "Repository", originally defined by Domain-Driven Design (Evans, 2003) as "a mechanism for encapsulating storage, retrieval, and search behavior which emulates a collection of objects".
- [@Bean](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html) - Indicates that a method produces a bean to be managed by the Spring container.
- [@RequestMapping](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestMapping.html) - Annotation for mapping web requests onto methods in request-handling classes with flexible method signatures.
  - [@GetMapping](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/GetMapping.html) - Annotation for mapping HTTP GET requests onto specific handler methods.
  - [@PostMapping](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/PostMapping.html) - Annotation for mapping HTTP POST requests onto specific handler methods.
  - [@PutMapping](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/PutMapping.html) - Annotation for mapping HTTP PUT requests onto specific handler methods.
  - [@DeleteMapping](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/DeleteMapping.html) - Annotation for mapping HTTP DELETE requests onto specific handler methods.
- [@PathVariable](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/PathVariable.html) - Annotation which indicates that a method parameter should be bound to a URI template variable. Supported for RequestMapping annotated handler methods.
- [@RequestParam](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestParam.html) - Annotation which indicates that a method parameter should be bound to a web request parameter.
- [@RequestBody](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/RequestBody.html) - Annotation indicating a method parameter should be bound to the body of the web request.
- [@ResponseStatus](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ResponseStatus.html) - Marks a method or exception class with the status code() and reason() that should be returned.

### CRUD REST API

### API Testing

## Cross-Origin Resource Sharing (CORS)

Cross-Origin Resource Sharing (CORS) is an HTTP-header based mechanism that allows a server to indicate any origins (domain, scheme, or port) other than its own from which a browser should permit loading resources. CORS also relies on a mechanism by which browsers make a "preflight" request to the server hosting the cross-origin resource, in order to check that the server will permit the actual request. In that preflight, the browser sends headers that indicate the HTTP method and headers that will be used in the actual request.

An example of a cross-origin request: the front-end JavaScript code served from https://domain-a.com uses XMLHttpRequest to make a request for https://domain-b.com/data.json.

For security reasons, browsers restrict cross-origin HTTP requests initiated from scripts. For example, XMLHttpRequest and the Fetch API follow the same-origin policy. This means that a web application using those APIs can only request resources from the same origin the application was loaded from unless the response from other origins includes the right CORS headers.

[@CrossOrigin](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/CrossOrigin.html) - Annotation for permitting cross-origin requests on specific handler classes and/or handler methods. Processed if an appropriate HandlerMapping is configured.

```java
@RestController
@RequestMapping("/api/runs")
@CrossOrigin("http://localhost:3000")
public class RunController {

  ...

}
```

## Data Validation

The Spring Framework provides support for the Java Bean Validation API.

Bean Validation provides a common way of validation through constraint declaration and metadata for Java applications. To use it, you annotate domain model properties with declarative validation constraints which are then enforced by the runtime. There are built-in constraints, and you can also define your own custom constraints.

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

```java
public record Run(
        Integer id,
        @NotEmpty
        String title,
        LocalDateTime startedOn,
        LocalDateTime completedOn,
        @Positive
        Integer miles,
        Location location
) {

  // ...

}
```

```properties
server.error.include-binding-errors=always
server.error.include-message=always
```

If we haven't had a chance to talk about [Spring Boot DevTools](./SpringCore.md) we can have that discussion now.

## Error Handling

TBD

## RunnerzUI

Let's check back in with DaShaun as he builds out the UI for Runnerz.

[RunnerzUI](./RunnerzUI.md)
