# Client side

## Intro

During the development of ZS versions 9.0 and 9.1, the UI was changed from serving pages using the standard ZF2 MVC, to a modern single page application built on Angular JS. Therefore, the application in the browser is loaded from static JavaScript and HTML files (without PHP intervention), and all the server-side data is served through web APIs. It's highly recommended to get familiar with AngularJS framework and building plugins' pages using the new methodologies.

Every Angular application has a global object that contains all the data, like the routing, list of modules, controllers and others. In our application it's called ```zsApp``` (it's defined on the global scope - which is "window")

## Angular pages

Every new page in the system has an **angular controller** which is a javascript file under ```public/js/controllers``` folder in (almost) every module, and a **view template** which is defined under "public/templates" folder and has a ".html" file extension. An Angular controller, is a javascript code encapsulated in a "controller" callback definition, and interacts with angular's objects and services, such as $scope, $http, etc. The template is an HTML code with angular's directives and placeholders.

## Controller

As already mentioned, the main angular object is called ```zsApp``` and it's defined in the global scope. All the created controllers, services, factories and filters have to be defined per that object. A standard angular controller is defined in the next way:

```javascript
zsApp.controller('<controller name>', ['<list of angular services>', function(<same list of angular services as parameters>) {
    // implementation here...
}]);
```

The interesting part here is the services. Among many common services, the most interesting are those which are responsible for the interaction between the controller and the view template, and for the communication with the web APIs. In addition to the various services defined by angular itself, there are a few important services created by us and provice ZS specific features, like the interaction with our web APIs, and some ZS UI unique tweaks.

Let's have a example in which we get list of all the available web APIs from the server, and display them on the screen.

```javascript
// define the controller, and get WebAPI service which is responsible for the interaction with the server-side
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

In the example above we use two services - $scope and WebAPI. The first one is built in service that is used to interact with the view template, i.e. all the properties of this object are visible from within the template, while the latter was created by ZS UI to make it possible to interact with ZS web APIs.

WebAPI is a ZS _promise_ service that wraps the standard built-in ```$http``` service (used for AJAX calls, full documentation [here](https://docs.angularjs.org/api/ng/service/$http){:target="_blank"}), and customizes it to fit ZS web APIs requirements like additional headers and specific request format. All the created ZS UI services are defined in ```<ZS installation dir>/public/spa-assets/js/app.js```. Naturally, some of the web APIs require GET or POST parameters, they should be defined by "params" and "data" properties accordingly.

As any other promise object, WebAPI has "then" and "finally" methods. The first, "then" method receives two callback parameters - The first callback is invoked on successful response, while the second on failed. The "finally" method receives one callback parameter and it's invoked once the request completes regardless its status. "finally" method is usually used to show/hide spinners.

## View template

View templates are responsible for the presentation level of our angular app. We use standard ".html" files which are usually located under ```public/templates``` in the modules. A view template may have "if" conditions, "for-each" loops, and use properties of the $scope object from the controller.

The example below domonstrates how easily can we display the data that we got from "getAvailableWebApis" web API (in the controller)

```html
<h1>Available web APIs</h1>
<ul>
    <li ng-for="item in availableWebApis">{{ item }}</li>
</ul>
```

## Routing 

Zend Server UI has a lot of sections and sub sections, where every page in every section has its unique URL, controller and a view template. List of all the routing definitions can be found at ```<ZS install folder>/public/spa-assets/js/app.js``` script, which is the main script of our application.

One example of a page:

```javascript
zsApp.state('zray-history', {
	url: '/zray/history',
	menu: {
		name: 'history',
		cat: 'zray'
	},
	title: 'History',
	templateUrl: '/ZendServer/ModuleResource/DevBar/templates/history.html',
	controller: 'ZRayHistoryController'
});
```

The object defines a unique URL, it's location and the title in the main menu, the controller and the view template. When creating pages in a plugin, this information is being taken from the ```module.config.php``` file automatically, just follow the configuration instructions.

## What's next?

Try to create a very simple plugin using Angular methodologies (use the skeleton plugin to start), with a static HTML just to get familiar with the initialization process. As a second phase, try to use existing web APIs to get some useful information, and as the advanced part, try to write your own web API. Here you'll need your basic ZF2 knowledge