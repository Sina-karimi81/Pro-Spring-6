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
* in setter injection the dependencies are injected using the setter methods instead of contructor and component can be created without an issue. this approach allows for the depencdencies to be injected later or the component can provide a default value for them and if later a dependency was added to the container it can replace the default. the @Autowired annotations must be used on the desired setter methods<br />
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
* also a @Resource annotation can be used as well which another version of @Autowired. nstead of using @Autowired and @Qualifier we can use @Resource(name = "someBeanName") to get the desired collection bean (or any other bean)
* we can also use *Method Injection* which is yet another way of injecting components with their desired dependncies. this injection method comes in two forms: 1- Lookup Method Injection 2- Method Replacement
* Lookup method injection is another way of obtaining dependencies
* Method replacement allow us to replace the implementation of any method method of any bean we desire without changing the original source code
* both these mechanisms use the powerful CGLIB library (you can read more about this library in it's git repo)
* as you know spring creates a single instance of any bean (singleton instance) and where that bean is needed the same instance is injected. Lookup method injection is used in scenarios where a singleton bean is depending on a non-singleton bean and it needs a new instance each time the dependency is used
* this works by having our singleton bean define a method (which is the lookup method) which returns an instance of the non-singleton bean. after that when you obtain a reference to the singleton, it is actually a dynamically created sublcass of the singleton bean that spring has created using CGLIB library and also implemented the lookup method. to avoid confusion most implementations use an abstract class and method so spring creates a subclass itself and there is no need for the user to check for everything
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/d8e613b9-eda6-422e-bd26-a94140a499eb)
* the *getMyKeyOpener()* method does the actual lookup and as you can see the *openLock()* method uses it directly to get a new instance each time it is called
* the @Scope annotation is used here to tell spring that it requires a new instance of this bean each time it is called upon in a component (scopes will discussed later)
* by usign the @Lookup annotation we tell spring which method to override. this method must not accept any arguments adn it must return the bean you want or any of it's subtypes
* lookup method injection is used in scenarios when we work with two beans of different lifecycles and it should be avoided otherwise since it bring a massive performance overhead with itself. it is also advised not to use this metod needlessly even if we have two beans of different lifecycles. so thisnk really hard and carefully when you want to use this mechanism
* Spring supports a complex bean naming mechanism to allow the flexibility to handle any situation. every bean must have at least one unique name in the application context
* if there are multiple bean of the same type with the same name spring will issue a NoSuchBeanDefinitionException durin context initialization
* beans that are craeted using @Component and @ComponentScan are named after their respective class name. for example a class with the name of SimpleBean that is annotated with @Component will have the name simpleBean in the context (the goes for classes annotated with @Configuration)
* as with most things in spring, if it has a default then it can be customized. buy extending an implementation of BeanNameGenerator interface we can create our own name generator for beans
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/b117a2c4-6c0b-4f13-9272-47584dd63660)
* the AnnotationBeanNameGenerator is an implementation of BeanNameGenerator that works for classes annotated with @Component and it's subsidiaries. it is intersting to know that the BeanNamingCfg class doesn't use the SimpleBeanNameGenerator since it is not discovered during component scanning
* for beans that are created using @Bean in configuration classes the name of the method that is used to get them is used as their name. in the previous code snippet, anotherSimpleBean is used as the name of the bean that is configured by that method
* in situations where the are multiple beans of the same type with different names, bean names must be used in order the get a bean from context otherwise it will issue a NoUniqueBeanDefinitionException
* when using either @Component and it's subsidiaries or @Bean, we can use their *value* attribute to explicitly name a bean. since this attribute is marked as default we can pass it values without using the name of the attribute
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/f89eb5b0-a45f-4832-94e8-ac58817bd5bf)
* in the above code snippet we can that a list of names is used for a bean. the first element is used as the default name and the rest are aliases for that bean which can be used to retrieve the bean
* speaking of aliases, the @AliasFor annotations in spring is used to declare aliases for annotation attributes and most spring annotations use it. this mean that attributes marked with this annotations can be used interchangeably
* another usage of this annotation is for the attributes of meta-annotation
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/2c417c6e-f286-449a-abb7-50d0ed67c40e)
* this means that using @Award and its prize attribute is the same as using @Trophy and its name attribute
* as we said earlier we are going to talk about bean scopes
* as a default behavior all spring beans are singleton. meaning a singe instance of them is created and used everywhere. but if for some reason we decide that we don't need this behavior and need a newe instance everytime for different need we change it without impacting application code that is why spring can also be referred to as instantiation mode agnostic
* in general singleton should be used when:
    - *shared object with no state*: an object that has no state can have a single instance and doesn't need a new instance for each dependent
    - *shared object with read-only state*: same as previous point. since it is only being read from it doesn't new a instance
    - *shared object with shared state*: if a bean has a state that must be shared it can be singleton but we should make sure that our synchronization for state writes are as granular as possible
    - *high throughput objects with writable state*: when we have a bean that is used a great deal we may find to have better performance to have a single instance and making sure that our synchronization for state writes are as granular as possible than creating a new instance constantly. this is particularly useful when our application creates a large number of objects or instantiation is expensive or we have only few writable states
* in general non-singleton should be used when:
    - *objects with writable state*: a bean that has lots of writable state may cause a greater cost of synchronization that creating a new instance for it
    - *objects with private state*: when we have a dependent object that requires private state to seperate itself from other dependent objects.
* bean scopes in spring include:
    - <b>singleton</b>: default scope, a single instance per container
    - <b>prototype</b>: a new instance will be created each it is requested by application
    - <b>request</b>: a new isntance is created for every http reqeust and then is destroyed when finished. only for web application
    - <b>session</b>: a new isntance is created for every http session and thes is destroyed when the session is closed. only for web application
    - <b>application</b>: a single instance of bean is created for the lifecycle of teh ServletContext. only for web application
    - <b>thread</b>: a new instance is created when requested by a new thread. the same instance is returned for the same thread. not the default behavior
    - <b>websocket</b>: a single instance of bean is created for the lifecycle of teh WebSocket. only for web application
    - <b>custom</b>: a custom scope that can be created by implementing the Scope interface and registering it in a configuration class
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/495e416a-8cb9-48a3-99f3-d07ce22c4f71)
