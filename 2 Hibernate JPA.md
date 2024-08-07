Hibernate is the default implementation of the JPA in spring boot
dependencies required
- MySQL driver
- spring data jpa

```
@Bean  
public CommandLineRunner commandLineRunner(String[] args) {  
    return runner -> {  
       System.out.println("Hello World");  
    };  
}
```

- command line runner is from the spring boot framework
- it is executed after the spring beans have been loaded

EntityClass
- a class which is mapped to a table
- Annotated with @Entity
- also annotated with @Table(name = "xyz") where xyz is the table name
- in this case, the class will be mapped to xyz table

Mapping fields to columns
- @Column(name = "xyz")
- using this annotating before a field inside a class will map it to xyz column in the table
- for mapping primary key, including the above step, we also need to specify it as ID and how does it stays unique
	- we annotate the primary key field with @ID
	- we also specify the primary key generation strategy, usually the below is used
	- @GeneratedValue(strategy = GenerationType.IDENTITY)
	- this means the primary key will be managed by the database (using auto increment)

DAO
- data access object
- responsible for interfacing with the database
- common design pattern
- crudapp ---> dao ---> database
- dao talks to entitymanager which talks to datasource which interfaces with database
- crudapp --- > dao ---> entitymanager ---> datasource ---> database

@Transactional
- used to begin and end a transaction in the JPA code

@Repository
- sub annotation of component
- specialized annotation for DAO
- applied to DAO implementations
- spring will automatically register DAO implementations
- supports component scanning
- translates JDBC exceptions

Using JPA to operate on database
- create an entity class which maps to a table (map fields to column as well)
- create DAO interface with methods to operate(CRUD methods)
- implement the CRUD method in the DAO implementation class
- Also, in the DAO implementation class, create an entityManager and inject it using the constructor
- write function in main class and inject the DAO object and make use of the CRUD method of the object for the operations

so in practice, we require 4 classes
- DAO interface
	- defines the prototype/signature of the methods
- DAOImpl class
	- implements DAO interface
	- creates an entity manager and injects it using constructor injection
	- also implements methods specified in the DAO interface
- Entity class
	- Maps the class to a table
	- maps the fields to columns
	- contains constructors to build objects
	- contains getters and setters
- Main class
	- create a Bean CommandLineRunner function and inject DAO interface
	- write functions for CRUD operations which makes use of methods in DAO implementation to make CRUD operations

JPQL
- similar to SQL but used entity class name instead of table name and field name instead of column name
- to build a query, we use
```
TypedQuery<Student> theQuery = entityManager.createQuery("FROM Student", Student.class);  
List<Student> studentData = theQuery.getResultList();
```
- to use a custom variable (eg: taken from user through form) inside JPQL, use setPerimeter as seen below
```
String theLastName = "Doe";
TypedQuery<Student> theQuery = entityManager.createQuery("FROM Student WHERE lastName=:theData", Student.class);  
theQuery.setParameter("theData", theLastName);  
return theQuery.getResultList();
```
- here, theData is the placeholder for theLastName which is passed to the query through theData
- we later set the value of theData as theLastName

@Service
- sub annotation of @Component
- applied to service implementation
- spring will automatically register the service implementation thanks to component scanning

