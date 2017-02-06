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

Next, we are going to dive deep into every folder/part of the module.

- [Module configuration and initialization (Module.php file)](UI_API/module_configuration.md)
- [Source files (controllers and models)](UI_API/source_files.md)
- [Static files (JS/CSS/Images)](UI_API/static_files.md)
- [View files](UI_API/view_files.md)
