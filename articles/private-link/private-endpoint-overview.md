---
title: Vad är Azure private Endpoint?
description: Läs mer om privat Azure-slutpunkt
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: a12f0c2e8ff5987a14b56ef12d49b8350cc1b3aa
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107501781"
---
# <a name="what-is-azure-private-endpoint"></a>Vad är en privat Azure-slutpunkt?

Azure Private Endpoint är ett nätverksgränssnitt som ger dig en privat och säker anslutning till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt VNet, vilket effektivt tar tjänsten till ditt VNet. Tjänsten kan vara en Azure-tjänst som Azure Storage, Azure Cosmos DB, SQL osv. eller din egen [Private Link Service](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Egenskaper för privat slutpunkt 
 En privat slutpunkt anger följande egenskaper: 


|Egenskap  |Beskrivning |
|---------|---------|
|Name    |    Ett unikt namn i resursgruppen.      |
|Undernät    |  Undernätet som ska distribueras och allokera privata IP-adresser från ett virtuellt nätverk. Information om krav för undernät finns i avsnittet Begränsningar i den här artikeln.         |
|Private Link resurs    |   Den privata länkresursen för att ansluta med hjälp av resurs-ID eller alias från listan över tillgängliga typer. En unik nätverksidentifierare genereras för all trafik som skickas till den här resursen.       |
|Målunderkälla   |      Underkällan för att ansluta. Varje privat länkresurstyp har olika alternativ att välja baserat på inställningar.    |
|Metod för anslutningsgodkännande    |  Automatisk eller manuell. Baserat på azure-behörigheter för rollbaserad åtkomstkontroll (Azure RBAC) kan din privata slutpunkt godkännas automatiskt. Om du försöker ansluta till en privat länkresurs utan Azure RBAC använder du den manuella metoden för att tillåta ägaren av resursen att godkänna anslutningen.        |
|Meddelande om begäran     |  Du kan ange ett meddelande för begärda anslutningar som ska godkännas manuellt. Det här meddelandet kan användas för att identifiera en specifik begäran.        |
|Anslutningsstatus   |   En skrivskyddade egenskap som anger om den privata slutpunkten är aktiv. Endast privata slutpunkter i ett godkänt tillstånd kan användas för att skicka trafik. Ytterligare tillgängliga tillstånd: <br>-**Godkänd:** Anslutningen godkändes automatiskt eller manuellt och är redo att användas.</br><br>-**Väntar:** Anslutningen har skapats manuellt och väntar på godkännande av ägaren till den privata länkresursen.</br><br>-**Nekad:** Anslutningen avvisades av ägaren till den privata länkresursen.</br><br>-**Frånkopplad:** Anslutningen har tagits bort av den privata länkens resursägare. Den privata slutpunkten blir informativ och bör tas bort för rensning. </br>|

Här är några viktiga detaljer om privata slutpunkter: 
- Privat slutpunkt möjliggör anslutning mellan konsumenter från samma virtuella nätverk, regionalt peerkopplade virtuella nätverk, globalt peerkopplade virtuella nätverk och lokalt med [VPN](https://azure.microsoft.com/services/vpn-gateway/) eller [Express Route](https://azure.microsoft.com/services/expressroute/) och tjänster som drivs av Private Link.
 
- Nätverksanslutningar kan bara initieras av klienter som ansluter till den privata slutpunkten. Tjänstleverantörer har ingen routningskonfiguration för att initiera anslutningar till tjänstkonsumenter. Anslutningar kan bara upprättas i en enda riktning.

- När du skapar en privat slutpunkt skapas även ett skrivskyddad nätverksgränssnitt för resursens livscykel. Gränssnittet tilldelas dynamiskt privata IP-adresser från undernätet som mappar till den privata länkresursen. Värdet för den privata IP-adressen förblir oförändrat under hela livscykeln för den privata slutpunkten.
 
- Den privata slutpunkten måste distribueras i samma region och prenumeration som det virtuella nätverket. 
 
- Den privata länkresursen kan distribueras i en annan region än det virtuella nätverket och den privata slutpunkten.
 
- Flera privata slutpunkter kan skapas med samma privata länkresurs. För ett enda nätverk som använder en gemensam DNS-serverkonfiguration rekommenderar vi att du använder en enda privat slutpunkt för en viss privat länkresurs för att undvika dubblettposter eller konflikter i DNS-upplösning. 
 
- Flera privata slutpunkter kan skapas på samma eller olika undernät i samma virtuella nätverk. Det finns gränser för hur många privata slutpunkter du kan skapa i en prenumeration. Mer information finns i [Azure-gränser.](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)

- Prenumerationen från den privata länkresursen måste också vara registrerad med resursprovidern Micosoft.Network. Mer information finns i [Azure-resursproviders.](../azure-resource-manager/management/resource-providers-and-types.md)

 
## <a name="private-link-resource"></a>Privat länkresurs 
En privat länkresurs är målmålet för en viss privat slutpunkt. Följande är en lista över tillgängliga resurstyper för privata länkar: 
 
|Resursnamn för privat länk  |Resurstyp   |Underkällor  |
|---------|---------|---------|
|**Private Link Service** (din egen tjänst)   |  Microsoft.Network/privateLinkServices       | tomt |
|**Azure Automation** |  Microsoft.Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Azure SQL Database** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  Sql Server (sqlServer)        | 
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Tabell (tabell, table_secondary)<BR> Kö (kö, queue_secondary)<BR> Fil (fil, file_secondary)<BR> Webb (webb, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> Data Lake File System Gen2 (dfs, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft.AzureCosmosDB/databaseAccounts    | Sql, MongoDB, Cassandra, Gremlin, Table|
|**Azure Database for PostgreSQL – enskild server** | Microsoft.DBforPostgreSQL/servers    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | mysqlServer |
|**Azure Database för MariaDB** | Microsoft.DBforServeraDB/servers    | mariadbServer |
|**Azure IoT Hub** | Microsoft.Devices/IotHubs    | iotHub |
|**Azure Key Vault** | Microsoft.KeyVault/vaults    | valv |
|**Azure Kubernetes Service – Kubernetes API** | Microsoft.ContainerService/managedClusters    | management |
|**Azure Search** | Microsoft.Search/searchService| searchService|  
|**Azure Container Registry** | Microsoft.ContainerRegistry/registries    | registry |
|**Azure App Configuration** | Microsoft.Appconfiguration/configurationStores    | configurationStores |
|**Azure Backup** | Microsoft.RecoveryServices/vaults    | valv |
|**Azure Event Hub** | Microsoft.EventHub/namespaces    | namnområde |
|**Azure Service Bus** | Microsoft.ServiceBus/namespaces | namnområde |
|**Azure Relay** | Microsoft.Relay/namnområden | namnområde |
|**Azure Event Grid** | Microsoft.EventGrid/topics    | ämne |
|**Azure Event Grid** | Microsoft.EventGrid/domains    | domän |
|**Azure App Service** | Microsoft.Web/sites    | webbplatser |
|**Azure App Service platser** | Microsoft.Web/sites    | platser –`<slot name>` |
|**Azure Machine Learning** | Microsoft.MachineLearningServices/workspaces    | amlworkspace |
|**SignalR** | Microsoft.SignalRService/SignalR    | signalR |
|**Azure Monitor** | Microsoft.Insights/privateLinkScopes    | azuremonitor |
|**Cognitive Services** | (Microsoft.CognitiveServices/accounts    | konto |
|**Azure File Sync** | Microsoft.StorageSync/storageSyncServices    | Afs |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Nätverkssäkerhet för privata slutpunkter 
När du använder privata slutpunkter för Azure-tjänster skyddas trafiken till en specifik privat länkresurs. Plattformen utför en åtkomstkontroll för att verifiera att nätverksanslutningarna endast når den angivna privata länkresursen. För att få åtkomst till ytterligare resurser i samma Azure-tjänst krävs ytterligare privata slutpunkter. 
 
Du kan helt låsa dina arbetsbelastningar från att komma åt offentliga slutpunkter för att ansluta till en Azure-tjänst som stöds. Den här kontrollen ger ytterligare ett nätverkssäkerhetslager för dina resurser genom att tillhandahålla ett inbyggt exfiltreringsskydd som förhindrar åtkomst till andra resurser som finns i samma Azure-tjänst. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Åtkomst till en privat länkresurs med hjälp av godkännandearbetsflöde 
Du kan ansluta till en privat länkresurs med hjälp av följande metoder för anslutningsgodkännande:
- **Godkänns** automatiskt när du äger eller har behörighet till den specifika privata länkresursen. Behörigheten som krävs baseras på resurstypen private link i följande format: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- **Manuell** begäran när du inte har den behörighet som krävs och vill begära åtkomst. Ett arbetsflöde för godkännande initieras. Den privata slutpunkten och efterföljande privata slutpunktsanslutning skapas i ett väntande tillstånd. Ägaren till den privata länkresursen ansvarar för att godkänna anslutningen. När den privata slutpunkten har godkänts kan den skicka trafik normalt, som du ser i följande arbetsflödesdiagram för godkännande.  

![arbetsflödesgodkännande](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Ägaren av den privata länkresursen kan utföra följande åtgärder över en privat slutpunktsanslutning: 
- Granska alla privata slutpunktsanslutningar. 
- Godkänn en privat slutpunktsanslutning. Motsvarande privata slutpunkt aktiveras för att skicka trafik till den privata länkresursen. 
- Avvisa en privat slutpunktsanslutning. Motsvarande privata slutpunkt uppdateras för att återspegla statusen.
- Ta bort en privat slutpunktsanslutning i alla tillstånd. Motsvarande privata slutpunkt uppdateras med ett frånkopplat tillstånd för att återspegla åtgärden. Ägaren av den privata slutpunkten kan bara ta bort resursen i det här läget. 
 
> [!NOTE]
> Endast en privat slutpunkt i ett godkänt tillstånd kan skicka trafik till en viss privat länkresurs. 

### <a name="connecting-using-alias"></a>Ansluta med alias
Alias är en unik moniker som genereras när tjänstägaren skapar den privata länktjänsten bakom en standardlastbalanserare. Tjänstägaren kan dela aliaset med sina konsumenter offline. Konsumenter kan begära en anslutning till privat länktjänst med hjälp av antingen resurs-URI:t eller aliaset. Om du vill ansluta med alias måste du skapa en privat slutpunkt med hjälp av metoden för manuellt anslutningsgodkännande. Om du vill använda metoden för manuellt anslutningsgodkännande anger du parametern för manuell begäran till true under flödet för att skapa privat slutpunkt. Mer information [finns i New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint) [och az network private-endpoint create.](/cli/azure/network/private-endpoint#az-network-private-endpoint-create) 

## <a name="dns-configuration"></a>DNS-konfiguration 
När du ansluter till en privat länkresurs med ett fullständigt kvalificerat domännamn (FQDN) som en del av anslutningssträngen är det viktigt att konfigurera DNS-inställningarna korrekt så att de matchar den allokerade privata IP-adressen. Befintliga Azure-tjänster kanske redan har en DNS-konfiguration som ska användas vid anslutning via en offentlig slutpunkt. Detta måste åsidosättas för att ansluta med din privata slutpunkt. 
 
Nätverksgränssnittet som är associerat med den privata slutpunkten innehåller den fullständiga uppsättningen information som krävs för att konfigurera din DNS, inklusive FQDN och privata IP-adresser som allokerats för en viss privat länkresurs. 

Fullständig detaljerad information om metodtips och rekommendationer för att konfigurera DNS för privata slutpunkter finns i artikeln [DNS-konfiguration för privat slutpunkt.](private-endpoint-dns.md)



 
## <a name="limitations"></a>Begränsningar
 
Följande tabell innehåller en lista över kända begränsningar när du använder privata slutpunkter: 


|Begränsning |Beskrivning |Åtgärd  |
|---------|---------|---------|
|Regler för nätverkssäkerhetsgrupp (NSG) och användardefinierade vägar gäller inte för privat slutpunkt    |NSG stöds inte på privata slutpunkter. Även om undernät som innehåller den privata slutpunkten kan ha en NSG kopplad till sig, kommer reglerna inte att gälla för trafik som bearbetas av den privata slutpunkten. Du måste ha [tvingande nätverksprinciper inaktiverade](disable-private-endpoint-network-policy.md) för att distribuera privata slutpunkter i ett undernät. NSG tillämpas fortfarande på andra arbetsbelastningar som finns i samma undernät. Vägar i alla klientundernät använder ett /32-prefix. Om du ändrar standardbeteendet för routning krävs en liknande UDR  | Kontrollera trafiken med hjälp av NSG-regler för utgående trafik på källklienter. Distribuera enskilda vägar med /32-prefix för att åsidosätta privata slutpunktsvägar. NSG-flödesloggar och övervakningsinformation för utgående anslutningar stöds fortfarande och kan användas        |


## <a name="next-steps"></a>Nästa steg
- [Skapa en privat slutpunkt för SQL Database med hjälp av portalen](create-private-endpoint-portal.md)
- [Skapa en privat slutpunkt för SQL Database powershell](create-private-endpoint-powershell.md)
- [Skapa en privat slutpunkt för SQL Database med CLI](create-private-endpoint-cli.md)
- [Skapa en privat slutpunkt för lagringskonto med hjälp av portalen](./tutorial-private-endpoint-storage-portal.md)
- [Skapa en privat slutpunkt för Azure Cosmos-konto med hjälp av portalen](../cosmos-db/how-to-configure-private-endpoints.md)
- [Skapa en egen Private Link tjänst med Azure PowerShell](create-private-link-service-powershell.md)
- [Skapa en egen Private Link för Azure Database for PostgreSQL – enskild server med hjälp av portalen](../postgresql/howto-configure-privatelink-portal.md)
- [Skapa en egen Private Link för Azure Database for PostgreSQL – enskild server med CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Skapa egna Private Link för Azure Database for MySQL med hjälp av portalen](../mysql/howto-configure-privatelink-portal.md)
- [Skapa en egen Private Link för Azure Database for MySQL med CLI](../mysql/howto-configure-privatelink-cli.md)
- [Skapa en egen Private Link för Azure Database for MariaDB med hjälp av portalen](../mariadb/howto-configure-privatelink-portal.md)
- [Skapa en egen Private Link för Azure Database for MariaDB med CLI](../mariadb/howto-configure-privatelink-cli.md)
- [Skapa en egen Private Link för Azure Key Vault med hjälp av portalen och CLI](../key-vault/general/private-link-service.md)
