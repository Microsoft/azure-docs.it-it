---
title: Tipi di risorsa dell'estensione
description: Elenca i tipi di risorse di Azure usati per estendere le funzionalità di altri tipi di risorse.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 84de9b66f9001985b8c7b92882f03ff8c7cbf431
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374015"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Tipi di risorse che estendono le funzionalità di altre risorse

Una risorsa di estensione è una risorsa che aggiunge le funzionalità di un'altra risorsa. Ad esempio, il blocco di risorsa è una risorsa di estensione. Applicare un blocco di risorsa a un'altra risorsa per impedirne l'eliminazione o la modifica. Non ha senso creare un blocco di risorsa da solo. Una risorsa di estensione viene sempre applicata a un'altra risorsa.

## <a name="extension-resource-types"></a>Tipi di risorsa dell'estensione

- Microsoft. Advisor/configurazioni
- Microsoft. Advisor/raccomandazioni
- Microsoft. Advisor/evitamenti
- Microsoft. AlertsManagement/Alerts
- Microsoft. AlertsManagement/alertsSummary
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/findOrphanRoleAssignments
- Microsoft.Authorization/locks
- Microsoft. Authorization/autorizzazioni
- Microsoft.Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft.Authorization/roleAssignments
- Microsoft. Authorization/roleAssignmentsUsageMetrics
- Microsoft. Authorization/roleDefinitions
- Microsoft. Billing/billingPeriods
- Microsoft. Billing/billingPermissions
- Microsoft. Billing/billingRoleAssignments
- Microsoft. Billing/billingRoleDefinitions
- Microsoft. Billing/createBillingRoleAssignment
- Microsoft. Blueprint/blueprintAssignments
- Microsoft. Blueprint/Blueprints
- Microsoft. ChangeAnalysis/resourceChanges
- Microsoft. consumer/AggregatedCost
- Microsoft. consumo/Saldi
- Microsoft. consumo/budget
- Microsoft. consumo/addebiti
- Microsoft. consumer/CostTags
- Microsoft. consumo/previsioni
- Microsoft. consumer/Marketplace
- Microsoft. consumer/OperationResults
- Microsoft. consumer/OperationStatus
- Microsoft. consumer/Pricesheets
- Microsoft. consumer/ReservationDetails
- Microsoft. consumer/ReservationRecommendationDetails
- Microsoft. consumer/ReservationRecommendations
- Microsoft. consumer/ReservationSummaries
- Microsoft. consumer/ReservationTransactions
- Microsoft. consumo/Tag
- Microsoft. consumo/termini
- Microsoft. consumer/UsageDetails
- Microsoft. consumo/crediti
- Microsoft. consumo/eventi
- Microsoft. consumo/molti
- Microsoft. consumo/prodotti
- Microsoft. consumer/tenant
- Microsoft. ContainerInstance/serviceAssociationLinks
- Microsoft. CostManagement/Alerts
- Microsoft. CostManagement/budgets
- Microsoft. CostManagement/costAllocationRules
- Microsoft. CostManagement/Dimensions
- Microsoft. CostManagement/Exports
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/previsione
- Microsoft. CostManagement/query
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/Reports
- Microsoft. CostManagement/showbackRules
- Microsoft. CostManagement/views
- Microsoft.CustomProviders/associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/software
- Microsoft. GuestConfiguration/softwareUpdateProfile
- Microsoft. GuestConfiguration/softwareUpdates
- Microsoft. Insights/Baseline
- Microsoft. Insights/calculatebaseline
- Microsoft. Insights/dataCollectionRuleAssociations
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/EventTypes
- Microsoft. Insights/extendedDiagnosticSettings
- Microsoft. Insights/guestDiagnosticSettingsAssociation
- Microsoft. Insights/logDefinitions
- Microsoft. Insights/log
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/metricNamespaces
- Microsoft. Insights/metricbaselines
- Microsoft. Insights/metrica
- Microsoft. Insights/cartelle di lavoro
- Microsoft. Insights/topologia
- Microsoft. Insights/transazioni
- Microsoft. Insights/vmInsightsOnboardingStatuses
- Microsoft. KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/Updates
- Microsoft. ManagedIdentity/identità
- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions
- Microsoft. OperationalInsights/storageInsightConfigs
- Microsoft. OperationsManagement/managementassociations
- Microsoft. PolicyInsights/attestazioni
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Microsoft. RecoveryServices/backupProtectedItems
- Microsoft. RecoveryServices/replicationEligibilityResults
- Microsoft. ResourceHealth/availabilityStatuses
- Microsoft. ResourceHealth/childAvailabilityStatuses
- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/Events
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/notifiche
- Microsoft. resources/Links
- Microsoft. resources/Tag
- Microsoft. Security/compliances
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/Assessment
- Microsoft. Security/complianceResults
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. SecurityInsights/Aggregations
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/automationRules
- Microsoft. SecurityInsights/segnalibri
- Microsoft. SecurityInsights/case
- Microsoft. SecurityInsights/dataconnectors
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/Entities
- Microsoft. SecurityInsights/entityQueries
- Microsoft. SecurityInsights/eventi imprevisti
- Microsoft. SecurityInsights/officeConsents
- Microsoft. SecurityInsights/Settings
- Microsoft. SecurityInsights/threatIntelligence
- Microsoft. SoftwarePlan/hybridUseBenefits
- Microsoft. Subscription/CreateSubscription
- Microsoft. support/supporttickets
- Microsoft. WorkloadMonitor/Components
- Microsoft. WorkloadMonitor/monitorInstances
- Microsoft. WorkloadMonitor/monitoraggi
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Passaggi successivi

- Per ottenere l'ID risorsa per una risorsa di estensione in un modello di Azure Resource Manager, usare [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Per un esempio di creazione di una risorsa di estensione in un modello, vedere [sottoscrizioni di eventi di griglia di eventi](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
