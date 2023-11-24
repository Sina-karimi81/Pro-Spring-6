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
* we go on to or next subject wich is resolving dependencies.
* spring is able to resolve any dependencies on it's own by looking and your configuration files or classes but if there is a situation where a component doesn't define a dependency through configuration files and attempts to get it directly from context, it may create this component ahead of the required dependency
* the solution here is to the @DependsOn annotation in our component to tell spring to create the dependency first.
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/85268415-d1cf-40b9-8e2e-9b813c64b528)
* if you notice you can see that the Singer class is impelementing the ApplicationContextAware interface which forces the class to implement a setter method by which spring injects an instance of the context into class so the class can access the context to get it's dependency
* this approach is to be avoided however, instead our class heirerachy should be designed well enough so that spring can detect these relationships implicity and doesn't need to be told explicity which components depend on which (spring manages a dependency graph internally)
* this approach is mostly suitable when working with legacy code
* next we go on to how to autowire our beans
* spring does so in 5 manners:
    - <b>byName</b>: spring attempts tp match each dependency using their respective name i.e using @Qualifier annotation ofcourse if it exists in ApplicationContext
    - <b>byType</b>: spring does this automatically where it matches dependencies using thair types if a bean of the same type exists in ApplicationContext i.e setter injection or field injection
    - <b>constructor</b>: just like "byType" but spring uses the constructor instead of setter or field injection. spring attempts to match to the constrcutor with greater number of arguments if it is not explicitly specified. for example if you have a String and Integer dependnecy and have two constructors which one takes one String argument and the other takes a String and Integer, spring uses the latter. (based on my searches if opposite of this were to happen, meaning that i have one dependnecy defined for a component for example of type Foo and i have two constructors one that accepts Foo and another that accepts Foo and Integer, spring will use the second one and if it cannot find any value for the second parameter by looking through context and configurations it will pass null for that argument so be careful in these scenarios)
    - <b>default</b>: spring will alternate between *constrcutor* and *byType* modes. if you have a default constructor (no arg) then *byType* is used otherwise *constructor*
    - <b>no</b>: no autowiring
* as i mentioned if a constrcutor is explicitly marked using @Autowired, spring will use the one that is the most "suitable" (take it with a grain of salt) if there are multiple suitable constructors the default constructor is used. if there is non a BeanInstantiationException is thrown<br />
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/ec742511-e57d-48ca-a210-3ee5e7a3824a)
* in this example the no arg constructor is called, and if it is removed we will get an exception from spring so we should annotate a constructor that suites us by @Autowired. becareful not two annotate two constrcutors the compiler won't care about it but it will confuse spring (feel free to try it yourself)
* if we are not using constrcutors and using setters annotated with @Autowired or field injection then it is byType inejction
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/1334a49f-ed38-48f9-a3ce-47aa9fa235d9)
* however if we have multiple beans of the same type the context (either defining the same twice with different names or multiple implementations of an interface) then spring cannot create the component since it is matching them by their type and there are multiple of the same type in context and we'll get NoUniqueBeanDefinitionException
* that is where we can use the *byName* autowiring type
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/a15b9674-d94a-4a43-adf1-a20cba91236e)

## Advanced Spring Configuration and Spring Boot
* in this chapter we are going to learn about managing bean lifecycle, making beans spring aware, Factory Beans, PropertyEditor Implementations, application context, testing spring applications, using spring boot and finally using configuration enhancements
### Spring's mpact on application portability
* most of the features we tyalk about in here are specific to spring and not available on other IoC containers
* as with many other things, there tradeoffs with using spring specific features (coupling to spring) or making our application portable with other IoC containers
* just be careful not to restrict yourself to be portable when there is no requirement for it otherwise it would put you in a disadvantage from the start
### Bean Life-Cycle Management
* in general in any IoC containe , beans are created in such way that they can be notified by the container in certain stages of their life cycle.
* in general there are two life cycle events that are relevant to a bean:
    - post initialization: after a bea has bean created and all the dependencies are resolved
    - pre destrcution: right before a bean is destroyed by spring
* however in spring, for a bean of prototype scope the pre destruction event is not fired
* spring provides three ways for a bean to hook into these events
    - interface based: a bean implements the interface required to use the callback method for the event to be triggered
    - method based: spring lets us specify a methodd to be used when these events are triggered. these methods are specified in our configuration for beans
    - annotation based: a method is marked with annotations related to post initialization or pre destruction phase
* these approaches have the same result however they differ in requirement and where to use them
* for example if you are coupled with spring or have many classes of the same type (i.e implementing the same interface or extending the same class) using the interface based approach may be more suitable where you are implement the method once and be done with it
### Hooking into Bean Creation
* we can do anything in the initialization callback but usually it is used to check whether the required dependencies were provided correctly or not
* because spring resolves dependencies using the contrcutor or setters those are not suitable places to check validity hence using initialization callback that is called after the dependencies are resolved
* the first appraoch we are going to discuss is the method based approach
* it is particaularly useful when we have few beans of the same type or trying to decouple or application from spring or working with beans that were already defined or provided by a third party
* all we have to do is to specifiy a method name in the initMethod of the @Bean
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/1ad0a16e-c785-47dc-a973-01c2fe68fe59)
* the only contraint on our init method is that it cannot accept inputs
* the benefits of this apraoched are negetaed when using static initialiaztion method because a beans state cannot be accessed to validate it
* the second appraoch is using the interface provided by spring called the *InitializationBean*
* still used to validate our dependencies, this interface provides a single method named *afterSetProperties()* which again works exactly like the init() method we used in the last approach
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/eb7f1e78-d0c3-4b3c-b9cc-942edc5860f4)
* the third approach is ti use the *@PostConstruct* annotation to mark the desired method
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/8c9f9e0c-1832-4ee7-bd48-34b12b9c5431)
* each approach has it is own drawbacks and benefits
    - using the method based has the benefit of decoupling our application from spring so it can be portable but we have to specify it for every bean
    - using the iterface, we define the mehtod once for all instances of the bean and reduces the amount of configuration and probability of errors but we are coupled to spring
    - using the annotation, we still have the portability option but the IoC container must upport JSR-250
* if we use all of the at the same time there is an order to them. first the bean is created and it's dependencies are resolved, then the @PostConstrcut method is called then the afterSetProperties() method and finally the method specified in the @Bean
### Hooking into Bean Destrcution
* we can call the *destroySingletons()* of ApplicationContext to destroy all the singleton bean (obviously...)
* typically this is done when our application is shutting down and allows us to shut down gracefully and close any resouces our beans are using or flush them to a database or terminate lingering threads
* the mechanisms used here are similar to the initialization mechanisms and are often used in conjunction with them. this enables us to configure or open a resource in initilize callback and then close it in destruction callback
* the first mechanism is to give the name of the desired method to the destroyMethod attribute of the @Bean
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/ef75124f-f024-405b-beab-d7ba35bf3658)
* the other mechanism is to implement the DisposableBean interface which bring a destroy() method with itself to our bean 
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/dec78665-2332-4ece-aab7-08b72fefb947)
* and finally using the @PreDestroy annotation
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/1b5df71e-d6d7-4458-ab0c-b4d7edfd7aaa)
* the usages and order of execution are the same as initialization callbacks
* the only drawback of destruction callbacks in spring is that they are not fired automatically and we should call the *ctx.close()* before our application is close however it is not that easy in a real application
* java allows us to create a shut down hook which is a thread that runs before the application shuts down. to do this we can use the *ctx.registerShutdownHook()* method and instead of *ctx.close()*
### Making Your Beans "Spring Aware"
* some times we may need to access the application context through our beans for requirement. for example a bean that registers a shutdown hook or a bean that needs to know it name
* it is worth saying that giving a bean name business logic is generally a bad idea and should be avoided but in certain situations such logging when there are multiple instances of the same using the bean name is a good idea
* by using the BeanNameAware interface and it's *setBeanName()* method., spring calls this method after configuring the bean and before callbacks (initialization and destruction)
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/772dad44-e5d6-49c7-8f8e-5b2e26c04a41)
* as mentioned earlier don't give bean namse business logic but if you need to do it use interfaces like Nameable and it's method *setName()* to give a bean a specific name using dependency injection.
* another way is to use the ApplicationContextAware interface and it's setApplicationContext method. by implementing this interface we can get a reference to the application context the manages a bean to get other bean (should be avoided if unneccessary) or register different hooks
### Use of FactoryBeans
* there are situation where we cannot create objects usign the new operator for example static factory methods. this brings up and issue in creating and injecting beans
* that why sprig provides FactoryBean interface to wrk as an adapter between these objects and spring beans. it's implmentations work as facory for other beans
* FactoryBeans aare configured like any other bean but when supllying a bean request spring calls the FactoryBean.getObject() method automatically to provide the desired dependency
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/b9fbfcab-8617-4b6d-9332-715f9013f463)
* the getObject() method returns the desired object, the getObjectType() method returns the type of the object return by getObject() for autowiring purposes but can return null if it's not know or know after the initialization of FactorBean, the isSingleton() return the scope of the bean returned by getObject(). if you use @Scope on the FactoryBean configuration it represents the FactoryBean itself not the object it returns<br />
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/12b0e5cb-b06d-4b70-a374-1e4f442975ae)
* we can access the FactoryBean directly as well but it is not recommended since it is a supporting infrastructure and there wouldn't be a nedd to access it directly
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/80d96a88-a163-4bb1-840d-ef3f3010e60c)\
### Property Editors
* PropertyEditor is an interface that allows us to change the the value of a property from and to String
* this was orriginally used to input property values as string and then convert them to their correct type
* sprig allows us to implement this interface to convert string based properties to their correct type
* spring uses/provides many implementations of PropertEditor interface which leave to yourself to check out


