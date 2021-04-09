---
title: Resurs leverantörer efter Azure-tjänster
description: Visar en lista över alla resurs leverantörs namn områden för Azure Resource Manager och Azure-tjänsten för namn området visas.
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: d8239a9305560eb77ac2c714b993373f0d5b16fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023018"
---
# <a name="resource-providers-for-azure-services"></a>Resursleverantörer för Azure-tjänster

Den här artikeln visar hur namn områden för resurs leverantörer mappas till Azure-tjänster. Om du inte känner till resurs leverantören kan du läsa [hitta resurs leverantör](#find-resource-provider).

## <a name="match-resource-provider-to-service"></a>Matcha Resource Provider till tjänst

De resurs leverantörer som är markerade med **-registrerade** registreras som standard för din prenumeration. Mer information finns i [registrering](#registration).

| Namn område för resurs leverantör | Azure-tjänst |
| --------------------------- | ------------- |
| Microsoft. AAD | [Azure Active Directory Domain Services](../../active-directory-domain-services/index.yml) |
| Microsoft. addons | grundläggande |
| Microsoft. ADHybridHealthService- [registrerad](#registration) | [Azure Active Directory](../../active-directory/index.yml) |
| Microsoft. Advisor | [Azure Advisor](../../advisor/index.yml) |
| Microsoft. AlertsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. AnalysisServices | [Azure Analysis Services](../../analysis-services/index.yml) |
| Microsoft. API Management | [API Management](../../api-management/index.yml) |
| Microsoft. AppConfiguration | [Azure App Configuration](../../azure-app-configuration/index.yml) |
| Microsoft. AppPlatform | [Azure Spring Cloud](../../spring-cloud/overview.md) |
| Microsoft. attestering | Tjänsten Azure attestering |
| Microsoft. Authorization- [registrerad](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. Automation | [Automation](../../automation/index.yml) |
| Microsoft. AutonomousSystems | [Autonoma system](https://www.microsoft.com/ai/autonomous-systems) |
| Microsoft. AVS | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft. AzureActiveDirectory | [Azure Active Directory B2C](../../active-directory-b2c/index.yml) |
| Microsoft. AzureArcData | Azure Arc-aktiverade datatjänster |
| Microsoft. AzureData | SQL Server registret |
| Microsoft. AzureStack | grundläggande |
| Microsoft. AzureStackHCI | [Azure Stack HCI](/azure-stack/hci/overview) |
| Microsoft.Batch | [Batch](../../batch/index.yml) |
| Microsoft. fakturering- [registrerad](#registration) | [Kostnadshantering och fakturering](/azure/billing/) |
| Microsoft. Bingkartssökning | [Bing-kartor](/BingMaps/#pivot=main&panel=BingMapsAPI) |
| Microsoft. blockchain | [Azure Blockchain Service](../../blockchain/workbench/index.yml) |
| Microsoft. BlockchainTokens | [Azure Blockchain-token](https://azure.microsoft.com/services/blockchain-tokens/) |
| Microsoft. skiss | [Azure Blueprint](../../governance/blueprints/index.yml) |
| Microsoft. BotService | [Azure Bot Service](/azure/bot-service/) |
| Microsoft. cache | [Azure Cache for Redis](../../azure-cache-for-redis/index.yml) |
| Microsoft. Capacity | grundläggande |
| Microsoft. CDN | [Content Delivery Network](../../cdn/index.yml) |
| Microsoft. CertificateRegistration | [App Service certifikat](../../app-service/configure-ssl-certificate.md#import-an-app-service-certificate) |
| Microsoft. ChangeAnalysis | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft.ClassicCompute | Klassisk virtuell distributions modell dator |
| Microsoft. ClassicInfrastructureMigrate | Klassisk migrering av distributions modell |
| Microsoft. ClassicNetwork | Klassiskt virtuellt nätverk för distributions modell |
| Microsoft. ClassicStorage | Klassisk distributions modell lagring |
| Microsoft. ClassicSubscription- [registrerad](#registration) | Klassisk distributionsmodell |
| Microsoft. CognitiveServices | [Cognitive Services](../../cognitive-services/index.yml) |
| Microsoft. Commerce – [registrerad](#registration) | grundläggande |
| Microsoft.Compute | [Virtual Machines](../../virtual-machines/index.yml)<br />[Virtual Machine Scale Sets](../../virtual-machine-scale-sets/index.yml) |
| Microsoft. förbrukning- [registrerad](#registration) | [Cost Management](/azure/cost-management/) |
| Microsoft. ContainerInstance | [Container Instances](../../container-instances/index.yml) |
| Microsoft. ContainerRegistry | [Container Registry](../../container-registry/index.yml) |
| Microsoft. container service | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft. CostManagement- [registrerad](#registration) | [Cost Management](/azure/cost-management/) |
| Microsoft. CostManagementExports | [Cost Management](/azure/cost-management/) |
| Microsoft. CustomerLockbox | [Customer Lockbox för Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md) |
| Microsoft. CustomProviders | [Anpassade providrar för Azure](../custom-providers/overview.md) |
| Microsoft. data- | [Azure Data Box](../../databox/index.yml) |
| Microsoft. DataBoxEdge | [Azure Stack Edge](../../databox-online/azure-stack-edge-overview.md) |
| Microsoft. Databricks | [Azure Databricks](/azure/azure-databricks/) |
| Microsoft. DataCatalog | [Data Catalog](../../data-catalog/index.yml) |
| Microsoft. DataFactory | [Data Factory](../../data-factory/index.yml) |
| Microsoft. DataLakeAnalytics | [Data Lake Analytics](../../data-lake-analytics/index.yml) |
| Microsoft. DataLakeStore | [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md) |
| Microsoft. data migration | [Azure Database Migration Service](../../dms/index.yml) |
| Microsoft. DataProtection | Dataskydd |
| Microsoft. DataShare | [Azure Data Share](../../data-share/index.yml) |
| Microsoft. DBforMariaDB | [Azure Database för MariaDB](../../mariadb/index.yml) |
| Microsoft. DBforMySQL | [Azure Database for MySQL](../../mysql/index.yml) |
| Microsoft. DBforPostgreSQL | [Azure Database for PostgreSQL](../../postgresql/index.yml) |
| Microsoft. DeploymentManager | [Azure Deployment Manager](../templates/deployment-manager-overview.md) |
| Microsoft. DesktopVirtualization | [Windows Virtual Desktop](../../virtual-desktop/index.yml) |
| Microsoft.Devices | [Azure IoT Hub](../../iot-hub/index.yml)<br />[Azure IoT Hub Device Provisioning-tjänst](../../iot-dps/index.yml) |
| Microsoft. DevOps | [Azure DevOps](/azure/devops/) |
| Microsoft. DevSpaces | [Azure Dev Spaces](../../dev-spaces/index.yml) |
| Microsoft. DevTestLab | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. DigitalTwins | [Azure Digital Twins](../../digital-twins/overview.md) |
| Microsoft.DocumentDB | [Azure Cosmos DB](../../cosmos-db/index.yml) |
| Microsoft. DomainRegistration | [App Service](../../app-service/index.yml) |
| Microsoft. DynamicsLcs | [Livs cykel tjänster](https://lcs.dynamics.com/Logon/Index ) |
| Microsoft. EnterpriseKnowledgeGraph | Företags kunskaps diagram |
| Microsoft. EventGrid | [Event Grid](../../event-grid/index.yml) |
| Microsoft. EventHub | [Event Hubs](../../event-hubs/index.yml) |
| Microsoft. features- [registrerade](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. GuestConfiguration | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. HanaOnAzure | [SAP HANA på stora Azure-instanser](../../virtual-machines/workloads/sap/hana-overview-architecture.md) |
| Microsoft. HardwareSecurityModules | [Azure Dedicated HSM](../../dedicated-hsm/index.yml) |
| Microsoft. HDInsight | [HDInsight](../../hdinsight/index.yml) |
| Microsoft. HealthcareApis | [Azure API för FHIR](../../healthcare-apis/fhir/index.yml) |
| Microsoft. HybridCompute | [Azure Arc](../../azure-arc/index.yml) |
| Microsoft. HybridData | [StorSimple](../../storsimple/index.yml) |
| Microsoft. HybridNetwork  | [Privata gräns zoner](../../networking/edge-zones-overview.md) |
| Microsoft. ImportExport | [Azure Import/Export](../../import-export/storage-import-export-service.md) |
| Microsoft. Insights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. IoTCentral | [Azure IoT Central](../../iot-central/index.yml) |
| Microsoft. IoTSpaces | [Azure Digital Twins](../../digital-twins/index.yml) |
| Microsoft.Intune | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. nyckel valv | [Key Vault](../../key-vault/index.yml) |
| Microsoft. Kubernetes | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft. KubernetesConfiguration | [Azure Kubernetes Service (AKS)](../../aks/index.yml) |
| Microsoft.Kusto | [Azure-datautforskaren](/azure/data-explorer/) |
| Microsoft. LabServices | [Azure Lab Services](../../lab-services/index.yml) |
| Microsoft. Logic | [Logic Apps](../../logic-apps/index.yml) |
| Microsoft. MachineLearning | [Machine Learning Studio](../../machine-learning/classic/index.yml) |
| Microsoft.MachineLearningServices | [Azure Machine Learning](../../machine-learning/index.yml) |
| Microsoft. Maintenance | [Azure-underhåll](../../virtual-machines/maintenance-control-cli.md) |
| Microsoft. ManagedIdentity | [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/index.yml) |
| Microsoft. ManagedNetwork | Virtuella nätverk som hanteras av PaaS Services |
| Microsoft. ManagedServices | [Azure Lighthouse](../../lighthouse/index.yml) |
| Microsoft. Management | [Hanteringsgrupper](../../governance/management-groups/index.yml) |
| Microsoft. Maps | [Azure Maps](../../azure-maps/index.yml) |
| Microsoft. Marketplace | grundläggande |
| Microsoft. MarketplaceApps | grundläggande |
| Microsoft. MarketplaceOrdering- [registrerad](#registration) | grundläggande |
| Microsoft. Media | [Media Services](../../media-services/index.yml) |
| Microsoft. Microservices4Spring | [Azure Spring Cloud](../../spring-cloud/overview.md) |
| Microsoft. Migrate | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft. MixedReality | [Azure Spatial Anchors](../../spatial-anchors/index.yml) |
| Microsoft. NetApp | [Azure NetApp Files](../../azure-netapp-files/index.yml) |
| Microsoft.Network | [Application Gateway](../../application-gateway/index.yml)<br />[Azure Bastion](../../bastion/index.yml)<br />[Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)<br />[Azure DNS](../../dns/index.yml)<br />[Azure ExpressRoute](../../expressroute/index.yml)<br />[Azure Firewall](../../firewall/index.yml)<br />[Azure Front Door Service](../../frontdoor/index.yml)<br />[Azure Private Link](../../private-link/index.yml)<br />[Load Balancer](../../load-balancer/index.yml)<br />[Network Watcher](../../network-watcher/index.yml)<br />[Traffic Manager](../../traffic-manager/index.yml)<br />[Virtual Network](../../virtual-network/index.yml)<br />[Virtuellt WAN](../../virtual-wan/index.yml)<br />[VPN Gateway](../../vpn-gateway/index.yml)<br /> |
| Microsoft. Notebook | [Azure Notebooks](https://notebooks.azure.com/help/introduction) |
| Microsoft. NotificationHubs | [Notification Hubs](../../notification-hubs/index.yml) |
| Microsoft. ObjectStore | Objekt Arkiv |
| Microsoft. OffAzure | [Azure Migrate](../../migrate/migrate-services-overview.md) |
| Microsoft. OperationalInsights | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. OperationsManagement | [Azure Monitor](../../azure-monitor/index.yml) |
| Microsoft. peering | [Azure Peering Service](../../peering-service/index.yml) |
| Microsoft. PolicyInsights | [Azure Policy](../../governance/policy/index.yml) |
| Microsoft. Portal – [registrerad](#registration) | [Azure-portalen](../../azure-portal/index.yml) |
| Microsoft. PowerBI | [Power BI](/power-bi/power-bi-overview) |
| Microsoft. PowerBIDedicated | [Power BI Embedded](/azure/power-bi-embedded/) |
| Microsoft. PowerPlatform | [Power Platform](/power-platform/) |
| Microsoft. ProjectBabylon | [Azure Data Catalog](../../data-catalog/overview.md) |
| Microsoft. Quantum | [Azure Quantum](https://azure.microsoft.com/services/quantum/) |
| Microsoft. RecoveryServices | [Azure Site Recovery](../../site-recovery/index.yml) |
| Microsoft. RedHatOpenShift | [Azure Red Hat OpenShift](../../virtual-machines/linux/openshift-get-started.md) |
| Microsoft. Relay | [Azure Relay](../../azure-relay/relay-what-is-it.md) |
| Microsoft. ResourceGraph- [registrerad](#registration) | [Azure Resource Graph](../../governance/resource-graph/index.yml) |
| Microsoft. ResourceHealth | [Azure Service Health](../../service-health/index.yml) |
| Microsoft. Resources- [registrerade](#registration) | [Azure Resource Manager](../index.yml) |
| Microsoft. SaaS | grundläggande |
| Microsoft. Scheduler | [Scheduler](../../scheduler/index.yml) |
| Microsoft. search | [Azure Cognitive Search](../../search/index.yml) |
| Microsoft. Security | [Security Center](../../security-center/index.yml) |
| Microsoft. SecurityInsights | [Azure Sentinel](../../sentinel/index.yml) |
| Microsoft. SerialConsole- [registrerad](#registration) | [Azures serie konsol för Windows](/troubleshoot/azure/virtual-machines/serial-console-windows) |
| Microsoft.ServiceBus | [Service Bus](/azure/service-bus/) |
| Microsoft. ServiceFabric | [Service Fabric](../../service-fabric/index.yml) |
| Microsoft. ServiceFabricMesh | [Service Fabric Mesh](../../service-fabric-mesh/index.yml) |
| Microsoft. Services | grundläggande |
| Microsoft. SignalRService | [Azure SignalR Service](../../azure-signalr/index.yml) |
| Microsoft. SoftwarePlan | Licens |
| Microsoft. Solutions | [Azure Managed Applications](../managed-applications/index.yml) |
| Microsoft.Sql | [Azure SQL Database](../../azure-sql/database/index.yml)<br /> [Hanterad Azure SQL-instans](../../azure-sql/managed-instance/index.yml) <br />[Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. SqlVirtualMachine | [SQL Server på Azure Virtual Machines](../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) |
| Microsoft.Storage | [Storage](../../storage/index.yml) |
| Microsoft. StorageCache | [Azure HPC Cache](../../hpc-cache/index.yml) |
| Microsoft. StorageSync | [Storage](../../storage/index.yml) |
| Microsoft. StorSimple | [StorSimple](../../storsimple/index.yml) |
| Microsoft. StreamAnalytics | [Azure Stream Analytics](../../stream-analytics/index.yml) |
| Microsoft. Subscription | grundläggande |
| Microsoft. support – [registrerad](#registration) | grundläggande |
| Microsoft. Synapse | [Azure Synapse Analytics](/azure/sql-data-warehouse/) |
| Microsoft. TimeSeriesInsights | [Azure Time Series Insights](../../time-series-insights/index.yml) |
| Microsoft. token | Token |
| Microsoft. VirtualMachineImages | [Azure Image Builder](../../virtual-machines/image-builder-overview.md) |
| Microsoft. VisualStudio | [Azure DevOps](/azure/devops/) |
| Microsoft. VMware | [Azure VMware Solution](../../azure-vmware/index.yml) |
| Microsoft. VMwareCloudSimple | [Azure VMware Solution by CloudSimple](../../vmware-cloudsimple/index.md) |
| Microsoft. VSOnline | [Azure DevOps](/azure/devops/) |
| Microsoft. Web | [App Service](../../app-service/index.yml)<br />[Azure Functions](../../azure-functions/index.yml) |
| Microsoft. WindowsDefenderATP | [Microsoft Defender Advanced Threat Protection](../../security-center/security-center-wdatp.md) |
| Microsoft. WindowsESU | Utökade säkerhets uppdateringar |
| Microsoft. WindowsIoT | [Windows 10 IoT Core Services](/windows-hardware/manufacture/iot/iotcoreservicesoverview) |
| Microsoft. WorkloadMonitor | [Azure Monitor](../../azure-monitor/index.yml) |

## <a name="registration"></a>Registrering

De resurs leverantörer som är markerade med **-registrerade** registreras som standard för din prenumeration. Om du vill använda andra resurs leverantörer måste du [registrera dem](resource-providers-and-types.md). Många resurs leverantörer registreras dock åt dig när du vidtar vissa åtgärder. Om du till exempel skapar en resurs via portalen registrerar portalen automatiskt alla oregistrerade resurs leverantörer som behövs. När du distribuerar resurser via en [Azure Resource Manager mall](../templates/overview.md)registreras även eventuella obligatoriska resurs leverantörer.

> [!IMPORTANT]
> Registrera bara en resurs leverantör när du är redo att använda den. Med registrerings steget kan du behålla de lägsta privilegierna i din prenumeration. En obehörig användare kan inte använda resurs leverantörer som inte är registrerade.

## <a name="find-resource-provider"></a>Hitta resurs leverantör

Om du har en befintlig infrastruktur i Azure, men inte är säker på vilken resurs leverantör som används, kan du använda antingen Azure CLI eller PowerShell för att hitta resurs leverantören. Ange namnet på den resurs grupp som innehåller de resurser som du vill hitta.

I följande exempel används Azure CLI:

```azurecli-interactive
az resource list -g examplegroup
```

Resultatet inkluderar resurs typen. Namn området för resurs leverantören är den första delen av resurs typen. I följande exempel visas resurs leverantören **Microsoft. nyckel valv** .

```json
[
  {
    ...
    "type": "Microsoft.KeyVault/vaults"
  }
]
```

I följande exempel används PowerShell:

```azurepowershell-interactive
Get-AzResource -ResourceGroupName examplegroup
```

Resultatet inkluderar resurs typen. Namn området för resurs leverantören är den första delen av resurs typen. I följande exempel visas resurs leverantören **Microsoft. nyckel valv** .

```azurepowershell
Name              : examplekey
ResourceGroupName : examplegroup
ResourceType      : Microsoft.KeyVault/vaults
...
```

## <a name="next-steps"></a>Nästa steg

Mer information om resurs leverantörer, inklusive hur du registrerar en resurs leverantör finns i [Azure Resource providers och-typer](resource-providers-and-types.md).