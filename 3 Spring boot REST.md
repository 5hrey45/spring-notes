@RestController
- adds support for REST

@RequestMapping("/api")
- exposed the base path for all the endpoints

@GetMapping("/endpointName")
- exposed that specific endpoint for GET requests

@GetMapping("/endpointName/{varName}")
- exposed an endpoint which can take a path variable (dynamic parameter while making a request)
- can use that parameter to serve the data
```
@GetMapping("/students/{studentId}")  
public Student getStudentById(@PathVariable int studentId) {  
    return studentList.get(studentId);  
}
```
- by default the variable names should match in the {varName} and in the function parameter
- in this case both the path variable inside the GetMapping and inside function parameter is same which is studentId

Spring REST exception handling
1. create a custom error response class (which will be used by Jackson to respond with error json)
2. create a custom exception class
3. Update REST service to throw exception
4. Add exception handler method using @ExceptionHandler

Custom error response class
- POJO class
- contains fields which will be converted into json and sent as response in case of error
- contains constructors and getters/setters for Jackson to handle

Create custom exception class
- extends RuntimeExceptions
- in constructor, we get the message from super
```
public class StudentNotFoundException extends RuntimeException {  
    public StudentNotFoundException(String message) {  
        super(message);  
    }  
}
```

Update REST service to throw exception
- check using if statement (like data not there, out of bounds etc.,)
- if so, throw our custom exception and pass in the error message
```
@GetMapping("/students/{studentId}")  
public Student getStudentById(@PathVariable int studentId) {  
    if(studentId < 0 || studentId > studentList.size() - 1) {  
        throw new StudentNotFoundException("Student id not found: " + studentId);  
    }  
    // getting the student in that index inside the list  
    return studentList.get(studentId);  
}
```

Add exception handler method using @ExceptionHandler
- Define exception handler methods with @ExceptionHandler annotation
- Exception handler will return a ResponseEntity
- ResponseEntity is a wrapper for HTTP response object
- ResponseEntity provides fine grained control to specify HTTP status code, headers and response body
```
@ExceptionHandler  
public ResponseEntity<StudentErrorResponse> handleException(StudentNotFoundException exc) {  
    // create a StudentErrorResponse  
    StudentErrorResponse error = new StudentErrorResponse();  
    error.setStatus(HttpStatus.NOT_FOUND.value());  
    error.setMessage(exc.getMessage());  
    error.setTimeStamp(System.currentTimeMillis());  
  
    // return EntityResponse  
    return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);  
}
```

We can have multiple @ExceptionHandler for different exceptions
- have one for generic errors (catch all)
```
@ExceptionHandler  
public ResponseEntity<StudentErrorResponse> handleException(Exception exc) {  
    StudentErrorResponse error = new StudentErrorResponse();  
    error.setStatus(HttpStatus.BAD_REQUEST.value());  
    error.setMessage(exc.getMessage());  
    error.setTimeStamp(System.currentTimeMillis());  
  
    // return EntityResponse  
    return new ResponseEntity<>(error, HttpStatus.BAD_REQUEST);  
}
```


@ControllerAdvice
- pre-process request to controllers
- post-process responses to handle exceptions
- used for global exception handling
- just create a new class and annotate it with @ControllerAdvice
- now instead of having the exception handlers in each controller, move it inside this class and it will be available to all the controllers globally
```
@ControllerAdvice  
public class StudentRestExceptionHandler {  
  
    @ExceptionHandler  
    public ResponseEntity<StudentErrorResponse> handleException(StudentNotFoundException exc) {  
        // create a StudentErrorResponse  
        StudentErrorResponse error = new StudentErrorResponse();  
        error.setStatus(HttpStatus.NOT_FOUND.value());  
        error.setMessage(exc.getMessage());  
        error.setTimeStamp(System.currentTimeMillis());  
  
        // return EntityResponse  
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);  
    }  
  
    // add exception handler to catch any other exceptoin (catch all)  
    @ExceptionHandler  
    public ResponseEntity<StudentErrorResponse> handleException(Exception exc) {  
        StudentErrorResponse error = new StudentErrorResponse();  
        error.setStatus(HttpStatus.BAD_REQUEST.value());  
        error.setMessage(exc.getMessage());  
        error.setTimeStamp(System.currentTimeMillis());  
  
        // return EntityResponse  
        return new ResponseEntity<>(error, HttpStatus.BAD_REQUEST);  
    }  
}
```


@Service is used to mark the service layer which allows spring to register and provides component scanning
for crud app, we make use of three main layers
- restcontroller which delegates the call to service layer
- service layer which delegates the call to dao
- dao which performs crud
- serviceImpl implements service interface
- @Transactional is used in service layer instead of dao layer due to convention

Instead of doing all of the above, we can simple use spring data jpa
- if allows us to specify the type of entity (employee, student etc.,) and primary key type
- provides most methods such findall(), findById(), dave(), deleteById() etc.,
- reduces boilerplate code
- no need for @Transactional, Jpa handles that as well
- inside the dao package, earlier we had
	- employeeDAO
	- employeeDAOImpl
- delete the above two
- now we will have only EmployeeRepository with below contents
```
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {  
  
    // we pass the entity and the primary key type to JpaRepository class  
    // that's it, no implementation required, spring data jpa will handle it}
```

- make changes in EmployeeServiceImpl accordingly, inject it instead of employeeDAO in constructor injection
```
@Autowired  
public EmployeeServiceImpl(EmployeeRepository theEmployeeRepository) {  
    employeeRepository = theEmployeeRepository;  
}
```
- use 
```
private EmployeeRepository employeeRepository;
```
- instead of
```
private EmployeeDAO employeeDAO;
```
- also replace employeeDAO with employeeRepository inside methods and use their proper methods (id instead of ID)


Similarly, we can use spring data rest to get all the endpoints without implementation
- it requires spring data jpa repository
- it scans the entity in jpa repository and creates endpoints
- add spring data rest in pom file
- no coding required!
- we don't require restController and service classes
- so delete the rest and service package
- so we end up with only
	- dao - employeeRepositoy
	- entity - employee

points to note using spring data rest
- endpoints exposed will be entity name with lowercase and appended 's'
- no base path, use "spring.data.rest.base-path=/api" in applicatio.properties to specify the base path
- in PUT requests, it ignores the id (primary key) sent in json to update, it only considers the id sent via the path variable in the URL

spring data rest configurations
- to change name of exposed endpoints, use @RepositoryRestResource(path = "endpointName") on top of the JpaRepository
```
@RepositoryRestResource(path = "members")  
public interface EmployeeRepository extends JpaRepository<Employee, Integer> {  
  
    // we pass the entity and the primary key type to JpaRepository class  
    // that's it, no implementation required, spring data jpa will handle it}
```

