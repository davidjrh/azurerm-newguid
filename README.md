# azurerm-newguid
Creation of Guids in an Azure Resource Manager template

## UPDATE!!! August 1st, 2017
I have blogged about "Providing a GUID function in Azure Resource Manager templates with Azure Functions" that works better than the current template on this repo http://davidjrh.intelequia.com/2017/08/providing-guid-function-in-azure.html 


---
This is a workaround for the problem described at https://feedback.azure.com/forums/281804-azure-resource-manager/suggestions/13067952-provide-guid-function-in-azure-resource-manager-te when you need a GUID on an Azure Resource Manager template. Currently there is no function to generate a GUID inside a RM template.

The basic idea is to reference this template in your deployment that will generate a GUID for later usage on the same template. 

## Examples

### Getting a guid and using it later

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [ 
    { 
        "apiVersion": "2015-01-01", 
        "name": "MyGuid", 
        "type": "Microsoft.Resources/deployments", 
        "properties": { 
          "mode": "incremental", 
          "templateLink": {
            "uri": "https://raw.githubusercontent.com/davidjrh/azurerm-newguid/master/NewGuid.json",
            "contentVersion": "1.0.0.0"
          }
        } 
    } 
  ],
  "outputs": {
    "result": {
      "type": "string",
      "value": "[reference('MyGuid').outputs.guid.value]"
    }
  }
}
```


### Getting multiple guids and using them later
```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [ 
    { 
        "apiVersion": "2015-01-01", 
        "name": "MyGuid0", 
        "type": "Microsoft.Resources/deployments", 
        "properties": { 
          "mode": "incremental", 
          "templateLink": {
            "uri": "https://raw.githubusercontent.com/davidjrh/azurerm-newguid/master/NewGuid.json",
            "contentVersion": "1.0.0.0"
          },
          "parameters": {
            "seed": {
              "value": "0"
            }
          }
        } 
    },
    { 
        "apiVersion": "2015-01-01", 
        "name": "MyGuid1", 
        "type": "Microsoft.Resources/deployments", 
        "properties": { 
          "mode": "incremental", 
          "templateLink": {
            "uri": "https://raw.githubusercontent.com/davidjrh/azurerm-newguid/master/NewGuid.json",
            "contentVersion": "1.0.0.0"
          },
          "parameters": {
            "seed": {
              "value": "1"
            }
          }
        } 
    }     
  ],
  "outputs": {
    "result0": {
      "type": "string",
      "value": "[reference('MyGuid0').outputs.guid.value]"
    },
    "result1": {
      "type": "string",
      "value": "[reference('MyGuid1').outputs.guid.value]"
    }    
  }
}
```

### Creating multiple Guids at once

```
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [ 
    { 
        "apiVersion": "2015-01-01", 
        "name": "[concat('MyGuid', copyIndex())]",
        "type": "Microsoft.Resources/deployments", 
        "copy": {
          "name": "guidCopy",
          "count": 7
        },        
        "properties": { 
          "mode": "incremental", 
          "templateLink": {
            "uri": "https://raw.githubusercontent.com/davidjrh/azurerm-newguid/master/NewGuid.json",
            "contentVersion": "1.0.0.0"
          },
          "parameters": {
            "seed": { "value": "[string(copyIndex())]" }
          }          
        } 
    } 
  ],
  "outputs": {
  }
}
```
