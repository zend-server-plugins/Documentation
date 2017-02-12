# UI Extensions API

## Intro

UI extensions allow us to extend the Zend Server administration UI, with additional pages, web services and general functionality.
The administration UI is built on top of Zend Framework 2 on the server side, and AngularJs v1.5.0 on the client side.
To be able to extend those technologies, some experience could be a great advantage, but don't worry - with basic knowledge of PHP/HTML/JS/CSS and the MVC design pattern of ZF2, you can dive into the reference below and start learning.

## Getting Started

### What is a UI extension

Zend Server UI is built on top of Zend Framework 2 (ZF2), and in most cases, every module represents one ZS section, e.g. 
JobQueue, CodeTracing, etc. A UI extension is a set of (one or more) ZF2 modules, which are added to the existing application modules, being loaded as the (UI) application starts up. As mentioned before, it is based on ZF2 MVC paradigm.

The best way to start writing your new plugin, is by cloning the [Skeleton plugin repository](https://github.com/zend-server-plugins/Skeleton), which is stored on GitHub as well. You may find our [first UI plugin](https://github.com/zend-server-plugins/zs_web_api_client) as an additional useful reference. Most of the examples in this documentation are taken from there.

### Structure

A Zend Server plugin must have a specific folder structure in order to work properly. The files tree must contain configuration files, 
Zend Server extensions folders, and optionally additional static files like scripts, styles and images.

A standard ZS plugin folder has the following structure:
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
The described plugin has "zray" and "route" folders, but here we are going to focus on the "ui" folder, which
contains ZF2 modules extending the functionality of our UI. 

Each module contained in the plugin has the following structure:
```
* config/
    * module.config.php // Routing information, location in the main menu, and other configurations
* src/
    * MyFirstExtensionModule/ - The name of the folder should reflect the namespace used in the source files
        * Controller/
            * WebApiController.php - Module controller, used as an entry point for the new web services
* public/ - Module's static files (JS/CSS/Images/Templates/...) location
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

Following up, we are going to dive deep into every folder in our module structure.

#### Reading List:
- [Module configuration and initialization (Module.php file)](UI_API/module_configuration.md)
- [Source files (controllers and models)](UI_API/source_files.md)
- [Static files (JS/CSS/Images)](UI_API/static_files.md)
- [View files](UI_API/view_files.md)

#### Getting to know Angular

To get familiar with Angular framework and its methodologies, please read the general instructions for the client side

- [Client side implementation using Angular](UI_API/client_side.md)

Any questions may be referred to zend.com
