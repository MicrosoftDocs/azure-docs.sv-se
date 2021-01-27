---
title: Vad är en privat Azure-slutpunkt?
description: Läs om den privata Azure-slutpunkten
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: allensu
ms.openlocfilehash: 88d44f4f8cedbad604eb59cde91f4eed79918c0f
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806655"
---
# <a name="what-is-azure-private-endpoint"></a>Vad är en privat Azure-slutpunkt?

Azure Private Endpoint är ett nätverksgränssnitt som ger dig en privat och säker anslutning till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt VNet, vilket effektivt tar tjänsten till ditt VNet. Tjänsten kan vara en Azure-tjänst, till exempel Azure Storage, Azure Cosmos DB, SQL osv. eller en egen [privat länk-tjänst](private-link-service-overview.md).
  
## <a name="private-endpoint-properties"></a>Egenskaper för privat slut punkt 
 En privat slut punkt anger följande egenskaper: 


|Egenskap  |Beskrivning |
|---------|---------|
|Name    |    Ett unikt namn inom resurs gruppen.      |
|Undernät    |  Under nätet för att distribuera och allokera privata IP-adresser från ett virtuellt nätverk. För under näts krav, se avsnittet begränsningar i den här artikeln.         |
|Privat länk resurs    |   Den privata länk resursen för att ansluta med resurs-ID eller alias i listan över tillgängliga typer. Ett unikt nätverks-ID skapas för all trafik som skickas till den här resursen.       |
|Mål under resurs   |      Den under resurs som ska anslutas. Varje privat länk resurs typ har olika alternativ för att välja baserat på preferens.    |
|Metod för godkännande av anslutning    |  Automatisk eller manuell. Den privata slut punkten kan godkännas automatiskt baserat på Azures rollbaserad åtkomst kontroll (Azure RBAC). Om du försöker ansluta till en privat länk resurs utan Azure RBAC använder du den manuella metoden för att tillåta resursens ägare att godkänna anslutningen.        |
|Begär ande meddelande     |  Du kan ange ett meddelande för begärda anslutningar som ska godkännas manuellt. Det här meddelandet kan användas för att identifiera en speciell begäran.        |
|Anslutningsstatus   |   En skrivskyddad egenskap som anger om den privata slut punkten är aktiv. Endast privata slut punkter i ett godkänt tillstånd kan användas för att skicka trafik. Ytterligare tillstånd är tillgängliga: <br>-**Godkänd**: anslutning har automatiskt eller manuellt godkänts och är redo att användas.</br><br>-**Väntar**: anslutningen skapades manuellt och väntar på att godkännas av ägaren till den privata länk resursen.</br><br>-**Avvisad**: anslutningen avvisades av ägaren till den privata länk resursen.</br><br>-**Frånkopplad**: anslutningen har tagits bort av den privata länkens resurs ägare. Den privata slut punkten blir informativ och bör tas bort för rensning. </br>|

Här följer några viktiga uppgifter om privata slut punkter: 
- Med privat slut punkt kan du ansluta mellan konsumenter från samma VNet, regionalt peer-virtuella nätverk, globalt peered virtuella nätverk och lokalt med hjälp av [VPN](https://azure.microsoft.com/services/vpn-gateway/) eller [Express Route](https://azure.microsoft.com/services/expressroute/) och tjänster som drivs av en privat länk.
 
- Nätverks anslutningar kan endast initieras av klienter som ansluter till den privata slut punkten. tjänst leverantörer har ingen konfiguration för routning för att initiera anslutningar till tjänst konsumenter. Anslutningar kan bara upprättas i en enda riktning.

- När du skapar en privat slut punkt skapas även ett skrivskyddat nätverks gränssnitt för resursens livs cykel. Gränssnittet tilldelas dynamiskt privata IP-adresser från under nätet som mappar till den privata länk resursen. Värdet för den privata IP-adressen förblir oförändrat för hela livs cykeln för den privata slut punkten.
 
- Den privata slut punkten måste distribueras i samma region som det virtuella nätverket. 
 
- Den privata länk resursen kan distribueras i en annan region än det virtuella nätverket och den privata slut punkten.
 
- Flera privata slut punkter kan skapas med samma privata länk resurs. Den rekommenderade metoden är att använda en enda privat slut punkt för en viss privat länk resurs för att undvika dubbla poster eller konflikter i DNS-matchning för ett enda nätverk som använder en gemensam DNS-serverkonfiguration. 
 
- Flera privata slut punkter kan skapas i samma eller olika undernät i samma virtuella nätverk. Det finns gränser för antalet privata slut punkter som du kan skapa i en prenumeration. Mer information finns i [Azure-gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

- Prenumerationen från den privata länk resursen måste också registreras med Micosoft. Network Resource Provider. Mer information finns i [Azure Resource providers](../azure-resource-manager/management/resource-providers-and-types.md).

 
## <a name="private-link-resource"></a>Privat länk resurs 
En privat länk resurs är mål målet för en specifik privat slut punkt. Följande är en lista över tillgängliga privata länk resurs typer: 
 
|Resurs namn för privat länk  |Resurstyp   |Under resurser  |
|---------|---------|---------|
|**Privat länk tjänst** (din egen tjänst)   |  Microsoft. Network/privateLinkServices       | tomt |
|**Azure Automation** |  Microsoft. Automation/automationAccounts | Webhook, DSCAndHybridWorker |
|**Azure SQL Database** | Microsoft. SQL/Servers    |  SQL Server (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft. SQL/Servers    |  SQL Server (sqlServer)        | 
|**Azure Storage**  | Microsoft. Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)<BR> Tabell (tabell, table_secondary)<BR> Kö (kö, queue_secondary)<BR> Fil (fil, file_secondary)<BR> Webb (webb, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft. Storage/storageAccounts    |  BLOB (BLOB, blob_secondary)<BR> Data Lake Gen2 för fil system (DFS, dfs_secondary)       |
|**Azure Cosmos DB** | Microsoft. AzureCosmosDB/databaseAccounts    | SQL, MongoDB, Cassandra, Gremlin, Table|
|**Azure Database for PostgreSQL-enskild server** | Microsoft. DBforPostgreSQL/servers    | postgresqlServer |
|**Azure Database for MySQL** | Microsoft. DBforMySQL/servers    | mysqlServer |
|**Azure Database för MariaDB** | Microsoft. DBforMariaDB/servers    | mariadbServer |
|**Azure IoT Hub** | Microsoft. Devices/IotHubs    | iotHub |
|**Azure Key Vault** | Microsoft. nyckel valv/-valv    | valv |
|**Azure Kubernetes-tjänst – Kubernetes-API** | Microsoft. container service/managedClusters    | management |
|**Azure Search** | Microsoft. search/searchService| searchService|  
|**Azure Container Registry** | Microsoft. ContainerRegistry/register    | registry |
|**Azure App Configuration** | Microsoft. Appconfiguration/configurationStores    | configurationStores |
|**Azure Backup** | Microsoft. RecoveryServices/valv    | valv |
|**Azure Event Hub** | Microsoft. EventHub/Namespaces    | namnområde |
|**Azure Service Bus** | Microsoft. Service Bus/namnrymder | namnområde |
|**Azure Relay** | Microsoft. Relay/namnrymder | namnområde |
|**Azure Event Grid** | Microsoft. EventGrid/ämnen    | ämne |
|**Azure Event Grid** | Microsoft. EventGrid/Domains    | domän |
|**Azure App Service** | Microsoft. Web/Sites    | webbplatser |
|**Azure Machine Learning** | Microsoft. MachineLearningServices/arbets ytor    | amlworkspace |
|**SignalR** | Microsoft. SignalRService/SignalR    | signalR |
|**Azure Monitor** | Microsoft. Insights/privateLinkScopes    | azuremonitor |
|**Cognitive Services** | (Microsoft. CognitiveServices/konton    | konto |
|**Azure File Sync** | Microsoft. StorageSync/storageSyncServices    | Databasserver |
    
  

  
 
## <a name="network-security-of-private-endpoints"></a>Nätverks säkerhet för privata slut punkter 
När du använder privata slut punkter för Azure-tjänster skyddas trafik till en enskild privat länk resurs. Plattformen utför en åtkomst kontroll för att verifiera att nätverks anslutningar når enbart den angivna privata länk resursen. För att få åtkomst till ytterligare resurser inom samma Azure-tjänst krävs ytterligare privata slut punkter. 
 
Du kan helt låsa dina arbets belastningar från att komma åt offentliga slut punkter för att ansluta till en Azure-tjänst som stöds. Den här kontrollen ger ytterligare ett nätverks säkerhets lager till dina resurser genom att tillhandahålla ett inbyggt exfiltrering skydd som förhindrar åtkomst till andra resurser som finns på samma Azure-tjänst. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>Åtkomst till en privat länk resurs med hjälp av godkännande arbets flöde 
Du kan ansluta till en privat länk resurs med hjälp av följande metoder för godkännande av anslutning:
- **Automatiskt** godkänd när du äger eller har behörighet för den specifika privata länk resursen. Den nödvändiga behörigheten baseras på resurs typen privat länk i följande format: Microsoft. \<Provider> /<resource_type>/privateEndpointConnectionApproval/action
- **Manuell** begäran när du inte har behörighet som krävs och vill begära åtkomst. Ett arbets flöde för godkännande kommer att initieras. Den privata slutpunkten och efterföljande privata slutpunktsanslutning skapas i ett väntande tillstånd. Ägaren till den privata länkresursen ansvarar för att godkänna anslutningen. När den har godkänts är den privata slut punkten aktive rad för att skicka trafik normalt, som du ser i följande arbets flödes diagram för godkännande.  

![arbets flödes godkännande](media/private-endpoint-overview/private-link-paas-workflow.png)
 
Resurs ägaren för privata länkar kan utföra följande åtgärder över en privat slut punkts anslutning: 
- Granska alla anslutningar för privat slut punkt. 
- Godkänn en privat slut punkts anslutning. Motsvarande privata slut punkt kommer att aktive ras för att skicka trafik till den privata länk resursen. 
- Avvisa en privat slut punkts anslutning. Motsvarande privata slut punkt kommer att uppdateras för att avspegla statusen.
- Ta bort en privat slut punkts anslutning i vilket tillstånd som helst. Motsvarande privata slut punkt kommer att uppdateras med frånkopplat tillstånd för att avspegla åtgärden. ägaren av den privata slut punkten kan bara ta bort resursen just nu. 
 
> [!NOTE]
> Endast en privat slut punkt i ett godkänt tillstånd kan skicka trafik till en specifik privat länk resurs. 

### <a name="connecting-using-alias"></a>Ansluta med alias
Alias är en unik moniker som skapas när tjänst ägaren skapar en privat länk tjänst bakom en standard belastningsutjämnare. Tjänstens ägare kan dela det här aliaset med sina konsumenter offline. Konsumenter kan begära en anslutning till privata länk tjänster med antingen resurs-URI eller alias. Om du vill ansluta med alias måste du skapa en privat slut punkt med hjälp av metoden för godkännande av manuellt anslutning. För att använda en manuell metod för godkännande av anslutning ställer du in en manuell begär ande parameter till sant under skapa flöde för privat slut punkt. Titta på [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) och [AZ Network Private-Endpoint Create](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) för information. 

## <a name="dns-configuration"></a>DNS-konfiguration 
När du ansluter till en privat länk resurs med hjälp av ett fullständigt kvalificerat domän namn (FQDN) som en del av anslutnings strängen, är det viktigt att konfigurera dina DNS-inställningar på rätt sätt för att matcha den allokerade privata IP-adressen. Befintliga Azure-tjänster kanske redan har en DNS-konfiguration som ska användas vid anslutning via en offentlig slut punkt. Detta måste åsidosättas för att ansluta med hjälp av din privata slut punkt. 
 
Nätverks gränssnittet som är kopplat till den privata slut punkten innehåller den fullständiga uppsättningen information som krävs för att konfigurera din DNS, inklusive FQDN och privata IP-adresser som allokerats för en specifik privat länk resurs. 

Fullständig detaljerad information om metod tips och rekommendationer för att konfigurera DNS för privata slut punkter finns i [artikeln om DNS-konfiguration för privat slut punkt](private-endpoint-dns.md).



 
## <a name="limitations"></a>Begränsningar
 
Följande tabell innehåller en lista med kända begränsningar när du använder privata slut punkter: 


|Begränsning |Beskrivning |Åtgärd  |
|---------|---------|---------|
|Regler för nätverks säkerhets gruppen (NSG) och användardefinierade vägar gäller inte för privat slut punkt    |NSG stöds inte för privata slut punkter. Medan undernät som innehåller den privata slut punkten kan ha NSG kopplade till sig, gäller inte reglerna för trafik som bearbetas av den privata slut punkten. Du måste ha [aktiverat tvingande nätverks principer](disable-private-endpoint-network-policy.md) för att distribuera privata slut punkter i ett undernät. NSG tillämpas fortfarande på andra arbets belastningar som finns i samma undernät. Vägar i alla klient under nät kommer att använda ett/32-prefix, och om du ändrar standarduppförandet för routning krävs ett liknande UDR  | Styr trafiken genom att använda NSG regler för utgående trafik på käll klienter. Distribuera enskilda vägar med/32-prefix för att åsidosätta privata slut punkts vägar. NSG flödes loggar och övervaknings information för utgående anslutningar stöds fortfarande och kan användas        |


## <a name="next-steps"></a>Nästa steg
- [Skapa en privat slut punkt för SQL Database med hjälp av portalen](create-private-endpoint-portal.md)
- [Skapa en privat slut punkt för SQL Database med PowerShell](create-private-endpoint-powershell.md)
- [Skapa en privat slut punkt för SQL Database med CLI](create-private-endpoint-cli.md)
- [Skapa en privat slut punkt för lagrings kontot med hjälp av portalen](./tutorial-private-endpoint-storage-portal.md)
- [Skapa en privat slut punkt för Azure Cosmos-konto med hjälp av portalen](../cosmos-db/how-to-configure-private-endpoints.md)
- [Skapa en egen privat länk-tjänst med hjälp av Azure PowerShell](create-private-link-service-powershell.md)
- [Skapa en egen privat länk för Azure Database for PostgreSQL-en server med hjälp av portalen](../postgresql/howto-configure-privatelink-portal.md)
- [Skapa en egen privat länk för Azure Database for PostgreSQL-enskild server med CLI](../postgresql/howto-configure-privatelink-cli.md)
- [Skapa en egen privat länk för Azure Database for MySQL med hjälp av portalen](../mysql/howto-configure-privatelink-portal.md)
- [Skapa en egen privat länk för Azure Database for MySQL med CLI](../mysql/howto-configure-privatelink-cli.md)
- [Skapa en egen privat länk för Azure Database for MariaDB med hjälp av portalen](../mariadb/howto-configure-privatelink-portal.md)
- [Skapa en egen privat länk för Azure Database for MariaDB med CLI](../mariadb/howto-configure-privatelink-cli.md)
- [Skapa en egen privat länk för Azure Key Vault med hjälp av portalen och CLI](../key-vault/general/private-link-service.md)
