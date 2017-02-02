# Module configuration and initialization

The file ```module.config.php``` returns a **configuration array**, which is a standard ZF2 way to describe and define module's details or meta-data (see [ZF2 configuration documentation](https://framework.zend.com/manual/2.4/en/tutorials/config.advanced.html)).
Our skeleton extension has a minimal set of a few predefined configuration values, which are required in order to let the extension work properly. We'll go over every key, and check what it is good for and what it provides us.

## Controllers

- Define the controllers of the module. In the example below, the only controller in the extension is used for web APIs. Please note that the name (array key) in the "invokables" list contains web APIs version suffix (after the dash character). The current web APIs version is defined in the ```<ZS installation folder>/gui/module/WebAPI/Module.php``` file. It's defined as a constant ```const WEBAPI_CURRENT_VERSION = '1.12';```. 
```
    'controllers' => array(
        'invokables' => array(
            'zsWebApiClient-1_12' => 'zsWebApiClient\Controller\WebApiController',
        ),
    ),
```

## ACL (Access control)

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

## Navigation

- In many cases (probably in most of them), a UI extension is created in order to add new pages to ZS UI system. 
The new page should have its representation in the main menu, along with several other required options. "Navigation" section is the one responsible for that. Every item (page) must have several definitions:
    - The **name**, the **icon** and the **location** of the page in the main menu. 
      The icon is defined with glyphicon name. Here is a [List of available glyphicons](http://glyphicons.com).
      The "administration" key (3rd level) defines in which main menu section the page will appear (In the next example the page will appear under "Administration" main menu section). List of all the available sections can be found in ```<ZS installation folder>/gui/config/autoload/navigation.global.config.php```
    - The **URL** of the new page. 
      In case of an angular implementation, the URL is provided as a path, relative to the base URL of the app. Therefore the path "/web-api-client" for instance, will be accessible through "http://localhost:10081/ZendServer/#!/web-api-client" 
      In case of a conservative ZF2 way, there's no need to define a URL, the controller and the action will define it automatically.
    - The **angular controller name** and the **view template** file. 
      The name of the controller has to be the same as defined in angular's ```app.controller(<controller name>, <fn>)``` method. 
      The view template is a full URL to the template.
    - External **resources** - JS/CSS files. The provided resources are loaded when the UI starts.
      Please keep in mind, that before Zend Server v9.0.1, only JS files could be provided, while CSS files had to be included inline (using <link ...>) inside the HTML template.

- Please note(!) Zend Server's UI is built on top of angularJs, therefore it's highly recommended to build new pages using angular methodologies. However, there's still an option to write extensions using the conservative way (where HTML code is generated from within PHP). In that case, pay attention to the differences in the "navigation" section. Instead of "url", "angularController" and "templateUrl", the keys "controller" and "action" should be used. Those keys define the corresponding ZF2 action/controller. Both cases can be found in [Zend Server skeleton plugin](https://github.com/zend-server-plugins/Skeleton). The controller name is the one defined under 'controllers' > 'invokables' section.

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

## Web APIs routes

- Unlike UI pages, web APIs won't have a main menu item, however, they must have an accessible URL. Web APIs routing definitions differ from regular pages navigation settings. All the web APIs are defined under "webapi_routes" section, where every entry has the same structure as a standard [ZF2 routing configuration](https://framework.zend.com/manual/2.4/en/in-depth-guide/understanding-routing.html). In our example, we are adding one web API, that obviously has a controller and an action, and we mention that it's defined in ZS web APIs version 1.12. Please note that the version number has to be the same as the version number of our defined controller (under "invokables" array in "controllers" section). The definition of our new web API is located under the name "zsWebApiClient" - that is a unique name for the system. \*The type "literal" means that we mention a unique URL for the web API (unlike pattern or regular expression).
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

# What's next?

Follow [modules' source files documentation](source_files.md) to get familiar with creating controllers, and writing actions and models. 