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
* **config/**
    * **module.config.php** - Routing information, location in the main menu, 
                            - and other configurations
* **src/**
    * **MyFirstExtensionModule/** - The name of the folder should reflect the namespace used in the source files
        * **Controller/**
            * **WebApiController.php** - Module controller, used as an entry point for the new web services
* **public/** - Module's static files (JS/CSS/Images/Templates/...) location
    * **js/**
        * **index.js**
        * ...
    * **css/**
        * **index.css**
        * ...
    * ...
* **views/** - Module's view files
    * **my-first-extension-module/** - The name of the folder should reflect the namespace - dash separated. (The 
                                    - namespace is usually defined in capitalized camel case, like "MyCustomNamespace").
        * **web-api/** - Lowercase controller name dash separated. i.e. WebApiController 
                    - becomes "web-api". the suffix "Controller" is emitted
            * **1x12/** - the version of the web API
                * **my-first-web-api.pjson.phtml** - The view file that contains the JSON response. 
                                                - The name of the view has to be the same as 
                                                - the controller action
                * **my-first-web-api.pxml.phtml** - The view file that contains the XML response
* **Module.php** - Module initializations and DI definitions
```

Next, we are going to dive deep into every folder/part of the module.

- [Module configuration and initialization (Module.php file)](http://github.com)
- [Source files (controllers and models)](http://github.com)
- [Static files (JS/CSS/Images)](http://github.com)
- [View files](http://github.com)


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



