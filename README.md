# Pro-Spring-6
A Repository Containing a Summary of Pro Spring 6 book

## Introducing IoC and DI in Spring
* Spring was built to be a dependency injection framework
* DI means that components that require a dependency delegaet the dependency resolution to an external service that will inject the dependencies
* the dependent component doesn't have a say in the dependencies that are injected, hence we have Inversion of Control
* IoC offers a simpler mechanism of providing dependencies for components and managing said dependencies throughout their life cycle
* IoC has two sub types: 1- dependency injection 2- dependency lookup
* in dependnecy look up a component must acquire a reference to the dependency while in dependency inejection the dependency is injected by IoC container
* dependency lookup comes in: 1- dependency pull 2- contextualized dependency lookup, and dependency injection comes in: 1- contructor dependency injection 2- setter dependency injection
* for dependency pull we get the required dependency by using the context directly
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/dc160904-1061-4628-af9d-860bab9fa1e9)
* for contextualized dependency lookup the component itself by accessing the container directly and not using some central regsitery like the context object tah we used earlier, the component does this by implementing ManagedComponent interface and performLookUp method. the cotainer calls every this method for every component tha implements it and provides the dependency
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/7f6a699a-9640-4de0-8daf-83406133dcd0)
* in contructor dependency injection, the container injects the dependencies via the contructor when instantiating the component. because the contructor is used the component enforces that the dependencies should always be injected since without the the component cannot be created and enables the creation of immutable objects. if there are multiple contrcutors the desired one should be marked with @Autowired annotation else it is optional
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/8314814a-f4ad-46a2-b924-77ac8a433911)
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/53e12433-0a6f-476e-9945-83056c2302c4)
* in setter injection the dependencies are injected using the setter methods instead of contructor and component can be created without an issue. this approach allows for the depencdencies to be injected later or the component can provide a default value for them and if later a dependency was added to the container it can replace the default. the @Autowired annotations must be used on the desired setter methods
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/edaeee87-13b4-4175-8e09-424c96d0f026)
* it is worth mentioning that setter injection enables us to use and interface as input but it should be used in situation where only a specific implementation is used by the components that have that dependency
* generally injection is favored over lookup since it has substantially less code and decouples the application from the IoC container
* in some cases it is required to use lookups to generate the initial components and dependencies and then let the container take care of the other injections
* the central point of Spring IoC container is the BeanFactory interface and it's implementations.
* in spring the term *"Bean"*  is used for any object that is managed by Spring container. the Spting container creates, configures and manages beans throughout their lifeCycles
* the ApplicationContext inteface is an extention of BeanFActory and provide other services alongside  DI services such as: 1- integration with Spring AOP 2- Application event handling 3- application layer-specific context 4- resource handlign for i18n
* beans can be configured using annotations and XML file but since spring 4 using annotations is recommended because the configuration is code based and easier to understand
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/38bd16de-a0c5-446b-bf4b-26f4a6ee65c0)
* the @Configuration tells Spring that this class is a which contains methods annotated with @Bean and it can also look for other beans using @ComponentScan.
* anotehr way to declare beans is to use classes annotated with @component and it's derivitives @Service and @Repository. @Component is used for simple Beans, @Service for bussiness beans that provide functionality and @Repository for beans that communicate with database. nonetheless the can be used iterchangeably since they are all recognized as beans
* the @ComponentScan can be configured to scan specific packages or classes if not it scan the package which it is a part of
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/536f8d76-3428-4e19-9e1b-c91906d4f430)
* there is another way of injecting beans and that is field injection where the dependency is injected directly into the field of a class (Instance variable) using reflection hence no need for contructor(s) and setters
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/2c322fd5-6138-47c9-af38-3faff541e73d)
* however this approach is not recommended:
  - *A risk of violating Single Responsibility Principle*: since there might be to many dependencyies nad this way it is harder to see it
  - *Dependency hiding*: using field injection it can become unclear what type of dependency to use or if it is mandatory (i.e in my exprience it is generally a mandatory dependency and unless there are multiple implementations which all are beans no confusion occurs)
  - *Dependency on Spring IoC*: we become dependennt to spring contaier and it's @Autowired annotation thus the bean cannot be instatiated independently
  - no field injection for final fields
  - difficulties in writing tests
* there is a intersting scenario which the dependnecies of a component are not other complex types and bean, they are simple data types such as Strings , Integers , Longs , etc. this is were we use @Value annotation to specify the value that should be injected
* the values can be simple constant values. the spring container can convert these values to any data type
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/5210d674-7fa2-456f-b454-28afd8eba0b6)
* or they can be configurable values that can be read from other objects or .properties files using SpEL (Spring Expression Language)
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/e11c3f80-e059-4a35-bc73-452894304085)
* we can even inject lists as well but there are some intersting edge cases
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/73086560-8e1f-45bd-978c-c846e6fadac4)
* the @Autowired annotations is symantically defined in a way that if it is placed on a collection of bean, if there exists single bean definitions of that type it fills the collections with those beans instead of the bean that was defined as a collection or to put it in other words treats them as a set of corresponding beans
* here is where we should use @Qualifier annotation to specify which bean should be used using the specifc bean's name
