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
* ui/
    * MyFirstExtensionModule/
    * MySecondExtensionModule/
    * MyThirdExtensionModule/
* LICENSE.txt
* README.md
* logo.png
* deployment.json

The "ui" folder contains the ZF2 modules of the plugin. Every module has the next structure

* config/
    * module.config.php
* src/
    * MyFirstExtensionModule/
        * Controller/
            * WebApiController.php
* public/
    * js/
        * index.js
        * ...
    * css/
        * index.css
        * ...
    * ...
* views/
    * my-first-extension-module/
        * web*api/
            * 1x12/
                * my-first-web-api.pjson.phtml
* Module.php






