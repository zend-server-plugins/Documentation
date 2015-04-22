Zend Server Plugins - API Version 1.0
==========

This document will describe the concepts of Zend Server Plugin, how to use and supply code samples. 

## What is Zend Server Plugin ?
Zend Server plugin is an archive file that consist with [Z-Ray extension](http://files.zend.com/help/Zend-Server/zend-server.htm#z-ray_concept.htm) and Routing information, each one of them is optional. The plugin can be installed throw the Zend Server UI.

## Plugin Structure
Plugin is comprised of two main directories `zray` and `route` each include the relevant files for Z-Ray extension and route information. Additional file is required `deployment.json` which describe the plugin package.

## Skeleton package
You can download the skeleton package from here.

## Table of Contents
- [deployment.json](DeploymentJson.md)
- [Z-Ray extension API](ZRayApi.md)
- [Routing information API](Routing.md)
