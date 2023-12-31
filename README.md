# Pro-Spring-6
A Repository Containing a Summary of Pro Spring 6 book. please beware that these are personal notes and understaing of the text so don't solely rely on them

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
### Spring's impact on application portability
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
* spring uses/provides many implementations of PropertEditor interface which I leave to yourself to check out
* however if none the basic implementations are good for your situation you create your custom PropertyEditor by implementing this interface or Since JDK 5 implementing PropertyEditorSupport interface that has a single method called setAsText()
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/47958626-f608-4ef3-b150-0c009d11b7c2)
* as you can see a map is used to register this editor where the key is the class or type we want to convert the text to
### Internationalization
* spring excels at supporting i18n (aka Internationalization) by using the MessageResource interface we can access string resources called messages stored in different languages
* ApplicationContext class implements this interface and while it is not needed to use ApplicationContext as a messageResource it is good to know about
* in any environment, the messages are loaded automatically by spring but automatic access is only available when using sprin MVC to build webapps
* spring provide 3 MessageResource implementations:
    - StaticMessageSorce which should not be used in production since it cannot be configured externally
    - ResourceBundleMessageSource which uses ResourceBundles in java to load messages
    - ReloadableResourceBundleMessageSource which like the ResourceBundleMessageSource but it has built in schedulers that reload the resource files
* each of these implementations also implement HierarcalMessageSource which enable nesting of message resources
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/6d437298-8553-4650-9d1f-50010a49258d)
* note that the bean defined for the message source must be named messageSource()
* the baseName property defines the files and the locale defines which file to choose from for example for baseName of Foo and different locales we have: 1- Foo_en_Us.properties 2- Foo_fa_PR
* you may ask why use the applicationContext as a message resource in webapps? well when using sprin MVC, spring accesses ApplicationContext from ApplicationObjectSupport class, wraps it (ApplicationContext) in a MessageResourceAccessor object and then the MessageResourceAccessor is used to work with message resources
* the other reason is that spring exposes the ApplicationContext to the view tier (the view part of the MVC model) so when message are accessed from JSP tags they are read automatically from ApplicationContext
* for using message source in stand alone apps we can use the MessageSourceResolvable interface and dependency injection
### Event Publication
* ApplicationContext can also work as a broker and publish events
* and event is a class that extends ApplicationEvent class
* any bean that imeplements the ApplicationListener<T> interface is automatically registered as a listener for events by spring where T is the type of message it receives
* events are published using hte ApplicationEventPublisher.publishEvent() so the publishing class must have knowledge of ApplicationContext (which extends ApplicationEventPublisher)
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/4e791f6e-24f8-47b0-8624-2672d92ef4df)
* when an event is raised the onApplicatioEvent() method is called by spring
* typically w euse events to execute logic that should be done quickly and outside of the normal flow of the program. for example to process of invalidating the data inside a cache where the component responsible for updating data notifies the cahce that some data is invalidated
* for long running processes using using JMS or RabbitMQ or Kafka is a better solution
### Accessing Resources
* often we need to access some properties that are stored in files or jar files in classpath or in a remote server
* to access these properties, spring has a Resource interface with 3 implementations: 1-FileSystemResource 2-ClassPathResource 3- UrlResource
* spring uses another interface called ResourceLoader and its implementations DefaultResourceLoader and ApplicationContext to locate and create Resource instances
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/e35efffa-f44a-447b-9e92-2f382e9fa06e)
* note that spring treats both file: and http: as UrlResource but you can use FileSystemResource if you want to
### Advanced JAva COnfiguration Classes
* look at the code below
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/66d31822-569f-4c80-98ee-53d72c6efa93)
* you notice that a @PropertySource annotation is used to access a properties file. this annotation is used to load properties files into spring ApplicationContext which accepts location as it's argument. several locations can be provided
* also if we have multiple configuration classes we can use @Import annotation to import other configuration classes inside another configuration class
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/bce9973f-abf9-4e04-a3cd-36aacf97a7fd)
### Profiles
* a profile instructs spring to configure the ApplicationContext in a specific way
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/70d1f528-08bf-4797-bf0e-e9c75054629b)
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/543a5883-f1e5-4172-b6d2-e89fb7543aa0)
* in the code snippets above we have two implementation of an interface in different packages with different profiles set for each
* by passing "-Dspring,profiles.active=..." to the JVM when running our stand alone spring app we can tell which profile to use to configure the context. in this example by passing the value "highschool" that implementation is loaded and by passing the "kindergarden" the implementation is loaded into context<br />
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/d1b3136f-3e25-43fd-8aa9-740fee763473)
* in the code snippet above the active profile can be either set from JVM argument like i mentioned before or it can be read from environement variables or .properties files
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/2b1fbff1-3fb8-4ab1-ab55-611611aa1a17)
* in this one the context is first created empty and after the profile is read it is filled by configuration classes and afterwards it is filled by the desired bean
* this mechanism enables developers to manage application runtime environment and configuration and it used to be done by build tools that packaged the profile alongside the JAR or WAR files which meant each environment had it own packeged files
* but by using this mechanism we can create multiple profiles and have it be set according to the desired environment
* but it has drawbacks like bundling this data would be error prone and making out packages a little bit bigger
### Environment and PropertSource Abstraction
* the Environment Bean (ctx.getEnvironment()) we saw earlier doesn't just control the profile but also the properties that hold the environment configuration
* by using hte abstraction we can access all the environment , system and application properties which are populated when ApplicationContext is bootstrapped
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/7f859d5f-0166-4fac-8c29-210de8d9eb3d)
* we put the properties in a map and then in a MapPropertySource implementaion of PropertySource which reads the key/value pairs and the put inside a MutablePropertySources which is a default implmentation of PropertySources and enables manipulation of existing propertSources
* spring accesses the properties in following order: 1- system properties for the running JVM 2- Environment variables 3- application defined properties
* spring environment is seperate from system properties and contains all the properties that were mentioned above. when we call System.getProperty() only the system properties are checked but when we call env.getProperty() all three property environment are checked in the mentioned precedence. so if a property is set in a higher level that will be shown
* however if we use the addFirst() method opposite to addLast() method this precedence will change when calling env.getProperty()
* as a side note we can read a property using the @Value(${property.name}) and inject to a variable or use @PropertySource on a config class, autowire an Environment object and pass it the required properties
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/83b422cd-cb80-4022-9eed-9dc6f9ce69bf)
### Testing Spring Applications
* testing is a prowerful practice and tool that enables us to ensure that our code works the way it was inteded to. when we are chaning our code it is the only that gives us the high enough confidence that our code is still the working the way it should after these changes
* what different kinds of test are and how to achieve TDD is out the scope of this book but it is encourged to read about them
* in here we are going to discuss how to test spring application
* spring offers a meriad of annotations that help us in testing our applications which we'll see the important ones during our time talking about testing
* by using the *spring-test* module alongside junit library (TestNG , Mockito , etc) we can test application's behavior
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/85ee407d-1090-4243-91ac-b20f6eb87964)
* notice that we used the Mockito framework to mock a behavoir of MessageProvider class ( Mockito is a great tool when you only want to test the code and don't need to actually connect to a database/server or read a property value and want to return a specific value from them )
* the ApplicationContext of test is apart from the ApplicationContext of the main program should it also should be created in our test classes
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/b731fb9b-2677-4f65-8f8c-282b2c4ca216)
* as you can see the test context is created manually by specifying the configuration files
* in order to access the beans we need and avoid duplicating the code above in every test method we can use the @BeforeAll annotation from jupiter library (aka Junit 5). when a test is run a method marked with this annotation is excecuted once but won't be excecuted other times the tests are run
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/28c4bea7-bdb7-4b2a-aa33-c3ac278cb9fb)
* but we can also avoid manually creating the ApplicationContext and use @ContextConfiguration and @ExtendWith annotations to create the context automatically or the get them both at once @SpringJunitConfig
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/e0e5a843-aed0-42c9-bcfe-86a5b52a6942)
* as you know we can use @Profile on a bean definition to make a bean active in a certain environment. other than that we can use profiles on test classes or methods to make them in active in certain environments. to make a class active for a specific profile we will use @ActiveProfile<br />
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/4d05faa6-6f7a-417e-bade-aae4057a0a0b)
* in the snippet above, the MessageProvider beans in configured to return a specific message in test profile and the test class is also configured to be run in test profile

## Spring AOP
* another one of spring's core feature is the support of AOP programming and implmenting cosscutting concers
* Crosscutting concerns are parts of a program that spread across other parts, making them hard to separate out. This can cause the same code to show up in many places and make different parts of the program too dependent on each other.
* by modularizing this pieces of code,known as concerns, by using AOP we can apply them to many parts of the program without any duplication and coupling
* Logging and security are often the main reasoons of using AOP
* it is worth mentioning that AOP and OOP comlete eachother and we cannot code an entire application using AOP
### AOP Concepts
* *joinpoints*: A joinpoint is a specific moment in your program, like when a method is called or when an object is created. It’s the place where we can add extra steps or actions in our program using AOP.
* *advice*: the code that is inserted by aop and executed at a particular joinpoint is called an advice
* *pointcut*: Think of a pointcut as a set of rules that decides which spots in your program, like when a method is called or an object is made (joinpoints), should get some additional instructions (advice). It’s like marking certain pages in a book where you want to add notes. By creating pointcuts, you basically organize where these notes (advice) should go in your app.
* *aspects*: An aspect is like a bundle that contains both the extra instructions (advice) and the rules for when to apply them (pointcuts), all wrapped up into a single class. This class tells your program what new actions to do and exactly when to do them.
* *weaving*: the process of combining the aspect and the code together. can be done at compile-time, run-time and load-time (by intercepting the JVM when its loading the classes)
* *target*: an object that is modified by an AOP process is called the target object
* *introduction*: this is a special kind of aop that enables us to add additional fields and methods to class without changing the class itself
### Types of AOP
* Static AOP: the weaving process is done as another step in building the application and the additional logic is added by modifying the bytecode. this is type has great performance because the end result is a java bytecode and is feature rich since it has been around for a long time but any changes to the aspect needs a recompile
* Dynamic AOP: the weaving process is done dynamically at runtime, different AOP providers implement it differently but in spring it is done by using proxies which we'll discuss a bit further. it lacks a bit in performance part but has the advantage of not needing a recompile after each change
* these two types often complement eachother and can be used together and not together. it is up to you
### Spring AOP Architecture
* Spring AOP can be split into two logical parts: 1- it's core programmatic functionality aka Spring AOP APi 2- a set of framework services that make AOP easier to use
* other components of spring such as Transaction management use AOP to simplfy development
* as we said spring uses proxies. an advised instance of a class is a result of ProxyFactory class creating a proxy instance of the target class that contains all the aspects weaven into it
* we can approach aspect creation programmatically using ProxyFactory class, or declarative proxy creation such as annotations and xml files and after a proxy is created, it is called instead and delegates the calls to the target object
* spring uses two proxy implementation: 1- JDK dynamic proxies which are used when the target object implements an interface because it only supports proxy creation for interfaces 2- CGLIB proxies which are used for concrete classes
* spring AOP only supports method invocation joinpoint which is the most useful
* in spring an aspect is represented by a class that implements the Advisor interface and spring provides many implementations and we can also create our own custom implementations
* there two other interfaces that extend Advisor: 1- PointcutAdvisor for implementations that use pointcuts 2- IntriductionAdvisor for classes that introduction applies
### ProxyFactory Class
* ProxyFactory class controls the weaving and proxy creation process. internally ProxyFactory deleggates teh proxy creation to an instance of DefaultAopProxyFactory class which in turn delegates to CGLIB or JDK dynamic
* we can set the target class using the setTarget() method and set advices using addAdvice() method. when we use the addAdvice() the given advices are appplied to all the methods of the target class
* if we need more control over the advice we can create an advisor and pass to the ProxyFactory using the addAdvisor() method
* there are ofcourse remove counterparts as removeAdvice() and removeAdvisor() and to check if a advice is passed before we use the adviceIncluded() method
* there are 6 advices in spring:
    - Before: applies logic before a joinpoint is executed. the advice has full access to the target of the method invocation and the arguments passed to the method (which it can change or throw an exception) as well. if an exception occurres in the advice the whole process is reverted and the method is not executed. one the most powerful advices which is normally used in application security processing
    - After returning: executed after a joinpoint has finished execution and returned a value.the advice has full access to the target of the method invocation and the arguments passed to the method and return value. if an exception occurres in the target method the advice is not executed and suprisingly we cannot modify the retun value
    - After: executed no matter the result of the target method and even if an exceptio happens
    - throws: executed after a method throws an exception, the advice has full access to the target of the method invocation and the arguments passed to the method and threw exception. we cannot ignore the exception that was thrown and return value instead. all we can do is to change the type of exception. this can be helpful in reclassifying our exception hierarchy and logging
    - Around: executed before and after a method invocation and we can control of the method is called at all or not. reflection is used to call the intercepted method. it enables us to modify the return value and prevent the execution of a method
    - Introduction: they are modeled as a special type of interceptors and we can specify teh implementation for methods that are being introduced as advice
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/e0efb416-2d0f-42e6-80e5-087f73a36947)
* this is an example of manually creating and adding advices
* beware of choosing the right and specific type of advice that you need and don't go overboard by using around advice all the time since it can be error prune and take more memory space
### Advisors and Pointcuts
* as we said earlier when we creat an aspect usign ProxyFactory class the advice is applied to all the methods which okay if we are usign the advice for logging purposes but not good if we want it to apply to specific methods only
* one way would be to check for the desired method in the advice, but this would be hard coding the methods and removes the element of reusability from the advice and the check that occurres each can become a performance issue
* by using pointcuts we can configure which methods the advice applies to and remove the hard coding part also when a method is called for the first time the applicability of advice is checked by the pointcut and the result is cached. other than this spring optimizes the unadvised methods when creating the proxies to have faster invocations
* there is term called target affinity which is used to describe the coupling and correctness between the advice and the target object
* in general if the target affinity is low enough we can use pointcuts (can be applied to a wide range of types) otherwise it is suited to hard code the logic
* to make pointcut we can implement the Pointcut interface that has two methods: getClassFilter(): ClassFilter and getMethodMatcher(): MethodMatcher , in most cases it is not neccessary since spring provides default implementations that we can use
* the getClassFilter() method checks whether the pointcut applies to the methods's class and the getMethodMatcher() checks whether the pointcut applies to the methods itself or not
* MethodMatcher interface has three methods: matches(Method , Class) , isRuntime() , matches(Method , Class , Object[]). isRuntime() checks whether MethodMatcher is dynamic or static which then determines which match() to use (as said before the results of all these are cached so not to be called again)
* when the pointcut is static the first matches() is used for every method of the target. if the pointcut is dynamic the first matches() is called to check the overall applicability, the second matches() is called for each invocation of the method to see if the pointcut should be applies or not based on the4 invocation
* obviously the static pointcut has better performance but in stuations where the adviec has a massive overhead it is better to use the dynamic pointcut
* spring provides many pointcut implementations:
    - AnnotationMatchingPointcut: looks for specific annotations on a class or method
    - AspectJExpressionPointcut: uses AspectJ weaver to evaluate a pointcut expression in aspectJ syntax\
    - Composablepointcut: used to compose two or more pointcuts together using operations such as union() and intersection()
    - ControlFlowPointcut: a special case pointcut that matches all the methods inside the controlFlow of another method, that is any method that is called directly or indirectly as the result of another method being called
    - DynamicMethodmatcherPointcut: used as the base class for building dynamic pointcuts
    - JdkRegexpMethodPointcut: allows for define pointcuts using regex expressions
    - NameMatchMethodPointcut: we can create a pointcut that performs a simple name check on a list of method names
    - StaticMethodMatcherPointcut: used as the base class for building static pointcuts
* for our examples we'll use the DefaultPointcutAdvisor which associates a single pointcut to a single advice
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/5bb167c8-cb26-473b-a28d-736b9816a2f6)
* in this example we made an around advice that only works on the sing() method of the GoodGuitarist class. the getClassFilter() retuns the GoodGuitarist.class so that the methods of this class are matched only and as you can see because both classes implement the same interface, proxies can be created based on the interface from a single DefaultPointcutAdvisor
* and now for the Dynamic pointcut creation
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/1682fa7e-6112-4027-94d2-5459478683c2)
* to use this pointcut we needed a method with arguments so we created a default method in the Singer interface as not to change the implementation of the classes
* we override the getClassFilter() same as before as to remove the checking of class the method-matcher methods
* it is true that the dynamic check is only one we nned but we still implement the static check method-matcher as well as to stop the dynamic check if the method signature (i.e the name in here) didn't match the desired method
* we configured the dynamic matcher method in a way that the method is advised if only the input us "C" so the first two invocations of sing() are advised in this example
* an intersting thing is that the sing(key) method is subject to two static checks. one for the initial phase when all methods are checkd and one for when it is first invoked
* sometimes we just want to match our pointcut given it's name and ignore all the other details. in these cases we can use _NameMatchMethodPointcut_ to mathc the given method name to a list of names.
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/8faf21f4-ec1f-49b0-8382-cccab53a00d0)
* as seen in the example above we created and instance of _NameMatchMethodPointcut_ (there is no need to subclass it) and given it a list of methods that we want to be advised. so both the sing() and rest() methods are advised but talk() is not
* for all _Pointcut_ that are implmented spring, spring also has a convenience advisor class as well that simplifies the advice creation
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/583f20e0-2b97-4bca-a3a1-b2f9c7ab746b)
* instead of using the _NameMatchMethodPointcut_ and _DefaultPointcutAdvisor_ we can the _NameMatchMethodPointcutAdvisor_ and use it's constructor and setmappedNames() method to pass the advice and the list of method names respectively
* however there are situations where we don't know all the details to a class like it's methods name or signature. here is where we can use regex and _JdkRegexpMethidPointcut_ class
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/ba31841a-1179-485c-a007-7ad784460058)
* here we have used a regex to mark every method that starts with a sing to be advised
* an intersting thing is that sring uses methods full name (i.e com.apress.prospring6.five.common.Guitarist.sing1) to match the methods to pointcuts which why there is a .* following the sides of the sing word in the regex input of setPattern() method (to be honest no developer, no matter how skiled and years of working can understand or write regex on their own!!! so i am only saying what the book says in this part)
* this is a powerful mechanism that allows us to mark the methods of a package that can be third party or legacy which we don't know much about as advised methods
* another way would be to use the AspectJ pointcut expression language which is similar to regex to mark our advised methods and _AspectJExpressionPointcut_ class which is the best way to go since it is very flexible and concise
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/bf6c68b0-ca4f-4271-a280-52e24bde4543)
* by using the srtExpression() method and passing in "excecution() * sing*(..))" we tell it to mark all the methods that start with the word sing (just regex, aspectJ expressions are things that you have to search all the time) have any argument and any return type
* so using AspectJ pointcut expresssion we can event filter the methods based on the arguments and return types which we couldn't do using regexes
* if we want to use a custom annotation to mark our methods we can use the _AnnotationMatchingPointcut_ class 
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/abce66f5-95a4-4ce9-9878-07595b5a685a)
* so in here we used the AnnotationMatchingPointcut.forMehtodAnnotation() method to give the annotations that mark the methods to advised. there is also a AnnotationMatchingPointcut.forClassAnnotation() that is used for annotations tha mark a class level
### Understanding Proxies
* we mentioned previously that spring uses JDK Proxy class and CGLIB to create proxies of the target class. now we are going to dive deeper and see what their difference actually is
* the core goal of proxy (read about the proxy design pattern!) is to intercept the calls that are made to the target object and if an advice is applied to them, pass them to spring AOP framework for the advices to be invoked (so the advices are managed and invoke by spring aop and proxies only intercept the calls and invoke the actual method after advice completion)
* proxies can be configured to expose themselves so we can retrieve and invoke their advised methods via the AopContext. the proxy class is responsible for this exposing. additionally all proxt classes implement the _Advised_ interface so the advice chain can be changed after the proxy is created and finally a proxy must make sure that if a method returns _this_( the proxy object itself) the actual proxy object is returned not the target object
* JDK proxy can only generate proxies from interfaces, so our target objec must imnplement at least one interface
* when using the JDK proxy, all the methods are intercepted JVM and passed to the invoke() method of the proxy object. invoke() checks whether the method is advised or not, if it is the advised are called and after that the actual method using reflection, if not the actual method using reflection
* as you may have noticed the JDK proxy doesn't make a distinction between advised and unadvised methods until they are inside the invoke() method. so there is a performance overhead
* in JDK proxies all the decisions on how to handle a method are handled at runtime but in CGLIB since it dynamically generates a bytrcode. (the created class is a subclass of the target object and CGLIB reuses code from the target whenever possible)
* when the CGLIB proxy is created, it asks spring how to handle the each method, this many of the decisions are handled only once contrary to JDK proxies
* since CGLIB generates bytecode it is more flexible on ow to handle each method, for example for unadvised methods, it generates appropriate code for them to be called directly, so reducing the overhead
* also they also determine whether it is possible for a method to return _this_, if not the method is allowed to be called directly
* CGLIB also handles fxed-advice chain (an advice chain that we guarantee is not going to change after proxy creation) differently and reducing their runtime overhead
* but poxies created by CGLIB have the limitations that normal subclasses do
* performance wise the difference between CGLIB and JDK proxy are not that much both for advised and unadvised methods. the only noticable difference is for when we use the frozen chain in CGLIB
### Advanced Use of Pointcuts
* ControlFlow Pointcut applies to all the method calls below a given method or below all methods in a class (inside the proxy object ofcourse, if the method is not part of the proxy object or the traget object for that matter, it will not be advised), basically when we want to advice a method if it was called by another method
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/d56bb3f0-dda8-4518-9d51-f215c962c6c0)
* in this example we are telling the ControlFlow Pointcut to advice all the methods that are called from the ControlFlowDemo.test() method
* notice that when we call the foo() method directly nothing happend but when it is called from the test() method it is advised
* ControlFlow Pointcut is extremely useful but it has a substantial performance overhead
* in our examples so far we haev advised only a single pointcut for a advisor which is good enough for most situations but there can be times where we have multiple pointcuts that we want to advice that's were we can use the Composable pointcut
* _ComposablePointcut_ has two methods: 1- union() (think of it as the OR operation like the ones in SQL) 2- intersection() (think of it as the AND operation like the ones in SQL), both these methods accept either an instance of ClassFilter, MethodMathcer or Pointcut interface.they add theire respective conditions to the call cahin for matching with joinpoints
* by default _ComposablePointcut_ is created to mathc all calsses and all methods but we can configure them and overridw the union() and intersection() to work with new classes and methods
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/cc59f17f-40de-48ae-8e95-f021e73b8733)
* at first our target object is advised for every method that start's with the words "si" using the _SingMethodMatcher_ class
* then it is unioned with the _TalkMethodMathcer_ class. this means that advise methods that either start woth "si" or have a signature of "talk"
* for the final part an intersection is used with _RestMethodMatcher_ class. this means that advise methods (that either start woth "si" or have a signature of "talk") and (methods that end with the words "st"). no mathod has both these conditions hence no method is advised
* the saame can be done for the ClassFilter as well
* there is another way ot create composite pointcuts and that is usng the _Pointcut_ class which bt itself provides the union() and intersection() methods and it provides a matches(Pointcut , Methhod , Class , Object[]) to check whether a pointcut mathces with the given arguments however it only supports these operations for only two pointcuts so if you need to combine _MethodMatcher_ and _ClassFilter_ with _Pointcut_, you should use _ComposablePointcut_
* we've seen all the pointcut implementations that spring offers, and if non of them suit your need you can always implements custom one. there are two patterns to combine pointcuts and advisors, we have seen and worked with the first pattern so far
* the second pattern which adopted by many spring documentation examples is to encapsulate the pointcut inside the Advisor implementation. this way we have a class thta implements both the _Pointcut_ and _PointcutAdvisor_ with _PointcutAdvisor.getPointcut()_ returning _this_
* the first pattern is more flexible however teh second pattern is useful when we want to use the same combination of pointcuts and advisors in different parts of the application and when each advisor must have a seperate intance of a pointcut
### Getting started with Introductions
* by using Introductions we can introduce new functionality to an existing class dynamically, in spring it is an implementation of any interface to an object
* this functionality is usually crosscutting and is not easily implemented usong traditional advices
* spring treat Introcutions as a special type of around advice and because they apply at the class level we cannot use pointcuts with them since introductions add the implementation of a class and pointcuts mark methods to be advised
* we can create an introduction by implementing the _IntroductionInterceptor_ interface which has a single invoke() method. since implementing methods in a single method can be error prune and difficult we opt to instead extend the _DelegatingIntroductionInterceptor_ class which is an implementation of _IntroductionInterceptor_
* our desired class, extend the _DelegatingIntroductionInterceptor_ class and implements the interface we want to introduce. the _DelegatingIntroductionInterceptor_ class then delegates all the calls to introduced methods to the corresponding method itself
* we add the Introduction by using the _ProxyFactory,addAdvisor()_ method
* as you know we can pass a single instance of advice to multiple classes this called per-class life cycle however this not true for introductions since they share state with classes are applied so every class or object must have a distinct instance of Introduciton. this is called oer-instance life cycle
* in our example we are going to implement an object modification detection framework. object modification detection is technique to make sure that the data we want to persist is actually modified and avoid unnecessary database access
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/8c2c5f2c-4b1b-4dd7-8d21-48ec17d1b45f)
* the tern **mixin** is referred to class that implements the desired interface and is introduced to the target objects
* another thing here is that the _IsModifiedMixin_ class has both method and state that it shares with the target object again noting why it is important to have different instances of Introductions
* we don't have to implement the invoke() method but in here it enables us to detect when a modification occurs.
* we only check when the object is not modified, then we check to see if the method is a setter method or not, if it is we retrieve the corresponding getter method and using that weh check whether the argument passed to the setter is different from the current value of the variable
* it is important to note that we should always call the super.invoke() when overriding invoke() since it is the _DelegatingIntroductionInterceptor_ class that handles the correct invocation
* another important note is that when we are creating the proxy we set the optimize flag (pf.setOptimize()) to true to force the use of CGLIB proxy. because if JDK proxy is used the resulting class in not an intance of the _Object_ class, in other words the interfaces are only implemented and it is not a subclass of _DelegatingIntroductionInterceptor_
* since there are no pointcuts used in junction with Introductions, all the methods of the proxy object are advised and this causes a performance overhead which can be neglected since we reduce the amount of code and the coupling needed to implemnent such logic
### Framework Services for AOP
* up until now we have been advising objects programmatically which is not bad by itself but it causes all the configuration to be hardcoded, luckily spring provides framework services that enables us to create advised proxies declaratively alongside using DI and AOP at the same time
* in this book we are going to work with _ProxyFactoryBean_ and @AspectJ-style annotations to achieve this, there is also an option of using spring aop namespace which we are not going to cover
#### ProxyFactoryBean
* the _ProxyFactoryBean_ class is an implementation of _FactoryBean_ that allows us to specify a target bean and provides a set advice and advisors for the target bean which are eventually merged into an AOP proxy
* this class applies interceptors before and after each method that is marked using the pointcuts that weconfigured in it to be executed before and after methods
* it also shares a common interface (_Advised_) with _ProxyFactory_ and as a result exposes many of the flags we've seen before e.g **frozen** , **optimize** , **exposeProxy**
* we simply define a bean to be passed as the proxy target, it is better to pass the target bean as anonymous object to prevent the application from accessing the unadvised object but there are always exceptions like the example we are going to provide
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/a61e5671-99f1-4c00-9279-f812784fed58)
* we create two prxoies, one will be advised directly so all methods are advised in the target bean and in two we will use _AspectJExpresssionPointcut_ and _DefaultPointcutAdvisor_ to only advice the sing() method
* the first thing to notice is that the pointcut is not a bean but a simple pojo that is set on the advisor bean since right now it is not needed to be shared
* the second one is that the prxoies created by the _ProxyFactoryBean_ are fulfilling the dependencies of our application and if you the unadvised object we need to go around the AOP context. although youre application must not know about the AOP context to begin with, that why i said earlier to pass the target bean as anonymous object
* we can also use _ProxyFactoryBean_ for Introductions and the rules are the same as I mentioned in the Introduction section
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/6f4a3f7c-90ae-4216-91d4-d437a19ca602)
* the example yields the same output as the previous example we used for Introduction
#### @AspectJ Style Annotations
* as of JDK 5 we can use annotations to advice our methods, however spring still uses its own proxying mechanisms for advising the target bean not AspectJ's weaving mechanism
* in this section we are going to implement the same examples using the AspectJ style annotations
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/9dfba5d0-08a0-4fa9-8cba-f434d40c5579)
* these are the beans we are going to use
* we are going to start with a simple Before advice. when using annotations we don't need to declare the pointcut since the annotation itself accepts the pointcut as an attribute
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/e6063ab3-2310-4aa4-ad36-5037ef358a47)
* this way we don't need to implement the _MethodBeforeAdvice_. another thing is the use of @Aspect to mark our class as an aspect class which groups together the advice declarations, pointcuts and other utilities
* the @Before annotation marks the method as a before advice which accepts a pointcut expression that means we want to advice all the methods that start with "sing", under the package of com.apress.prospring6.five( .. represents all the subpackages ). also the sing* should receive one argument of type Guitar
* the method accepts a _JoinPoint_ as an argument not the method, object or the arguments passed to the target method. we can use this class to access the information of the target. this argument is optional. if we define it for a method spring will autimatically pass to it the method
* we use the configuration class below to detect beans and classes marked with @Aspect
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/cc992aaf-b437-4817-82ff-c48aa664c88c)
* the @EnableAspectJAutoProxy annotation in configuration classes is what enables support for aspect classes, the proxyTargetClass flag indicates the use of CGLIB to create proxies or not
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/28da0ee5-0ca8-4a30-9dc0-1929e8c2aa52)
* the test method creates an application context based onf the configuration class and adds the aspect bean defined by the _BeforeAdviceV1_ class. if the context is created the execute method is runned and we can see the advised output
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/7c5f2a0a-0943-4539-acd0-d9bb7f86518e)
* in this version, the pointcut is declared using the @Pointcut annotation on a method that must return void and can accept arguments, then the method is used in the advice annotation. this helps with reusing the pointcuts over and over without code duplication. the output is the same as before.
* we can compose aspects together as well like the example below
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/62f3ccf4-449b-411e-86cf-2ffb89aa4601)
* here we've said that in addition to previous conditions, the target bean's name must start with "John". hence the new @Pointcut and the "&&" operator. the ouput is identical to previous examples
* we can also modify the advice to do some checks on the arguments of the method being advised aswell
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/3dd2895e-54b2-4adb-ab50-5b5a314d5114)
* notice the pointcut expression has the name of the arguments. it must have the same name as the method's arguments and the same order
* now we move on to around advice
* the annotation used is @Around and the method in works with has the type of _ProceedingJoinPoint_ since this type has to have the possibility of calling the actual target method
* this example simply prints a message before and after the method is called. everything else is the same as the @Before example we had before
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/869d1f56-60ed-4242-aab8-65f668b46a20)
* lets use the argument of the method aswell. to see the invocations we extend the _NewDocumentarist_ and change the brand of the guitar
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/ba3cd438-d7eb-4f29-bba0-b0ca2c7399d0)
* now let's go to After advice
* there are three annotations we can use for after advice
    - @After(finally): declares a advice that is executed regardless of the target method returning normally or throwing exception. typically used for releasing resources or sending notifications
    - @AfterReturning: declares an advice that is executed after the target method returns normally
    - @AfterThrowing: declares an advice that is executed only when the target method throws an exception
* we are going to need a new implementation of Singer interface
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/6e2c075b-88e2-45c0-9508-870a715a1267)
* we start with @After advice
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/fbeec2fb-b7d8-4bba-8990-a839598b1f9b)
* notice that the after advice has access to the argument of the target method and can make use of it but it cannot access the exception being thrown
* for the @AfterReturning and @AfterThrowing the code remains the same
* there is something to note and that is that @AfterThrowing can intercept the exception thrown by the target method and replace it with a exception of another type unlike the other two after advices
* @AfterThrowing advoce cannot prevent the exception from being thrown by the target method but it can replace the exception thrown by it
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/1186b7b5-30f3-437f-944b-641fe2a6a6fd)
* we can also decare introduction using annotations aswell
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/e88ed6f5-6b78-4bf2-a167-66233d63c49f)
* the interface to be implemented is determined by the type of the annotated field. teh calue attribute of the @DeclareParents is used to tell spring for what types the introduction must happen. any bean of matching type is wrapped in proxy that implements the _Performer_ interface abd introduces the behavior described by _Dancer_ class
#### Aspect Instatiation Models
* the @Aspect is not sufficient enough for auto detection, hence using the @Component. meaning the aspect classes are singleton beans
* but there may be a scenario where we need more than one aspect bean to be created, such as one per target. this can be done via configuration done through the attribute that the @aspect annotation provides. this attribute can be initialized with an AspectJ expression configuring how many aspect beans should be created and when
* ofcourse the spring configuration must be changed as well
* to create an aspect bean for each target bean, the @Aspect  should receive as a parameter a pertarget expression pointing at the type of the intended target beans
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/d5722bbf-db27-4cd8-a427-ad4ed8a787fd)
* an alternative is to use the perthis expression, pointing at the target method
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/97323fe5-8e5c-43da-b1d6-9c0a61f0fdf3)
* so you might ask what is the difference between **perthis** and **pertarget**? the difference is represented by teh object being examined when an advised joinpoint is reached. **pertarget** specifies a type expression, whihc means a new aspect is instantiated for every new object that is the target of an advice. **perthis** specifies a method expression which means a new aspect is instantiated foe every new object referenced by **this** at the target advice

## Spring Data Access with JDBC
* now we are going to turn an importatn aspect of every program: data persisting and retrieval. almost every program needs to persist data to some sort of data store and tyhe most usual one is a database
* in this chapter we will compare the traditional way of using JDBC and Spring JDBC, connecting to a databaseand how it is managed, retrieving and mapping records to java objects, CRUDs and testing JDBC code
* java applications and databases cannot communicate directly and they need a translator to that for them, which is a software called a driver
* there are three places where we might need a translator: 1- we ususally need the driver 2- if we want to introduce a persistence layer like hibernate we need two translators 3- if we add Spring Data to map records to POJOs we need three translators
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/014e87ad-340a-4c7b-aecc-b5effcec09bb)
* these two are the entities that we are going to work with
* we are going to start with a simple _SingerDAO_ (DAO means Data Access Object) to encapsulate all of the data access methods for singer
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/dbc558ef-1dde-4d66-af9e-6c25ef81d87f)
* a java application needs a connection instance to communicate with a database, the _CoreDAO_ interface is simple interface that groups methods related to connection management: getting a connection and closing a connection
### Exploring JDBC Infrastructure
* JDBC provide a standard way for java apps r oaccess data stored in a databse . the core of this infrasturcture is a driver that is specific to each database
* once a driver is loaded it registers itself with a _DriverManager_ class. this class manages a list of drivers and provides statis methods to get connections to the database using the drivers. the getConnection() method of this class returns a driver implemented _Connection_ interface which enables us to run SQL statements in the target database
* the JDBC framework is quite complex and it comes with some development difficulty. the first level of this complexity is to make sure that our code manages the connections. a connection is an expensive resource to establish which puts a strain on the underlying database, making it slow if there are multiple concurrent openm connections
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/697efaa2-065c-4565-8b98-6abe46ddc3a8)
* in our examples we are going to make a connection for every statement we are going to execute. this lacks performance but if we keep a connection open it will bring the database to a halt
* any JDBC driver that palys the role of translator between java and SQL database must implement the _java.sql.Connection_ interface
* to check if a driver is present in our classpath we can do the following
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/9cb9aa20-83c4-457e-97b5-4c33e078e7b7)
* here we are tying to see if we have a driver for MariaDB
* now we are going to see an implementation of the findAll() , insert(..) and delete(..) methods of SingerDAO
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/c3382afa-3edc-4c83-a7b0-85950306b8f6)
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/b5f88b8f-ca4f-46a5-a5db-4c47c745eb1a)
* you can clearly notice the amount of code needed to make sure a connection to the database can be used and checking the SQLException that might be thrown. in earlier version the _Connection_ interface didn't implement _AutoClosable_ so there would have been more boiler plate code and code duplication as well since there would have been a need for many helper methods leading to more bugs being introduced
* this is where DAO framework and spring come in
### Spring JDBC Infrastructure
* the spring jdbc support is devided in five packages:
    - jdbc.core: this package contains the foundations of JDBC classes in spring such as _JdbcTemplate_ class
    - jdbc.datasource: contains helper classes and DataSource implementations to that we can use to run JDBC code outside JEE container
    - jdbc.object: contains classes tha help convert the data returned from DB to objects or a list of objects
    - jdbc.support: contains the SQLException class for SQL to java translation support
    - jdbc.config: contains classes that support JDBC configuration within Spring's ApplicationContext
#### Database Connections and DataSources
* we can use spring to manage the database connection for us by providing a bean that implements _javax.sql.DataSource_ . the differnce between DataSource and Connection is that DataSource provides and manages connections
* _DriverManagerDataSource_ is the simplest _DataSource_ implementation. it simply calls the _DriverManager_ to get a connection. this class is mostly used for testing purposes. the configurations are usually read from a properties file for easy maintenance and different deployment environments
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/64c8cf84-2c22-459e-a11c-06ce2b32f4ed)
* the tesitng is easy aswell. you just need to create a application context based on the configuration class
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/bb363b31-8fca-4831-9bab-2f61706fc5c3)
* in real world applications we can use teh Apache Commons basicDataSource class or a DataSource implemented by the jEE application server (like JBoss, WildFly, ....). we can even use a DataSource in plain JDBC code, but in most cases we need a central place to configure teh DataSource. spring allows us to declare a DataSource bean and set teh connection properties in the application context definition files. (we can use the above mentioned implementations in a spring application managed app as well. e.g we can replace the _SimpleDriverDataSource_ with _BasicDataSource_ )
* another way to configure a DataSource bean is to use JNDI. it would be like this
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/98b2ee8c-a0e4-4e56-ab76-89512582a870)
* _JndiTemplate_ is a helper class that simplifies the JNDI operations
* spring allows us to configure teh DAtaSource in almost any way and hides the actual implementation or location of the data source from the rest of the application. the connection management is also delegated to the data source bean
#### Embedded Database Support
* from version 3.0 spring supports embedded databse support, whihc automatically runs an embedded databse and exposes it as a DataSource for the application. this is extremely useful for local developement and testing. in the code below we'll see a minimal configuration for embedded H2 database
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/daeffd49-1dfe-4c72-8187-d2cbcf835227)
* we gave it the database and the creation scripts in order of DDL scripts first and then DML scripts.
#### Using DataSources in DAO Classes
* the DAO pattern is used to seperate data accessing api from the business services and layers
* it consists of
    - DAO interface which defines the standard operations
    - DAO implementation which implements the defined operaitons
    - Model objects (aka entities) that map database record to objects
* we will create a _SingerDAO_
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/a9a5e5cd-f422-4a43-b50a-4796ee73da3f)
* the interface doesn't need to know how the data is updated or retrieved, so the datasource is added in the implementation rather than the interface. it also avoids declaring getters and setters for stubs
* now the the bean is created with the datasource property set to the datasource bean. to check it is set currectly we have used initializingBean and afterSetProperties() (refer to chapter 4)
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/04d87ef9-2a6c-4519-a39c-1504f7010a07)
#### Exception Handling
* spring advocates using runtime exceptions instead of checked exceptions so we need a mechanism to translate the checked _SQLException_ into a runtime exception
* Spring provides a default implementation (_SQLErrorCodeSQLExceptionTranslator_ class) of the _SQLExceptionTranslator_ interface, which translates the generic SQL error codes into Spring JDBC exceptions (ofcourse you can implement your own translator)
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/cacb4bde-e75c-4389-8497-2693f72efbbf)
* _DataAccessException_ is the root in the hierarchy of the data access runtime exceptions. extensions of this class provide info for real cause of an exception during data access
* to use the _MariaDBErrorCodestranslator_ we have to stop using connections and instead wrap the datasource in spring's _JdbcTemplate_
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/f1a7ea2e-99d9-4d6c-b944-3949f7487698)
* during execution of a statement if error code -12345 is encountered spring will use the transaltor we defined otherwise it  will use the default mechanism for translation. ofcourse we can create a bean of out translator and inject into a bean of _JdbcTemplate_
### The JdbcTemplate Class
* this class represents the core of spring JDBC and can execute all types of SQL statements and can return any type of result
#### initializing _JdbcTemplate_ in a DAO class
* queries we are going to use in from now on
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/f4bcfc0f-8f2f-4d78-8794-a0a75d973a85)
* this class needs to be created by passing the datasource object to it. it is generally a good practice to initializa teh JdbcTemplate within the same method where the data source object is injected by spring
* JdbcTemplate is alos thread safe, meaning it can be created once and injected in all DAO beans
* we'll refactor the _SingerDAO_ to use _JdbcTemplate_
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/38deb123-38d1-46b7-bd7a-0477f8ffc18f)
* we have used the queryForObject() to retrieve the full name for a specific id. first the SQL string is passed, the the type of returned data and finally the parameters to a query which is a vararg. the ? (called a placeholder) marks the parameters that is needed by the SQL query
* when using a placeholder the order of parameters is important and the order we put them in the varargs should be tha same as the palceholder
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/d7953576-d60d-4b4f-b7ba-837eda891b1b)
#### using Named Parameters with _NamedParameterJdbcTemplate_
* some develepors prefer to use a named paramters to specifically bound each parameter as intended. this is done through the _NamedParameterJdbcTemplate_ class an extension of _JdbcTemplate_ class
* the initialization is the same as _JdbcTemplate
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/fddb625a-8e11-4422-8f40-859a8fbc946d)
* instead of a placeholder the named parameter :singerId is used
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/84f4782d-430a-4a6b-8450-c404d28b3e93)
#### Retrieving Entity Objects with RowMapper<T>
* when a query returns a list of rows instead of a single value we have to use the _RowMapper<T>_ interface to map each row to a POJO
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/2c6f666a-b6f8-4d6b-b38b-7f3dfcf30105)
* the mapRow() method transforms the records inside the ResultSet to the entity that we want
* we have used a lambda expression to skip explicit implementation
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/084b512f-a77b-4508-8e35-9acb67610f7b)
#### Retrieving Nested Entity Objects with ResultSetExtractor
* now we are going to get the albums alongside the singers usign a join and have a entity accordingly (a Set<Album> inside Singer)
* for this we need to use the _ResultSetExtractor<T>_ interface
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/afe52df6-08a0-47d9-b1a5-872a73cf6d84)
* we need to implement the extracData() method. in here we have used a lambda expression to simplify the extraction process
* ![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/f5e20f64-5cc9-487d-8f70-88c804bdac3c)
####  Spring Classes That Model JDBC Operaions
* alongside the _JdbcTemplate_ spring provides classes that model JDBC data operations and lets us perform operations of converting ResultSet to entities in a more object-orineted fashion
* we are going to look at:
   - MappingSqlQuery<T> allows us to wrap the query and the mapRow() in a single class
   - SqlUpdate allows us to wrap any update SQL query inside it. provides many utility methods for binding parameters and retrieving the generated keys
   - BatchSqlUpdate allows us to perform batch updates
   - SqlFunction<T> allows us to call stored functions in a database with arguments and return types. the _StoredProcedure_ class does the same for procedures
   - setting up jdbc DAO using annotations
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/900c6456-362f-4657-8095-c0324776ceae)
* we have used @Repository to create a bean of this DAO cass. the _BasicDataSourceCfg_ class works with this bean. @Repository also instructs spring to perform databse specific SQL exceptions to the more application friendly DataAccessException hierarchy in spring
#### Querying Data Using _MappingSqlQuery<T>_
* with this class, we provide the datasource and the query, then we implement the mapRow() method to map each resultSet record to a domain object
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/9141f98f-cd2f-4d96-9944-dcee329bd5d7)
* we use the execute() method inside the findAll() method, which is indirectly inherited from the _SqlQuery<T>_ class. we also use the super() method in the constructor of _SelectAllSinger_ class to create it
* we can also pass parameters as well
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/ab29555b-37fc-4915-936d-8b49350c78a2)
* we have used declareParamter() Method which is indirectly inherited from _RdbmsOperation_ class. testing this class will be same as _SelectAllSinger_ class
#### Updating data by using _SqlUpdate_
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/74c7b28b-596e-4533-ad48-ab0cc8bbd1e1)
* everything here is similar to before, the updateByNameParam() method takes a map of parameter names and their values.
#### Inserting Data and Retrieving teh Genrated Key
* we can isnert data by implementing _SqlUpdate_ as well. the generated id after insertion  and we can retrieve it from the database
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/6c433027-dbf7-4444-b69a-0f998cd5dba3)
* the _InsertSinger_ class is mostly similar to it's update counterpart. we call the setGeneratedKeyColumnName() to give the name of the Primary Key column. the method setReturnGeneratedKeys() instructs the underlying JDBC driver to return the keys created for inserted records
* when calling the updateByNamedParam() method we pass an instance of _KeyHolder_ which will have the generated keys stored in it and we can then retrieve them from it
#### Batch operations with _BatchSqlUpdate_
* for batch operations we use the _BatchSqlUpdate_ class
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/7aeb4802-06da-486e-b78d-084c475ccced)
* notice that we use setBatchSize() method to set the batch size (dah!! :) ) for the JDBC insert operation
* each time we call the insertWithAlbum() method, a new instance of _InsertSingerAlbum_ is create because _BatchSqlUpdate_ is not thread safe. _BatchSqlUpdate_ will queue up the insert operations until they reach the batch size and then inserts them in a batch. after completion we call the flush() method to execute the insert operations that haven't reached the queue size
#### Calling Stored Functions by Using _SqlFunction_
* spring provides classes that simplify calling stored functions/procedures using JDBC. here we are going to use the _SqlFunction<T>_. we have a getFirstNameById function in the database. this function accepts the id of recored and return the concatenation of the first name and last name
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/11bfbc94-263a-476e-acf0-8f2033d64284)
* the execute() method returns a list of Strings (the return type is the type we specified in the _SqlFunction<T>_), but we only the first one since there should be only one record with the given id
#### Spring Data project: JDBC Extensions
* in recent years where new technologies have emerged for databases, spring created the spring data project to provide useful extensions on top already existing functionality to interact with databses other than traditional databases
* one such extesion is the JDBC extension. it provides advanced features to facilitate the development of JDBC applications
  - QueryDSL: is a domain specific language that provides that provides a framework for developing type safe queries. it provides QueryDslJdbcTemplate to use QueryDSL instead of raw SQL
    ![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/9c8682a2-5bb3-468d-8bab-2fd9c0c387be)
  - Advanced support for oracle database: this extension provides advanced support for oracle databases. it supports oracle specific session settings and provides classes taht integrate with oracle advanced queueing. and provides native support for oracle's xml types, STRUCT and ARRAY and so on
#### Spring JDBC Testing Annotations
* in this part we are going to look at some jdbc testing annotations such as @Sql, @SqlConfig, @SqlGroup, @SqlMergeMethod. all the test shown here use an in memory database and create their own application context
* @Sql is used on a method or class to configure SQL scripts or statements to be executed against a given database during testing. can be used with plain JDBC or more complex applications using transactions and persistence
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/96585906-353c-47bc-8386-5f7f5f0697e7)
* the test application context is created using @SpringJUnitJupiterConfig. this is a composed annotation that combines @ExtendWith(SpringExtension.class) with @ContexConfiguration.
* we have used the @Sql on teh class to give the address of DDL scripts to create the tables or destroy them. the @Sql on the method is sued to give the method a DML script to run and fill the table with data before method's execution
* @Sql can be configured using the @SqlConfigure to provide more details about the script being executed. @Sql also provides a executionPhase attribute that is used to specify the if the sctipts should be executed before or after method execution
* the @SqlMergeMode(MergeMode.MERGE) annotation indicates that method level @Sql scripts declarations must be merged with class level @Sql scripts whereas @SqlMergeMode(MergeMode.OVERRIDE) means method delcarations override the class level ones. if we have both class level and method level @Sql we should use @SqlMergeMode or we will have exceptions since class level @Sql are ignored if @SqlMergeMode is not specified
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/3dc03ebb-577d-4188-a52a-22451ba49dd8)
* @Sql can be grouped together using the @SqlGroup annotations. having multiple @Sql on method is allowed but it is nicer and cleaner to use @SqlGroup
* @DisplayName annotation is a typical Jupiter annotations used to declare a custom display value for the annotated class or method
#### Introducing Testcontainers
* there situations where we cannot test a given code, for example functions or procedures cannot be tested in a H2 database since it diesn't have these concepts. so these kinds of things need to be tested in a real database but preparing a test database may be costly, so an alternative would be to use a database in a container
* Testcontainers is a java library that supports Junit tests and provides lightweight and throwaway intances of common databases , or anything else that can run in a docker container. the good thing is taht we don't need to create a container manually since Testcontainer prepares everything itself
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/e0739130-50bf-4817-be39-e08a2b43b8b3)
* this a progarmmatic approach where we created a _MaraiDBContainer_ class that accepts a string which is the docker image and version. then we have used the life cycle annotations to start and shutdown the container gracefully. this container provides all the properties that is needed to create a datasource
* although this appraoch gets the job done it is recommended to rely on Junit life cycle management to srat and stop the container. this allows us to use a single configuration class. to do this we should do the following:
  - declare our _MaraiDBContainer_ as a static field in our test class and add the @TestContainers annotation on the class (a Junit Jupiter extension) to  activate the automatic startup and stop of the container
  - annotate the container field usign @Container to let junit know that this field is our container
  - use @DynamicPropertySource on a method to register the container properties as a configuration properties for the datasource bean
  - we are also going to need to provide the script so we will use the @Sql* family to populate the container
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/99f16fa8-d803-459a-aac1-5e55485e6d54)
* using Testcontainers simplifies things bbut they are not perfect sometime valid SQL queries are not recognized and some changes must be made to them or the files must be copied to the container and executed directly on it
* our first approach must be to change the script itself to work but if that doesn't work we have to copy the files onto the container
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/7aec016a-e644-456a-b6f9-1f8c1370c06e)
* since we are ruuning the scripts directly on the container we don't need the @Sql* family annotations anymore. copying the files can be done in many ways. the way we showed above works for MariaDB, it gets the job done
#### Considerations for Using JDBC
* with many features, usign JDBC can siplify our interactiosn with RDBMS, however it requires alot of code to be develop easpecially when transforming the aresultSet to Java POJOs
* on top of JDBC there are alot of open source libraries that to help close the gap between relational data and java OO model like iBATIS (a data mapper framework) that transforms ResultSet to domain object using xml descriptor files. there are also many ORM framework tha focus on object model, rather than the query for example Hibernate, EclipseLink and OpenJPA which all comply with JPA specification
* in recent years developers mostly rely on ORM tool instead of using JDBC directly however in cases where you need to have absolute control over the query (for example for performance purposes) JDBC is vialbe option
* the buaty is that spring lets us mix and match different solutions together. for example we can use hibernate as our main ORM and JDBC as a supplement for more complex queries and batch operations. we can mix them in a single business operation and wrap them under a single transaction

## Spring With Hibernate
* preciously we talked about JDBC and how spring made JDBC development easier, but we still we have to write a lot of code to get the job done. to make things even simpler persistence frameworks were created. in this chapter we are going to focus on Hibernate, one the most commonly used ORM frameworks and also take look at JOOQ as well
* the purpose of an ORM lobrary is to close the gap between the relational data model of a RDBMS and object oriented model in java so that programmers can focus on working with object model and easily perform persistence actions
* since Hibernate provides and implementations for JPA, we can choose to use Hibernate's own API or JPA API
* we are going to cover the following:
  - Configuring Hibernate SessionFactory: this is the core concept of hibernate and we are going to see how we can configure it
  - Major ORM concepts using hibernate: we'll learn major concepts of how to use hibernate to map a POJO to a relational Entity and their relationships
  - Data operations: we are going to learn how to perform data operations
* our data model is the same as the last chapter but we are going to add an instrument table to it as well
### Configuring Hibernate SessionFactory
* as mentioned before, the core concept of hibernate is the _Session_ Interface and it is obtained using _SessionFactory_ class. spring provides classes that support configuring _SessionFactory_ as a spring bean with desired properties
* the configurations are built on top of a DataSource configuration but in this chapter we are going to use HikariCP to setup connection pooling
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/f24c7507-f821-4a6e-8f67-c8f1765e911e)
* there are few things to note here
 - taransaction manager: hibernate session factory requires a transanction manager fro transactional data access. we have used @EnableTransationManagement to do this. if XML config was used we needed a bean with the exact name of transactionManager. with java config only the type is important
 - @ComponentScan is used to detect beans with @Repository which contain methods for accessing data
 - SessionFactory bean is the most importatn part of this configuration. first teh datasource is configured, then we isntruct it to scan packages for entities and finally the hibernate properties which we listed above in the comments
### ORM Mapping Using Hibernate Annotations
* next we have to maodel the java POJO entity and their mapping to the underlying relational data
* there are 2 approaches for this. first is to create the POJOs first and the configure the HBM2DDl to auto or create so the relational data model is created by hibernate however this is not suitable for produciton environment.
* the second appraoch is to start with the realational data model and then create the POJOs absed on them. this appraoch is preffered for development and production since it provides greater control over the data model and also optimization of performance
* as said earlier we have the same entities as before: _Singer_ and _Album_ (_Singer_ has a one to many relation with _Album_). a new Entity of _Instrument_ is added which has many to many relation with _Singer_. we also have an _AbstractEntity_ class that stores the id and version (used for auditing) fields since they are a common part of both _Singer_ and _Album_. _AbstractEntity_ is marked with @MappedSuperclass that designates a class whose mapping inforamtion is applies to entities that inherit from it
### Simple Mapping
![pro spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/94e84dc0-0436-45c6-aa92-f48e28dcd765)
* these are our entiteies. we have ommitted the code related to mapping for now to focus on other things
* first we annotate a class with @Entity to mark it as a mapped entity class, also a class marked with this annotation must have field annotated with @Id, this column represents teh primary key of the table. the @Table annotation defines the table name in the database this entity is being mapped to. for each mapped attribute (i.e field) we can annotate it with @Column to give it the name of the column it is representing (if the type and name of the coulmn is the same as the field then there is no need to use @Column)
* @GeneratedValue tells hibernate how the id value is generated. the IDENTITY strategy means that the id value is generated by the back end during insertion
* for the version attribute we annotate it with @Version. this instructs hibernate to use a optimistic licking mechanism using the version attribute to control it. when we update a record, hibernate compares the value of version with the one in the database andif they are the same the record is updated and the value of version is incremented. if not hibernate will throw a _StaleObjectStateException_ which spring translates to _HibernateOptimisticLockingFailureException_. here we have used an integer to represent the version but hibrnate supports timestamp as well
* it is recommended to use integer instead of timestamp since hibernate increments the value by one. when using the timestamp hibernate will update the latest timestamp and it is a littel unsafe since two concurrent transaction may load and update the saem record in the same millisecond
#### One To Many Mappings
* hibernate has the ability to model many kinds of association. yhe most commonly used of thos are one to many and many to many. below is an example of one to many association between _Singer_ and _Album_
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/f2523478-230a-4d78-989a-bc90316ea2f2)
* the getAlbums() and getSinger() method are respectively annotated with @OneToMany and @ManyToOne indicating both side of the relationship that they have. (a singer can have zero to many albums, many albums can be from the same singer
* the mappedBy attribute of @OneToMany, indicates the property in album class that providdes the association i.e the foreign key (the singer attribute in album table)
* the cascade attribute tell hibernate what to do to children if somethings happens to a parent. CascadeType.ALL means any changes made to _Singer_ are propegated to _Albums_ linked to it
* orphanRemoval means that if the album set is updated, the albums that are not in it anymore should be deleted from the DB
* @JoinColumn is neccessary to specify the inderlying foreign key column name
#### Many To Many Mappings
* every singer can have zero to many instrument and any instrument can be used by zero to many singers
* this is many to mant mapping and it requires a join table which in our case is the Singer_Instrument Table
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/6160e745-66e3-4541-bd36-68a6db4b14d9)
* the getInstruments() method is annotated with @ManyToMany. the @JoinTable annotation is used to indicate the underlying join table. the name is the join table's name. the joinColumns defines the foreign key to the Singer Table and inverseJoinColumns defines the foreign key to the other side which is instrument table
#### Hibernate Session Interface
* when interacting with a DB we should use the _Session_ inteface which is obtained from _SessionFactory_
* the _SessionFactory_ instance can be autowired to repository classes the be used to communicate with the DB
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/26b5e1a2-93e7-49a5-9746-3c856b4d57c2)
* We will talk about @Transactional in chapter 9. using the @repository enables the translation exceptions to spring's runtime and DataAccessException
### Querying Data By Using Hibernate Query Language
* when using ORMs there is no need to always use native SQL code to communicate with the DB (since they work with the object model). in the case of hibernate, tha tis using Hibernate Query Language which hibernate will translate to SQL
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/0be21258-4f81-491d-83a0-766eb506caa6)
* the sessionFactory.getCurrentSession() method gets a hold of session interface. then the session.createQuery() is called. the statement retrieves all the singers from tthe DB. and alternative would be "select s from Singer s"
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/1a722c21-c8df-4537-92e8-eddf7315fd49)
* but in the output theres is no album or instrument data. let's see why
#### Querying with Association Fetching
* by default hibernate fetches data in lazily fashion, meaning a table will not be joined with it's associations. this is done for performance
* there are to ways to fetch the associations, first is to use @ManyToMany(fetch=FetchType.EAGER). this tells hibernate to fetch the associated record in every query but this will impact the performance
* the other option is to tell hibernate to fetch the associations when required. if we use _Criteria_ we can call the Criteria.setFetchMode() to eagerly fetch the data. if we use namedQuery we can use the fetch operator to get the data. we are going to dfemonstrate the latter appraoch with findAllWithAlbum()
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/441e0d1e-e8cb-4c3f-9c1b-8094c6a082c1)
* pay attention to the left jooin clause which tells spring to fetch data eagerly, also we have used the distinct operatoe other wise hibernate will return duplicate data (a singer will be returned for each album record)
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/c7095808-b828-4a77-a43b-c251a1712e27)
* another example would be the finById() method
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/1e19a74a-a8aa-4fc8-8b06-bace2af9829c)
* the getNamedQuery() returns an instance of the _Query_ interface. to provide the paramater for the query we call the setParameter(). if there are more than one parameter we can provide them with methods setParameterList() or setParameters()
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/067161d6-aa38-4529-9b0c-f513c8f61e03)
### Inserting Data
* inserting data with hibernate is simple as well. as ypu remember it was alot of effort adn code to try to get the primary key after insertion when we used plain JDBC. with Hibernate none of that is needed and it return the populated entity with the geenrated primary keys by itself
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/f422ed28-9d29-4c07-b64f-5bda81a14035)
* the saveOrUpdate() can be used for both operations as the name suggests. one thing is that upon insertion of singer entity, all the associations it has are inserted along side it as well
![Pro Spring](https://github.com/Sina-karimi81/Pro-Spring-6/assets/83176938/537179cf-be70-48e7-bf20-1e6346c25cc7)
* we've overriden the hibernate configuration to log the SQL queries that are used against the database
### Updating Data
### Deleting Data
### Executing SQL Native Queries
### Executing Projections With Hibernate
### Calling Stored Functions With Hibernate
### Configuring Hibernate to Generate Tables from Entities
### Annotating Methods or Fields?
### Consideration When Using Hibernate
### Introducing JOOQ

