## 1. What is Spring IOC
Spring IoC (Inversion of Control) is a principle where the control of object
creation and dependency management is transferred from the developer to the
Spring container. Instead of manually creating objects with new keyword,
Spring creates and manages them for you.

## 2. What is IOC Container
IoC Container is the core of Spring framework that manages the lifecycle of
Beans. It reads configuration metadata (annotations or XML), creates Bean
instances using reflection, injects dependencies, and destroys Beans when
the application shuts down. The main interface is BeanFactory, and
ApplicationContext is the enhanced version used in practice.

## 3. What are the Advantages of IOC
IoC decouples components from each other, making the code easier to maintain
and test. Since dependencies are injected rather than hardcoded, you can
easily swap implementations without changing the dependent class. It also
simplifies testing because you can inject mock objects instead of real ones.

## 4. What is Dependency Injection
Dependency Injection is the mechanism Spring uses to implement IoC. Instead
of a class creating its own dependencies, Spring injects them from outside.
This means the class only declares what it needs, and Spring provides it
automatically at runtime using reflection.

## 5. Demo Code for Dependency Injection
See screenshot in project.

## 6. Different Types of Dependency Injection
There are three types of dependency injection in Spring. Constructor injection
passes dependencies through the constructor. Setter injection passes
dependencies through setter methods. Field injection injects dependencies
directly into fields using the @Autowired annotation.

## 7. Pros and Cons of Each Type

Constructor Injection is the most recommended approach. Dependencies are
mandatory and cannot be null, supports final fields for immutability, and
makes the code easy to test. The only downside is that having too many
constructor parameters may indicate the class has too many responsibilities.

Setter Injection is suitable for optional dependencies. It allows changing
dependencies after object creation. The downside is that the object may be
in an incomplete state if not all setters are called before use.

Field Injection is the simplest to write but not recommended. It uses
reflection which has performance overhead, makes testing difficult because
you cannot inject dependencies without a Spring context, and hides the
dependencies making the code harder to understand.

## 8. @Component vs @Bean
@Component is a class-level annotation that tells Spring to automatically
detect and register the class as a Bean during component scanning. @Bean is
a method-level annotation used inside a @Configuration class to manually
define and configure a Bean. Use @Component for your own classes and @Bean
when you need to configure third-party classes that you cannot modify.

## 9. What is @Configuration and @ComponentScan
@Configuration marks a class as a source of Bean definitions, similar to an
XML configuration file. Methods inside it annotated with @Bean will be
registered as Beans in the IoC container. @ComponentScan tells Spring which
packages to scan for classes annotated with @Component, @Service, @Repository,
and @Controller. Spring Boot automatically applies @ComponentScan to the
package of the main application class.

## 10. @Controller vs @RestController
@Controller marks a class as a web controller that handles HTTP requests and
returns a view name to be rendered as HTML. @RestController is a combination
of @Controller and @ResponseBody, meaning every method automatically
serializes the return value to JSON and writes it directly to the HTTP
response body. In modern RESTful API development, @RestController is always
used instead of @Controller.

## 11. @Controller vs @Service vs @Repository
All three are specializations of @Component and register the class as a
Spring Bean. @Controller is used in the presentation layer to handle HTTP
requests. @Service is used in the business logic layer to process data.
@Repository is used in the data access layer to interact with the database.
They are functionally similar but provide semantic clarity and allow Spring
to apply layer-specific behaviors, for example @Repository automatically
translates database exceptions into Spring exceptions.

## 12. Spring Bean Scope
Spring supports five bean scopes. Singleton creates one shared instance for
the entire application. Prototype creates a new instance every time the Bean
is requested. Request creates a new instance for each HTTP request. Session
creates a new instance for each user session. Application creates one instance
per Servlet context shared across all sessions.

## 13. Singleton vs Prototype
Singleton scope creates only one instance that is shared across the entire
application. It is suitable for stateless Beans like services and repositories.
Prototype scope creates a new instance every time the Bean is requested from
the container. It is suitable for stateful Beans that hold temporary data
specific to a single use, such as a shopping cart or a form processing object.

## 14. Use Cases for Each Scope

Singleton use cases: a service class that handles business logic and has no
state, a repository class that connects to the database, and a configuration
class that holds application-wide settings.

Prototype use cases: a shopping cart object that holds items for a single
checkout process, a PDF report generator that builds a unique document each
time, and a form object that collects user input for a single submission.

Request use cases: storing the authenticated user information for a single
HTTP request, tracking request-specific metadata like request ID or timestamp,
and holding temporary validation results for a single request.

Session use cases: storing logged-in user profile information throughout their
session, keeping track of user preferences like language or theme, and
maintaining a multi-step form wizard where data persists across multiple pages.

## 15. Session vs Cookie
Cookie is a small piece of data stored on the client side in the browser.
It is sent with every HTTP request automatically and can persist even after
the browser is closed depending on the expiry setting. Session is stored on
the server side and identified by a session ID that is usually kept in a
cookie. Session data is more secure because the actual data never leaves the
server. The main difference is that cookies store data on the client and are
less secure, while sessions store data on the server and are more secure but
consume server memory.