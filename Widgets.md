Z-Ray Widgets
==========

Z-Ray provides a few basic widgets for displaying content and providing a storage facility for keeping the information to be displayed. This storage facility, in effect a model, is then hooked to all of the widgets, so that when content is modified, the widgets automatically get updated.

Initiating a storage is performed thus:
```javascript
var storage = zray.getStorage('uniqueName');
```

Later on, each widget registers for changes in the storage object.

Widgets can be accessed using the JavaScript  object zray, which exposes the following methods:
```javascript
// data handling
registerDataHandler(extension, namespace, userFunc)
getExtensionMetadata(extension)
getStorage(namespace)
updateMenuTitle(extension, namespace, label)
getMenuTitle(extension, namespace, htmlFlag)

// widgets
createTable(dataStorage, $container)
createSummaryTable(dataStorage, $container)
createPager(storage, $container)
createSearch(storage, $container, maintable)
createTreeTable(dataStorage, $container)
createGeneralTreeTable(dataStorage, $container)
```

Each widget will be described later on this document.


## Z-Ray data handling
Using a custom panel receives the stored extension data, and manipulates it. Later on, it displays it to the user. To get the data in the viewscript, we will need to add the following JavaScript  code:

```javascript
zray.registerDataHandler('ExtensionName', 'dataType', 
function(extensionData, requestData) {
		// handle extensionData
	}
);
```

The registerDataHandler gets called whenever the data to our extension and dataType is ready for consumption.


## Z-Ray table widget
Z-Ray supplies a table widget for creating a table that gets populated according to the storage we defined. This widget consists of two separate parts: HTML and Javascript.

```php
<?php 
// Define the table HTML
$tableParams = array(
    'tableId' 	 => 'unique-container-id',
    'tableWidth' => '4', // optional (1 to 4, 1-narrow, 3-wide)
);

echo $this->zrayTable($tableParams);
?>
```

And the JavaScript  code:

```javascript
// create the storage
var storage = zray.getStorage('demoStorage');
// create the main table and bind it to the storage and HTML element
var maintable = zray.createTable(storage, 
jQuery('#<?php echo $tableParams['tableId']; ?>'));

// create columns descriptors for each column you wish to display
maintable.setColumns([Array of columns descriptors]);

// insert the data into the storage
zray.registerDataHandler('Demo', 'dataType',
function(extensionData, requestData) {
		storage.setData(extensionData);
	}
);
```

Each column descriptor should be an object of the following structure:

```javascript
{
    label: 'Person name', 	// the title of the column on the screen
    propertyName: 'name', 	// the data property name
    
    width: 		'40%', 	      // optional, width of the column
    attributes: 	{},		    // optional, column html attributes
    sortable: 	true,	 		  // optional, determine if column is sortable
    defaultSort: 'asc', 	 	// optional, default sort direction asc/desc

    // optional, in case of custom HTML
    getHtml: function(value, record) {
      // return manipulated value
    }
}
```


## Z-Ray search and pagination widget
Z-Ray introduces two widgets that filter and limit the data displayed by the table widget. These widgets work on the same storage object as the table widget.

We need to update the panel configurations (Module.php) and add javascript hooking code:

```php
public function config() {
  return array(
    'extension' => array('name' => 'zrayDemo' ),
      'defaultPanels' => array(
        'myDefaultPanel' => false
      ),
      'panels' => array(
        'demo' => array(
        'display'     => true,
        'logo'        => 'logo.png',
        'menuTitle' 	=> 'Custom Panel',
        'panelTitle'	=> 'Custom Panel',

        // control search, pagination and report
        'searchId' 	=> 'demo-extension-search', // unique id 
        'pagerId'	  => 'demo-extension-pager',  // unique id 
      ),
    ),
  );
}
```

Now we simply create the widget and bind it to the storage using JavaScript :

```javascript
// create pager
zray.createPager(storage, 
jQuery('#<?php echo $tableParams['pagerId']; ?>'));


// create search
zray.createSearch(storage, 
jQuery('#<?php echo $tableParams['searchId']; ?>'), maintable);
```


## Z-Ray tree widget
Similar to the table widget, Z-Ray introduces a tree widget to create a tree structured view.
The only difference is the JavaScript  initialization.

```javascript
// create the main table and bind it to the storage and HTML element
var maintable = zray.createTreeTable(storage, 
jQuery('#<?php echo $tableParams['tableId']; ?>'));

// no need to define the columns descriptors
```

## Z-Ray general tree widget
The general tree widget is similar to the tree widget, with a known structured data and column descriptors. The data in all of its levels should have the same defined structure.

Here is an example of a data set:

```php
Array
(
	[name] => user
	[age] => 40
	[kids] => Array
    	(
        	[0] => Array
            	(
                	[name] => kid1
                	[age] => 14
                	[kids] =>
            	)
        	[1] => Array
            	(
                	[name] => kid2
                	[age] => 9
                	[kids] =>
            	)
    	)
)
```

And the JavaScript code is

```javascript
var maintable = zray.createGeneralTreeTable(storage, jQuery('#<?php echo $tableParams['tableId']; ?>'));
maintable.setColumns([
{
  label: 'Name',
  propertyName: 'name',
  width: '20%'
},
{
  label: 'Age',
  propertyName: 'value',
  width: '40%'
},
{
  label: 'Kids',
  propertyName: 'count',
  width: '40%'
}
]);
```

## Z-Ray resources
You can load external resources into each panel, such as JavaScript, CSS, etc.
This is done by adding a resources section to use panel description at Module.php

```php
public function config() {
  return array(
    'extension' => array('name' => 'zrayDemo' ),
      'defaultPanels' => array(
        'myDefaultPanel' => false
      ),
      'panels' => array(
        'demo' => array(
        'display'    => true,
        'logo'       => 'logo.png',
        'menuTitle' 	=> 'Custom Panel',
        'panelTitle'	=> 'Custom Panel',

        // control search, pagination and report
        'searchId' 	=> 'demo-extension-search', // unique id 
        'pagerId'	=> 'demo-extension-pager',  // unique id 
      ),
     'resources'  => array(
  	  'chartsJS'   => 'charts.js' 
       'chartsCSS'   => 'charts.css' 
      )
    ),
  );
}
```

Once this is done, the files content will be exposed using the $params['resources'] variable in the panel display.

```html
<script type="text/javascript">
<?php echo $params['resources'] ['chartJS']; ?>
</script>

<style>
<?php echo $params['resources'] ['chartCSS']; ?>
<style>
```


## Z-Ray tabs
You can create simple tabs using the following HTML code:

```html
<ul class="tabs">
<li>
	<input type="radio" name="demo-tabs" id="tab-demo1" checked />
    <label for="tab-demo1">Graph</label>
	<div id="tab-content-demo1" class="tab-content">
  	The tab content
  </div>
</li>
...
</ul>
```

## Z-Ray summary table
Summary tables are tables that let you filter the data using a check-box selection. They are similar to thge regular tables widget and are usually located to the left of it.
We start by declaring that we want to use a summary table by adding the container id and the width in the viewscript:

```php
<?php
$tableParams = array(
   'tableId'      		=> 'unique-main-table-id',
   'tableWidth'   		=> '3',
   'summaryTableId'  		=> 'unique-summary-table-id ',
   'summaryTableWidth'  	=> '1',
);
?>
```

And the JavaScript structure should look like this:

```javascript
// create the table
var summaryTable = zray.createSummaryTable(storage, jQuery('#<?php echo $tableParams['summaryTableId']; ?>'));

// set the columns structure
summaryTable.setColumns(...);

// set by which column the user choice will filter
summaryTable.setFilterColumn('name');

// set callback function to create the table rows from all the data
// return an array of rows, with columns that match to setColumns
summaryTable.setUpdateCallback(function (data) { });
```

One important thing is to set the first column in setColumns to create a check-box column:

```javascript
summaryTable.setColumns([
{
  label: 'name',
  propertyName: 'name',
  width: '10%',
  getHtml: function(value, record) {
  return summaryTable.createCheckboxColumn(value, record, summaryTable);
  }
},
...
```

## Additional methods
`zray` object expose additional methods that can be used:

- `updateMenuTitle(extension, namespace, label)` will update the menu title according to extension and namespace, handy in case you want to update records count.

- `getMenuTitle(extension, namespace, htmlFlag)` will return the menu title for given extension and namespace. Use the boolean `htmlFlag` to specify if you want the label text or html content, default to false. 
