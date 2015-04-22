Z-Ray Data Collection
==========

## Z-Ray Extension Include File (zray.php) and the ZrayExtension class
The `zray.php` file is a mandatory file which should be placed in the root directory of the extension's directory. This file will be automatically included by Z-Ray and executed prior to the application's script execution. Note that this script is considered part of PHP's runtime.

The `zray.php` is the file to use to implement the Z-Ray extension storage routines. This script is used to implement collection of data from the application, and at the end of the request, this information is stored in the database.

## zrayExtension
Class for declaring a new extension. Serves as the main API for defining trace actions and metadata. This class can be instantiated for as many extensions as necessary.

**NOTE:** By default, ZRayExtension is disabled, meaning that all future calls to 'traceFunction' or 'traceFile' will be queued until the extension is enabled.

```php
<?php
// Create a default extension. Disabled
$zre = new ZRayExtension('my-extension');
?>
```

OR, you can allocate a new ZRayExtension enabled (i.e. all calls to traceFunctions/traceFile will start working), not recommended.

```php
<?php
// Create a default extension. Enabled
$zre = new ZRayExtension('my-extension', true);
?>
```

## zrayExtension::setEnabled()
As mentioned earlier, a ZRayExtension is allocated disabled - this means that no data is collected until the extension is enabled.

To enable it, one must do one of the following:

1. Explicitly call to `$zre->setEnabled()` - this call enables ZRayExtension unconditionally
2. Pass true to the ZRayExtension constructor's second argument
3. Enable it asynchronously by using the method: `$zre->setEnabledAfter` (see next section)


## zrayExtension::setEnabledAfter($initFunctionsName);
This function will enable ZRayExtension if the function $initFunctionName is invoked.
It relies on the knowledge of the extension developer to provide a method name which is called to initialize the library.

For example, a Zend Framework 2 ZRayExtension will use code similar to this:

```php
<?php
function onFunctionEnter($context, &$storage) {}
function onFunctionLeave($context, &$storage) {}

// Create new extension - disabled
$zre = new ZRayExtension("ZendFramework2");

// Trace the 'triggerListeners' function
$zre->traceFunction('Zend\EventManager\EventManager::triggerListeners', 'onFunctionEnter', 'onFunctionLeave');

// start tracing only when 'Mage::run()' is called
$zre->setEnabledAfter('Mage::run');
?>
```

In the above example, the extension will start collecting data only after the function `Mage::run()` is called.


## zrayExtension::traceFunction($pattern, $onenter, $onleave);
traceFunction declares a trace on a particular name pattern of a method or function. The trace allows passing of two Callables which are fired when entering the function described by the pattern and when leaving it, respectively.

A function can be declared using its name. Methods are declared using class name and method name (Class::method).

```php
<?php
$zre->traceFunction('strtolower', function(){echo 'enter';}, function(){echo 'leave';});
?>
```

Legitimate Callables are lambda's, function names, an array of the form array('class', 'static method') or array($object, 'method'), an object that implements an __invoke() method.

Note that patterns are compared against concrete method calls - a method overridden in an inheriting class will not be traced if traceFunction is called against the superclass:

```php
<?php
$zre->traceFunction('super::foo', function(){echo 'enter';}, function(){});
?>
```
... and in index.php

```php
<?php
class super {
function foo(){}
}

class child extends super{
function foo(){}
}

$test = new child();
$test->foo();
?>
```

In this case, no tracing will be done.

The Callable passed to the traceFunction accepts two parameters:
```php
function($context, & $storage);
```

`$context` is an array of information about the function. It includes several keys that contain strings or arrays of data:

Name | Description
------------ | -------------
functionName | The function or method's name
functionArgs | An array of the arguments passed to the function. This will be a numbered array with the parameters in their order of appearance.
exceptionThrown | Did the function throw an exception?
exception | the exception object that was thrown (can be NULL)
this | The $this context of a method. Will be null for global or lambda functions that have no context
returnValue | The function's return value
locals | the functions variables, as they were at the end of the function. Variables can be accessed like this: `$context['locals']['myLocalVariable']` (note the missing $ sign from the variable name).
timesCalled | The number which the current function called
durationInclusive | The duration of the function include it's sub childrens
durationExclusive | The duration of the function itself
CalledFromFile | The filename which the function was called
CalledFromLine | The file line which the function was called
extension | The zrayExtension instance

`$storage` is a reference to the storage array. This variable must be declared with an ampersand sign to remain a reference - otherwise it will not be usable. This array is used in the traceFunction to store information into the custom data database.

Note that `$storage` is provided per instance of a trace function call. This means that different traces of the same function will get empty $storage arrays. However, the same $storage is passed to the onenter and onleave callables, so that you may access parameters stored onenter in onleave.

## zrayExtension::untraceFunction($functionName);
The opposite of traceFunction, which will remove the tracing for a given function name.

## zrayExtension::traceFile($filename, $onenter, $onleave);
Similar to traceFunction, but instead of passing the traced function name to the function, the file name (in full path) is passed.
This has the effect of calling `traceFunction()` for every function defined in `$filename`


## zrayExtension::setMetadata($metadata);
This method accepts any parameter and stores it as the extension's metadata array. This metadata is then provided as-is to Z-Ray when the custom data is loaded as part of the request's payload.

This medium can be useful for passing configuration hints or other details to your viewscript that were retrieved during the application request's execution.

You can use the unique key 'logo' to set the default logo for your extension. This is done by passing a full path to the logo file, for example:

```php
<?php
$zre->setMetadata(array(
	'logo' => __DIR__ . DIRECTORY_SEPARATOR . 'logo.png',
	'foo' => 'bar',
));
?>
```
