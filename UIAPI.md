# UI Extensions API

## Intro

UI extensions allow us to extend the Zend Server administration UI by creating additional pages, web services and general functionality.
The UI is build on top of Zend Framework 2 on the server side and, AngularJs v1.5.0 on the client side.
Therefore, some experience with these technologies could be a great advantage, but don't worry, with basic knowledge in PHP/HTML/JS/CSS 
and the MVC design pattern, can be good enough.

## Getting Started

### What is a UI extension

Zend Server UI is built on top of Zend Framework 2 (ZF2), and in most cases every module represents one ZS section like 
JobQueue, CodeTracing, and others. A UI extension is a set of (one or more) ZF2 modules that are added to the existing application modules, and are loaded
when the application starts. As mentioned before, there's no need to be familiar with ZF2, however, basic knowledge of MVC is pretty much required
to understand the concept.

Best way to start writing a new plugin, is by cloning the [Skeleton plugin repository](https://github.com/zend-server-plugins/Skeleton), which is stored on GitHub as well.

### Structure

A Zend Server plugin must have a specific folder structure in order to work properly. The files tree must contain configuration files, 
Zend Server extensions folders, and optionally additional static files like scripts, styles and images.

A standard ZS plugin folder has the next structure
```
* ui/
    * MyFirstExtensionModule/
    * MySecondExtensionModule/
    * MyThirdExtensionModule/
* zray/
* route/
* LICENSE.txt
* README.md
* logo.png
* deployment.json
```
The described plugin has "zray" and "route" folders, but here we'll focus on the "ui" folder which
contains ZF2 modules that will extend the functionality of our UI. 

Every module in the plugin has the next structure
```
* config/
    * module.config.php - Routing information, location in the main menu, 
                            - and other configurations
* src/
    * MyFirstExtensionModule/ - The name of the folder should reflect the namespace used in the source files
        * Controller/
            * WebApiController.php - Module controller, used as an entry point for the new web services
* *public/* - Module's static files (JS/CSS/Images/Templates/...) location
    * js/
        * index.js
        * ...
    * css/
        * index.css
        * ...
    * ...
* views/ - Module's view files
    * my-first-extension-module/ - The name of the folder should reflect the namespace - dash separated. (The 
                                    - namespace is usually defined in capitalized camel case, like "MyCustomNamespace").
        * web-api/ - Lowercase controller name dash separated. i.e. WebApiController 
                    - becomes "web-api". the suffix "Controller" is emitted
            * 1x12/ - the version of the web API
                * my-first-web-api.pjson.phtml - The view file that contains the JSON response. 
                                                - The name of the view has to be the same as 
                                                - the controller action
                * my-first-web-api.pxml.phtml - The view file that contains the XML response
* Module.php - Module initializations and DI definitions
```

Next, we are going to dive deep into every folder/part of the module.

### Module configuration

The file ```module.config.php``` returns a configuration array, which is a standard ZF2 way to describe and define module's details or meta-data (see [ZF2 configuration documentation](https://framework.zend.com/manual/2.4/en/tutorials/config.advanced.html)).
Our skeleton extension has a minimal set of a few predefined configuration values, which are required in order to let the extension work properly. We'll go over every key, and check what it is good for and what it provides us.

#### Controllers

- Define the controllers of the module. In the example below, the only controller in the extension is used for web APIs. Please note that the name (array key) in the "invokables" list contains web APIs version suffix (after the dash character). The current web APIs version is defined in the ```<ZS installation folder>/gui/module/WebAPI/Module.php``` file. It's defined as a constant ```const WEBAPI_CURRENT_VERSION = '1.12';```. 
```
    'controllers' => array(
        'invokables' => array(
            'zsWebApiClient-1_12' => 'zsWebApiClient\Controller\WebApiController',
        ),
    ),
```

#### ACL (Access control)

- When creating a new page or a new web API in Zend Server, we might want to restrict the access to a specific role, or limit the access to specific actions. That, can be done by adding definitions per web API or page, under "route" key in "acl" section. For every web API or controller, we define which users under which role are allowed to use our newly created resource, or specific actions. The list of all the available roles is defined under ```\Application\Module::ACL_ROLE_*``` (as constants). 
The key "allowedMethods" is an array with the allowed methods in our controller (without the "Action" method name suffix). For instance, if we have a method "getMyListAction", it will be defined as ```'allowedMethods' => array('getMyList', ...),```. Alternatively, when the key "allowedMethods" is empty, there's no restriction by methods.

```
    'acl' => array(
        'route' => array(
            'zsWebApiClient' => array(
                'role' => \Application\Module::ACL_ROLE_ADMINISTRATOR,
                'allowedMethods' => array(),
            ),
        ),
    ),
```

#### Navigation

- In many cases (probably in most of them), a UI extension is created in order to add new pages to ZS UI system. 
The new page should have its representation in the main menu, along with several other required options. "Navigation" section is the one responsible for that. Every item (page) must have several definitions:
    - The **name**, the **icon** and the **location** of the page in the main menu. 
      The icon is defined with glyphicon name. Here is a [List of available glyphicons](http://glyphicons.com).
      The "administration" key (3rd level) defines in which main menu section the page will appear (In the next example the page will appear under "Administration" main menu section). List of all the available sections can be found in ```<ZS installation folder>/gui/config/autoload/navigation.global.config.php```
    - The **URL** of the new page. 
      In case of an angular implementation, the URL is provided as a path, relative to the base URL of the app. Therefore the path "/web-api-client" for instance, will be accessible through "http://localhost:10081/ZendServer/#!/web-api-client" 
      In case of a conservative ZF2 way, there's no need to define a URL, the controller and the action will define it automatically.
    - The **controller name** and the **view template** file. 
      The name of the controller has to be the same as defined in angular's ```app.controller(<controller name>, <fn>)``` method. 
      The view template is a full URL to the template.
    - External **resources** - JS/CSS files. The provided resources are loaded when the UI starts.
      Please keep in mind, that before Zend Server v9.0.1, only JS files could be provided, while CSS files had to be included (using <link ...>) inside the HTML template.

- Please note(!) Zend Server's UI is built on top of angularJs, therefore it's highly recommended to build new pages using angular methodologies. However, there's still an option to write extensions using the conservative way (where HTML code is generated from within PHP). In that case, pay attention to the differences in the "navigation" section. Instead of "url", "angularController" and "templateUrl", the keys "controller" and "action" should be used. Those keys define the corresponding ZF2 action/controller. Both cases can be found in [Zend Server skeleton plugin](https://github.com/zend-server-plugins/Skeleton)

```
'navigation' => array(
    'default' => array(
        'administration' => array(
            'pages' => array(
                array(
                    'label' => 'Web Api Client',
                    'route' => 'extensions',
                    'url' => '/web-api-client',
                    'templateUrl' => '/ZendServer/ModuleResource/zsWebApiClient/templates/index.html',
                    'angularController' => 'zsWebApiClientController',
                    'resources' => array(
                        'js' => array(
                            '/ZendServer/ModuleResource/zsWebApiClient/js/index.js',
                        ),
                        'css' => array(
                            '/ZendServer/ModuleResource/zsWebApiClient/css/index.css',
                        ),
                    ),
                ),
            ),
        ),
    ),
),
```

#### Web APIs

- Unlike new pages, Web APIs won't have their representation in the main menu. however, they do have to have a URL, a controller and an action. Web APIs are defined in "webapi_routes" section, where every entry looks like a standard [ZF2 routing configuration](https://framework.zend.com/manual/2.4/en/in-depth-guide/understanding-routing.html). In the example below, "zsWebApiClient" is the semantic name of the web API, that defines the options for the routing - the route itself (the URL of the web API) and it's implementation in the code - the controller and the action. Pay attention to the version number, that version has to be the same as defined per the web API controller under "invokables" array. The URL below will be accessible through "http://localhost:10081/ZendServer/Api/getAvailableWebApis"

```
    'webapi_routes' => array(
        'zsWebApiClient' => array(
            'type'  => 'Zend\Mvc\Router\Http\Literal',
            'options' => array(
                'route' => '/Api/getAvailableWebApis',
                'defaults' => array(
                    'controller' => 'zsWebApiClient',
                    'action'     => 'getAvailableWebApis',
                    'versions'   => array('1.12'),
                ),
            ),
        ),
    ),
```

#### Static files

- Almost every module in ZS UI system has a "public" folder which contains static files like JS, CSS, and images that are relevant to that specific module. That method has the "right" structure and looks wonderful, but unlike the main "public" folder (<ZS installation dir>/gui/public), those inner "public" folders are inaccessible from the web. Therefore, we have created kind of a "proxy" service that makes static files inside modules' folders, visible to the web. 

The service is called "ModuleResource" and it makes the static files accessible using the next URL pattern:
```
    http://localhost:10081/ZendServer/ModuleResource/<module name>/<path inside module "public folder">
```

Example:
```
* modules/
    * MyModule/
        * public/
            * js/
                * main.js
            * css/
                * main.css
```

Those two files have the next URLs correspondingly
``` 
    http://localhost:10081/ZendServer/ModuleResource/MyModule/js/main.js
    http://localhost:10081/ZendServer/ModuleResource/MyModule/css/main.css
```

### Client side

Every new page in the system has an **angular controller** which is a javascript file under ```public/js/controllers``` folder in (almost) every module, and a **view template** which is defined under "public/templates" folder and has a ".html" file extension. The controller is a javascript code encapsulated in a controller callback definition, and interacts with angular's objects, such as $scope, $http, etc. The template is an HTML code with angular's directives and placeholders.

#### Controller

The main angular object is called ```zsApp``` and it's defined in the global scope. All the created controllers, services, factories and filters have to be defined per that object. A standard angular controller is defined in the next way

```
zsApp.controller('<controller name>', ['<angular services>', function(<same angular services as parameters>) {
    // implementation here...
}]);
```

The interesting part here is the services. Among many common services, the most interesting are those which are responsible for the interaction between the controller and the view template, and for the communication with the web APIs. In addition to the various services defined by angular itself, there are a few important services created by us and provice ZS specific features, like the interaction with our web APIs, and some ZS UI unique tweaks.

Let's have a example in which we get list of all the available web APIs from the server, and display them on the screen.
```
zsApp.controller('zsWebApiClientController', ['$scope', 'WebAPI', function($scope, WebAPI) {

    // initialize the scope variable
    $scope.availableWebApis = [];

    // send request to the server, and set the "availableWebApis" variable
    WebAPI({
        method: 'get',
        url: '/ZendServer/Api/getAvailableWebApis'
    }).then(function(res) {
        // success callback
        $scope.availableWebApis = res.data.responseData.webapis;
    }, function() {
        // failure callback
        alert('Error occurred :(');
    });
}]);
```

In the example above we use two services - $scope and WebAPI. The first one is built in service that is used to interact with the view template, i.e. all the properties of this object are visible from within the template, while the latter was created by ZS UI, to make it possible to interact with ZS web APIs.

WebAPI is a ZS _promise_ service that wraps the standard built-in ```$http``` service (used for AJAX calls, full documentation [here](https://docs.angularjs.org/api/ng/service/$http)), and customizes it to fit ZS web APIs requirements like additional headers and specific request format. All the created ZS UI services are defined in ```<ZS installation dir>/public/spa-assets/js/app.js```. Naturally, some of the web APIs require GET or POST parameters, they should be defined by "params" and "data" properties accordingly.

As any other promise object, WebAPI has "then" and "finally" methods. The first, "then" method receives two callback parameters - The first callback is invoked on successful response, while the second on failed. The "finally" method receives one callback parameter and it's invoked once the request completes regardless its status. "finally" method is usually used to show/hide spinners.

#### View template

View templates are responsible for the presentation level of our angular app. We use standard ".html" files which are usually located under ```public/templates``` in the modules. A view template may have "if" conditions, "for-each" loops, and use properties of the $scope object from the controller.

The example below domonstrates how easily can we display the data that we got from "getAvailableWebApis" web API (in the controller)

```
<h1>Available web APIs</h1>
<ul>
    <li ng-for="item in availableWebApis">{{ item }}</li>
</ul>
```



