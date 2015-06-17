#Route Extension API#
This document describes the Route extension API concepts, and provides usage examples.
Zend Server Route plugins help Zend Server understand the specific routing system of your application. Using this application awareness, Zend Server can better aggregate monitoring events to prevent event propagation, and analyze the URLs displayed by the URL Insight feature. 
##Plugin Structure##
A Route plugin is comprised of a 'route.php' file which contains all the plugin logic. 
##ZAppsPlugin##
Zend internally defined class that inherits the plugin object.
```php
<?php
class MyAppPlugin extends ZAppsPlugin {
}
```
##setWatchedFunction##
setWatchedFunction enables you to track one of your application functions. It provides you with the ability to set two callback functions that will be called whenever the application function is executed. The first callback will be called when the function is entered, and the second will be called when the function exits.
You can also have access to the function parameters using $context[‘functionArgs’].

```php
$myAppPlugin>setWatchedFunction(MyRouteClass::resolveRoute, array($myAppPlugin, ‘resolveRouteEnter’), array($myAppPlugin, ‘resolveRouteLeave’);
```
##setRequestRoute##
This method is defined by 'ZAppsPlugin' and is used to define the current request route in Zend Server. 
```php
$this->setRequestRoute($myRoute);
```
##Full Example##
The following example defines the routing for Magento requests:
```php
<?php

	class MagentoPlugin extends ZAppsPlugin {
		
		public function resolveRouteEnter($context) {
			
		}
		
		public function resolveRouteLeave($context) {
			if (!$this->resolved) {
				$this->resolved = true;	
				$route = array(					
					"module" => Mage::app()->getFrontController()->getRequest()->getModuleName(),
					"controller" => Mage::app()->getFrontController()->getRequest()->getControllerName(),
					"action" => Mage::app()->getFrontController()->getRequest()->getActionName()
					);
										
				$this->setRequestRoute($route);		
			}
		}		
		
		private $resolved = false;
	}
	
	$magentoPlugin = new MagentoPlugin();
	$magentoPlugin->setWatchedFunction("Mage_Core_Controller_Varien_Front::dispatch", 
		array($magentoPlugin, "resolveRouteEnter"), 
		array($magentoPlugin, "resolveRouteLeave"));
```
