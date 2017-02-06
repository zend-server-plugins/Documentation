# Source files

The source section, all the files under "src" folder, contains all the server side scripts of the module. Basically, it's about models and controllers. The newly created web APIs' server side implementation will be located here. In case of a conservative approach using the standard ZF2 MVC pattern rather then angular way, this section will contain also the controllers for serving the new pages. I will focus on the angulars approach, where the data is served to the client via web APIs, that work with JSONs as their output.

Let's take a look at the structure of the "src" folder. (The names are taken from the Zs-Web-Api-Client plugin)
```
...
src/
    zsWebApiClient/ - This is the namespace of the module.
        Controller/ - All the controllers will be located in that folder. 
                      Other folders, like "Models" or "Components" will be placed at the same level (siblings).
            WebApiController.php
            ...
        ...
    ...
...
```

The folder name at the first level, is the namespace of the module. That's the same name that will appear in the namespace in the source files at the top.
Under the namespace folder, will appear all the sections of the server side implementation code, and the namespace of those source files will be ```namespace zsWebApiClient\Controller``` or ```namespace zsWebApiClient\Model```.

Let's take a look at our ```WebApiController.php``` file. That controller is used to dispatch and serve web APIs, but it has the same structure as any other controller file.

```
// define the namespace
namespace zsWebApiClient\Controller;

// declare usage of the base class for our controller - that's mandatory!
use ZendServer\Mvc\Controller\WebAPIActionController;

// The Web API controller should extend Zend Server's base controller
class WebApiController extends WebAPIActionController
{
	
	// define controller's action method - 
	// Please note that the method name has "Action" suffix
	public function getAvailableWebApisAction() {
		// check that the request method is right. (In this case it expects to get POST request)
		$this->isMethodPost();

		// The most common way to get the request parameters with their defaults
		// (Please check other ZS web API controllers for more references)
		$params = $this->getParameters(
			array(
				'param_1' => 'default value for param_1',
				'param_2' => 'default value for param_2',
				// ...
			)
		);

		// .. some controller code here

		// OPTION #1: return an array - when returning an array, the corresponding view file must be created
		return array(
			'foo' => 'bar',
			'baz' => 'bat',
		);

		// OPTION #2: return "WebApiResponseContainer" instance. In that case, no "view" file is required
		// and the output will be served as JSON response automatically (or XML, depends on the request headers)
		return new \WebAPI\View\WebApiResponseContainer(array(
			'foo' => 'bar',
			'baz' => 'bat',
		));
	}
	
}

```

When a controller returns an array, the system expects to view files to be defined, please refer to [view files documentation](view_files.md) to get more information.

