# Azure Monitor Baseline Alerts - Additional AMBA Alerts

## Introduction
This repo provides additional alert configurations for Azure resources which are currently not in scope in the main AMBA repo here: https://azure.github.io/azure-monitor-baseline-alerts/welcome/

## Network/routeServers

The Alert state column shows the state that the alert is deployed with by default. This default value maps to the following principles:
- Enabled: The alert is deployed as enabled by default and has to be considered a Must Have alert
- Disabled: The alert is deployed as disabled by default and has to be considered a Nice to Have alert. Customers can enable the alert up to their choice.

|Name|Type|Alert state|Description|
|---|---|---|---|
|[BgpPeerStatus](#bgp-peer-status)|Metric|Enabled| Up or Down Status of BGP Peers|
|[CountOfRoutesAdvertisedToPeer](#count-of-routes-advertised-to-peer)|Metric|Enabled| Total number of routes advertised to a Peer (count)|
|[CountOfRoutesLearnedFromPeer](#count-of-routes-learned-from-peer)|Metric|Enabled| Total number of routes learned from a Peer (count)|
|[RoutingInfrastructureUnits](#routing-infrastructure-units)|Metric|Disabled| Total number of routing infrastructure units, which represent the virtual hub's capacity |
|[SpokeVMUtilization](#spoke-vm-utilization)|Metric|Disabled| Number of deployed spoke VMs as a percentage of the total number of spoke VMs that the hub's routing infrastructure units can support|
|[VirtualHubDataProcessed](#virtual-hub-data-processed)|Metric|Disabled| Data processed by the Virtual Hub Router. Data on how much traffic traverses the virtual hub router in a given time period. (bytes)|

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
[View routeServerBgpPeerStatus.json](templates/policy/routeServerBgpPeerStatus.json)
</details>

<details>
<summary>Deploy to ARM Template</summary>
[View routeServerBgpPeerStatus.json](templates/arm/routeServerBgpPeerStatus.json)
</details> 




## References:
Virtual Hubs: https://learn.microsoft.com/en-us/azure/azure-monitor/reference/supported-metrics/microsoft-network-virtualhubs-metrics
Virtual Routers (Route Server): https://learn.microsoft.com/en-us/azure/azure-monitor/reference/supported-metrics/microsoft-network-virtualrouters-metrics

