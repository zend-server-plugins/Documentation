Z-Ray Extension API
==========

This document will describe the Z-Ray extensions API concepts, how to use and supply code samples.

## Extension Structure
A Z-Ray extension is comprised of two main components:

1. A `zray.php` file - the zray extension include file, which is expected to be located in the root of the extension directory.
2. Other files, such as view scripts, JavaScript and PHP components.

The extension implements a tracing functionality for collecting and storing data into the custom data zray database table. This information is available for use using the zrayGetCustomData Web API action, and is utilized by Z-Ray for display purposes.

Unless specified otherwise, any data type stored using the ZrayExtension class will be used to automatically generate a data table based on the information saved in the data type.

A view script may be added to the extension to create a custom display.

## Code Examples
You can find code examples for all the content in this document (and more!) in our GitHub account http://github.com/zend-server-extensions. You can find an example Z-Ray extension here: http://github.com/zend-server-extensions/Z-Ray-Samples. 

<br/><br/>

## Table of Contents

- [Z-Ray Data Collection](DataCollection.md)
	- [Z-Ray Extension Include File (zray.php) and the ZrayExtension class](DataCollection.md#z-ray-extension-include-file-zrayphp-and-the-zrayextension-class)
	- [zrayExtension](DataCollection.md#zrayextension)
	- [zrayExtension::setEnabled()](DataCollection.md#zrayextensionsetenabled)
	- [zrayExtension::setEnabledAfter($initFunctionsName);](DataCollection.md#zrayextensionsetenabledafterinitfunctionsname)
	- [zrayExtension::traceFunction($pattern, $onenter, $onleave);](DataCollection.md#zrayextensiontracefunctionpattern-onenter-onleave)
	- [zrayExtension::untraceFunction($functionName);](DataCollection.md#zrayextensionuntracefunctionfunctionname)
	- [zrayExtension::traceFile($filename, $onenter, $onleave);](DataCollection.md#zrayextensiontracefilefilename-onenter-onleave)
	- [zrayExtension::setMetadata($metadata);](DataCollection.md#zrayextensionsetmetadatametadata)
- [Z-Ray Data Display](DataDisplay.md#)
	- [Automatically generated panels](DataDisplay.md#automatically-generated-panels)
	- [Z-Ray Extension Module.php](DataDisplay.md#z-ray-extension-modulephp)
	- [Z-Ray extension viewscript](DataDisplay.md#z-ray-extension-viewscript)
- [Z-Ray Widgets](Widgets.md#)
	- [Z-Ray data handling](Widgets.md#z-ray-data-handling)
	- [Z-Ray table widget](Widgets.md#z-ray-table-widget)
	- [Z-Ray search and pagination widget](Widgets.md#z-ray-search-and-pagination-widget)
	- [Z-Ray tree widget](Widgets.md#z-ray-tree-widget)
	- [Z-Ray general tree widget](Widgets.md#z-ray-general-tree-widget)
	- [Z-Ray resources](Widgets.md#z-ray-resources)
	- [Z-Ray tabs](Widgets.md#z-ray-tabs)
	- [Z-Ray summary table](Widgets.md#z-ray-summary-table)
	- [Additional methods](Widgets.md#additional-methods)
