# azurerm-newguid
Creation of Guids in an Azure Resource Manager template

This is a workaround for the problem described at https://feedback.azure.com/forums/281804-azure-resource-manager/suggestions/13067952-provide-guid-function-in-azure-resource-manager-te when you need a GUID on an Azure Resource Manager template. Currently there is no function to generate a GUID inside a RM template.

The basic idea is to reference this template in your deployment that will generate a GUID for later usage on the same template. 

## Example

