# Source files

## Intro

The source section, all the files under "src" folder, contains all the server side scripts of the module. Basically, it's about models and controllers. The newly created web APIs' server side implementation will be located here. In case of a conservative approach using the standard ZF2 MVC pattern rather then angular way, this section will contain also the controllers for serving the new pages. We will focus on the angulars approach, where the data is served to the client via web APIs, that work with JSONs as their output.

## Structure

Let's take a look at the structure of the "src" folder. (The names are taken from the ZS-Web-Api-Client plugin)
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

The folder name at the first level is the namespace of the module. That's the same name that will appear in the namespace in the source files at the top.
Under the namespace folder, will appear all the sections of the server side implementation code, and the namespace of those source files will be ```namespace zsWebApiClient\Controller``` or ```namespace zsWebApiClient\Model```.

## The controller

Our web API controller is defined in ```WebApiController.php``` file. That controller is used to dispatch and serve web APIs and it has the same structure as any other controller file. The explanation about every line of code is in the comment above the row.

```php
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

		// OPTION #1: return an array - when returning an array, 
		// the corresponding view file must be created
		return array(
			'foo' => 'bar',
			'baz' => 'bat',
		);

		// OPTION #2: return "WebApiResponseContainer" instance. In that case, 
		// no "view" file is required and the output will be served as JSON 
		// response automatically (or XML, depends on the request headers)
		return new \WebAPI\View\WebApiResponseContainer(array(
			'foo' => 'bar',
			'baz' => 'bat',
		));
	}
	
}

```

Please note! When a controller returns an array, the system expects the view files to be defined, please refer to [view files documentation](view_files.md) to get more information.

## Models

Models are files that are responsible for the business logic, database mappers/access, some calculations, and other code segments of the module. Models are classes under module's namespace and usually are created from within controllers or other models. In many cases, modules are used together with the dependency injection (DI) design pattern, but here we will present its simplest usage, just to get the feeling of how to get started to work with it.

Our example plugin does not have any database access or some special business logic, so it has no model files. Therefore, we are going to create some abstract example model. Our model will return list of users from the DB, it will be called ```Users.php``` and it will be created under ```src/zsWebApiClient/Model```. Please note, the "Model" folder is a sibling of the "Controller" folder (located on the same level). The model file has the next content.

```php
// the same namespace base as the controller 
namespace zsWebApiClient\Model;

// declaration of usage of another namespace
use Zend\Db\TableGateway\TableGateway;

class User {
	
	// ..
	
	public function getUsers() {

		// get the DB adapter (assume that it's already defined in the class)
		$dbAdapter = $this->getDbAdapter();

		// initialize the table gateway
		$tableGateway = new TableGateway('users', $dbAdapter);

		// get list of users from the DB. Assign it to the $usersList variable
		$usersList = $tableGateway->select();

		return $usersList;
	}

	// ..

}
```

(More information about [ZF2 tableGateway](https://framework.zend.com/manual/2.4/en/modules/zend.db.table-gateway.html){:target="_blank"} can be found on ZF2 official documentation)

Now that the model class was created, we'd probably want to use it in our controller. In order to get the instance of the class, we are going to use the "locator" object (of ZF2) which is available for us using ```$this->getLocator()``` call. Our controller would look like this with the usage or our new model:

```php
// ..

class WebApiController extends WebAPIActionController {
	// ..
	public function getUsersAction() {
		// ..

		// Get the instance of our model using the "locator" (which is already defined for us)
		$usersModel = $this->getLocator()->get('zsWebApiClient\Model\User');

		// use the method of the model class
		$usersList = $usersModel->getUsers();

		// ..
	}
}
```

## Other sections

In addition to Controllers and Models, a module may have DB mappers, view helpers, containers, independent components, etc. All these parts are defined the same way as the model which is described above. The initializtion of those components is made in the same way using the "locator". The components will be recognized by their namespace and included using the ZF2 autoloader. 

Generally, Zend Server UI is a quite robust application, therefore most of the configuration and the initializtions are already defined, and you may just use them in your plugin. To get more code examples or just inspire yourself, check the already defined modules in ZS UI.

## What next?

Follow [view files documentation](view_files.md) to get familiar with creating view files, with all their types and versions.