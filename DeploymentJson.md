Deployment.json
==========

This file describes the package content.

example:

	{
	  "name" : "magento",
	  "display_name" : "Magento",
	  "version" : "1.0.0",
	  "type": ["zray", "route"],
	  "eula": "EULA.txt",
	  "readme": "README.txt",
	  "logo": "logo.png",
	  "dependencies": {
        "plugin": {
            "min": "1.0",
            "max": "2.0"
        },
        "php": {
            "min": "2.7.0"
        },
        "zendservercomponent": [{
            "name": "Zend Monitor"
        }],
        "extension": [{
            "name": "oci8",
            "equals": "2.0.8"
        }, {
            "name": "gd"
        }],
        "directive": [{
            "name": "bcmath.scale",
            "equals": "0"
        }]
      }
	}

Name | Required | Description
------------ | ------------- | -------------
name | true | The name of the package. Try to be as unique as possible since this is the identifier of your package from now on. Lowercase is recommended.
display_name | true |  The plugin's display name, visible to users and on the online gallery.
version | true | The version of the package.
type | true| array of one or more of the following values: `zray`, `route`.
eula | false | Path to the end user agreement file, markdown supported.
readme | false | Path to the readme file, markdown supported.
logo | false | Path to the package logo file, transparent PNG is recommended.
dependencies | false | List of dependencies required by the plugin.

### dependencies 
Dependencies is a list of the following sections and their values:

 - plugin - The plugin API version
	 - min
	 - max
	 - equals
	 - exclude
 - php
	 - min
	 - max
	 - equals
	 - exclude
 - zendserver
	 - min
	 - max
	 - equlas
	 - exclude
 - zendservercomponent - array of objects:
	 - name
	 - min
	 - max
	 - equals
	 - exclude
	 - conflicts
 - zendframework
	 - min
	 - max
	 - equals
	 - exclude
 - extension - array of objects:
	 - name
	 - min
	 - max
	 - equals
	 - exclude
	 - conflicts
 - directive - array of objects:
	 - name
	 - min
	 - max
	 - equals
 - library - array of objects:
	 - name
	 - min
	 - max
	 - equals
	 - deployed
