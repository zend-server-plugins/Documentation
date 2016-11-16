# UI Extensions API

## Intro

UI extensions allow us to extend the UI by creating additional pages, web services and general functionality.
Zend Server's UI is build on top of Zend Framework 2 on the server side and AngularJs v1.5.0 on the client side,
therefore, knowing these technologies is a great advantage, but don't worry, having basic knowledge in PHP/HTML/JS/CSS 
and the MVC design pattern is more than enough.

## Getting Started

### What is a UI extension

Zend Server UI is built on top of Zend Framework 2 (ZF2), and in most cases every module represents one ZS section like 
JobQueue, CodeTracing, etc. A UI extension is a set of (one or more) ZF2 modules that are added to the existing modules and are loaded
when the application starts. As mentioned before, there's no need to know ZF2, but basic knowledge of MVC is pretty much required
to understand the concept.

### Structure

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
The plugin has "zray" and "route" folders, but here we'll focus on the "ui" folder, which
contains one or more ZF2 modules. Every module has the next structure
```
* config/
    * *module.config.php* - Routing information, location in the main menu, and other module configurations
* src/
    * MyFirstExtensionModule/
        * Controller/
            * *WebApiController.php* - Module controller, used as an entry point
* *public/* - Module's static files (JS/CSS/Images) location
    * js/
        * index.js
        * ...
    * css/
        * index.css
        * ...
    * ...
* views/ - Module's view files
    * my-first-extension-module/ - Module's namespace. Defined as dash separated (The namespace is usually defined in capitalized camel case.
        * web*api/
            * 1x12/ - the version of the web API
                * my-first-web-api.pjson.phtml - The view file that contains the JSON response. 
                                                - The name of the view has to be the same as the controller action
                * my-first-web-api.pxml.phtml - The view file that contains the XML response
* Module.php - Module initializations and DI definitions
```





