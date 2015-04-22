Z-Ray Data Display
==========

## Automatically generated panels
When using the storage mechanism of the extensions, Z-Ray will automatically attempt to generate tables based on the information stored.

The data passed must be an array of rows, while each row can consist of an array of data, an associative array, an associative multi-level array or object. For example:

```php
$storage['myParams'][] = array(
'col1' => 'foo', 
'col2' => 'bar', 
'col3' => 'test');


$storage['myArray'][] = array(
'name' => 'foo', 
'values' => array('col2' => 'bar', 'col3' => 'test'));


$objectOrClass = new ClassName();
$storage['myObject']['firstObject'] = $objectOrClass;
```

If an array or associative array is passed, a grid table will be created with columns as the names of the keys. This table can be sorted lexicographically by default and can be free-text searched. 

If a multi-level array or an object is passed, a tree table will be created.


Custom panels
Custom panels give us the freedom to create whatever you can think of with the stored data. No limitations. You can create or manipulate the data, display it as you like, add external elements like graphs, etc.

To create custom panels, we need to extend the extension tree structure with additional resources that describe how each panel works:

ExtensionName
- Panels (directory)
  - myCustomPanel.phtml
  - ... list of custom panels
- Module.php
- zray.php

## Z-Ray Extension Module.php
The `Module.php` file is a configuration file for the different panels, defaults and customs.

The file content should look like this:

```php
<?php
namespace Samples;
class Module extends \ZRay\ZRayModule {

    public function config() {
        return array(
            // The name defined in ZRayExtension
            'extension' => array(
                'name' => 'samples',
            ),
            // Prevent those default panels from being displayed
            'defaultPanels' => array(
                'multiplyGeneralTree' => false,
            ),
            // configure all custom panels
            'panels' => array(
                'customTable' => array(
                    'display'       	=> true,
                    'logo'          	=> 'logo.png',
                    'menuTitle'     	=> 'Custom Table',
                    'panelTitle'	=> 'Custom Table',
                    'searchId' 	=> 'samples-custom-table-search',
                    'pagerId'		=> 'samples-custom-table-pager',
                ),
                'generalTable' => array(
                    'display'       	=> true,
                    'logo'          	=> 'logo.png',
                    'menuTitle' 	=> 'General Table',
                    'panelTitle'	=> 'General Table',
                ),
            )
        );
    }
}
```

DefaultPanels determine if a certain default panel should be displayed or not. You can only specify those you wish to hide.

Panels is a list of custom panels and their configurations:

Parameter | Description
------------ | -------------
display | whether to display the panel or not
alwaysShow | true in order to show the panel even if the extension wasn't used in the current request
logo | the filename of the logo (relative to the extension root)
menuTitle | the panel's menu title (the bottom one)
panelTitle | the panel's content panel (inside the opened panel)
searchId | unique dom id for the search widget, optional.
pagerId | unique dom id for the pager widget, optional.

## Z-Ray extension viewscript
The viewscript is a file which can contain HTML , css and JavaScript  code, that will be loaded by Z-Ray for displaying a new Z-Ray custom panel. 
This file is usually broken down into two parts: The HTML template and placeholders, and the JavaScript  functionality definition which uses the HTML placeholders to display elements such as tables and widgets.

The viewscripts should be placed inside the Panels directory with the name of the custom panel and phtml extension, e.g. `Panels/customTable.phtml` (phtml is a regular PHP file containing HTML  content).
