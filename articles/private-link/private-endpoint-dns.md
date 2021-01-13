---
title: DNS-konfiguration för privat slutpunkt i Azure
description: Lär dig om DNS-konfiguration för privat slut punkt i Azure
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: allensu
ms.openlocfilehash: 859768345c2b88e38e09d897391ac8a3501fd901
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134083"
---
# <a name="azure-private-endpoint-dns-configuration"></a>DNS-konfiguration för privat slutpunkt i Azure


När du ansluter till en privat länk resurs med ett fullständigt kvalificerat domän namn (FQDN) som en del av anslutnings strängen, är det viktigt att konfigurera dina DNS-inställningar på rätt sätt för att matcha den allokerade privata IP-adressen. Befintliga Microsoft Azure-tjänster kanske redan har en DNS-konfiguration som ska användas vid anslutning via en offentlig slut punkt. Den här konfigurationen måste åsidosättas för att ansluta med hjälp av din privata slut punkt. 
 
Nätverks gränssnittet som är kopplat till den privata slut punkten innehåller den fullständiga uppsättningen information som krävs för att konfigurera din DNS, inklusive FQDN och privata IP-adresser som tilldelats för en viss privat länk resurs. 
 
Du kan använda följande alternativ för att konfigurera dina DNS-inställningar för privata slut punkter: 
- **Använd värd filen (rekommenderas endast för testning)**. Du kan använda värd filen på en virtuell dator för att åsidosätta DNS.  
- **Använd en privat DNS-zon**. Du kan använda [privata DNS-zoner](../dns/private-dns-privatednszone.md) för att åsidosätta DNS-matchningen för en viss privat slut punkt. En privat DNS-zon kan länkas till det virtuella nätverket för att lösa vissa domäner.
- **Använd DNS-vidarebefordraren (valfritt)**. Du kan använda DNS-vidarebefordraren för att åsidosätta DNS-matchningen för en viss privat länk resurs. Om din [DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) finns i ett virtuellt nätverk kan du skapa en regel för vidarebefordran av DNS för att använda en privat DNS-zon för att förenkla konfigurationen för alla privata länk resurser.

> [!IMPORTANT]
> Rekommenderas inte för att åsidosätta en zon som används aktivt för att lösa offentliga slut punkter. Anslutningar till resurser kan inte lösas korrekt utan DNS-vidarebefordran till den offentliga DNS-tjänsten. Du kan undvika problem genom att skapa ett annat domän namn eller följa det föreslagna namnet för varje tjänst nedan. 



## <a name="azure-services-dns-zone-configuration"></a>Konfiguration av DNS-zon för Azure-tjänster
Azure-tjänster skapar en DNS-post för kanoniskt namn (CNAME) på den offentliga DNS-tjänsten för att omdirigera matchningen till det föreslagna privata domän namnet. Du kan åsidosätta upplösningen med den privata IP-adressen för dina privata slut punkter. 
 
Dina program behöver inte ändra anslutnings-URL: en. När du försöker matcha med en offentlig DNS-tjänst kommer DNS-servern nu att matcha dina privata slut punkter. Processen påverkar inte dina befintliga program. 

> [!IMPORTANT]
> Privata nätverk använder redan den privata DNS-zonen för en specifik typ, kan bara ansluta till offentliga resurser om de inte har några privata slut punkts anslutningar, annars krävs en motsvarande DNS-konfiguration i den privata DNS-zonen för att DNS-namnmatchningen ska kunna slutföras. 

För Azure-tjänster använder du de rekommenderade zon namnen enligt beskrivningen i följande tabell:

| Resurs typ för privat länk/under resurs |Privat DNS zonnamn | Offentliga DNS-zon vidarebefordrare |
|---|---|---|
| Azure Automation/(Microsoft. Automation/automationAccounts)/webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL Database (Microsoft. SQL/Servers)/SQL Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft. SQL/Servers)/SQL Server  | privatelink.database.windows.net | database.windows.net |
| Lagrings konto (Microsoft. Storage/storageAccounts)/BLOB (BLOB, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| Lagrings konto (Microsoft. Storage/storageAccounts)/tabell (tabell, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| Lagrings konto (Microsoft. Storage/storageAccounts)/kö (kö, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| Lagrings konto (Microsoft. Storage/storageAccounts)/fil (fil, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| Lagrings konto (Microsoft. Storage/storageAccounts)/Webb (webb, web_secondary) | privatelink.web.core.windows.net | web.core.windows.net |
| Azure Data Lake fil systemets Gen2 (Microsoft. Storage/storageAccounts)/Data Lake fil system Gen2 (DFS, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/tabell | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL-enskild server (Microsoft. DBforPostgreSQL/servers)/postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL (Microsoft. DBforMySQL/servers)/multisqlserver | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB (Microsoft. DBforMariaDB/servers)/mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault (Microsoft. nyckel valv/valv)/valv | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes service – Kubernetes API (Microsoft. container service/managedClusters)/hantering | privatelink. {region}. azmk8s. io | {region}. azmk8s. io |
| Azure Search (Microsoft. search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry (Microsoft. ContainerRegistry/register)/register | privatelink.azurecr.io | azurecr.io |
| Azure App konfiguration (Microsoft. AppConfiguration/configurationStores)/configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup (Microsoft. RecoveryServices/valv)/valv | privatelink. {region}. backup. windowsazure. com | {region}. backup. windowsazure. com |
| Azure Event Hubs (Microsoft. EventHub/Namespaces)/namnrymd | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft. Service Bus/Namespaces)/namnrymd | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net<br/>privatelink.servicebus.windows.net<sup>1</sup> | azure-devices.net<br/>servicebus.windows.net |
| Azure Relay (Microsoft. Relay/Namespaces)/namnrymd | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft. EventGrid/topics)/topic | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft. EventGrid/Domains)/Domain | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft. Web/Sites)/Sites | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/arbetsytes)/arbets yta | privatelink.api.azureml.ms | api.azureml.ms |
| IoT Hub (Microsoft. Devices/IotHubs)/IotHub | privatelink.azure-devices.net | azure-devices.net |
| SignalR (Microsoft. SignalRService/SignalR)/SignalR | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft. Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Cognitive Services (Microsoft. CognitiveServices/Accounts)/Account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft. StorageSync/storageSyncServices)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/factors)/dataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/factors)/Portal |  privatelink.azure.com  |  azure.com  |
| Azure cache för Redis (Microsoft. cache/Redis)/redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

<sup>1</sup> För användning med IoT Hub den inbyggda Event Hub-kompatibla slut punkten. Läs mer i [stöd för privata Länkar för IoT Hub inbyggda slut punkten](../iot-hub/virtual-network-support.md#built-in-event-hub-compatible-endpoint)
 
## <a name="dns-configuration-scenarios"></a>Scenarier för DNS-konfiguration

FQDN för tjänsterna matchas automatiskt till en offentlig IP-adress. För att matcha den privata IP-adressen för den privata slut punkten måste du ändra DNS-konfigurationen på lämpligt sätt.

DNS är en viktig komponent för att programmet ska fungera korrekt genom att den privata slut punktens IP-adress har matchats.

Beroende på dina inställningar finns följande scenarier med DNS-matchning integrerad:

- [Arbets belastningar för virtuella nätverk utan anpassad DNS-Server](#virtual-network-workloads-without-custom-dns-server)
- [Lokala arbets belastningar med en DNS-vidarebefordrare](#on-premises-workloads-using-a-dns-forwarder)
- [Virtuella nätverk och lokala arbets belastningar med en DNS-vidarebefordrare](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder)

> [!NOTE]
> [Azure Firewall DNS-proxy](../firewall/dns-settings.md#dns-proxy) kan användas som DNS-vidarebefordrare för [lokala arbets belastningar](#on-premises-workloads-using-a-dns-forwarder) och [virtuella nätverks arbets belastningar med en DNS-vidarebefordrare](#virtual-network-and-on-premises-workloads-using-a-dns-forwarder).

## <a name="virtual-network-workloads-without-custom-dns-server"></a>Arbets belastningar för virtuella nätverk utan anpassad DNS-Server

Den här konfigurationen är lämplig för virtuella nätverks arbets belastningar utan en anpassad DNS-server. I det här scenariot frågar klienten efter den privata slut punktens IP-adress till den Azure- [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)som har DNS-tjänsten. Azure DNS ansvarar för DNS-matchning av privata DNS-zoner.

> [!NOTE]
> I det här scenariot används den Azure SQL Database-rekommenderade privata DNS-zonen. För andra tjänster kan du justera modellen med följande referens: [konfiguration av DNS-zon för Azure-tjänster](#azure-services-dns-zone-configuration).

För att konfigurera korrekt behöver du följande resurser:

- Virtuellt klient nätverk

- Privat DNS zon [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md)  med [typen A Record](../dns/dns-zones-records.md#record-types)

- Information om privat slut punkt (FQDN-postnamn och privat IP-adress)

Följande skärm bild illustrerar DNS-matchningsfel från virtuella nätverks arbets belastningar med hjälp av den privata DNS-zonen:

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="Ett enda virtuellt nätverk och Azure-tillhandahållen DNS":::

Den här modellen kan utökas till flera peer-kopplade virtuella nätverk som är kopplade till samma privata slut punkt. Detta kan göras genom [att lägga till nya virtuella nätverks länkar](../dns/private-dns-virtual-network-links.md) till den privata DNS-zonen för alla peer-distribuerade virtuella nätverk.

> [!IMPORTANT]
> En enda privat DNS-zon krävs för den här konfigurationen. Att skapa flera zoner med samma namn för olika virtuella nätverk kräver manuella åtgärder för att slå samman DNS-posterna.

> [!IMPORTANT]
> Om du använder en privat slut punkt i en nav-och-eker-modell från en annan prenumeration återanvänder du samma privata DNS-zon på hubben.

I det här scenariot finns en topologi för [nav och ekrar](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) med ekrar som delar en gemensam privat slut punkt och alla eker-virtuella nätverk är länkade till samma privata DNS-zon. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hubb och eker med Azure-tillhandahållen DNS":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Lokala arbets belastningar med en DNS-vidarebefordrare

För lokala arbets belastningar för att matcha ett fullständigt domän namn för en privat slut punkt i den privata IP-adressen, måste du använda en DNS-vidarebefordrare för att distribuera den [offentliga DNS-zonen](#azure-services-dns-zone-configuration) för Azure-tjänsten i Azure.

Följande scenario är lämpligt för ett lokalt nätverk som har en DNS-vidarebefordrare i Azure, vilket i sin tur ansvarar för att matcha alla DNS-frågor via en vidarebefordrare på server nivå till Azure-168.63.129.16 DNS- [](../virtual-network/what-is-ip-address-168-63-129-16.md). 

> [!NOTE]
> I det här scenariot används den Azure SQL Database-rekommenderade privata DNS-zonen. För andra tjänster kan du justera modellen med följande referens: [konfiguration av DNS-zon för Azure-tjänster](#azure-services-dns-zone-configuration).

För att konfigurera korrekt behöver du följande resurser:

- Lokalt nätverk
- Virtuellt nätverk [som är anslutet till lokalt](/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-vidarebefordrare distribuerad i Azure 
- Privat DNS zoner [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md) med [typen A Record](../dns/dns-zones-records.md#record-types)
- Information om privat slut punkt (FQDN-postnamn och privat IP-adress)

Följande diagram illustrerar DNS-matchningsfel från ett lokalt nätverk som använder en DNS-vidarebefordrare som distribueras i Azure, där upplösningen görs av en privat DNS-zon som är [länkad till ett virtuellt nätverk](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Lokalt med Azure DNS":::

Den här konfigurationen kan utökas för ett lokalt nätverk som redan har en DNS-lösning på plats. Den lokala DNS-lösningen måste konfigureras för att vidarebefordra DNS-trafik till Azure DNS via en [villkorlig vidarebefordrare](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) som refererar till den DNS-vidarebefordrare som har distribuerats i Azure.

> [!NOTE]
> I det här scenariot används den Azure SQL Database-rekommenderade privata DNS-zonen. För andra tjänster kan du justera modellen med följande referens: [konfiguration av DNS-zon för Azure-tjänster](#azure-services-dns-zone-configuration)

För att konfigurera korrekt behöver du följande resurser:

- Lokalt nätverk med en anpassad DNS-lösning på plats 
- Virtuellt nätverk [som är anslutet till lokalt](/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-vidarebefordrare distribuerad i Azure
- Privat DNS zoner [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md)  med [typen A Record](../dns/dns-zones-records.md#record-types)
- Information om privat slut punkt (FQDN-postnamn och privat IP-adress)

Följande diagram illustrerar DNS-matchningsfel från ett lokalt nätverk som villkorligt vidarebefordrar DNS-trafik till Azure, där lösningen görs av en privat DNS-zon som är [länkad till ett virtuellt nätverk](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> Den villkorliga vidarebefordran måste göras till den rekommenderade [offentliga DNS-zonen vidarebefordrare](#azure-services-dns-zone-configuration). Exempel: `database.windows.net` i stället för **privatelink**. Database.Windows.net.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Lokal vidarebefordran till Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Virtuella nätverk och lokala arbets belastningar med en DNS-vidarebefordrare

För en allmän metod som är lämplig för arbets belastningar som behöver åtkomst till en privat slut punkt från virtuella och lokala nätverk måste du använda en delad DNS-vidarebefordrare för att göra en matchning av den [offentliga DNS-zonen](#azure-services-dns-zone-configuration) för Azure-tjänsten distribuerad i Azure.

Följande scenario är lämpligt för ett lokalt nätverk som har en DNS-vidarebefordrare i Azure, och virtuella nätverk som behöver åtkomst till den privata slut punkten som finns i ett delat nav nätverk.  

Den här DNS-vidarebefordraren ansvarar för att matcha alla DNS-frågor via en vidarebefordrare på server nivå till den Azure-angivna [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)för DNS-tjänsten.

> [!IMPORTANT]
> En enda privat DNS-zon krävs för den här konfigurationen. Alla klient anslutningar från lokala och peer-baserade [virtuella nätverk](../virtual-network/virtual-network-peering-overview.md) måste också använda samma privata DNS-zon.

> [!NOTE]
> I det här scenariot används den Azure SQL Database-rekommenderade privata DNS-zonen. För andra tjänster kan du justera modellen med följande referens: [konfiguration av DNS-zon för Azure-tjänster](#azure-services-dns-zone-configuration).

För att konfigurera korrekt behöver du följande resurser:

- Lokalt nätverk
- Virtuellt nätverk [som är anslutet till lokalt](/azure/architecture/reference-architectures/hybrid-networking/)
- [Peer-kopplat virtuellt nätverk](../virtual-network/virtual-network-peering-overview.md) 
- DNS-vidarebefordrare distribuerad i Azure
- Privat DNS zoner [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md)  med [typen A Record](../dns/dns-zones-records.md#record-types)
- Information om privat slut punkt (FQDN-postnamn och privat IP-adress)

Följande diagram illustrerar DNS-matchningsfel från ett lokalt och virtuellt nätverk som använder en DNS-vidarebefordrare som distribueras i Azure, där upplösningen görs av en privat DNS-zon som är [länkad till ett virtuellt nätverk](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Hybrid scenario":::

## <a name="next-steps"></a>Nästa steg
- [Lär dig om privata slut punkter](private-endpoint-overview.md)
