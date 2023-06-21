# Getting Started

  - start.spring.io
    - Maven
    - Spring Boot 3.1.0
    - Project Metadata
      - Group: info.kcdc
      - Artifact: runnerz
      - Package Name: info.kcdc.runnerz
    - Dependencies
      - Spring Boot Actuator
      - Spring Web
    - Explore
    - Generate & Download

## Project Structure

  - Understanding the project structure
    - `pom.xml`
      - Spring Boot Starters
      - Dependencies
          - Versions
          - Adding your own dependencies
    - Main Application Class
      - @SpringBootApplication annotation

## Integrated Development Environment (IDE)

  - How to start the application
    - IDE
    - Maven
    - View the application / browser & command-line

## Structuring you code

- Structuring your code
  - package by layer
  - package by feature
  - Hexagonal architecture

There is no requirement for structuring your code in a specific way. However, there are some best practices that you should follow.

- Try to avoid creating a class that does not include a package declaration (default package).
- Main Application in root package above other classes.
- If you're on a team create a plan that makes sense for your team (and not just you).
- Architecture (Layered/Hexagonal/).

## Hello World

```java
@RestController
@RequestMapping("/api")
class HelloController {

    @GetMapping("/hello")
    public String hello(@RequestParam() Optional<String> name) {
        return String.format("Hello, %s!", name.orElse("World"));
    }

}
```
> Application.java

We will come back to the API later.

## Front End

- Spring Boot Front Ends
  - Thymeleaf
  - Apache Freemarker
  - Mustache
  - Groovy Templates
  - Vaadin

## Quick Front End Example

- start.vaadin.com
- sign in / log in
- Theme
  - Dark
  - Make everything "Large"
- Settings
  - Project: Runner-UI
  - Group ID: dev.dashaun.web
  - Artifact ID: runner-ui
- Generate Project

Make your own or checkout the `init` branch from `runnerz-ui`

## Configuration Properties

```text
server.port=${PORT:8081}
apiBase=http://localhost:8080
```

## Hello World - Client

```java
@Service
class HelloClient {
    private final RestTemplate restTemplate;

    public HelloClient(RestTemplateBuilder restTemplateBuilder, @Value("${apiBase}") String runnerServiceUri) {
        this.restTemplate = restTemplateBuilder.rootUri(runnerServiceUri).build();
    }

    public String hello(String name) {
        if (name.length() > 0) {
            return restTemplate.getForObject("/api/hello?name={name}", String.class, name);
        } else {
            return restTemplate.getForObject("/api/hello", String.class);
        }
    }

}
```
> Add to HelloWorldView.java

## HelloWorldView - Consumer

Call the API and display the results.

```java
@PageTitle("Hello World")
@Route(value = "hello", layout = MainLayout.class)
@RouteAlias(value = "", layout = MainLayout.class)
public class HelloWorldView extends HorizontalLayout {

    private final TextField name;
    private final Button sayHello;

    //Inject the client
    public HelloWorldView(HelloClient helloClient) {
        name = new TextField("Your name");
        sayHello = new Button("Say hello");
        //Use the client
        sayHello.addClickListener(e -> {
            Notification.show(helloClient.hello(name.getValue()));
        });
        sayHello.addClickShortcut(Key.ENTER);

        setMargin(true);
        setVerticalComponentAlignment(Alignment.END, name, sayHello);

        add(name, sayHello);
    }

}
```

## Before moving on

Get the front end `UI` talking to the backend `API` before moving on.

A working UI can be checked out from the `hello-world-client` branch of `runnerz-ui`.
