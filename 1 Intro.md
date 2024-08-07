Spring Boot is a dependency injection framework
Inversion of control, we delegate the creation and injecting the objects to spring

Bean - simple java class which is managed by spring (if the class is a bean, then it can be injected by spring)

@Component - marks the class as a spring bean
it marks the class as a candidate to be managed by spring (so that it can be injected when required)

@Autowired - it will create the dependency (object) and inject it (either through constructor or setter injection)
- optional when there is only one constructor, spring will automatically inject the dependencies.

@Qualifier - if multiple dependencies are available to inject, this will specify which one to inject
name of the class but first char is lower case
eg: @Qualifier("trackCoach") where TrackCoach is the class name

@Primary - instead of using @Qualifier and specifying which class to inject, we can mark a class as @Primary so that in case of multiple dependencies, the primary class will be injected
only one class can be marked as primary

but Qualifier has higher priority - even if there is a primary class, the class mentioned in qualifier will be injected

@Lazy - lazy initialization
by default, when spring boot application starts, it scans all the @components, and creates an instance i.e., all beans are initialized
in lazy initialization the bean is initialized only when
- it is needed for dependency injection
- it is explicitly requested

Bean Scopes
- Singleton
	- each dependency to the same class is referenced to the same bean instance
	- two objects with the same dependency point to the same bean
- Prototype
	- for every dependency request, a new bean instance is created and then injected
	- two objects with the same dependency point to different beans

Bean init and destroy methods
@PostConstruct
- a function which runs after bean is constructed, acts like a initializer for that bean
- used to setup handles (like opening files, sockets, db)
- think of it like constructor for class

@PreDestroy
- function which runs before the bean is destroyed
- used to clean up resources (close files, sockets, db)
- think of it like destructor for class

**Note**
- For "prototype" scoped beans, Spring does not call the destroy method.
- Prototype beans are lazy by default. There is no need to use the @Lazy annotation for prototype scopes beans.


@Configuration and @Bean
- if we are not able to use @Component, then we can configure it as spring bean using @Bean
- create a new config class, annotate it with @Configuration
- inside, create a new method which will return an instance of the dependency (return new dependency;) and annotate this method with @Bean
- the bean id will be the method name (use ID (method name in this case) inside of @Qualifier or @Primary)
- use @Bean("id-name") for custom bean ID (need to use "id-name" while using qualifier or primary)

