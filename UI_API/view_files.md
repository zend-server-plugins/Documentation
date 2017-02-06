# View files

## Intro

View files are simple HTML files with ".phtml" extension, that means, PHP code segments can be used inside. View files are defined in several cases:

+ When the plugin is built in a conservative way, where the pages are served from the server-side using the standard MVC pattern, without the usage of Angular. (prior to Zend Server v9.0)

+ When we want to override the default web APIs output. Currently, ZS UI provides ```WebApiResponseContainer``` service, which receives an array as a parameter and handles the standard output from web APIs - by default it outputs the result in JSON, but when the request requires XML response, the service knows how to handle it and builds a nice XML. Rough estimation - in most cases there's no need to override the default output object/behavior.

## structure

View files are defined under "views" folder (in the same level with "src", "config" and other module's folders). The first level under the "views" folder is the namespace directiry (same as "src" structure), dash-separated name.

```
views/
    zs-web-api-client/ - namespace lowercase dash separated
        web-api/ - controller name, also lowercase dash separated, without 
                   the "controller" suffix
            1x12/ - the version number (as it's defined in the configuration file)
                get-available-web-apis.pjson.phtml - same as method name, also lowercase dash separated,
                                                     without "Action" suffix. (The original name is
                                                     "getAvailableWebApisAction")
                get-available-web-apis.pxml.phtml - represents the XML output of the same method
```

Please note! The files have ".pjson.phtml" and ".pxml.phtml" extensions for JSON and XML responses accordingly.

Every controller action (that has view files) must have these two view files. The system selects the right file automatically according to the request headers.

## Version

In the example above, the view files are located under a folder called "1x12". That is the version number. That version number is defined in the ```module.config.php``` file, in the section "controllers" > "invokables", where the name of the controller is defined with trailing version number (after the underscore "_").

Views for "regular" controller actions (not web APIs), do not have the version wrapping folder, and obviously don't have two files, since it's an HTML.


## Examples

An example of a view file (json):

```php
"responseData":{
	"webapis": <?php echo json_encode($webapis); ?>
}
```

The file itself is structured as JSON, under "responseData" key. The content of that file is injected into web API response layout, which has other data regarding the request, but that's irrelevant for plugin's development. XML response may look like:
```php
<responseData>
	<people>
		<?php foreach ($people as $person) { ?>
		<person>
			<id><?php echo $person['id']; ?></id>
			<name><?php echo $person['name']; ?></name>
			<family><?php echo $person['family']; ?></family>
			<bmonth><?php echo $person['bmonth']; ?></bmonth>
		</person>
		<?php } ?>
	</people>
</responseData>
```

Same here, the wrapping element is called "responseData".

## What's next

Follow [static files documentation](static_files.md) to start working with JavaScript files, CSS styles, images and other static files within the module