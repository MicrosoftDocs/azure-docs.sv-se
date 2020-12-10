---
title: Översikt över Azure Service-Taggar
titlesuffix: Azure Virtual Network
description: Lär dig om service märken. Service märken hjälper till att minimera komplexiteten vid skapande av säkerhets regler.
services: virtual-network
documentationcenter: na
author: allegradomel
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: ece6dfb5277e7ca363548c8d90021835a96a61ba
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96937363"
---
# <a name="virtual-network-service-tags"></a>Tjänst taggar för virtuellt nätverk
<a name="network-service-tags"></a>

En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras, vilket minimerar komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler.

Du kan använda service märken för att definiera nätverks åtkomst kontroller för [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) eller [Azure-brandväggen](https://docs.microsoft.com/azure/firewall/service-tags). Använd tjänst Taggar i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange namnet på service tag-namnet, till exempel **API Management**, i lämpligt *käll* -eller *mål* fält för en regel, kan du tillåta eller neka trafiken för motsvarande tjänst.

Du kan använda service märken för att uppnå nätverks isolering och skydda dina Azure-resurser från det allmänna Internet samtidigt som du får åtkomst till Azure-tjänster som har offentliga slut punkter. Skapa regler för inkommande/utgående nätverks säkerhets grupper för att neka trafik till/från **Internet** och tillåta trafik till/från **AzureCloud** eller andra [tillgängliga Service märken](#available-service-tags) för vissa Azure-tjänster.

![Nätverks isolering av Azure-tjänster med hjälp av service märken](./media/service-tags-overview/service_tags.png)

## <a name="available-service-tags"></a>Tillgängliga tjänst etiketter
Följande tabell innehåller alla tjänst taggar som är tillgängliga för användning i regler för [nätverks säkerhets grupper](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

Kolumnerna visar om taggen:

- Är lämpligt för regler som avser inkommande eller utgående trafik.
- Stöder [regional](https://azure.microsoft.com/regions) omfattning.
- Kan användas i [Azure brand Väggs](https://docs.microsoft.com/azure/firewall/service-tags) regler.

Som standard återspeglar service märken intervallen för hela molnet. Vissa service märken ger också mer detaljerad kontroll genom att begränsa motsvarande IP-intervall till en angiven region. Till exempel representerar service tag- **lagringen** Azure Storage för hela molnet, men **Storage. väst** begränsar intervallet till endast lagrings-IP-adressintervall från regionen Väst. Följande tabell visar om varje service tag stöder det regionala omfånget.  

| Tagga | Syfte | Kan använda inkommande eller utgående? | Kan regionala? | Kan använda med Azure-brandväggen? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ActionGroup** | Åtgärds grupp. | Inbound (Inkommande) | Inga | Inga |
| **ApiManagement** | Hanterings trafik för Azure API Management dedikerade distributioner. <br/><br/>*Obs:* Den här taggen representerar Azure API Management-tjänstens slut punkt för kontroll planet per region. Detta gör det möjligt för kunderna att utföra hanterings åtgärder för API: er, åtgärder, principer, NamedValues som kon figurer ATS på API Management tjänsten.  | Inbound (Inkommande) | Ja | Ja |
| **ApplicationInsightsAvailability** | Application Insights tillgänglighet. | Inbound (Inkommande) | Inga | Inga |
| **AppConfiguration** | App-konfiguration. | Outbound (Utgående) | Inga | Inga |
| **AppService**    | Azure App Service. Den här taggen rekommenderas för utgående säkerhets regler till webbappar och Function-appar.  | Outbound (Utgående) | Ja | Ja |
| **AppServiceManagement** | Hanterings trafik för distributioner avsedda för App Service-miljön. | Båda | Inga | Ja |
| **AzureActiveDirectory** | Azure Active Directory. | Outbound (Utgående) | Inga | Ja |
| **AzureActiveDirectoryDomainServices** | Hanterings trafik för distributioner avsedda för Azure Active Directory Domain Services. | Båda | Inga | Ja |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | Outbound (Utgående) | Inga | Inga |
| **AzureBackup** |Azure Backup.<br/><br/>*Obs:* Den här taggen har ett beroende på **lagrings** -och **AzureActiveDirectory** -taggarna. | Outbound (Utgående) | Inga | Ja |
| **AzureBotService** | Azure Bot Service. | Outbound (Utgående) | Inga | Inga |
| **AzureCloud** | Alla [offentliga IP-adresser för data Center](https://www.microsoft.com/download/details.aspx?id=56519). | Outbound (Utgående) | Ja | Ja |
| **AzureCognitiveSearch** | Azure-Kognitiv sökning. <br/><br/>Den här taggen eller de IP-adresser som omfattas av den här taggen kan användas för att ge indexerare säker åtkomst till data källor. Mer information finns i [anslutnings dokumentationen för indexeraren](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) . <br/><br/> *Obs!* IP-adressen för Sök tjänsten ingår inte i listan över IP-intervall för den här tjänst tag gen och **måste också läggas** till i IP-brandväggen för data källor. | Inbound (Inkommande) | Inga | Inga |
| **AzureConnectors** | Azure Logic Apps anslutningar för avsökning/backend-anslutningar. | Inbound (Inkommande) | Ja | Ja |
| **AzureContainerRegistry** | Azure Container Registry. | Outbound (Utgående) | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos DB. | Outbound (Utgående) | Ja | Ja |
| **AzureDatabricks** | Azure Databricks. | Båda | Inga | Inga |
| **AzureDataExplorerManagement** | Hantering av Azure-Datautforskaren. | Inbound (Inkommande) | Inga | Inga |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Outbound (Utgående) | Inga | Ja |
| **AzureDevSpaces** | Azure dev-utrymmen. | Outbound (Utgående) | Inga | Inga |
| **AzureDevOps** | Azure dev-Ops.<br/><br/>*Obs: den här taggen kan för närvarande inte konfigureras via Azure Portal*| Inbound (Inkommande) | Inga | Ja |
| **AzureDigitalTwins** | Azure Digital-dubbla.<br/><br/>*Obs:* Den här taggen eller de IP-adresser som omfattas av den här taggen kan användas för att begränsa åtkomsten till slut punkter som kon figurer ATS för händelse vägar. *Den här taggen kan för närvarande inte konfigureras via Azure Portal* | Inbound (Inkommande) | Inga | Ja |
| **AzureEventGrid** | Azure Event Grid. | Båda | Inga | Inga |
| **AzureFrontDoor. frontend** <br/> **AzureFrontDoor. backend** <br/> **AzureFrontDoor.FirstParty**  | Azure-front dörr. | Båda | Inga | Inga |
| **AzureInformationProtection** | Azure Information Protection.<br/><br/>*Obs:* Den här taggen har ett beroende av taggarna **AzureActiveDirectory**, **AzureFrontDoor. frontend** och **AzureFrontDoor. FirstParty** . | Outbound (Utgående) | Inga | Inga |
| **AzureIoTHub** | Azure-IoT Hub. | Outbound (Utgående) | Inga | Inga |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Obs:* Den här taggen har ett beroende av **AzureActiveDirectory** -taggen. | Outbound (Utgående) | Ja | Ja |
| **AzureLoadBalancer** | Azure Infrastructure belastningsutjämnare. Taggen översätts till den [virtuella IP-adressen för värden](security-overview.md#azure-platform-considerations) (168.63.129.16) där Azures hälso avsökningen kommer. Detta omfattar bara avsöknings trafik, inte verklig trafik till din server dels resurs. Om du inte använder Azure Load Balancer kan du åsidosätta den här regeln. | Båda | Inga | Inga |
| **AzureMachineLearning** | Azure Machine Learning. | Båda | Inga | Ja |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon och anpassade mått (GB-slutpunkter).<br/><br/>*Obs:* För Log Analytics har den här taggen ett beroende på **lagrings** tag gen. | Outbound (Utgående) | Inga | Ja |
| **AzureOpenDatasets** | Azure Open-datauppsättningar.<br/><br/>*Obs:* Den här taggen har ett beroende på taggen **AzureFrontDoor. frontend** och **Storage** . | Outbound (Utgående) | Inga | Inga |
| **AzurePlatformDNS** | Standard-DNS-tjänsten (Basic Infrastructure).<br/><br>Du kan använda den här taggen för att inaktivera standard-DNS. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [överväganden för Azure-plattformen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Vi rekommenderar också att du utför testningen innan du använder den här taggen. | Outbound (Utgående) | Inga | Inga |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), som är en grundläggande infrastruktur tjänst.<br/><br/>Du kan använda den här taggen för att inaktivera standard IMDS. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [överväganden för Azure-plattformen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). Vi rekommenderar också att du utför testningen innan du använder den här taggen. | Outbound (Utgående) | Inga | Inga |
| **AzurePlatformLKM** | Windows-licensiering eller nyckel hanterings tjänst.<br/><br/>Du kan använda den här taggen för att inaktivera standardvärdena för licensiering. Var försiktig när du använder den här taggen. Vi rekommenderar att du läser [överväganden för Azure-plattformen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations).  Vi rekommenderar också att du utför testningen innan du använder den här taggen. | Outbound (Utgående) | Inga | Inga |
| **AzureResourceManager** | Azure Resource Manager. | Outbound (Utgående) | Inga | Inga |
| **AzureSignalR** | Azure-SignalR. | Outbound (Utgående) | Inga | Inga |
| **AzureSiteRecovery** | Azure Site Recovery.<br/><br/>*Obs:* Den här taggen har ett beroende av taggarna **AzureActiveDirectory**, **AzureKeyVault**, **EventHub**,**GuestAndHybridManagement** och **Storage** . | Outbound (Utgående) | Inga | Inga |
| **AzureTrafficManager** | IP-adresser för Azure Traffic Manager-avsökning.<br/><br/>Mer information om Traffic Manager avsöknings-IP-adresser finns i [vanliga frågor och svar om Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Inbound (Inkommande) | Inga | Ja |  
| **BatchNodeManagement** | Hanterings trafik för distributioner avsedda för Azure Batch. | Båda | Inga | Ja |
| **CognitiveServicesManagement** | Adress intervall för trafik för Azure-Cognitive Services. | Båda | Inga | Inga |
| **DataFactory**  | Azure Data Factory | Båda | Inga | Inga |
| **DataFactoryManagement** | Hanterings trafik för Azure Data Factory. | Outbound (Utgående) | Inga | Inga |
| **Dynamics365ForMarketingEmail** | Adress intervallen för Marketing e-posttjänsten för Dynamics 365. | Outbound (Utgående) | Ja | Inga |
| **EventHub** | Azure-Event Hubs. | Outbound (Utgående) | Ja | Ja |
| **GatewayManager** | Hanterings trafik för distributioner avsedda för Azure VPN Gateway och Application Gateway. | Inbound (Inkommande) | Inga | Inga |
| **GuestAndHybridManagement** | Azure Automation-och gäst konfiguration. | Outbound (Utgående) | Inga | Ja |
| **HDInsight** | Azure HDInsight. | Inbound (Inkommande) | Ja | Inga |
| **Internet** | IP-adressutrymmet som ligger utanför det virtuella nätverket och som kan användas av det offentliga Internet.<br/><br/>Adress intervallet omfattar det [offentliga IP-adressutrymmet som ägs av Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Båda | Inga | Inga |
| **LogicApps** | Logic Apps. | Båda | Inga | Inga |
| **LogicAppsManagement** | Hanterings trafik för Logic Apps. | Inbound (Inkommande) | Inga | Inga |
| **MicrosoftCloudAppSecurity** | Microsoft Cloud App Security. | Outbound (Utgående) | Inga | Inga |
| **MicrosoftContainerRegistry** | Container Registry för Microsoft container-avbildningar. <br/><br/>*Obs:* Den här taggen har ett beroende av taggen **AzureFrontDoor. FirstParty** . | Outbound (Utgående) | Ja | Ja |
| **PowerQueryOnline** | Power Query online. | Båda | Inga | Inga |
| **ServiceBus** | Azure Service Bus trafik som använder Premium-tjänstens nivå. | Outbound (Utgående) | Ja | Ja |
| **ServiceFabric** | Azure-Service Fabric.<br/><br/>*Obs:* Den här taggen representerar Service Fabric tjänstens slut punkt för kontroll planet per region. Detta gör det möjligt för kunderna att utföra hanterings åtgärder för sina Service Fabric-kluster från sitt VNET (t. ex. slut punkt. https://westus.servicefabric.azure.com) | Båda | Inga | Inga |
| **SQL** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL och Azure Synapse Analytics.<br/><br/>*Obs:* Den här taggen representerar tjänsten, men inte vissa instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure SQL Database, men inte en specifik SQL-databas eller -server. Den här taggen gäller inte för SQL-hanterad instans. | Outbound (Utgående) | Ja | Ja |
| **SqlManagement** | Hanterings trafik för SQL-dedikerade distributioner. | Båda | Inga | Ja |
| **Storage** | Azure Storage. <br/><br/>*Obs:* Den här taggen representerar tjänsten, men inte vissa instanser av tjänsten. Taggen kan till exempel representera tjänsten Azure Storage, men inte ett specifikt Azure Storage-konto. | Outbound (Utgående) | Ja | Ja |
| **StorageSyncService** | Tjänsten för synkronisering av lagring. | Båda | Inga | Inga |
| **WindowsVirtualDesktop** | Virtuellt Windows-skrivbord. | Båda | Inga | Ja |
| **VirtualNetwork** | Det virtuella nätverkets adress utrymme (alla IP-adressintervall som definierats för det virtuella nätverket), alla anslutna lokala adress utrymmen, [peer](virtual-network-peering-overview.md) -kopplade virtuella nätverk, virtuella nätverk som är anslutna till en [virtuell nätverksgateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), den [virtuella IP-adressen för värden](security-overview.md#azure-platform-considerations)och de adressprefix som används för [användardefinierade vägar](virtual-networks-udr-overview.md). Den här taggen kan också innehålla standard vägar. | Båda | Inga | Inga |

>[!NOTE]
>I den klassiska distributions modellen (före Azure Resource Manager) stöds en delmängd av de taggar som anges i föregående tabell. Dessa taggar har stavats på olika sätt:
>
>| Klassisk stavning | Motsvarande Resource Manager-tagg |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Service taggar för Azure-tjänster anger de adressprefix från det angivna molnet som används. Till exempel kommer de underliggande IP-intervall som motsvarar **SQL** -taggnamnet i det offentliga Azure-molnet att skilja sig från de underliggande områdena i Azure Kina-molnet.

> [!NOTE]
> Om du implementerar en [tjänstslutpunkt för virtuellt nätverk](virtual-network-service-endpoints-overview.md) för en viss tjänst, till exempel Azure Storage eller Azure SQL Database, lägger Azure till en [väg](virtual-networks-udr-overview.md#optional-default-routes) till ett undernät för virtuella nätverk för tjänsten. Adressprefix i vägen är samma adressprefix eller CIDR-intervall, som de för motsvarande service tag.

## <a name="service-tags-on-premises"></a>Lokala tjänst Taggar  
Du kan hämta aktuell service tag-information och intervall information som ska ingå som en del av dina lokala brand Väggs konfigurationer. Den här informationen är den aktuella tidpunkts listan för de IP-intervall som motsvarar varje service tag. Du kan hämta informationen via programmering eller via en nedladdning av en JSON-fil, enligt beskrivningen i följande avsnitt.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Använd API för identifiering av service tag (offentlig för hands version)
Du kan hämta den aktuella listan över service märken program mässigt tillsammans med information om IP-adressintervall:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Även om det finns i en offentlig för hands version kan identifierings-API: t returnera information som är mindre aktuell än information som returneras av JSON-nedladdningar. (Se nästa avsnitt.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Identifiera service märken med hjälp av nedladdnings bara JSON-filer 
Du kan ladda ned JSON-filer som innehåller den aktuella listan över tjänst Taggar tillsammans med information om IP-adressintervall. Listorna uppdateras och publiceras varje vecka. Platser för varje moln är:

- [Offentlig Azure-](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure Kina](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)   

IP-adressintervallet i de här filerna finns i CIDR-format. 

> [!NOTE]
>En delmängd av den här informationen har publicerats i XML-filer för [Azure offentlig](https://www.microsoft.com/download/details.aspx?id=41653), [Azure Kina](https://www.microsoft.com/download/details.aspx?id=42064)och [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770). Dessa XML-nedladdningar kommer att bli föråldrade den 30 juni 2020 och kommer inte längre att vara tillgängliga efter det datumet. Du bör migrera till med hjälp av identifierings-API: t eller JSON-filens nedladdningar enligt beskrivningen i föregående avsnitt.

### <a name="tips"></a>Tips 
- Du kan identifiera uppdateringar från en publikation till nästa genom att notera ökade *changeNumber* -värden i JSON-filen. Varje underavsnitt (till exempel **lagring. väst**) har en egen *changeNumber* som ökar när ändringar sker. Den översta nivån i filens *changeNumber* ökar när något av underavsnitten ändras.
- Exempel på hur du kan parsa service tag-informationen (till exempel hämta alla adress intervall för lagring i väster) finns i dokumentationen för [service tag Discovery API PowerShell](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [skapar en nätverks säkerhets grupp](tutorial-filter-network-traffic.md).
