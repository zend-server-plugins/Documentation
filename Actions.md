Z-Ray Actions
==========

**Note**: available from API 1.0.3 (Zend Server 8.5)

Z-Ray frontend can communicate with the backend to run actions or get additional data for display. The communication is done via ajax request and will be caught and executed by Z-Ray in case attachAction was used with the same action name.

## Trigger Action

Actions can be triggered by using the `zray.runAction(extension, action, params, userFunc)` method.

Parameter | Description
------------ | -------------
extension | Current extension name
action | Name for the action, will be caught in server side
params | Parameters object (optional)
userFunc | Method to be executed once the request completed (optional)

**Example:**
```javascript
var params = {'filepath': filePath, 'code': code};
zray.runAction('Online Editor', 'saveCode', params, function(response) {
	// inspect response and do something
});
```

## Attach Action 
``zrayExtension::attachAction($actionName, $runAfterMethod, $callable);``

This method allow us to declare in server side that we want to listen (or attach) to a specific action and act upon its arrival. Once a request with the attached action was called the `$callable` method will be executed only **after** the `$runAfterMethod` was called.

So for example if we want to attach to upcoming action called `saveCode` we will use
```php
<?php
$zre->attachAction('saveCode', 'authenticate', function(){ // do something });
?>
```
Once a request with action `saveCode` is called then the `function(){...}` part will be executed only after the `authenticate` method was called. If `authenticate` method wasn't used in the request nothing will be executed.

**Note**: Requests with action parameter won't show up any output except the `callable` method output. This is done to ensure you will only get the output you meant for and not the regular request output.