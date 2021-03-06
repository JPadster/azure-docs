---
title: Create an activity log alert with a Resource Manager Template  | Microsoft Docs
description: Get notified when your Azure resources are created.
author: anirudhcavale
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.assetid:
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: ancav

---
# Create an activity log alert with a Resource Manager Template
This article shows how you can use an [Azure Resource Manager template](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-authoring-templates) to configure activity log alerts. This enables you to automatically set up alerts on your resources when they are created as part of your automated deployment process.

The basic steps are as follows:

1.	Create a template as a JSON file that describes how to create the activity log alert.
2.	[Deploy the template using any deployment method.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

## Resource Manager template for an activity log alert
To create an activity log alert using a Resource Manager template, you create a resource of type `microsoft.insights/activityLogAlerts` and fill in all related properties. Below is a template that creates an activity log alert.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ] 
        },
        "actions": {
          "actionGroups": 
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```

You can also [visit our Quickstart Gallery](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Insights) for some examples of Activity Log alert templates.

## Next Steps
- Learn more about [Alerts](monitoring-overview-alerts.md)  
- How to add [action groups using a Resource Manager template](monitoring-create-action-group-with-resource-manager-template.md)
- [Create an Activity Log Alert to monitor all autoscale engine operations on your subscription.](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Create an Activity Log Alert to monitor all failed autoscale scale in/scale out operations on your subscription](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)
