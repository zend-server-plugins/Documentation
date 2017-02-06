# Static files

## Intro

Modules in ZS UI system can have a "public" folder which may contain static files like JS, CSS, images and others. These assets may be used by that specific module. However, unlike the main "public" folder (<ZS installation dir>/gui/public), modules' public folders have no specific URL and they cannot be directly accessed from the web. Therefore, our UI platform has a script called "ModuleResource", which operates as a gateway or a proxy that make the assets be available for the browser. The URL to the static files inside a module can be accessed via the next URL pattern:
```
    http://localhost:10081/ZendServer/ModuleResource/<module name>/<path inside module "public" folder>
```

## Structure

```
* modules/
    * MyModule/ - the name of the module
        * public/ - module's public folder - files will be available through ZS gateway
            * js/
                * main.js
            * css/
                * main.css
```

We can access these two files using the next URLs correspondingly
``` 
    http://localhost:10081/ZendServer/ModuleResource/MyModule/js/main.js
    http://localhost:10081/ZendServer/ModuleResource/MyModule/css/main.css
```

## What's next

Follow [Angular's documentation](client_side.md) to get familiar with creating angular controllers and templates.