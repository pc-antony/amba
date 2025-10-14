# Azure Monitor Baseline Alerts - Additional AMBA Alerts

## Introduction
This repo provides additional alert configurations for Azure resources which are currently not in scope in the main AMBA repo here: https://azure.github.io/azure-monitor-baseline-alerts/welcome/

## Network/routeServers

The Alert state column shows the state that the alert is deployed with by default. This default value maps to the following principles:
- Enabled: The alert is deployed as enabled by default and has to be considered a Must Have alert
- Disabled: The alert is deployed as disabled by default and has to be considered a Nice to Have alert. Customers can enable the alert up to their choice.

|Name|Type|Alert state|Description|
|---|---|---|---|
|[PeeringAvailability](#peering-availability) |Metric|Enabled| BGP Availability between VirtualRouter and remote peers (%)|
|[BgpPeerStatus](#bgp-peer-status)|Metric|Enabled| Up or Down Status of BGP Peers|
|CountOfRoutesAdvertisedToPeer |Metric|Enabled| Total number of routes advertised to a Peer (count)|
|CountOfRoutesLearnedFromPeer |Metric|Enabled| Total number of routes learned from a Peer (count)|
|RoutingInfrastructureUnits |Metric|Disabled| Total number of routing infrastructure units, which represent the virtual hub's capacity |
|SpokeVMUtilization |Metric|Disabled| Number of deployed spoke VMs as a percentage of the total number of spoke VMs that the hub's routing infrastructure units can support|
|VirtualHubDataProcessed |Metric|Disabled| Data processed by the Virtual Hub Router. Data on how much traffic traverses the virtual hub router in a given time period. (bytes)|

### Peering Availability
   *BGP Availability between VirtualRouter and remote peers*
   
**Properties:**
|Property|Value|
|---|---|
|metricName|PeeringAvailability|
|metricNamespace|Microsoft.Network/virtualRouters|
|autoMitigate|false|
|enabled|true|
|criterionType|StaticThresholdCriterion|
|timeAggregation|Average|
|operator|LessThan|
|threshold|1|
|evaluationFrequency|PT5M|
|windowSize|PT5M|
|severity|0|

### BGP Peer Status
   *Up or Down Status of BGP Peers*

**Properties:**
|Property|Value|
|---|---|
|metricName|bgpPeerStatus|
|metricNamespace|Microsoft.Network/virtualhubs|
|autoMitigate|false|
|enabled|true|
|criterionType|StaticThresholdCriterion|
|timeAggregation|Average|
|operator|LessThan|
|threshold|1|
|evaluationFrequency|PT5M|
|windowSize|PT5M|
|severity|0|

#### Templates:
[![Deploy to ARM](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fpc-antony%2famba%2frefs%2fheads%2fmain%2ftemplates%2farm%2frouteServerBgpPeerStatus.json)

<details>
<summary>Deploy to Azure Policy</summary>

```
{
    "mode": "All",
    "parameters": {
      "severity": {
        "type": "String",
        "metadata": {
          "displayName": "Severity",
          "description": "Severity of the Alert"
        },
        "allowedValues": [
          "0",
          "1",
          "2",
          "3",
          "4"
        ],
        "defaultValue": "1"
      },
      "windowSize": {
        "type": "String",
        "metadata": {
          "displayName": "Window Size",
          "description": "Window size for the alert"
        },
        "allowedValues": [
          "PT1M",
          "PT5M",
          "PT15M",
          "PT30M",
          "PT1H",
          "PT6H",
          "PT12H",
          "P1D"
        ],
        "defaultValue": "PT5M"
      },
      "evaluationFrequency": {
        "type": "String",
        "metadata": {
          "displayName": "Evaluation Frequency",
          "description": "Evaluation frequency for the alert"
        },
        "allowedValues": [
          "PT1M",
          "PT5M",
          "PT15M",
          "PT30M",
          "PT1H"
        ],
        "defaultValue": "PT1M"
      },
      "autoMitigate": {
        "type": "String",
        "metadata": {
          "displayName": "Auto Mitigate",
          "description": "Auto Mitigate for the alert"
        },
        "allowedValues": [
          "true",
          "false"
        ],
        "defaultValue": "true"
      },
      "enabled": {
        "type": "String",
        "metadata": {
          "displayName": "Alert State",
          "description": "Alert state for the alert"
        },
        "allowedValues": [
          "true",
          "false"
        ],
        "defaultValue": "true"
      },
      "threshold": {
        "type": "String",
        "metadata": {
          "displayName": "Threshold",
          "description": "Threshold for the alert"
        },
        "defaultValue": "80"
      },
      "effect": {
        "type": "String",
        "metadata": {
          "displayName": "Effect",
          "description": "Effect of the policy"
        },
        "allowedValues": [
          "deployIfNotExists",
          "disabled"
        ],
        "defaultValue": "deployIfNotExists"
      },
      "MonitorDisableTagName": {
        "type": "String",
        "metadata": {
          "displayName": "Monitoring disabled tag name",
          "description": "Tag name used to disable monitoring at the resource level. Set to true if monitoring should be disabled."
        },
        "defaultValue": "MonitorDisable"
      },
      "MonitorDisableTagValues": {
        "type": "Array",
        "metadata": {
          "displayName": "Monitoring disabled tag values(s)",
          "description": "Tag value(s) used to disable monitoring at the resource level. Set to true if monitoring should be disabled."
        },
        "defaultValue": [
          "true",
          "Test",
          "Dev",
          "Sandbox"
        ]
      }
    },
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Network/expressRouteGateways"
          },
          {
            "field": "[concat('tags[', parameters('MonitorDisableTagName'), ']')]",
            "notIn": "[parameters('MonitorDisableTagValues')]"
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]",
        "details": {
          "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
          ],
          "type": "Microsoft.Insights/metricAlerts",
          "name": "[concat(replace(field('fullName'),'/','-'), '-ExpressRouteGatewayCpuUtilization')]",
          "existenceCondition": {
            "allOf": [
              {
                "field": "Microsoft.Insights/metricAlerts/criteria.Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria.allOf[*].metricNamespace",
                "equals": "Microsoft.Network/expressRouteGateways"
              },
              {
                "field": "Microsoft.Insights/metricAlerts/criteria.Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria.allOf[*].metricName",
                "equals": "ExpressRouteGatewayCpuUtilization"
              },
              {
                "field": "Microsoft.Insights/metricalerts/scopes[*]",
                "equals": "[field('id')]"
              },
              {
                "field": "Microsoft.Insights/metricAlerts/enabled",
                "equals": "[parameters('enabled')]"
              },
              {
                "field": "Microsoft.Insights/metricAlerts/evaluationFrequency",
                "equals": "[parameters('evaluationFrequency')]"
              },
              {
                "field": "Microsoft.Insights/metricAlerts/windowSize",
                "equals": "[parameters('windowSize')]"
              },
              {
                "field": "Microsoft.Insights/metricalerts/severity",
                "equals": "[parameters('severity')]"
              },
              {
                "field": "Microsoft.Insights/metricAlerts/autoMitigate",
                "equals": "[parameters('autoMitigate')]"
              },
              {
                "field": "Microsoft.Insights/metricAlerts/criteria.Microsoft-Azure-Monitor-SingleResourceMultipleMetricCriteria.allOf[*].timeAggregation",
                "equals": "Average"
              },
              {
                "field": "Microsoft.Insights/metricAlerts/criteria.Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria.allOf[*].StaticThresholdCriterion.operator",
                "equals": "GreaterThan"
              },
              {
                "field": "Microsoft.Insights/metricAlerts/criteria.Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria.allOf[*].StaticThresholdCriterion.threshold",
                "equals": "[if(contains(field('tags'), '_amba-ExpressRouteGatewayCpuUtilization-threshold-Override_'), field('tags._amba-ExpressRouteGatewayCpuUtilization-threshold-Override_'), parameters('threshold'))]"
              }
            ]
          },
          "deployment": {
            "properties": {
              "mode": "incremental",
              "template": {
                "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                  "resourceName": {
                    "type": "String",
                    "metadata": {
                      "displayName": "resourceName",
                      "description": "Name of the resource"
                    }
                  },
                  "resourceId": {
                    "type": "String",
                    "metadata": {
                      "displayName": "resourceId",
                      "description": "Resource ID of the resource emitting the metric that will be used for the comparison"
                    }
                  },
                  "severity": {
                    "type": "String"
                  },
                  "windowSize": {
                    "type": "String"
                  },
                  "evaluationFrequency": {
                    "type": "String"
                  },
                  "autoMitigate": {
                    "type": "String"
                  },
                  "enabled": {
                    "type": "String"
                  },
                  "threshold": {
                    "type": "String"
                  }
                },
                "variables": {},
                "resources": [
                  {
                    "type": "Microsoft.Insights/metricAlerts",
                    "apiVersion": "2018-03-01",
                    "name": "[concat(parameters('resourceName'), '-ExpressRouteGatewayCpuUtilization')]",
                    "location": "global",
                    "tags": {
                      "_deployed_by_amba": true
                    },
                    "properties": {
                      "description": "Metric Alert for Network expressRouteGateways ExpressRouteGatewayCpuUtilization",
                      "severity": "[parameters('severity')]",
                      "enabled": "[parameters('enabled')]",
                      "scopes": [
                        "[parameters('resourceId')]"
                      ],
                      "evaluationFrequency": "[parameters('evaluationFrequency')]",
                      "windowSize": "[parameters('windowSize')]",
                      "criteria": {
                        "allOf": [
                          {
                            "name": "ExpressRouteGatewayCpuUtilization",
                            "metricNamespace": "Microsoft.Network/expressRouteGateways",
                            "metricName": "ExpressRouteGatewayCpuUtilization",
                            "operator": "GreaterThan",
                            "threshold": "[parameters('threshold')]",
                            "timeAggregation": "Average",
                            "criterionType": "StaticThresholdCriterion"
                          }
                        ],
                        "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria"
                      },
                      "autoMitigate": "[parameters('autoMitigate')]",
                      "parameters": {
                        "severity": {
                          "value": "[parameters('severity')]"
                        },
                        "windowSize": {
                          "value": "[parameters('windowSize')]"
                        },
                        "evaluationFrequency": {
                          "value": "[parameters('evaluationFrequency')]"
                        },
                        "autoMitigate": {
                          "value": "[parameters('autoMitigate')]"
                        },
                        "enabled": {
                          "value": "[parameters('enabled')]"
                        },
                        "threshold": {
                          "value": "[parameters('threshold')]"
                        }
                      }
                    }
                  }
                ]
              },
              "parameters": {
                "resourceName": {
                  "value": "[replace(field('fullName'),'/','-')]"
                },
                "resourceId": {
                  "value": "[field('id')]"
                },
                "severity": {
                  "value": "[parameters('severity')]"
                },
                "windowSize": {
                  "value": "[parameters('windowSize')]"
                },
                "evaluationFrequency": {
                  "value": "[parameters('evaluationFrequency')]"
                },
                "autoMitigate": {
                  "value": "[parameters('autoMitigate')]"
                },
                "enabled": {
                  "value": "[parameters('enabled')]"
                },
                "threshold": {
                  "value": "[if(contains(field('tags'), '_amba-ExpressRouteGatewayCpuUtilization-threshold-Override_'), field('tags._amba-ExpressRouteGatewayCpuUtilization-threshold-Override_'), parameters('threshold'))]"
                }
              }
            }
          }
        }
      }
    }
  }
```
</details>
<details>
<summary>Deploy to ARM Template</summary>

```
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "alertName": {
      "type": "string",
      "minLength": 1,
      "metadata": {
        "description": "Name of the alert"
      }
    },
    "alertDescription": {
      "type": "string",
      "defaultValue": "Metric Alert for Route Server BGP Peer Status",
      "metadata": {
        "description": "Description of alert"
      }
    },
    "targetResourceId": {
      "type": "string",
      "minLength": 1,
      "metadata": {
        "description": "List of Azure resource Ids seperated by a comma. For example - /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroup/resource-group-name/Microsoft.compute/virtualMachines/vm-name"
      }
    },
    "targetResourceRegion": {
      "type": "string",
      "metadata": {
        "description": "Azure region in which target resources to be monitored are in (without spaces). For example: UKSouth"
      }
    },
    "targetResourceType": {
      "type": "string",
      "minLength": 1,
      "metadata": {
        "description": "Resource type of target resources to be monitored."
      }
    },
    "isEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Specifies whether the alert is enabled"
      }
    },
    "alertSeverity": {
      "type": "int",
      "defaultValue": 1,
      "allowedValues": [
        0,
        1,
        2,
        3,
        4
      ],
      "metadata": {
        "description": "Severity of alert {0,1,2,3,4}"
      }
    },
    "operator": {
      "type": "string",
      "defaultValue": "LessThan",
      "allowedValues": [
        "Equals",
        "GreaterThan",
        "GreaterThanOrEqual",
        "LessThan",
        "LessThanOrEqual"
      ],
      "metadata": {
        "description": "Operator comparing the current value with the threshold value."
      }
    },
    "threshold": {
      "type": "string",
      "defaultValue": "1",
      "metadata": {
        "description": "The threshold value at which the alert is activated."
      }
    },
    "timeAggregation": {
      "type": "string",
      "defaultValue": "Maximum",
      "allowedValues": [
        "Average",
        "Minimum",
        "Maximum",
        "Total",
        "Count"
      ],
      "metadata": {
        "description": "How the data that is collected should be combined over time."
      }
    },
    "windowSize": {
      "type": "string",
      "defaultValue": "PT5M",
      "allowedValues": [
        "PT1M",
        "PT5M",
        "PT15M",
        "PT30M",
        "PT1H",
        "PT6H",
        "PT12H",
        "P1D"
      ],
      "metadata": {
        "description": "Period of time used to monitor alert activity based on the threshold. Must be between one minute and one day. ISO 8601 duration format."
      }
    },
    "evaluationFrequency": {
      "type": "string",
      "defaultValue": "PT5M",
      "allowedValues": [
        "PT1M",
        "PT5M",
        "PT15M",
        "PT30M",
        "PT1H"
      ],
      "metadata": {
        "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
      }
    },
    "currentDateTimeUtcNow": {
      "type": "string",
      "defaultValue": "[utcNow()]",
      "metadata": {
          "description": "The current date and time using the utcNow function. Used for deployment name uniqueness"
      }
    },
    "telemetryOptOut": {
      "type": "string",
      "defaultValue": "No",
      "allowedValues": [
          "Yes",
          "No"
      ],
      "metadata": {
          "description": "The customer usage identifier used for telemetry purposes. The default value of False enables telemetry. The value of True disables telemetry."
      }
    }
  },
  "variables": {
    "pidDeploymentName": "[take(concat('pid-8bb7cf8a-bcf7-4264-abcb-703ace2fc84d-', uniqueString(resourceGroup().id, parameters('alertName'), parameters('currentDateTimeUtcNow'))), 64)]",
    "varTargetResourceId": "[split(parameters('targetResourceId'), ',')]"
  },
  "resources": [
    {
      "type": "Microsoft.Insights/metricAlerts",
      "apiVersion": "2018-03-01",
      "name": "[parameters('alertName')]",
      "location": "global",
      "tags": {
        "_deployed_by_amba": true
      },
      "properties": {
        "description": "[parameters('alertDescription')]",
        "scopes": "[variables('varTargetResourceId')]",
        "targetResourceType": "[parameters('targetResourceType')]",
        "targetResourceRegion": "[parameters('targetResourceRegion')]",
        "severity": "[parameters('alertSeverity')]",
        "enabled": "[parameters('isEnabled')]",
        "evaluationFrequency": "[parameters('evaluationFrequency')]",
        "windowSize": "[parameters('windowSize')]",
        "criteria": {
          "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
          "allOf": [
            {
              "name": "1st criterion",
              "metricName": "BgpPeerStatus",
              "dimensions": [],
              "operator": "[parameters('operator')]",
              "threshold": "[parameters('threshold')]",
              "timeAggregation": "[parameters('timeAggregation')]",
              "criterionType": "StaticThresholdCriterion"
            }
          ]
        }
      }
    },
    {
      "condition": "[equals(parameters('telemetryOptOut'), 'No')]",
      "apiVersion": "2023-07-01",
      "name": "[variables('pidDeploymentName')]",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "template": {
              "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
              "resources": []
          }
      }
    }
  ]
}
```
</details> 




## References:
Virtual Hubs: https://learn.microsoft.com/en-us/azure/azure-monitor/reference/supported-metrics/microsoft-network-virtualhubs-metrics
Virtual Routers (Route Server): https://learn.microsoft.com/en-us/azure/azure-monitor/reference/supported-metrics/microsoft-network-virtualrouters-metrics

