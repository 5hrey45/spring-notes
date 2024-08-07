Instead of using the HttpServeletRequest and accessing the form data using get parameter like below (note that "studentName" is the HTML form field name)
```
@RequestMapping("/processFormv2")  
public String letsShout(HttpServletRequest request, Model model) {  
    // read the request parameter from the HTML form  
    String name = request.getParameter("studentName");  
  
    String theName = "Yo! " + name.toUpperCase();  
  
    model.addAttribute("message", theName);  
    return "helloworld";  
}
```

We can use @RequestParam to bind the form data to a variable and achieve the same result as above code as seen below:
```
@RequestMapping("/processFormv2")  
public String letsShout(@RequestParam("studentName") String name, Model model) {  
    String theName = "Yo! " + name.toUpperCase();  
  
    model.addAttribute("message", theName);  
    return "helloworld";  
}
```


### Injecting data from model to view using Thymeleaf

Add the field and getter and setter in object class
```
Student class
*******************

private List<String> favoriteOS;

public List<String> getFavoriteOS() {
	return favoriteOS;
}

public void setFavoriteOS(List<String> favoriteOS) {
	this.favoriteOS = favoriteOS;
}
```

create a variable and populate it with data in controller, here, we are fetching from app.prop
```
application.properties
************************************

systems=Windows,Linux,MacOS,Android,ios
```

```
student controller
****************************

// autopopulate the list from app.prop file
@Value("${systems}")
private List<String> systems;
```

add that variable to model
```
@GetMapping("/showStudentForm")
public String showForm(Model theModel) {
	theModel.addAttribute("student", new Student());

	// add the countries to the model
	theModel.addAttribute("countries", countries);

	theModel.addAttribute("demographics", demographics);

	theModel.addAttribute("systems", systems);
	return "admission-form";
}
```

extract that data from model using thymeleaf syntax
```
student form
***********************

<input type="checkbox" th:field="*{favoriteOS}" th:each="system : ${systems}" th:value="${system}" th:text="${system}" />
<br>
```

inject it into view
```
<span th:text="'favorite OS: ' + ${student.favoriteOS}" />
```

## Spring MVC validation
