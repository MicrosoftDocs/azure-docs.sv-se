---
title: Filnamnstillägg för resurstyper
description: Visar en lista över Azures resurs typer som används för att utöka funktionerna i andra resurs typer.
ms.topic: conceptual
ms.date: 11/14/2020
ms.openlocfilehash: 5561c480dd5a2849588ed2288eb5bcc35fc1446c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94658459"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Resurs typer som utökar funktioner i andra resurser

En tilläggs resurs är en resurs som lägger till en annan resurss funktioner. Resurs låsning är till exempel en tilläggs resurs. Du kan använda ett resurs lås på en annan resurs för att förhindra att den tas bort eller ändras. Det är inte klokt att skapa ett resurs lås av sig självt. En tilläggs resurs används alltid på en annan resurs.

## <a name="microsoftadvisor"></a>Microsoft. Advisor

- Microsoft. Advisor/konfigurationer
- Microsoft. Advisor/rekommendationer
- Microsoft. Advisor/undertrycker

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

- Microsoft. AlertsManagement/Alerts

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/låsen
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions

## <a name="microsoftautomanage"></a>Microsoft. autohantera

- Microsoft. automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft. fakturering

- Microsoft. fakturering/billingPeriods
- Microsoft. fakturering/billingPermissions
- Microsoft. fakturering/billingRoleAssignments
- Microsoft. fakturering/billingRoleDefinitions
- Microsoft. fakturering/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft. skiss

- Microsoft. skiss/blueprintAssignments
- Microsoft. skiss/skisser

## <a name="microsoftconsumption"></a>Microsoft. förbrukning

- Microsoft. förbrukning/AggregatedCost
- Microsoft. förbrukning/saldon
- Microsoft. förbrukning/budgetar
- Microsoft. förbrukning/avgifter
- Microsoft. förbrukning/CostTags
- Microsoft. förbrukning/kredit
- Microsoft. förbrukning/händelser
- Microsoft. förbrukning/prognoser
- Microsoft. förbrukning/partier
- Microsoft. förbrukning/Marketplace
- Microsoft. förbrukning/Pricesheets
- Microsoft. förbrukning/produkter
- Microsoft. förbrukning/ReservationDetails
- Microsoft. förbrukning/ReservationRecommendationDetails
- Microsoft. förbrukning/ReservationRecommendations
- Microsoft. förbrukning/ReservationSummaries
- Microsoft. förbrukning/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

- Microsoft. ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

- Microsoft. CostManagement/Alerts
- Microsoft. CostManagement/budgetar
- Microsoft. CostManagement/dimensions
- Microsoft. CostManagement/export
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/Prediktion
- Microsoft. CostManagement/Insights
- Microsoft. CostManagement/fråga
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/-rapporter
- Microsoft. CostManagement/vyer

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

- Microsoft. CustomProviders/associationer

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/Software

## <a name="microsoftinsights"></a>Microsoft. Insights

- Microsoft. Insights/bas linje
- Microsoft. Insights/dataCollectionRuleAssociations
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/eventtypes
- Microsoft. Insights/extendedDiagnosticSettings
- Microsoft. Insights/guestDiagnosticSettingsAssociation
- Microsoft. Insights/logDefinitions
- Microsoft. Insights/loggar
- Microsoft. Insights/metricbaselines
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/metricNamespaces
- Microsoft. Insights/mått
- Microsoft. Insights/mina arbets böcker
- Microsoft. Insights/topologi
- Microsoft. Insights/transaktioner

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

- Microsoft. KubernetesConfiguration/Extensions
- Microsoft. KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/uppdateringar

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

- Microsoft. ManagedIdentity/identiteter

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

- Microsoft. OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

- Microsoft. OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

- Microsoft. PolicyInsights/attesteringar
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft. PolicyInsights/-reparationer

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

- Microsoft. RecoveryServices/backupProtectedItems
- Microsoft. RecoveryServices/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/Events
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/Notifications

## <a name="microsoftresources"></a>Microsoft. Resources

- Microsoft. Resources/Links
- Microsoft. Resources/Tags

## <a name="microsoftsecurity"></a>Microsoft. Security

- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/bedömningar
- Microsoft. Security/Compliances
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/Devices
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/iotSensors
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

- Microsoft. SecurityInsights/agg regeringar
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/automationRules
- Microsoft. SecurityInsights/bok märken
- Microsoft. SecurityInsights/fall
- Microsoft. SecurityInsights/dataConnectors
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/entiteter
- Microsoft. SecurityInsights/incidenter
- Microsoft. SecurityInsights/inställningar
- Microsoft. SecurityInsights/threatIntelligence
- Microsoft. SecurityInsights/watchlists

## <a name="microsoftserialconsoleppe"></a>Microsoft. SerialConsole. skyddsutrustning

- Microsoft. SerialConsole. skyddsutrustning/serialPorts

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

- Microsoft. SoftwarePlan/hybridUseBenefits

## <a name="microsoftsupport"></a>Microsoft. support

- Microsoft. support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft. WorkloadMonitor

- Microsoft. WorkloadMonitor/Components
- Microsoft. WorkloadMonitor/monitorInstances
- Microsoft. WorkloadMonitor/övervakare
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Nästa steg

- Om du vill hämta resurs-ID för en tilläggs resurs i en Azure Resource Manager mall använder du [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Ett exempel på hur du skapar en tilläggs resurs i en mall finns [Event Grid händelse prenumerationer](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
