---
title: Supporto dei tag per le risorse
description: Informazioni sui tipi di risorse di Azure che supportano i tag. Include informazioni dettagliate per tutti i servizi di Azure.
ms.topic: conceptual
ms.date: 04/20/2021
ms.openlocfilehash: b196cae267a8d7dc878f055f6b2d70a3ff6f9313
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773964"
---
# <a name="tag-support-for-azure-resources"></a>Supporto dei tag per le risorse di Azure
Questo articolo descrive se un tipo di risorsa supporta [tag](tag-resources.md). La colonna con etichetta **Supports tags** indica se il tipo di risorsa ha una proprietà per il tag. La colonna con etichetta **Tag nel report dei** costi indica se il tipo di risorsa passa il tag al report dei costi. È possibile visualizzare i costi in base ai tag [nell'analisi dei costi di Gestione](../../cost-management-billing/costs/group-filter.md) costi e nella fattura di Azure e nei dati di utilizzo [giornalieri.](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)

Per ottenere gli stessi dati come file con valori delimitati da virgole, scaricare [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Passare a uno spazio dei nomi del provider di risorse:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.AnyBuild](#microsoftanybuild)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppAssessment](#microsoftappassessment)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automanage](#microsoftautomanage)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AVS](#microsoftavs)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureArcData](#microsoftazurearcdata)
> - [Microsoft.AzureCIS](#microsoftazurecis)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureSphere](#microsoftazuresphere)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cascade](#microsoftcascade)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.ClusterStor](#microsoftclusterstor)
> - [Microsoft.Codespaces](#microsoftcodespaces)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.ConnectedCache](#microsoftconnectedcache)
> - [Microsoft.ConnectedVehicle](#microsoftconnectedvehicle)
> - [Microsoft.ConnectedVMwarevSphere](#microsoftconnectedvmwarevsphere)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataProtection](#microsoftdataprotection)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EdgeOrder](#microsoftedgeorder)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Experimentation](#microsoftexperimentation)
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthBot](#microsofthealthbot)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.HybridNetwork](#microsofthybridnetwork)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Insights](#microsoftinsights)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSecurity](#microsoftiotsecurity)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Maintenance](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.MobileNetwork](#microsoftmobilenetwork)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.Notebooks](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.PowerPlatform](#microsoftpowerplatform)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.ProviderHub](#microsoftproviderhub)
> - [Microsoft.Purview](#microsoftpurview)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceConnector](#microsoftresourceconnector)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.ScVmm](#microsoftscvmm)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.ServiceLinker](#microsoftservicelinker)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Singularity](#microsoftsingularity)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.Synapse](#microsoftsynapse)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft.VMware](#microsoftvmware)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
> - [Microsoft.VSOnline](#microsoftvsonline)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsESU](#microsoftwindowsesu)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | DomainServices | Sì | Sì |
> | DomainServices/oucontainer | No | No |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | supportProviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | No | No |
> | addsservices | No | No |
> | agents | No | No |
> | anonymousapiusers | No | No |
> | configurazione | No | No |
> | log | No | No |
> | reports | No | No |
> | servicehealthmetrics | No | No |
> | services | No | No |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | advisorScore | No | No |
> | configurazioni | No | No |
> | generateRecommendations | No | No |
> | metadata | No | No |
> | raccomandazioni di film | No | No |
> | suppressions | No | No |

## <a name="microsoftagfoodplatform"></a>Microsoft.AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | farmBeats | Sì | Sì |
> | farmBeats/eventGridFilters | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | actionRules | Sì | Sì |
> | alerts | No | No |
> | alertsList | No | No |
> | alertsMetaData | No | No |
> | alertsSummary | No | No |
> | alertsSummaryList | No | No |
> | migrateFromSmartDetection | No | No |
> | resourceHealthAlertRules | Sì | Sì |
> | smartDetectorAlertRules | Sì | Sì |
> | smartGroups | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | servers | Sì | Sì |

## <a name="microsoftanybuild"></a>Microsoft.AnyBuild

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì | Sì |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | deletedServices | No | No |
> | getDomainOwnershipIdentifier | No | No |
> | reportFeedback | No | No |
> | service | Sì | Sì |
> | validateServiceName | No | No |

> [!NOTE]
> Azure API Management supporta solo la creazione di un massimo di 15 coppie nome/valore di tag per ogni servizio.

## <a name="microsoftappassessment"></a>Microsoft.AppAssessment

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | migrateProjects | Sì | Sì |
> | migrateProjects/assessments | No | No |
> | migrateProjects/assessments/assessedApplications | No | No |
> | migrateProjects/assessments/assessedApplications/machines | No | No |
> | migrateProjects/assessments/assessedMachines | No | No |
> | migrateProjects/assessments/assessedMachines/applications | No | No |
> | migrateProjects/assessments/machinesToAssess | No | No |
> | migrateProjects/sites | No | No |
> | migrateProjects/sites/applianceConfigurations | No | No |
> | migrateProjects/sites/machines | No | No |
> | osVersions | No | No |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | configurationStores | Sì | No |
> | configurationStores/eventGridFilters | No | No |
> | configurationStores/keyValues | No | No |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Spring | Sì | Sì |
> | Spring/apps | No | No |
> | Spring/apps/deployments | No | No |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Sì | Sì |
> | defaultProviders | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | No | No |
> | accessReviewScheduleSettings | No | No |
> | classicAdministrators | No | No |
> | dataAliases | No | No |
> | dataPolicyManifests | No | No |
> | denyAssignments | No | No |
> | elevateAccess | No | No |
> | findOrphanRoleAssignments | No | No |
> | locks | No | No |
> | autorizzazioni | No | No |
> | policyAssignments | No | No |
> | policyDefinitions | No | No |
> | policyExemptions | No | No |
> | policySetDefinitions | No | No |
> | privateLinkAssociations | No | No |
> | providerOperations | No | No |
> | resourceManagementPrivateLinks | Sì | Sì |
> | roleAssignmentApprovals | No | No |
> | roleAssignments | No | No |
> | roleAssignmentScheduleInstances | No | No |
> | roleAssignmentScheduleRequests | No | No |
> | roleAssignmentSchedules | No | No |
> | roleAssignmentsUsageMetrics | No | No |
> | roleDefinitions | No | No |
> | roleEligibilityScheduleInstances | No | No |
> | roleEligibilityScheduleRequests | No | No |
> | roleEligibilitySchedules | No | No |
> | roleManagementPolicies | No | No |
> | roleManagementPolicyAssignments | No | No |

## <a name="microsoftautomanage"></a>Microsoft.Automanage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | configurationProfileAssignments | No | No |
> | configurationProfilePreferences | Sì | Sì |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Sì | Sì |
> | automationAccounts/configurations | Sì | Sì |
> | automationAccounts/jobs | No | No |
> | automationAccounts/privateEndpointConnectionProxies | No | No |
> | automationAccounts/privateEndpointConnections | No | No |
> | automationAccounts/privateLinkResources | No | No |
> | automationAccounts/runbooks | Sì | Sì |
> | automationAccounts/softwareUpdateConfigurations | No | No |
> | automationAccounts/webhooks | No | No |

> [!NOTE]
> Automazione di Azure supporta solo la creazione di un massimo di 15 coppie nome/valore di tag per ogni risorsa di Automazione.

## <a name="microsoftavs"></a>Microsoft.AVS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | privateClouds | Sì | Sì |
> | privateClouds/addons | No | No |
> | privateClouds/authorizations | No | No |
> | privateClouds/cloudLinks | No | No |
> | privateClouds/clusters | No | No |
> | privateClouds/clusters/datastores | No | No |
> | privateClouds/globalReachConnections | No | No |
> | privateClouds/hcxEnterpriseSites | No | No |
> | privateClouds/scriptExecutions | No | No |
> | privateClouds/scriptPackages | No | No |
> | privateClouds/scriptPackages/scriptCmdlets | No | No |
> | privateClouds/workloadNetworks | No | No |
> | privateClouds/workloadNetworks/dhcpConfigurations | No | No |
> | privateClouds/workloadNetworks/dnsServices | No | No |
> | privateClouds/workloadNetworks/dnsZones | No | No |
> | privateClouds/workloadNetworks/gateways | No | No |
> | privateClouds/workloadNetworks/portMirroringProfiles | No | No |
> | privateClouds/workloadNetworks/segments | No | No |
> | privateClouds/workloadNetworks/virtualMachines | No | No |
> | privateClouds/workloadNetworks/vmGroups | No | No |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | environments | No | No |
> | environments/accounts | No | No |
> | environments/accounts/namespaces | No | No |
> | environments/accounts/namespaces/configurations | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Sì | No |
> | b2ctenants | No | No |
> | guestUsages | Sì | Sì |

## <a name="microsoftazurearcdata"></a>Microsoft.AzureArcData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | DataController | Sì | Sì |
> | dataWarehouseInstances | Sì | Sì |
> | postgresInstances | Sì | Sì |
> | sqlManagedInstances | Sì | Sì |
> | sqlServerInstances | Sì | Sì |

## <a name="microsoftazurecis"></a>Microsoft.AzureCIS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | autopilotEnvironments | Sì | Sì |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Sqlserverregistrations | Sì | Sì |
> | sqlServerRegistrations/sqlServers | No | No |

## <a name="microsoftazuresphere"></a>Microsoft.AzureSphere

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | catalogs | Sì | Sì |
> | cataloghi/prodotti | Sì | Sì |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | No | No |
> | edgeSubscriptions | Sì | Sì |
> | linkedSubscriptions | Sì | Sì |
> | registrations | Sì | Sì |
> | registrazioni/customerSubscriptions | No | No |
> | registrations/products | No | No |

## <a name="microsoftazurestackhci"></a>Microsoft.AzureStackHCI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì | Sì |
> | galleryImages | Sì | Sì |
> | networkInterfaces | Sì | Sì |
> | virtualHardDisks | Sì | Sì |
> | virtualMachines | Sì | Sì |
> | virtualNetworks | Sì | Sì |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft.BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Sì | Sì |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Sì | Sì |
> | batchAccounts/certificates | No | No |
> | batchAccounts/pools | No | No |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | billingAccounts | No | No |
> | billingAccounts/agreements | No | No |
> | billingAccounts/billingPermissions | No | No |
> | billingAccounts/billingProfiles | No | No |
> | billingAccounts/billingProfiles/billingPermissions | No | No |
> | billingAccounts/billingProfiles/billingRoleAssignments | No | No |
> | billingAccounts/billingProfiles/billingRoleDefinitions | No | No |
> | billingAccounts/billingProfiles/billingSubscriptions | No | No |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | No | No |
> | billingAccounts/billingProfiles/customers | No | No |
> | billingAccounts/billingProfiles/instructions | No | No |
> | billingAccounts/billingProfiles/invoices | No | No |
> | billingAccounts/billingProfiles/invoices/pricesheet | No | No |
> | billingAccounts/billingProfiles/invoices/transactions | No | No |
> | billingAccounts/billingProfiles/invoiceSections | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | No | No |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | No | No |
> | billingAccounts/billingProfiles/invoiceSections/products | No | No |
> | billingAccounts/billingProfiles/invoiceSections/products/transfer | No | No |
> | billingAccounts/billingProfiles/invoiceSections/products/updateAutoRenew | No | No |
> | billingAccounts/billingProfiles/invoiceSections/transactions | No | No |
> | billingAccounts/billingProfiles/invoiceSections/transfers | No | No |
> | billingAccounts/billingProfiles/invoiceSections/validateDeleteInvoiceSectionEligibility | No | No |
> | billingAccounts/BillingProfiles/patchOperations | No | No |
> | billingAccounts/billingProfiles/paymentMethods | No | No |
> | billingAccounts/billingProfiles/policies | No | No |
> | billingAccounts/billingProfiles/pricesheet | No | No |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | No | No |
> | billingAccounts/billingProfiles/products | No | No |
> | billingAccounts/billingProfiles/reservations | No | No |
> | billingAccounts/billingProfiles/transactions | No | No |
> | billingAccounts/billingProfiles/validateDeleteBillingProfileEligibility | No | No |
> | billingAccounts/billingProfiles/validateDetachPaymentMethodEligibility | No | No |
> | billingAccounts/billingRoleAssignments | No | No |
> | billingAccounts/billingRoleDefinitions | No | No |
> | billingAccounts/billingSubscriptions | No | No |
> | billingAccounts/billingSubscriptions/elevateRole | No | No |
> | billingAccounts/billingSubscriptions/invoices | No | No |
> | billingAccounts/createBillingRoleAssignment | No | No |
> | billingAccounts/createInvoiceSectionOperations | No | No |
> | billingAccounts/customers | No | No |
> | billingAccounts/customers/billingPermissions | No | No |
> | billingAccounts/customers/billingSubscriptions | No | No |
> | billingAccounts/customers/initiateTransfer | No | No |
> | billingAccounts/customers/policies | No | No |
> | billingAccounts/customers/products | No | No |
> | billingAccounts/customers/transactions | No | No |
> | billingAccounts/customers/transfers | No | No |
> | billingAccounts/departments | No | No |
> | billingAccounts/departments/billingPermissions | No | No |
> | billingAccounts/departments/billingRoleAssignments | No | No |
> | billingAccounts/departments/billingRoleDefinitions | No | No |
> | billingAccounts/departments/billingSubscriptions | No | No |
> | billingAccounts/enrollmentAccounts | No | No |
> | billingAccounts/enrollmentAccounts/billingPermissions | No | No |
> | billingAccounts/enrollmentAccounts/billingRoleAssignments | No | No |
> | billingAccounts/enrollmentAccounts/billingRoleDefinitions | No | No |
> | billingAccounts/enrollmentAccounts/billingSubscriptions | No | No |
> | billingAccounts/invoices | No | No |
> | billingAccounts/invoices/transactions | No | No |
> | billingAccounts/invoices/transactionSummary | No | No |
> | billingAccounts/invoiceSections | No | No |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | No | No |
> | billingAccounts/invoiceSections/billingSubscriptions | No | No |
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | No | No |
> | billingAccounts/invoiceSections/elevate | No | No |
> | billingAccounts/invoiceSections/initiateTransfer | No | No |
> | billingAccounts/invoiceSections/patchOperations | No | No |
> | billingAccounts/invoiceSections/productMoveOperations | No | No |
> | billingAccounts/invoiceSections/products | No | No |
> | billingAccounts/invoiceSections/products/transfer | No | No |
> | billingAccounts/invoiceSections/products/updateAutoRenew | No | No |
> | billingAccounts/invoiceSections/transactions | No | No |
> | billingAccounts/invoiceSections/transfers | No | No |
> | billingAccounts/lineOfCredit | No | No |
> | billingAccounts/patchOperations | No | No |
> | billingAccounts/payableOverage | No | No |
> | billingAccounts/paymentMethods | No | No |
> | billingAccounts/payNow | No | No |
> | billingAccounts/products | No | No |
> | billingAccounts/reservations | No | No |
> | billingAccounts/transactions | No | No |
> | billingPeriods | No | No |
> | billingPermissions | No | No |
> | billingProperty | No | No |
> | billingRoleAssignments | No | No |
> | billingRoleDefinitions | No | No |
> | createBillingRoleAssignment | No | No |
> | departments | No | No |
> | enrollmentAccounts | No | No |
> | invoices | No | No |
> | promozioni | No | No |
> | transfers | No | No |
> | transfers/acceptTransfer | No | No |
> | transfers/declineTransfer | No | No |
> | transfers/operationStatus | No | No |
> | transfers/validateTransfer | No | No |
> | validateAddress | No | No |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | mapApis | Sì | Sì |
> | updateCommunicationPreference | No | No |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Sì | Sì |
> | membri di <a0/&gt; | Sì | Sì |
> | watchers | Sì | Sì |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokens

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | TokenServices | Sì | Sì |
> | TokenServices/BlockchainNetworks | No | No |
> | TokenServices/Gruppi | No | No |
> | TokenServices/Gruppi/Account | No | No |
> | TokenServices/TokenTemplates | No | No |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | No | No |
> | blueprintAssignments/assignmentOperations | No | No |
> | blueprintAssignments/operations | No | No |
> | blueprints | No | No |
> | blueprints/artifacts | No | No |
> | blueprints/versions | No | No |
> | blueprints/versions/artifacts | No | No |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | botServices | Sì | Sì |
> | botServices/channels | No | No |
> | botServices/connections | No | No |
> | hostSettings | No | No |
> | languages | No | No |
> | Modelli | No | No |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Redis | Sì | Sì |
> | Redis/EventGridFilters | No | No |
> | Redis/privateEndpointConnectionProxies | No | No |
> | Redis/privateEndpointConnectionProxies/validate | No | No |
> | Redis/privateEndpointConnections | No | No |
> | Redis/privateLinkResources | No | No |
> | redisEnterprise | Sì | Sì |
> | redisEnterprise/databases | No | No |
> | RedisEnterprise/privateEndpointConnectionProxies | No | No |
> | RedisEnterprise/privateEndpointConnectionProxies/validate | No | No |
> | RedisEnterprise/privateEndpointConnections | No | No |
> | RedisEnterprise/privateLinkResources | No | No |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | appliedReservations | No | No |
> | autoQuotaIncrease | No | No |
> | calculateExchange | No | No |
> | calculatePrice | No | No |
> | calculatePurchasePrice | No | No |
> | catalogs | No | No |
> | commercialReservationOrders | No | No |
> | exchange | No | No |
> | ownReservations | No | No |
> | placePurchaseOrder | No | No |
> | reservationOrders | No | No |
> | reservationOrders/calculateRefund | No | No |
> | reservationOrders/merge | No | No |
> | reservationOrders/reservations | No | No |
> | reservationOrders/reservations/revisions | No | No |
> | reservationOrders/return | No | No |
> | reservationOrders/split | No | No |
> | reservationOrders/swap | No | No |
> | reservations | No | No |
> | resourceProviders | No | No |
> | resources | No | No |
> | validateReservationOrder | No | No |

## <a name="microsoftcascade"></a>Microsoft.Cascade

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | siti | Sì | Sì |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No | No |
> | CdnWebApplicationFirewallPolicies | Sì | Sì |
> | edgenodes | No | No |
> | Profili | Sì | Sì |
> | profiles/afdendpoints | Sì | Sì |
> | profiles/afdendpoints/routes | No | No |
> | profiles/customdomains | No | No |
> | profiles/endpoints | Sì | Sì |
> | profiles/endpoints/customdomains | No | No |
> | profiles/endpoints/origingroups | No | No |
> | profiles/endpoints/origins | No | No |
> | profiles/origingroups | No | No |
> | profiles/origingroups/origins | No | No |
> | profili/set di regole | No | No |
> | profili/set di regole/regole | No | No |
> | profili/segreti | No | No |
> | profili/criteri di sicurezza | No | No |
> | validateProbe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Sì | Sì |
> | certificateOrders/certificates | No | No |
> | validateCertificateRegistrationInformation | No | No |

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | modifiche | No | No |
> | profile | No | No |
> | resourceChanges | No | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | domainNames | No | No |
> | domainNames/capabilities | No | No |
> | domainNames/internalLoadBalancers | No | No |
> | domainNames/serviceCertificates | No | No |
> | domainNames/slots | No | No |
> | domainNames/slots/roles | No | No |
> | domainNames/slots/roles/metricDefinitions | No | No |
> | domainNames/slots/roles/metrics | No | No |
> | moveSubscriptionResources | No | No |
> | operatingSystemFamilies | No | No |
> | operatingSystems | No | No |
> | quotas | No | No |
> | resourceTypes | No | No |
> | validateSubscriptionMoveAvailability | No | No |
> | virtualMachines | No | No |
> | virtualMachines/diagnosticSettings | No | No |
> | virtualMachines/metricDefinitions | No | No |
> | virtualMachines/metrics | No | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | expressRouteCrossConnections | No | No |
> | expressRouteCrossConnections/peerings | No | No |
> | gatewaySupportedDevices | No | No |
> | networkSecurityGroups | No | No |
> | quotas | No | No |
> | reservedIps | No | No |
> | virtualNetworks | No | No |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No | No |
> | virtualNetworks/virtualNetworkPeerings | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | capabilities | No | No |
> | disks | No | No |
> | images | No | No |
> | osImages | No | No |
> | osPlatformImages | No | No |
> | publicImages | No | No |
> | quotas | No | No |
> | storageAccounts | No | No |
> | storageAccounts/blobServices | No | No |
> | storageAccounts/fileServices | No | No |
> | storageAccounts/metricDefinitions | No | No |
> | storageAccounts/metrics | No | No |
> | storageAccounts/queueServices | No | No |
> | storageAccounts/services | No | No |
> | storageAccounts/services/diagnosticSettings | No | No |
> | storageAccounts/services/metricDefinitions | No | No |
> | storageAccounts/services/metrics | No | No |
> | storageAccounts/tableServices | No | No |
> | storageAccounts/vmImages | No | No |
> | vmImages | No | No |

## <a name="microsoftclusterstor"></a>Microsoft.ClusterStor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | nodes | Sì | Sì |

## <a name="microsoftcodespaces"></a>Microsoft.Codespaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | plans | Sì | No |
> | registeredSubscriptions | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | accounts/privateEndpointConnectionProxies | No | No |
> | accounts/privateEndpointConnections | No | No |
> | accounts/privateLinkResources | No | No |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | RateCard | No | No |
> | UsageAggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Sì | Sì |
> | cloudServices | Sì | Sì |
> | cloudServices/networkInterfaces | No | No |
> | cloudServices/publicIPAddresses | No | No |
> | cloudServices/roleInstances | No | No |
> | cloudServices/roleInstances/networkInterfaces | No | No |
> | cloudServizi/ruoli | No | No |
> | diskAccesses | Sì | Sì |
> | diskEncryptionSets | Sì | Sì |
> | disks | Sì | Sì |
> | galleries | Sì | Sì |
> | galleries/applications | No | No |
> | raccolte/applicazioni/versioni | No | No |
> | galleries/images | No | No |
> | galleries/images/versions | No | No |
> | hostGroups | Sì | Sì |
> | hostGroups/hosts | Sì | Sì |
> | images | Sì | Sì |
> | proximityPlacementGroups | Sì | Sì |
> | restorePointCollections | Sì | Sì |
> | restorePointCollections/restorePoints | No | No |
> | sharedVMExtensions | Sì | Sì |
> | sharedVMExtensions/versions | No | No |
> | sharedVMImages | Sì | Sì |
> | sharedVMImages/versions | No | No |
> | snapshots | Sì | Sì |
> | sshPublicKeys | Sì | Sì |
> | virtualMachines | Sì | Sì |
> | virtualMachines/extensions | Sì | Sì |
> | virtualMachines/metricDefinitions | No | No |
> | virtualMachines/runCommands | Sì | Sì |
> | virtualMachineScaleSets | Sì | Sì |
> | virtualMachineScaleSets/extensions | No | No |
> | virtualMachineScaleSets/networkInterfaces | No | No |
> | virtualMachineScaleSets/publicIPAddresses | Sì | No |
> | virtualMachineScaleSets/virtualMachines | No | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No | No |

> [!NOTE]
> Non è possibile aggiungere un tag a una macchina virtuale contrassegnata come generalizzata. Si contrassegna una macchina virtuale come generalizzata con [Set-AzVm -Generalized](/powershell/module/Az.Compute/Set-AzVM) o [az vm generalize](/cli/azure/vm#az_vm_generalize).

## <a name="microsoftconnectedcache"></a>Microsoft.ConnectedCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Sì | Sì |

## <a name="microsoftconnectedvehicle"></a>Microsoft.ConnectedVehicle

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | platformAccounts | Sì | Sì |
> | registeredSubscriptions | No | No |

## <a name="microsoftconnectedvmwarevsphere"></a>Microsoft.ConnectedVMwarevSphere

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Pool di risorse | Sì | Sì |
> | VCenter | Sì | Sì |
> | VCenter/InventoryItems | No | No |
> | VirtualMachines | Sì | Sì |
> | VirtualMachines/Extensions | Sì | Sì |
> | VirtualMachines/GuestAgents | No | No |
> | VirtualMachines/HybridIdentityMetadata | No | No |
> | VirtualMachineTemplates | Sì | Sì |
> | VirtualNetworks | Sì | Sì |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | No | No |
> | Saldi | No | No |
> | Budget | No | No |
> | Charges | No | No |
> | CostTags | No | No |
> | credits | No | No |
> | eventi | No | No |
> | Previsioni | No | No |
> | lots | No | No |
> | Marketplace | No | No |
> | Pricesheets | No | No |
> | products | No | No |
> | ReservationDetails | No | No |
> | ReservationRecommendationDetails | No | No |
> | ReservationRecommendations | No | No |
> | ReservationSummaries | No | No |
> | ReservationTransactions | No | No |
> | Tag | No | No |
> | tenants | No | No |
> | Termini | No | No |
> | UsageDetails | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | containerGroups | Sì | Sì |
> | serviceAssociationLinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | registries | Sì | Sì |
> | registries/agentPools | Sì | Sì |
> | registries/builds | No | No |
> | registries/builds/cancel | No | No |
> | registries/builds/getLogLink | No | No |
> | registries/buildTasks | Sì | Sì |
> | registries/buildTasks/steps | No | No |
> | registries/connectedRegistries | No | No |
> | registries/connectedRegistries/deactivate | No | No |
> | registries/eventGridFilters | No | No |
> | registries/exportPipelines | No | No |
> | registries/generateCredentials | No | No |
> | registries/getBuildSourceUploadUrl | No | No |
> | registries/GetCredentials | No | No |
> | registries/importImage | No | No |
> | registries/importPipelines | No | No |
> | registries/pipelineRuns | No | No |
> | registries/privateEndpointConnectionProxies | No | No |
> | registries/privateEndpointConnectionProxies/validate | No | No |
> | registries/privateEndpointConnections | No | No |
> | registries/privateLinkResources | No | No |
> | registries/queueBuild | No | No |
> | registries/regenerateCredential | No | No |
> | registries/regenerateCredentials | No | No |
> | registries/replications | Sì | Sì |
> | registries/runs | No | No |
> | registries/runs/cancel | No | No |
> | registries/scheduleRun | No | No |
> | registries/scopeMaps | No | No |
> | registries/taskRuns | No | No |
> | registries/tasks | Sì | Sì |
> | registries/tokens | No | No |
> | registries/updatePolicies | No | No |
> | registries/webhooks | Sì | Sì |
> | registries/webhooks/getCallbackConfig | No | No |
> | registries/webhooks/ping | No | No |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | containerServices | Sì | Sì |
> | managedClusters | Sì | Sì |
> | ManagedClusters/eventGridFilters | No | No |
> | openShiftManagedClusters | Sì | Sì |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Avvisi | No | No |
> | BillingAccounts | No | No |
> | Budget | No | No |
> | CloudConnectors | No | No |
> | Connettori | Sì | Sì |
> | costAllocationRules | No | No |
> | Departments | No | No |
> | Dimensioni | No | No |
> | EnrollmentAccounts | No | No |
> | Esportazioni | No | No |
> | ExternalBillingAccounts | No | No |
> | ExternalBillingAccounts/Alerts | No | No |
> | ExternalBillingAccounts/Dimensions | No | No |
> | ExternalBillingAccounts/Forecast | No | No |
> | ExternalBillingAccounts/Query | No | No |
> | ExternalSubscriptions | No | No |
> | ExternalSubscriptions/Alerts | No | No |
> | ExternalSubscriptions/Dimensions | No | No |
> | ExternalSubscriptions/Forecast | No | No |
> | ExternalSubscriptions/Query | No | No |
> | fetchPrices | No | No |
> | Previsione | No | No |
> | GenerateDetailedCostReport | No | No |
> | GenerateReservationDetailsReport | No | No |
> | Informazioni dettagliate | No | No |
> | Query | No | No |
> | register | No | No |
> | Reportconfigs | No | No |
> | Report | No | No |
> | Azioni pianificate | No | No |
> | Impostazioni | No | No |
> | showbackRules | No | No |
> | Viste | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | DisableLockbox | No | No |
> | EnableLockbox | No | No |
> | requests | No | No |
> | TenantOptedIn | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | associations | No | No |
> | resourceProviders | Sì | Sì |

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | instances | Sì | Sì |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | jobs | Sì | Sì |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Sì | Sì |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | aree di lavoro | Sì | Sì |
> | workspaces/dbWorkspaces | No | No |
> | workspaces/virtualNetworkPeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | catalogs | Sì | Sì |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | dataFactories | Sì | Sì |
> | dataFactories/diagnosticSettings | No | No |
> | dataFactories/metricDefinitions | No | No |
> | dataFactorySchema | No | No |
> | factories | Sì | Sì |
> | factories/integrationRuntimes | No | No |

> [!NOTE]
> Se si dispone di runtime di integrazione SSIS di Azure nel data factory, il costo di esecuzione verrà contrassegnato con data factory tag. L'esecuzione dei runtime di integrazione SSIS di Azure deve essere arrestata e riavviata per applicare nuovi tag data factory al costo di esecuzione.

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | accounts/dataLakeStoreAccounts | No | No |
> | accounts/storageAccounts | No | No |
> | accounts/storageAccounts/containers | No | No |
> | accounts/transferAnalyticsUnits | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | accounts/eventGridFilters | No | No |
> | accounts/firewallRules | No | No |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | DatabaseMigrations | No | No |
> | services | Sì | Sì |
> | services/projects | Sì | Sì |
> | SqlMigrationServices | Sì | Sì |

## <a name="microsoftdataprotection"></a>Microsoft.DataProtection

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Sì | Sì |
> | ResourceGuards | Sì | Sì |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | accounts/shares | No | No |
> | accounts/shares/datasets | No | No |
> | accounts/shares/invitations | No | No |
> | accounts/shares/providersharesubscriptions | No | No |
> | accounts/shares/synchronizationSettings | No | No |
> | accounts/sharesubscriptions | No | No |
> | accounts/sharesubscriptions/consumerSourceDataSets | No | No |
> | accounts/sharesubscriptions/datasetmappings | No | No |
> | accounts/sharesubscriptions/triggers | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | servers | Sì | Sì |
> | servers/advisors | No | No |
> | servers/keys | No | No |
> | servers/privateEndpointConnectionProxies | No | No |
> | servers/privateEndpointConnections | No | No |
> | servers/privateLinkResources | No | No |
> | servers/queryTexts | No | No |
> | servers/recoverableServers | No | No |
> | servers/resetQueryPerformanceInsightData | No | No |
> | servers/start | No | No |
> | servers/stop | No | No |
> | servers/topQueryStatistics | No | No |
> | servers/virtualNetworkRules | No | No |
> | servers/waitStatistics | No | No |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Sì | Sì |
> | servers | Sì | Sì |
> | servers/advisors | No | No |
> | servers/keys | No | No |
> | servers/privateEndpointConnectionProxies | No | No |
> | servers/privateEndpointConnections | No | No |
> | servers/privateLinkResources | No | No |
> | servers/queryTexts | No | No |
> | servers/recoverableServers | No | No |
> | servers/resetQueryPerformanceInsightData | No | No |
> | servers/start | No | No |
> | servers/stop | No | No |
> | servers/topQueryStatistics | No | No |
> | servers/upgrade | No | No |
> | servers/virtualNetworkRules | No | No |
> | servers/waitStatistics | No | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Sì | Sì |
> | serverGroups | Sì | Sì |
> | serverGroupsv2 | Sì | Sì |
> | servers | Sì | Sì |
> | servers/advisors | No | No |
> | servers/keys | No | No |
> | servers/privateEndpointConnectionProxies | No | No |
> | servers/privateEndpointConnections | No | No |
> | servers/privateLinkResources | No | No |
> | servers/queryTexts | No | No |
> | servers/recoverableServers | No | No |
> | servers/resetQueryPerformanceInsightData | No | No |
> | servers/topQueryStatistics | No | No |
> | servers/virtualNetworkRules | No | No |
> | servers/waitStatistics | No | No |
> | serversv2 | Sì | Sì |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | artifactSources | Sì | Sì |
> | rollouts | Sì | Sì |
> | serviceTopologies | Sì | Sì |
> | serviceTopologies/services | Sì | Sì |
> | serviceTopologies/services/serviceUnits | Sì | Sì |
> | steps | Sì | Sì |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Sì | Sì |
> | applicationgroups/applications | No | No |
> | applicationgroups/desktops | No | No |
> | applicationgroups/startmenuitems | No | No |
> | hostpools | Sì | Sì |
> | pool host/msixpackages | No | No |
> | hostpools/sessionhosts | No | No |
> | hostpools/sessionhosts/usersessions | No | No |
> | hostpools/usersessions | No | No |
> | scalingPlans | Sì | Sì |
> | aree di lavoro | Sì | Sì |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Pool elastici | Sì | Sì |
> | ElasticPools/IotHubTenants | Sì | Sì |
> | ElasticPools/IotHubTenants/securitySettings | No | No |
> | Hub IoT | Sì | Sì |
> | IotHubs/eventGridFilters | No | No |
> | IotHubs/securitySettings | No | No |
> | ProvisioningServices | Sì | Sì |
> | usages | No | No |

## <a name="microsoftdeviceupdate"></a>Microsoft.DeviceUpdate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | accounts/instances | Sì | Sì |
> | registeredSubscriptions | No | No |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | pipelines | Sì | Sì |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | controllers | Sì | Sì |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | labcenters | Sì | Sì |
> | labs | Sì | Sì |
> | labs/environments | Sì | Sì |
> | labs/serviceRunners | Sì | Sì |
> | labs/virtualMachines | Sì | Sì |
> | schedules | Sì | Sì |

## <a name="microsoftdigitaltwins"></a>Microsoft.DigitalTwins

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Sì | Sì |
> | digitalTwinsInstances/endpoints | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | cassandraClusters | Sì | Sì |
> | databaseAccountNames | No | No |
> | databaseAccounts | Sì | Sì |
> | restorableDatabaseAccounts | No | No |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | domains | Sì | Sì |
> | domains/domainOwnershipIdentifiers | No | No |
> | generateSsoRequest | No | No |
> | topLevelDomains | No | No |
> | validateDomainRegistrationInformation | No | No |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | lcsprojects | No | No |
> | lcsprojects/clouddeployments | No | No |
> | lcsprojects/connectors | No | No |

## <a name="microsoftedgeorder"></a>Microsoft.EdgeOrder

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Indirizzi | Sì | Sì |
> | orderCollections | Sì | Sì |
> | orders | Sì | Sì |
> | productFamiliesMetadata | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | services | Sì | Sì |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | domains | Sì | Sì |
> | domains/topics | No | No |
> | eventSubscriptions | No | No |
> | extensionTopics | No | No |
> | partnerNamespaces | Sì | Sì |
> | partnerNamespaces/eventChannels | No | No |
> | partnerRegistrazioni | Sì | Sì |
> | partnerTopics | Sì | Sì |
> | partnerTopics/eventSubscriptions | No | No |
> | systemTopics | Sì | Sì |
> | systemTopics/eventSubscriptions | No | No |
> | topics | Sì | Sì |
> | topicTypes | No | No |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì | Sì |
> | spazi dei nomi | Sì | Sì |
> | namespaces/authorizationrules | No | No |
> | namespaces/disasterrecoveryconfigs | No | No |
> | namespaces/eventhubs | No | No |
> | namespaces/eventhubs/authorizationrules | No | No |
> | namespaces/eventhubs/consumergroups | No | No |
> | namespaces/networkrulesets | No | No |
> | namespaces/privateEndpointConnections | No | No |

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Sì | Sì |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì | Sì |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | configurazioni di funzionalità | No | No |
> | featureProviderNamespaces | No | No |
> | Provider di funzionalità | No | No |
> | funzionalità | No | No |
> | provider | No | No |
> | subscriptionFeatureRegistrations | No | No |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | enroll | No | No |
> | galleryitems | No | No |
> | generateartifactaccessuri | No | No |
> | myareas | No | No |
> | area/aree | No | No |
> | myareas/areas/areas | No | No |
> | myareas/areas/areas/galleryitems | No | No |
> | myareas/areas/galleryitems | No | No |
> | myareas/galleryitems | No | No |
> | register | No | No |
> | resources | No | No |
> | retrieveresourcesbyid | No | No |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Sì | Sì |
> | autoManagedVmConfigurationProfiles | Sì | Sì |
> | configurationProfileAssignments | No | No |
> | guestConfigurationAssignments | No | No |
> | software | No | No |
> | softwareUpdateProfile | No | No |
> | softwareUpdates | No | No |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Sì | Sì |
> | sapMonitors | Sì | Sì |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | DMS dedicati | Sì | Sì |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | clusterPool | Sì | Sì |
> | clusterPool/cluster | Sì | Sì |
> | clusters | Sì | Sì |
> | clusters/applications | No | No |

## <a name="microsofthealthbot"></a>Microsoft.HealthBot

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | healthBots | Sì | Sì |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | services | Sì | Sì |
> | services/iomtconnectors | No | No |
> | services/iomtconnectors/connections | No | No |
> | services/iomtconnectors/mappings | No | No |
> | services/privateEndpointConnectionProxies | No | No |
> | services/privateEndpointConnections | No | No |
> | services/privateLinkResources | No | No |
> | aree di lavoro | Sì | Sì |
> | workspaces/dicomservices | Sì | Sì |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | machines | Sì | Sì |
> | machines/assessPatches | No | No |
> | machines/extensions | Sì | Sì |
> | machines/installPatches | No | No |
> | machines/privateLinkScopes | No | No |
> | privateLinkScopes | Sì | Sì |
> | privateLinkScopes/privateEndpointConnectionProxies | No | No |
> | privateLinkScopes/privateEndpointConnections | No | No |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | DataManagers | Sì | Sì |

## <a name="microsofthybridnetwork"></a>Microsoft.HybridNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | devices | Sì | Sì |
> | funzioni di rete | Sì | Sì |
> | networkFunctionVendors | No | No |
> | registeredSubscriptions | No | No |
> | Fornitori | No | No |
> | Vendor/vendorskus | No | No |
> | Fornitori/vendorkus/previewsubscriptions | No | No |
> | virtualNetworkFunctions | Sì | Sì |
> | virtualNetworkFunctionVendors | No | No |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | components | Sì | Sì |
> | ambiti di rete | Sì | Sì |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | jobs | Sì | Sì |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | actionGroups | Sì | Sì |
> | activityLogAlerts | Sì | Sì |
> | alertrules | Sì | Sì |
> | autoscalesettings | Sì | Sì |
> | components | Sì | Sì |
> | components/linkedStorageAccounts | No | No |
> | components/ProactiveDetectionConfigs | No | No |
> | diagnosticSettings | No | No |
> | guestDiagnosticSettings | Sì | Sì |
> | guestDiagnosticSettingsAssociation | Sì | Sì |
> | logprofiles | Sì | Sì |
> | metricAlerts | Sì | Sì |
> | privateLinkScopes | Sì | Sì |
> | privateLinkScopes/privateEndpointConnections | No | No |
> | privateLinkScopes/scopedResources | No | No |
> | queryPacks | Sì | Sì |
> | queryPacks/queries | No | No |
> | scheduledQueryRules | Sì | Sì |
> | webtests | Sì | Sì |
> | workbooks | Sì | Sì |
> | workbooktemplates | Sì | Sì |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | appTemplates | No | No |
> | IoTApps | Sì | Sì |

## <a name="microsoftiotsecurity"></a>Microsoft.IoTSecurity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | defenderSettings | No | No |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Grafico | Sì | Sì |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | deletedManagedHSMs | No | No |
> | deletedVaults | No | No |
> | hsmPools | Sì | Sì |
> | managedHSMs | Sì | Sì |
> | insiemi di credenziali | Sì | Sì |
> | vaults/accessPolicies | No | No |
> | vaults/eventGridFilters | No | No |
> | vaults/keys | No | No |
> | vaults/keys/versions | No | No |
> | vaults/secrets | No | No |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Sì | Sì |
> | registeredSubscriptions | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | Estensioni | No | No |
> | sourceControlConfigurations | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì | Sì |
> | clusters/attacheddatabaseconfigurations | No | No |
> | clusters/databases | No | No |
> | clusters/databases/dataconnections | No | No |
> | clusters/databases/eventhubconnections | No | No |
> | clusters/databases/principalassignments | No | No |
> | clusters/databases/scripts | No | No |
> | clusters/dataconnections | No | No |
> | clusters/principalassignments | No | No |
> | clusters/sharedidentities | No | No |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | labaccounts | Sì | No |
> | labplans | Sì | Sì |
> | labs | Sì | Sì |
> | users | No | No |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Sì | Sì |
> | integrationAccounts | Sì | Sì |
> | integrationServiceEnvironments | Sì | Sì |
> | integrationServiceEnvironments/managedApis | No | No |
> | isolatedEnvironments | Sì | Sì |
> | flussi di lavoro | Sì | Sì |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Sì | Sì |
> | webServices | Sì | Sì |
> | Aree di lavoro | Sì | Sì |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | modelinventories | Sì | Sì |
> | virtualclusters | Sì | Sì |
> | aree di lavoro | Sì | Sì |
> | workspaces/batchEndpoints | Sì | Sì |
> | workspaces/batchEndpoints/deployments | Sì | Sì |
> | workspaces/batchEndpoints/deployments/jobs | No | No |
> | workspaces/batchEndpoints/jobs | No | No |
> | aree di lavoro/codici | No | No |
> | aree di lavoro/codici/versioni | No | No |
> | workspaces/computes | No | No |
> | aree di lavoro/dati | No | No |
> | aree di lavoro/archivi dati | No | No |
> | aree di lavoro/ambienti | No | No |
> | workspaces/eventGridFilters | No | No |
> | aree di lavoro/processi | No | No |
> | workspaces/labelingJobs | No | No |
> | aree di lavoro/linkedServices | No | No |
> | aree di lavoro/modelli | No | No |
> | aree di lavoro/modelli/versioni | No | No |
> | aree di lavoro/onlineEndpoints | Sì | Sì |
> | workspaces/onlineEndpoints/deployments | Sì | Sì |

> [!NOTE]
> I tag dell'area di lavoro non vengono propagati ai cluster di calcolo e alle istanze di calcolo.

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | applyUpdates | No | No |
> | configurationAssignments | No | No |
> | maintenanceConfigurations | Sì | Sì |
> | publicMaintenanceConfigurations | No | No |
> | updates | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | Identità | No | No |
> | userAssignedIdentities | Sì | Sì |

## <a name="microsoftmanagednetwork"></a>Microsoft.ManagedNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Sì | Sì |
> | managedNetworks/managedNetworkGroups | Sì | Sì |
> | managedNetworks/managedNetworkPeeringPolicies | Sì | Sì |
> | notifica | Sì | Sì |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | No | No |
> | registrationAssignments | No | No |
> | registrationDefinitions | No | No |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | getEntities | No | No |
> | managementGroups | No | No |
> | managementGroups/settings | No | No |
> | resources | No | No |
> | startTenantBackfill | No | No |
> | tenantBackfillStatus | No | No |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | accounts/creators | Sì | Sì |
> | accounts/eventGridFilters | No | No |
> | accounts/privateAtlases | Sì | Sì |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Mac | No | No |
> | offers | No | No |
> | offerTypes | No | No |
> | offerTypes/publishers | No | No |
> | offerTypes/publishers/offers | No | No |
> | offerTypes/publishers/offers/plans | No | No |
> | offerTypes/publishers/offers/plans/agreements | No | No |
> | offerTypes/publishers/offers/plans/configs | No | No |
> | offerTypes/publishers/offers/plans/configs/importImage | No | No |
> | privategalleryitems | No | No |
> | privateStoreClient | No | No |
> | privateStores | No | No |
> | privateStores/AdminRequestApprovals | No | No |
> | privateStores/offers | No | No |
> | privateStores/offers/acknowledgeNotification | No | No |
> | privateStores/queryNotificationsState | No | No |
> | privateStores/RequestApprovals | No | No |
> | privateStores/requestApprovals/query | No | No |
> | privateStores/requestApprovals/withdrawPlan | No | No |
> | products | No | No |
> | publishers | No | No |
> | publishers/offers | No | No |
> | publishers/offers/amendments | No | No |
> | register | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Sì | Sì |
> | updateCommunicationPreference | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | agreements | No | No |
> | offertypes | No | No |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | mediaservices | Sì | Sì |
> | mediaservices/accountFilters | No | No |
> | mediaservices/assets | No | No |
> | mediaservices/assets/assetFilters | No | No |
> | mediaservices/contentKeyPolicies | No | No |
> | mediaservices/eventGridFilters | No | No |
> | mediaservices/graphInstances | No | No |
> | mediaservices/graphTopologies | No | No |
> | mediaservices/liveEventOperations | No | No |
> | mediaservices/liveEvents | Sì | Sì |
> | mediaservices/liveEvents/liveOutputs | No | No |
> | mediaservices/liveOutputOperations | No | No |
> | mediaservices/mediaGraphs | No | No |
> | mediaservices/privateEndpointConnectionOperations | No | No |
> | mediaservices/privateEndpointConnectionProxies | No | No |
> | mediaservices/privateEndpointConnections | No | No |
> | mediaservices/streamingEndpointOperations | No | No |
> | mediaservices/streamingEndpoints | Sì | Sì |
> | mediaservices/streamingLocators | No | No |
> | mediaservices/streamingPolicies | No | No |
> | mediaservices/transforms | No | No |
> | mediaservices/transforms/jobs | No | No |
> | videoAnalyzers | Sì | Sì |
> | videoAnalyzers/edgeModules | No | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | appClusters | Sì | Sì |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Sì | Sì |
> | migrateprojects | Sì | Sì |
> | moveCollections | Sì | Sì |
> | projects | Sì | Sì |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Sì | Sì |
> | objectAnchorsAccounts | Sì | Sì |
> | objectUnderstandingAccounts | Sì | Sì |
> | remoteRenderingAccounts | Sì | Sì |
> | spatialAnchorsAccounts | Sì | Sì |

## <a name="microsoftmobilenetwork"></a>Microsoft.MobileNetwork

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | networks | Sì | Sì |
> | reti/siti | Sì | Sì |
> | packetCores | Sì | Sì |
> | The sims | Sì | Sì |
> | sims/simProfiles | Sì | Sì |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Sì | No |
> | netAppAccounts/accountBackups | No | No |
> | netAppAccounts/capacityPools | Sì | No |
> | netAppAccounts/capacityPools/volumes | Sì | No |
> | netAppAccounts/capacityPools/volumes/snapshots | No | No |
> | netAppAccounts/volumeGroups | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Sì | Sì |
> | applicationGatewayWebApplicationFirewallPolicies | Sì | Sì |
> | applicationSecurityGroups | Sì | Sì |
> | azureFirewallFqdnTags | No | No |
> | azureFirewalls | Sì | No |
> | bastionHosts | Sì | No |
> | bgpServiceCommunities | No | No |
> | connections | Sì | Sì |
> | ddosCustomPolicies | Sì | Sì |
> | ddosProtectionPlans | Sì | Sì |
> | dnsOperationStatuses | No | No |
> | dnszones | Sì | Sì |
> | dnszones/A | No | No |
> | dnszones/AAAA | No | No |
> | dnszones/all | No | No |
> | dnszones/CAA | No | No |
> | dnszones/CNAME | No | No |
> | dnszones/MX | No | No |
> | dnszones/NS | No | No |
> | dnszones/PTR | No | No |
> | dnszones/recordsets | No | No |
> | dnszones/SOA | No | No |
> | dnszones/SRV | No | No |
> | dnszones/TXT | No | No |
> | expressRouteCircuits | Sì | Sì |
> | expressRouteCrossConnections | Sì | Sì |
> | expressRouteGateways | Sì | Sì |
> | expressRoutePorts | Sì | Sì |
> | expressRouteServiceProviders | No | No |
> | firewallPolicies | Sì | Sì |
> | frontdoors | Sì, ma limitato (vedere la [nota seguente)](#frontdoor) | Sì |
> | frontdoorWebApplicationFirewallManagedRuleSets | Sì, ma limitato (vedere la [nota seguente)](#frontdoor) | No |
> | frontdoorWebApplicationFirewallPolicies | Sì, ma limitato (vedere la [nota seguente)](#frontdoor) | Sì |
> | getDnsResourceReference | No | No |
> | internalNotify | No | No |
> | ipGroups | Sì | Sì |
> | loadBalancers | Sì | Sì |
> | localNetworkGateways | Sì | Sì |
> | natGateways | Sì | Sì |
> | networkIntentPolicies | Sì | Sì |
> | networkInterfaces | Sì | Sì |
> | networkProfiles | Sì | Sì |
> | networkSecurityGroups | Sì | Sì |
> | networkWatchers | Sì | Sì |
> | networkWatchers/connectionMonitors | Sì | No |
> | networkWatchers/flowLogs | Sì | No |
> | networkWatchers/lenses | Sì | No |
> | networkWatchers/pingMeshes | Sì | No |
> | p2sVpnGateways | Sì | Sì |
> | privateDnsOperationStatuses | No | No |
> | privateDnsZones | Sì | Sì |
> | privateDnsZones/A | No | No |
> | privateDnsZones/AAAA | No | No |
> | privateDnsZones/all | No | No |
> | privateDnsZones/CNAME | No | No |
> | privateDnsZones/MX | No | No |
> | privateDnsZones/PTR | No | No |
> | privateDnsZones/SOA | No | No |
> | privateDnsZones/SRV | No | No |
> | privateDnsZones/TXT | No | No |
> | privateDnsZones/virtualNetworkLinks | Sì | Sì |
> | privateEndpoints | Sì | Sì |
> | privateLinkServices | Sì | Sì |
> | publicIPAddresses | Sì | Sì |
> | publicIPPrefixes | Sì | Sì |
> | routeFilters | Sì | Sì |
> | routeTables | Sì | Sì |
> | serviceEndpointPolicies | Sì | Sì |
> | trafficManagerGeographicHierarchies | No | No |
> | trafficmanagerprofiles | Sì | Sì |
> | trafficmanagerprofiles/heatMaps | No | No |
> | trafficManagerUserMetricsKeys | No | No |
> | virtualHubs | Sì | Sì |
> | virtualNetworkGateways | Sì | Sì |
> | virtualNetworks | Sì | Sì |
> | virtualNetworks/subnet | No | No |
> | virtualNetworkTaps | Sì | Sì |
> | virtualWans | Sì | No |
> | vpnGateways | Sì | Sì |
> | vpnSites | Sì | Sì |
> | webApplicationFirewallPolicies | Sì | Sì |

<a id="frontdoor"></a>

> [!NOTE]
> Ad servizio Frontdoor di Azure, è possibile applicare tag durante la creazione della risorsa, ma l'aggiornamento o l'aggiunta di tag non è attualmente supportato.


## <a name="microsoftnotebooks"></a>Microsoft.Notebooks

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | No | No |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì | No |
> | namespaces/notificationHubs | Sì | No |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Sì | Sì |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Sì | Sì |
> | ImportSites | Sì | Sì |
> | MasterSites | Sì | Sì |
> | ServerSites | Sì | Sì |
> | VMwareSites | Sì | Sì |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì | Sì |
> | deletedWorkspaces | No | No |
> | linkTargets | No | No |
> | querypacks | Sì | Sì |
> | storageInsightConfigs | No | No |
> | aree di lavoro | Sì | Sì |
> | workspaces/dataExports | No | No |
> | workspaces/dataSources | No | No |
> | workspaces/linkedServices | No | No |
> | workspaces/linkedStorageAccounts | No | No |
> | workspaces/metadata | No | No |
> | workspaces/query | No | No |
> | workspaces/scopedPrivateLinkProxies | No | No |
> | workspaces/storageInsightConfigs | No | No |
> | aree di lavoro/tabelle | No | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | managementassociations | No | No |
> | managementconfigurations | Sì | Sì |
> | solutions | Sì | Sì |
> | Viste | Sì | Sì |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | cdnPeeringPrefixes | No | No |
> | legacyPeerings | No | No |
> | peerAsns | No | No |
> | peerings | Sì | Sì |
> | peeringServiceCountries | No | No |
> | peeringServiceProviders | No | No |
> | peeringServizi | Sì | Sì |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Attestati | No | No |
> | eventGridFilters | No | No |
> | policyEvents | No | No |
> | policyMetadata | No | No |
> | policyStates | No | No |
> | policyTrackedResources | No | No |
> | remediations | No | No |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | consoles | No | No |
> | dashboards | Sì | Sì |
> | configurazioni tenant | No | No |
> | userSettings | No | No |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Sì | Sì |
> | tenants | Sì | Sì |
> | tenant/aree di lavoro | No | No |
> | workspaceCollections | Sì | Sì |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | autoScaleVCores | Sì | Sì |
> | capacities | Sì | Sì |

## <a name="microsoftpowerplatform"></a>Microsoft.PowerPlatform

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | enterprisePolicies | Sì | Sì |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | deletedAccounts | No | No |

## <a name="microsoftproviderhub"></a>Microsoft.ProviderHub

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | No | No |
> | providerRegistrations/customRollouts | No | No |
> | providerRegistrations/defaultRollouts | No | No |
> | providerRegistrations/resourceTypeRegistrations | No | No |

## <a name="microsoftpurview"></a>Microsoft.Purview

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | deletedAccounts | No | No |
> | getDefaultAccount | No | No |
> | removeDefaultAccount | No | No |
> | setDefaultAccount | No | No |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | Aree di lavoro | Sì | Sì |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | No | No |
> | insiemi di credenziali | Sì | Sì |

## <a name="microsoftredhatopenshift"></a>Microsoft.RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Sì | Sì |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì | Sì |
> | namespaces/authorizationrules | No | No |
> | namespaces/hybridconnections | No | No |
> | namespaces/hybridconnections/authorizationrules | No | No |
> | namespaces/privateEndpointConnections | No | No |
> | namespaces/wcfrelays | No | No |
> | namespaces/wcfrelays/authorizationrules | No | No |

## <a name="microsoftresourceconnector"></a>Microsoft.ResourceConnector

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | appliances | Sì | Sì |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | query | Sì | Sì |
> | resourceChangeDetails | No | No |
> | resourceChanges | No | No |
> | resources | No | No |
> | resourcesHistory | No | No |
> | subscriptionsStatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | No | No |
> | childAvailabilityStatuses | No | No |
> | childResources | No | No |
> | emergingissues | No | No |
> | eventi | No | No |
> | impactedResources | No | No |
> | metadata | No | No |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | deployments | Sì | No |
> | deployments/operations | No | No |
> | deploymentScripts | Sì | Sì |
> | deploymentScripts/logs | No | No |
> | collegamenti | No | No |
> | provider | No | No |
> | resourceGroups | Sì | No |
> | subscriptions | Sì | No |
> | templateSpecs | Sì | Sì |
> | templateSpecs/versions | Sì | Sì |
> | tenants | No | No |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | scala Web | Sì | Sì |
> | resources | Sì | Sì |
> | saasresources | No | No |

## <a name="microsoftscvmm"></a>Microsoft.ScVmm

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Nuvole | Sì | Sì |
> | VirtualMachines | Sì | Sì |
> | VirtualMachineTemplates | Sì | Sì |
> | VirtualNetworks | Sì | Sì |
> | server vmm | Sì | Sì |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | No | No |
> | searchServices | Sì | Sì |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | No | No |
> | advancedThreatProtectionSettings | No | No |
> | alerts | No | No |
> | alertsSuppressionRules | No | No |
> | allowedConnections | No | No |
> | applicationWhitelistings | No | No |
> | assessmentMetadata | No | No |
> | assessments | No | No |
> | autoDismissAlertsRules | No | No |
> | automations | Sì | Sì |
> | AutoProvisioningSettings | No | No |
> | Compliances | No | No |
> | dell'account di integrazione | No | No |
> | dataCollectionAgents | No | No |
> | devices | No | No |
> | deviceSecurityGroups | No | No |
> | discoveredSecuritySolutions | No | No |
> | externalSecuritySolutions | No | No |
> | InformationProtectionPolicies | No | No |
> | ingestionSettings | No | No |
> | insights | No | No |
> | iotAlerts | No | No |
> | iotAlertTypes | No | No |
> | iotDefenderSettings | No | No |
> | iotRecommendations | No | No |
> | iotRecommendationTypes | No | No |
> | iotSecuritySolutions | Sì | Sì |
> | iotSecuritySolutions/analyticsModels | No | No |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | No | No |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | No | No |
> | iotSecuritySolutions/iotAlerts | No | No |
> | iotSecuritySolutions/iotAlertTypes | No | No |
> | iotSecuritySolutions/iotRecommendations | No | No |
> | iotSecuritySolutions/iotRecommendationTypes | No | No |
> | iotSensors | No | No |
> | iotSites | No | No |
> | jitNetworkAccessPolicies | No | No |
> | jitPolicies | No | No |
> | onPremiseIotSensors | No | No |
> | criteri | No | No |
> | pricings | No | No |
> | regulatoryComplianceStandards | No | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls | No | No |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | No | No |
> | secureScoreControlDefinitions | No | No |
> | secureScoreControls | No | No |
> | secureScores | No | No |
> | secureScores/secureScoreControls | No | No |
> | securityContacts | No | No |
> | securitySolutions | No | No |
> | securitySolutionsReferenceData | No | No |
> | securityStatuses | No | No |
> | securityStatusesSummaries | No | No |
> | serverVulnerabilityAssessments | No | No |
> | Scheda Impostazioni | No | No |
> | sqlVulnerabilityAssessments | No | No |
> | sottoAssessimenti | No | No |
> | attività | No | No |
> | topologies | No | No |
> | workspaceSettings | No | No |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | aggregations | No | No |
> | alertRules | No | No |
> | alertRuleTemplates | No | No |
> | automationRules | No | No |
> | bookmarks | No | No |
> | cases | No | No |
> | dataConnectors | No | No |
> | dataConnectorsCheckRequirements | No | No |
> | Arricchimento | No | No |
> | entities | No | No |
> | entityQueries | No | No |
> | entityQueryTemplates | No | No |
> | incidents | No | No |
> | officeConsents | No | No |
> | Scheda Impostazioni | No | No |
> | threatIntelligence | No | No |
> | watchlist | No | No |

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | consoleServices | No | No |
> | serialPorts | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | spazi dei nomi | Sì | Sì |
> | namespaces/authorizationrules | No | No |
> | namespaces/disasterrecoveryconfigs | No | No |
> | namespaces/eventgridfilters | No | No |
> | namespaces/networkrulesets | No | No |
> | namespaces/privateEndpointConnections | No | No |
> | namespaces/queues | No | No |
> | namespaces/queues/authorizationrules | No | No |
> | namespaces/topics | No | No |
> | namespaces/topics/authorizationrules | No | No |
> | namespaces/topics/subscriptions | No | No |
> | namespaces/topics/subscriptions/rules | No | No |
> | premiumMessagingRegions | No | No |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | scala Web | Sì | Sì |
> | clusters | Sì | Sì |
> | clusters/applications | No | No |
> | containerGroups | Sì | Sì |
> | containerGroupSets | Sì | Sì |
> | edgeclusters | Sì | Sì |
> | edgeclusters/applications | No | No |
> | managedclusters | Sì | Sì |
> | managedclusters/applications | No | No |
> | managedclusters/applications/services | No | No |
> | managedclusters/applicationTypes | No | No |
> | managedclusters/applicationTypes/versions | No | No |
> | managedclusters/nodetypes | No | No |
> | networks | Sì | Sì |
> | secretstores | Sì | Sì |
> | secretstores/certificates | No | No |
> | secretstores/secrets | No | No |
> | volumes | Sì | Sì |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | scala Web | Sì | Sì |
> | containerGroups | Sì | Sì |
> | gateways | Sì | Sì |
> | networks | Sì | Sì |
> | chiavi private | Sì | Sì |
> | volumes | Sì | Sì |

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | Linker | No | No |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | No | No |
> | providerRegistrations/resourceTypeRegistrations | No | No |
> | rollouts | Sì | Sì |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | SignalR | Sì | Sì |
> | SignalR/eventGridFilters | No | No |
> | WebPubSub | Sì | Sì |

## <a name="microsoftsingularity"></a>Microsoft.Singularity

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | Sì |
> | accounts/accountQuotaPolicies | No | No |
> | accounts/groupPolicies | No | No |
> | account/processi | No | No |
> | accounts/storageContainers | No | No |
> | images | No | No |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | No | No |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Sì | Sì |
> | scala Web | Sì | Sì |
> | jitRequests | Sì | Sì |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | longtermRetentionManagedInstance/longtermRetentionDatabase/longtermRetentionBackup | No | No |
> | longtermRetentionServer/longtermRetentionDatabase/longtermRetentionBackup | No | No |
> | managedInstances | Sì | Sì |
> | managedInstances/databases | No | No |
> | managedInstances/databases/backupShortTermRetentionPolicies | No | No |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | No | No |
> | managedInstances/databases/vulnerabilityAssessments | No | No |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | No | No |
> | managedInstances/encryptionProtector | No | No |
> | managedInstances/keys | No | No |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | No | No |
> | managedInstances/vulnerabilityAssessments | No | No |
> | servers | Sì | Sì |
> | servers/administrators | No | No |
> | servers/communicationLinks | No | No |
> | servers/databases | Sì (vedere [la nota seguente)](#sqlnote) | Sì |
> | servers/encryptionProtector | No | No |
> | servers/firewallRules | No | No |
> | servers/keys | No | No |
> | servers/restorableDroppedDatabases | No | No |
> | servers/serviceobjectives | No | No |
> | servers/tdeCertificates | No | No |
> | virtualClusters | Sì | Sì |

<a id="sqlnote"></a>

> [!NOTE]
> Il database master non supporta i tag, ma altri database, inclusi i database Azure Synapse Analytics, supportano i tag. Azure Synapse Analytics database deve essere attivo (non sospeso).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Sì | Sì |
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | No | No |
> | SqlVirtualMachines | Sì | Sì |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | No | No |
> | storageAccounts | Sì | Sì |
> | storageAccounts/blobServices | No | No |
> | storageAccounts/fileServices | No | No |
> | storageAccounts/queueServices | No | No |
> | storageAccounts/services | No | No |
> | storageAccounts/services/metricDefinitions | No | No |
> | storageAccounts/tableServices | No | No |
> | usages | No | No |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | amlFilesystems | Sì | Sì |
> | caches | Sì | Sì |
> | caches/storageTargets | No | No |
> | usageModels | No | No |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | replicationGroups | No | No |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sì | Sì |
> | storageSyncServices/registeredServers | No | No |
> | storageSyncServices/syncGroups | No | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No | No |
> | storageSyncServices/syncGroups/serverEndpoints | No | No |
> | storageSyncServices/workflows | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sì | Sì |
> | storageSyncServices/registeredServers | No | No |
> | storageSyncServices/syncGroups | No | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No | No |
> | storageSyncServices/syncGroups/serverEndpoints | No | No |
> | storageSyncServices/workflows | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Sì | Sì |
> | storageSyncServices/registeredServers | No | No |
> | storageSyncServices/syncGroups | No | No |
> | storageSyncServices/syncGroups/cloudEndpoints | No | No |
> | storageSyncServices/syncGroups/serverEndpoints | No | No |
> | storageSyncServices/workflows | No | No |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | managers | Sì | Sì |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | clusters | Sì | Sì |
> | clusters/privateEndpoints | No | No |
> | streamingjobs | Sì (vedere la nota seguente) | Sì |

> [!NOTE]
> Impossibile aggiungere un tag quando streamingjobs è in esecuzione. Interrompere la risorsa per aggiungere un tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | No | No |
> | acceptOwnership | No | No |
> | acceptOwnershipStatus | No | No |
> | alias | No | No |
> | cancel | No | No |
> | changeTenantRequest | No | No |
> | changeTenantStatus | No | No |
> | CreateSubscription | No | No |
> | abilitare | No | No |
> | criteri | No | No |
> | ridenominazione | No | No |
> | SubscriptionDefinitions | No | No |
> | SubscriptionOperations | No | No |
> | subscriptions | No | No |

## <a name="microsoftsynapse"></a>Microsoft.Synapse

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Sì | Sì |
> | aree di lavoro | Sì | Sì |
> | workspaces/bigDataPools | Sì | Sì |
> | workspaces/operationStatuses | No | No |
> | workspaces/sqlDatabases | Sì | Sì |
> | workspaces/sqlPools | Sì | Sì |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | environments | Sì | No |
> | environments/accessPolicies | No | No |
> | environments/eventsources | Sì | No |
> | environments/privateEndpointConnectionProxies | No | No |
> | environments/privateEndpointConnections | No | No |
> | environments/privateLinkResources | No | No |
> | environments/referenceDataSets | Sì | No |

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | stores | Sì | Sì |
> | stores/accessPolicies | No | No |
> | stores/services | No | No |
> | stores/services/tokens | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Sì | Sì |
> | imageTemplates/runOutputs | No | No |

## <a name="microsoftvmware"></a>Microsoft.VMware

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report sui costi |
> | ------------- | ----------- | ----------- |
> | ArcZones | Sì | Sì |
> | Pool di risorse | Sì | Sì |
> | VCenter | Sì | Sì |
> | VCenters/InventoryItems | No | No |
> | virtualmachines | Sì | Sì |
> | VirtualMachineTemplates | Sì | Sì |
> | VirtualNetworks | Sì | Sì |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Sì | Sì |
> | dedicatedCloudServices | Sì | Sì |
> | virtualMachines | Sì | Sì |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | devices | Sì | Sì |
> | registeredSubscriptions | No | No |
> | Fornitori | No | No |
> | vendors/skus | No | No |
> | vendors/vnfs | No | No |
> | virtualNetworkFunctionSkus | No | No |
> | vnfs | Sì | Sì |

## <a name="microsoftvsonline"></a>Microsoft.VSOnline

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | account | Sì | No |
> | plans | Sì | No |
> | registeredSubscriptions | No | No |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | No | No |
> | apiManagementAccounts/apiAcls | No | No |
> | apiManagementAccounts/apis | No | No |
> | apiManagementAccounts/apis/apiAcls | No | No |
> | apiManagementAccounts/apis/connectionAcls | No | No |
> | apiManagementAccounts/apis/connections | No | No |
> | apiManagementAccounts/apis/connections/connectionAcls | No | No |
> | apiManagementAccounts/apis/localizedDefinitions | No | No |
> | apiManagementAccounts/connectionAcls | No | No |
> | apiManagementAccounts/connections | No | No |
> | billingMeters | No | No |
> | certificates | Sì | Sì |
> | connectionGateways | Sì | Sì |
> | connections | Sì | Sì |
> | customApis | Sì | Sì |
> | deletedSites | No | No |
> | functionAppStacks | No | No |
> | generateGithubAccessTokenForAppserviceCLI | No | No |
> | hostingEnvironments | Sì | Sì |
> | hostingEnvironments/eventGridFilters | No | No |
> | hostingEnvironments/multiRolePools | No | No |
> | hostingEnvironments/workerPools | No | No |
> | kubeEnvironments | Sì | Sì |
> | publishingUsers | No | No |
> | raccomandazioni di film | No | No |
> | resourceHealthMetadata | No | No |
> | runtimes | No | No |
> | serverFarms | Sì | Sì |
> | serverFarms/eventGridFilters | No | No |
> | serverFarms/firstPartyApps | No | No |
> | serverFarms/firstPartyApps/keyVaultSettings | No | No |
> | siti | Sì | Sì |
> | sites/config  | No | No |
> | sites/eventGridFilters | No | No |
> | sites/hostNameBindings | No | No |
> | sites/networkConfig | No | No |
> | sites/premieraddons | Sì | Sì |
> | sites/slots | Sì | Sì |
> | sites/slots/eventGridFilters | No | No |
> | sites/slots/hostNameBindings | No | No |
> | sites/slots/networkConfig | No | No |
> | sourceControls | No | No |
> | staticSites | Sì | Sì |
> | validate | No | No |
> | verifyHostingEnvironmentVnet | No | No |
> | webAppStacks | No | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftwindowsesu"></a>Microsoft.WindowsESU

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Sì | Sì |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Sì | Sì |

## <a name="microsoftworkloadbuilder"></a>Microsoft.WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | migrationAgents | Sì | Sì |
> | carichi di lavoro | Sì | Sì |
> | carichi di lavoro/istanze | No | No |
> | carichi di lavoro/versioni | No | No |
> | workloads/versions/artifacts | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Tipo di risorsa | Tag supportati | Tag nel report dei costi |
> | ------------- | ----------- | ----------- |
> | monitors | No | No |

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come applicare tag alle risorse, vedere [Usare tag per organizzare le risorse di Azure](tag-resources.md).
