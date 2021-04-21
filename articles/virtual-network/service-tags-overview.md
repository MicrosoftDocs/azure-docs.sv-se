---
title: Översikt över Azure-tjänsttaggar
titlesuffix: Azure Virtual Network
description: Läs mer om tjänsttaggar. Tjänsttaggar hjälper till att minimera komplexiteten vid skapande av säkerhetsregel.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/14/2021
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 4a18e0f257ac8d7b7d981d53249cf29f2f8f38bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790157"
---
# <a name="virtual-network-service-tags"></a>Tjänsttaggar för virtuellt nätverk
<a name="network-service-tags"></a>

En tjänsttagg representerar en grupp med IP-adressprefix från en viss Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras, vilket minimerar komplexiteten i frekventa uppdateringar av nätverkssäkerhetsregler.

Du kan använda tjänsttaggar för att definiera nätverksåtkomstkontroller [för nätverkssäkerhetsgrupper](./network-security-groups-overview.md#security-rules) [eller Azure Firewall](../firewall/service-tags.md). Använd tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange namnet på tjänsttaggen, till  exempel  **ApiManagement,** i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. 

> [!NOTE] 
> Från och med mars 2021 kan du också använda tjänsttaggar i stället för explicita IP-intervall i [användardefinierade vägar](./virtual-networks-udr-overview.md). Den här funktionen är för närvarande i allmänt tillgänglig förhandsversion. 

Du kan använda tjänsttaggar för att uppnå nätverksisolering och skydda dina Azure-resurser från det allmänna Internet vid åtkomst till Azure-tjänster som har offentliga slutpunkter. Skapa regler för inkommande/utgående nätverkssäkerhetsgrupp för att neka trafik till/från **Internet** och tillåta trafik till/från **AzureCloud** eller andra tillgängliga tjänsttaggar [för](#available-service-tags) specifika Azure-tjänster.

![Nätverksisolering av Azure-tjänster med hjälp av tjänsttaggar](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Tillgängliga tjänsttaggar
Följande tabell innehåller alla tjänsttaggar som är tillgängliga för användning i [regler för nätverkssäkerhetsgrupp.](./network-security-groups-overview.md#security-rules)

Kolumnerna anger om taggen:

- Passar för regler som omfattar inkommande eller utgående trafik.
- Stöder [regionalt omfång.](https://azure.microsoft.com/regions)
- Kan användas i [Azure Firewall](../firewall/service-tags.md) regler.

Som standard återspeglar tjänsttaggar intervallen för hela molnet. Vissa tjänsttaggar tillåter också mer detaljerad kontroll genom att begränsa motsvarande IP-intervall till en angiven region. Tjänsttaggen Storage  representerar till exempel Azure Storage för hela molnet, men **Storage.WestUS** begränsar intervallet till endast lagrings-IP-adressintervallen från regionen WestUS. I följande tabell visas om varje tjänsttagg stöder ett sådant regionalt omfång.  

| Tagg | Syfte | Kan du använda inkommande eller utgående? | Kan vara regional? | Kan du använda med Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| **ActionGroup** | Åtgärdsgrupp. | Inkommande | Inga | Inga |
| **ApiManagement** | Hanteringstrafik för Azure API Management-dedikerade distributioner. <br/><br/>*Obs!* Den här taggen representerar Azure API Management-tjänstslutpunkten för kontrollplanet per region. På så sätt kan kunder utföra hanteringsåtgärder på API:er, åtgärder, principer, NamedValues som konfigurerats på API Management tjänsten.  | Inkommande | Ja | Ja |
| **ApplicationInsightsAvailability** | Application Insights tillgänglighet. | Inkommande | Inga | Inga |
| **AppConfiguration** | App Configuration. | Utgående | Inga | Inga |
| **AppService**    | Azure App Service. Den här taggen rekommenderas för utgående säkerhetsregler till webbappar och funktionsappar.  | Utgående | Ja | Ja |
| **AppServiceManagement** | Hanteringstrafik för distributioner som är dedikerade App Service-miljön. | Båda | Inga | Ja |
| **AzureActiveDirectory** | Azure Active Directory. | Utgående | Inga | Ja |
| **AzureActiveDirectoryDomainServices** | Hanteringstrafik för distributioner som är dedikerade Azure Active Directory Domain Services. | Båda | Inga | Ja |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | Utgående | Inga | Inga |
| **AzureAPIForFHIR** | Azure API for FHIR (FHIR (elektronisk standard för utbyte av vårdinformation)).<br/><br/> *Obs! Den här taggen kan för närvarande inte konfigureras via Azure-portalen.*| Utgående | Inga | Inga |
| **AzureArcInfrastructure** | Azure Arc-aktiverade servrar, Azure Arc-aktiverade Kubernetes- och gästkonfigurationstrafik.<br/><br/>*Obs!* Den här taggen är beroende av **taggarna AzureActiveDirectory,****AzureTrafficManager** och **AzureResourceManager.** *Den här taggen kan för närvarande inte konfigureras via Azure Portal*.| Utgående | Inga | Ja |
| **AzureBackup** |Azure Backup.<br/><br/>*Obs!* Den här taggen är beroende av **taggarna Storage** **och AzureActiveDirectory.** | Utgående | Inga | Ja |
| **AzureBotService** | Azure Bot Service. | Utgående | Inga | Inga |
| **AzureCloud** | Alla [offentliga IP-adresser för datacenter.](https://www.microsoft.com/download/details.aspx?id=56519) | Utgående | Ja | Ja |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Den här taggen eller IP-adresserna som omfattas av den här taggen kan användas för att bevilja indexerare säker åtkomst till datakällor. Mer information [finns i dokumentationen om](../search/search-indexer-troubleshooting.md#connection-errors) indexeraranslutning. <br/><br/> *Obs!* Söktjänstens IP-adress ingår inte i listan över IP-intervall för den här tjänsttaggen och måste också läggas till i IP-brandväggen för datakällor.  | Inkommande | Inga | Inga |
| **AzureConnectors** | Den här taggen representerar de IP-adresser som används för hanterade anslutningsappar som gör inkommande webhook-återanrop till Azure Logic Apps-tjänsten och utgående anrop till sina respektive tjänster, till exempel Azure Storage eller Azure Event Hubs. | Inkommande/utgående | Ja | Ja |
| **AzureContainerRegistry** | Azure Container Registry. | Utgående | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos DB. | Utgående | Ja | Ja |
| **AzureDatabricks** | Azure Databricks. | Båda | Inga | Inga |
| **AzureDataExplorerManagement** | Azure Data Explorer Management. | Inkommande | Inga | Inga |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Utgående | Inga | Ja |
| **AzureDevSpaces** | Azure Dev Spaces. | Utgående | Inga | Inga |
| **AzureDevOps** | Azure Dev Ops.<br/><br/>*Obs! Den här taggen kan för närvarande inte konfigureras via Azure Portal*| Inkommande | Inga | Ja |
| **AzureDigitalTwins** | Azure Digital Twins.<br/><br/>*Obs!* Den här taggen eller IP-adresserna som omfattas av den här taggen kan användas för att begränsa åtkomsten till slutpunkter som konfigurerats för händelsevägar. *Den här taggen kan för närvarande inte konfigureras via Azure Portal* | Inkommande | Inga | Ja |
| **AzureEventGrid** | Azure Event Grid. | Båda | Inga | Inga |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure Front Door. | Båda | Inga | Inga |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Obs!* Den här taggen är beroende av **taggarna AzureActiveDirectory,** **AzureFrontDoor.Frontend** och **AzureFrontDoor.FirstParty.** | Utgående | Inga | Inga |
| **AzureIoTHub** | Azure IoT Hub. | Utgående | Inga | Inga |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Obs!* Den här taggen är beroende av **taggen AzureActiveDirectory.** | Utgående | Ja | Ja |
| **AzureLoadBalancer** | Lastbalanserare för Azure-infrastrukturen. Taggen översätts till värdens virtuella [IP-adress](./network-security-groups-overview.md#azure-platform-considerations) (168.63.129.16) som Azure-hälsoavsökningarna kommer från. Detta omfattar endast avsökningstrafik, inte verklig trafik till din backend-resurs. Om du inte använder Azure Load Balancer kan du åsidosätta den här regeln. | Båda | Inga | Inga |
| **AzureMachineLearning** | Azure Machine Learning. | Båda | Inga | Ja |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon och anpassade mått (GiG-slutpunkter).<br/><br/>*Obs!* För Log Analytics krävs även lagringstaggen.  Om Linux-agenter används **krävs även taggen GuestAndHybridManagement.** | Utgående | Inga | Ja |
| **AzureOpenDatasets** | Azure Open Datasets.<br/><br/>*Obs!* Den här taggen är beroende av **taggen AzureFrontDoor.Frontend** **och Storage.** | Utgående | Inga | Inga |
| **AzurePlatformDNS** | Den grundläggande infrastrukturen (standard) DNS-tjänsten.<br/><br>Du kan använda den här taggen för att inaktivera standard-DNS. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser Överväganden [för Azure-plattformen.](./network-security-groups-overview.md#azure-platform-considerations) Vi rekommenderar också att du utför testning innan du använder den här taggen. | Utgående | Inga | Inga |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), som är en grundläggande infrastrukturtjänst.<br/><br/>Du kan använda den här taggen för att inaktivera standard-IMDS. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [Överväganden för Azure-plattformen.](./network-security-groups-overview.md#azure-platform-considerations) Vi rekommenderar också att du utför testning innan du använder den här taggen. | Utgående | Inga | Inga |
| **AzurePlatformLKM** | Windows-licensiering eller nyckelhanteringstjänst.<br/><br/>Du kan använda den här taggen för att inaktivera standardvärdena för licensiering. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [Överväganden för Azure-plattformen.](./network-security-groups-overview.md#azure-platform-considerations)  Vi rekommenderar också att du utför testning innan du använder den här taggen. | Utgående | Inga | Inga |
| **AzureResourceManager** | Azure Resource Manager. | Utgående | Inga | Inga |
| **AzureSignalR** | Azure SignalR. | Utgående | Inga | Inga |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Obs!* Den här taggen är beroende av **taggarna AzureActiveDirectory,** **AzureKeyVault,** **EventHub,****GuestAndHybridManagement** och **Storage.** | Utgående | Inga | Inga |
| **AzureTrafficManager** | Azure Traffic Manager IP-adresser för avsökning.<br/><br/>Mer information om IP Traffic Manager avsökningsadresser finns i [vanliga Azure Traffic Manager vanliga frågor och svar.](../traffic-manager/traffic-manager-faqs.md) | Inkommande | Inga | Ja |  
| **BatchNodeManagement** | Hanteringstrafik för distributioner som är dedikerade Azure Batch. | Båda | Inga | Ja |
| **CognitiveServicesManagement** | Adressintervall för trafik för Azure Cognitive Services. | Båda | Inga | Inga |
| **DataFactory**  | Azure Data Factory | Båda | Inga | Inga |
| **DataFactoryManagement** | Hanteringstrafik för Azure Data Factory. | Utgående | Inga | Inga |
| **Dynamics365ForMarketingEmail** | Adressintervall för marknadsförings-e-posttjänsten för Dynamics 365. | Utgående | Ja | Inga |
| **EventHub** | Azure Event Hubs. | Utgående | Ja | Ja |
| **GatewayManager** | Hanteringstrafik för distributioner som är dedikerade till Azure VPN Gateway och Application Gateway. | Inkommande | Inga | Inga |
| **GuestAndHybridManagement** | Azure Automation och gästkonfiguration. | Utgående | Inga | Ja |
| **HDInsight** | Azure HDInsight. | Inkommande | Ja | Inga |
| **Internet** | IP-adressutrymmet som är utanför det virtuella nätverket och som kan nås av det offentliga Internet.<br/><br/>Adressintervallet innehåller det [Azure-ägda offentliga IP-adressutrymmet](https://www.microsoft.com/download/details.aspx?id=41653). | Båda | Inga | Inga |
| **LogicApps** | Logic Apps. | Båda | Inga | Inga |
| **LogicAppsManagement** | Hanteringstrafik för Logic Apps. | Inkommande | Inga | Inga |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Utgående | Inga | Inga |
| **MicrosoftContainerRegistry** | Containerregister för Microsoft-containeravbildningar. <br/><br/>*Obs!* Den här taggen är beroende av **taggen AzureFrontDoor.FirstParty.** | Utgående | Ja | Ja |
| **PowerBI** | PowerBi. *Obs! Den här taggen kan för närvarande inte konfigureras via Azure Portal.* | Båda | Inga | Inga|
| **PowerQueryOnline** | Power Query Online. | Båda | Inga | Inga |
| **ServiceBus** | Azure Service Bus trafik som använder Premium-tjänstnivån. | Utgående | Ja | Ja |
| **ServiceFabric** | Azure Service Fabric.<br/><br/>*Obs!* Den här taggen representerar Service Fabric tjänstslutpunkt för kontrollplan per region. Detta gör att kunder kan utföra hanteringsåtgärder för sina Service Fabric från sina virtuella nätverk (t.ex. slutpunkt. https:// westus.servicefabric.azure.com) | Båda | Inga | Inga |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL och Azure Synapse Analytics.<br/><br/>*Obs!* Den här taggen representerar tjänsten, men inte specifika instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure SQL Database, men inte en specifik SQL-databas eller -server. Den här taggen gäller inte för SQL Managed Instance. | Utgående | Ja | Ja |
| **SqlManagement** | Hanteringstrafik för SQL-dedikerade distributioner. | Båda | Inga | Ja |
| **Storage** | Azure Storage. <br/><br/>*Obs!* Den här taggen representerar tjänsten, men inte specifika instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure Storage, men inte ett specifikt Azure Storage-konto. | Utgående | Ja | Ja |
| **StorageSyncService** | Tjänst för synkronisering av lagring. | Båda | Inga | Inga |
| **WindowsVirtualDesktop** | Windows Virtual Desktop. | Båda | Inga | Ja |
| **VirtualNetwork** | Det virtuella nätverkets adressutrymme (alla IP-adressintervall som definierats för det virtuella nätverket), alla anslutna lokala adressutrymmen, [peerkopplade](virtual-network-peering-overview.md) virtuella nätverk, virtuella nätverk som är anslutna till en virtuell nätverksgateway, [](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)värdens virtuella [IP-adress](./network-security-groups-overview.md#azure-platform-considerations)och adressprefix som används på användardefinierade [vägar.](virtual-networks-udr-overview.md) Den här taggen kan också innehålla standardvägar. | Båda | Inga | Inga |

>[!NOTE]
>I den klassiska distributionsmodellen (innan Azure Resource Manager) stöds en delmängd av taggarna som anges i föregående tabell. De här taggarna stavas på olika sätt:
>
>| Klassisk stavning | Motsvarande Resource Manager tagg |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Tjänsttaggar för Azure-tjänster anger adressprefix från det specifika moln som används. Till exempel skiljer sig de underliggande  IP-intervallen som motsvarar SQL-taggvärdet i det offentliga Azure-molnet från de underliggande intervallen i Azure China-molnet.

> [!NOTE]
> Om du implementerar en [tjänstslutpunkt för virtuellt nätverk](virtual-network-service-endpoints-overview.md) för en viss tjänst, till exempel Azure Storage eller Azure SQL Database, lägger Azure till en [väg](virtual-networks-udr-overview.md#optional-default-routes) till ett undernät för virtuella nätverk för tjänsten. Adressprefixen i vägen är samma adressprefix, eller CIDR-intervall, som för motsvarande tjänsttagg.

## <a name="service-tags-on-premises"></a>Tjänsttaggar lokalt  
Du kan hämta den aktuella tjänsttagg- och intervallinformationen som ska ingå som en del av dina lokala brandväggskonfigurationer. Den här informationen är den aktuella listan med tidpunkter för de IP-intervall som motsvarar varje tjänsttagg. Du kan hämta informationen programmatiskt eller via en JSON-filhämtning, enligt beskrivningen i följande avsnitt.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Använda API:et för identifiering av tjänsttaggar (offentlig förhandsversion)
Du kan programmässigt hämta den aktuella listan över tjänsttaggar tillsammans med information om IP-adressintervall:

- [REST](/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
- [Azure CLI](/cli/azure/network#az_network_list_service_tags)

> [!NOTE]
> Det tar upp till 4 veckor för nya tjänsttaggdata att spridas i API-resultaten. Ändringsnumret i svarsmetadata ökas när detta sker. Det kan finnas tillfälliga skillnader i resultat när olika platsvärden anges. När du använder resultatet för att skapa NSG-regler bör du ange platsparamatern så att den matchar NSG:ns region. 

> [!NOTE]
> API-data representerar de taggar som kan användas med NSG-regler, en delmängd av taggarna som för närvarande finns i den nedladdningsbara JSON-filen. I den offentliga förhandsversionen garanterar vi inte att data förblir desamma från en uppdatering till nästa. 

### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Identifiera tjänsttaggar med hjälp av nedladdningsbara JSON-filer 
Du kan ladda ned JSON-filer som innehåller den aktuella listan över tjänsttaggar tillsammans med information om IP-adressintervall. De här listorna uppdateras och publiceras varje vecka. Platser för varje moln är:

- [Azure, offentlig](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure Kina](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)   

IP-adressintervallen i dessa filer är i CIDR-notation. 

> [!NOTE]
>En delmängd av den här informationen har publicerats i XML-filer för [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)och Azure [Germany](https://www.microsoft.com/download/details.aspx?id=54770). Dessa XML-nedladdningar kommer att bli inaktuella den 30 juni 2020 och kommer inte längre att vara tillgängliga efter det datumet. Du bör migrera till identifierings-API:et eller JSON-filnedladdningarna enligt beskrivningen i föregående avsnitt.

### <a name="tips"></a>Tips 
- Du kan identifiera uppdateringar från en publikation till nästa genom att se ökade *changeNumber-värden* i JSON-filen. Varje underavsnitt (till exempel **Storage.WestUS**) har ett eget *changeNumber* som ökas när ändringarna sker. Den översta nivån i filens *changeNumber* ökas när något av underavsnitten ändras.
- Exempel på hur du parsar information om tjänsttaggar (till exempel hämta alla adressintervall för Storage i Usa, västra) finns i [PowerShell-dokumentationen för](/powershell/module/az.network/Get-AzNetworkServiceTag) API:et för identifiering av tjänsttaggar.
- När nya IP-adresser läggs till i tjänsttaggar kommer de inte att användas i Azure under minst en vecka. Det ger dig tid att uppdatera alla system som kan behöva spåra DE IP-adresser som är associerade med tjänsttaggar.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur [du skapar en nätverkssäkerhetsgrupp](tutorial-filter-network-traffic.md).
