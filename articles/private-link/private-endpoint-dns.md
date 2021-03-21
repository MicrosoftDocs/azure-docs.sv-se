---
title: DNS-konfiguration för privat slutpunkt i Azure
description: Lär dig om DNS-konfiguration för privat slut punkt i Azure
services: private-link
author: asudbring
ms.service: private-link
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: allensu
ms.openlocfilehash: 42c5b315c9c3560c400c685448a11dc61bf64eb6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102215620"
---
# <a name="azure-private-endpoint-dns-configuration"></a>DNS-konfiguration för privat slutpunkt i Azure

Det är viktigt att konfigurera dina DNS-inställningar på rätt sätt för att matcha den privata slut punktens IP-adress till det fullständigt kvalificerade domän namnet (FQDN) för anslutnings strängen.

Befintliga Microsoft Azure-tjänster kanske redan har en DNS-konfiguration för en offentlig slut punkt. Den här konfigurationen måste åsidosättas för att ansluta med hjälp av din privata slut punkt. 
 
Nätverks gränssnittet som är kopplat till den privata slut punkten innehåller information om hur du konfigurerar DNS. Informationen om nätverks gränssnittet innehåller FQDN och privata IP-adresser för din privata länk resurs. 
 
Du kan använda följande alternativ för att konfigurera dina DNS-inställningar för privata slut punkter: 
- **Använd värd filen (rekommenderas endast för testning)**. Du kan använda värd filen på en virtuell dator för att åsidosätta DNS.  
- **Använd en privat DNS-zon**. Du kan använda [privata DNS-zoner](../dns/private-dns-privatednszone.md) för att åsidosätta DNS-matchningen för en privat slut punkt. En privat DNS-zon kan länkas till det virtuella nätverket för att lösa vissa domäner.
- **Använd DNS-vidarebefordraren (valfritt)**. Du kan använda DNS-vidarebefordraren för att åsidosätta DNS-matchningen för en privat länk resurs. Skapa en regel för vidarebefordran av DNS om du vill använda en privat DNS-zon på din [DNS-Server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) som finns i ett virtuellt nätverk.

> [!IMPORTANT]
> Rekommenderas inte för att åsidosätta en zon som används aktivt för att lösa offentliga slut punkter. Anslutningar till resurser kan inte lösas korrekt utan DNS-vidarebefordran till den offentliga DNS-tjänsten. Du kan undvika problem genom att skapa ett annat domän namn eller följa det föreslagna namnet för varje tjänst nedan. 

## <a name="azure-services-dns-zone-configuration"></a>Konfiguration av DNS-zon för Azure-tjänster
Azure skapar en DNS-post för kanoniskt namn (CNAME) på den offentliga DNS-tjänsten. Posten CNAME omdirigerar matchningen till det privata domän namnet. Du kan åsidosätta upplösningen med den privata IP-adressen för dina privata slut punkter. 
 
Dina program behöver inte ändra anslutnings-URL: en. Vid matchning till en offentlig DNS-tjänst kommer DNS-servern att matcha dina privata slut punkter. Processen påverkar inte dina befintliga program. 

> [!IMPORTANT]
> Privata nätverk använder redan den privata DNS-zonen för en specifik typ, kan bara ansluta till offentliga resurser om de inte har några privata slut punkts anslutningar, annars krävs en motsvarande DNS-konfiguration i den privata DNS-zonen för att DNS-namnmatchningen ska kunna slutföras. 

För Azure-tjänster använder du de rekommenderade zon namnen enligt beskrivningen i följande tabell:

| Resurs typ för privat länk/under resurs |Privat DNS zonnamn | Offentliga DNS-zon vidarebefordrare |
|---|---|---|
| Azure Automation/(Microsoft. Automation/automationAccounts)/webhook, DSCAndHybridWorker | privatelink.azure-automation.net | azure-automation.net |
| Azure SQL Database (Microsoft. SQL/Servers)/sqlServer | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics (Microsoft. SQL/Servers)/sqlServer  | privatelink.database.windows.net | database.windows.net |
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
| Azure Site Recovery (Microsoft. RecoveryServices/valv)/valv | {region}. privatelink. siterecovery. windowsazure. com | {region}. hypervrecoverymanager. windowsazure. com |
| Azure Event Hubs (Microsoft. EventHub/Namespaces)/namnrymd | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus (Microsoft. Service Bus/Namespaces)/namnrymd | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure IoT Hub (Microsoft. Devices/IotHubs)/iotHub | privatelink.azure-devices.net<br/>privatelink.servicebus.windows.net<sup>1</sup> | azure-devices.net<br/>servicebus.windows.net |
| Azure Relay (Microsoft. Relay/Namespaces)/namnrymd | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid (Microsoft. EventGrid/topics)/topic | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid (Microsoft. EventGrid/Domains)/Domain | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Web Apps (Microsoft. Web/Sites)/Sites | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning (Microsoft. MachineLearningServices/arbetsytes)/amlworkspace | privatelink.api.azureml.ms<br/>privatelink.notebooks.azure.net | api.azureml.ms<br/>notebooks.azure.net<br/>instances.azureml.ms<br/>aznbcontent.net |
| SignalR (Microsoft. SignalRService/SignalR)/SignalR | privatelink.service.signalr.net | service.signalr.net |
| Azure Monitor (Microsoft. Insights/privateLinkScopes)/azuremonitor | privatelink.monitor.azure.com<br/> privatelink.oms.opinsights.azure.com <br/> privatelink.ods.opinsights.azure.com <br/> privatelink.agentsvc.azure-automation.net | monitor.azure.com<br/> oms.opinsights.azure.com<br/> ods.opinsights.azure.com<br/> agentsvc.azure-automation.net |
| Cognitive Services (Microsoft. CognitiveServices/Accounts)/Account | privatelink.cognitiveservices.azure.com  | cognitiveservices.azure.com  |
| Azure File Sync (Microsoft. StorageSync/storageSyncServices)/AFS |  privatelink.afs.azure.net  |  afs.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/factors)/dataFactory |  privatelink.datafactory.azure.net  |  datafactory.azure.net  |
| Azure Data Factory (Microsoft. DataFactory/factors)/Portal |  privatelink.adf.azure.com  |  adf.azure.com  |
| Azure cache för Redis (Microsoft. cache/Redis)/redisCache | privatelink.redis.cache.windows.net | redis.cache.windows.net |

<sup>1</sup> För användning med IoT Hub den inbyggda Event Hub-kompatibla slut punkten. Läs mer i [stöd för privata Länkar för IoT Hub inbyggda slut punkten](../iot-hub/virtual-network-support.md#built-in-event-hub-compatible-endpoint)

### <a name="china"></a>Kina

| Resurs typ för privat länk/under resurs |Privat DNS zonnamn | Offentliga DNS-zon vidarebefordrare |
|---|---|---|
| Azure SQL Database (Microsoft. SQL/Servers)/SQL Server | privatelink.database.chinacloudapi.cn | database.chinacloudapi.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/SQL | privatelink.documents.azure.cn | documents.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/MongoDB | privatelink.mongo.cosmos.azure.cn | mongo.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Cassandra | privatelink.cassandra.cosmos.azure.cn | cassandra.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/Gremlin | privatelink.gremlin.cosmos.azure.cn | gremlin.cosmos.azure.cn |
| Azure Cosmos DB (Microsoft. AzureCosmosDB/databaseAccounts)/tabell | privatelink.table.cosmos.azure.cn | table.cosmos.azure.cn |
| Azure Database for PostgreSQL-enskild server (Microsoft. DBforPostgreSQL/servers)/postgresqlServer | privatelink.postgres.database.chinacloudapi.cn | postgres.database.chinacloudapi.cn |
| Azure Database for MySQL (Microsoft. DBforMySQL/servers)/multisqlserver | privatelink.mysql.database.chinacloudapi.cn  | mysql.database.chinacloudapi.cn  |
| Azure Database for MariaDB (Microsoft. DBforMariaDB/servers)/mariadbServer | privatelink.mariadb.database.chinacloudapi.cn | mariadb.database.chinacloudapi.cn |

## <a name="dns-configuration-scenarios"></a>Scenarier för DNS-konfiguration

FQDN för tjänsterna matchas automatiskt till en offentlig IP-adress. Ändra DNS-konfigurationen om du vill matcha den privata IP-adressen för den privata slut punkten.

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

Du kan utöka den här modellen till peer-kopplade virtuella nätverk som är kopplade till samma privata slut punkt. [Lägg till nya virtuella nätverks länkar](../dns/private-dns-virtual-network-links.md) till den privata DNS-zonen för alla peer-virtuella nätverk.

> [!IMPORTANT]
> En enda privat DNS-zon krävs för den här konfigurationen. Att skapa flera zoner med samma namn för olika virtuella nätverk kräver manuella åtgärder för att slå samman DNS-posterna.

> [!IMPORTANT]
> Om du använder en privat slut punkt i en nav-och-eker-modell från en annan prenumeration återanvänder du samma privata DNS-zon på hubben.

I det här scenariot finns en topologi för [hubb och eker](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) -nätverk. Eker-nätverken delar en privat slut punkt. De virtuella eker-nätverken är länkade till samma privata DNS-zon. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Hubb och eker med Azure-tillhandahållen DNS":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>Lokala arbets belastningar med en DNS-vidarebefordrare

För lokala arbets belastningar för att matcha FQDN för en privat slut punkt använder du en DNS-vidarebefordrare för att lösa Azure-tjänstens [offentliga DNS-zon](#azure-services-dns-zone-configuration) i Azure.

Följande scenario gäller för ett lokalt nätverk som har en DNS-vidarebefordrare i Azure. Den här vidarebefordraren löser DNS-frågor via en vidarebefordrare på server nivå till Azures angivna DNS- [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md). 

> [!NOTE]
> I det här scenariot används den Azure SQL Database-rekommenderade privata DNS-zonen. För andra tjänster kan du justera modellen med följande referens: [konfiguration av DNS-zon för Azure-tjänster](#azure-services-dns-zone-configuration).

För att konfigurera korrekt behöver du följande resurser:

- Lokalt nätverk
- Virtuellt nätverk [som är anslutet till lokalt](/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-vidarebefordrare distribuerad i Azure 
- Privat DNS zoner [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md) med [typen A Record](../dns/dns-zones-records.md#record-types)
- Information om privat slut punkt (FQDN-postnamn och privat IP-adress)

Följande diagram illustrerar DNS-matchningsfel från ett lokalt nätverk. Konfigurationen använder en DNS-vidarebefordrare som distribueras i Azure. Upplösningen görs av en privat DNS-zon som är [länkad till ett virtuellt nätverk](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Lokalt med Azure DNS":::

Den här konfigurationen kan utökas för ett lokalt nätverk som redan har en DNS-lösning på plats. Den lokala DNS-lösningen har kon figurer ATS för att vidarebefordra DNS-trafik till Azure DNS via en [villkorlig vidarebefordrare](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). Den villkorliga vidarebefordraren hänvisar till den DNS-vidarebefordrare som distribueras i Azure.

> [!NOTE]
> I det här scenariot används den Azure SQL Database-rekommenderade privata DNS-zonen. För andra tjänster kan du justera modellen med följande referens: [konfiguration av DNS-zon för Azure-tjänster](#azure-services-dns-zone-configuration)

För att konfigurera korrekt behöver du följande resurser:

- Lokalt nätverk med en anpassad DNS-lösning på plats 
- Virtuellt nätverk [som är anslutet till lokalt](/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-vidarebefordrare distribuerad i Azure
- Privat DNS zoner [privatelink.Database.Windows.net](../dns/private-dns-privatednszone.md)  med [typen A Record](../dns/dns-zones-records.md#record-types)
- Information om privat slut punkt (FQDN-postnamn och privat IP-adress)

Följande diagram illustrerar DNS-matchningen från ett lokalt nätverk. DNS-matchning vidarebefordras villkorligt till Azure. Lösningen görs av en privat DNS-zon som är [länkad till ett virtuellt nätverk](../dns/private-dns-virtual-network-links.md).

> [!IMPORTANT]
> Den villkorliga vidarebefordran måste göras till den rekommenderade [offentliga DNS-zonen vidarebefordrare](#azure-services-dns-zone-configuration). Exempel: `database.windows.net` i stället för **privatelink**. Database.Windows.net.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Lokal vidarebefordran till Azure DNS":::

## <a name="virtual-network-and-on-premises-workloads-using-a-dns-forwarder"></a>Virtuella nätverk och lokala arbets belastningar med en DNS-vidarebefordrare

För arbets belastningar som har åtkomst till en privat slut punkt från virtuella och lokala nätverk använder du en DNS-vidarebefordrare för att lösa den [offentliga DNS-zon](#azure-services-dns-zone-configuration) för Azure-tjänsten som distribuerats i Azure.

Följande scenario gäller för ett lokalt nätverk med virtuella nätverk i Azure. Båda nätverken har åtkomst till den privata slut punkten som finns i ett nätverk med delad hubb.

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

Följande diagram visar DNS-matchning för både nätverk, lokala och virtuella nätverk. Upplösningen använder en DNS-vidarebefordrare. Upplösningen görs av en privat DNS-zon som är [länkad till ett virtuellt nätverk](../dns/private-dns-virtual-network-links.md):

:::image type="content" source="media/private-endpoint-dns/hybrid-scenario.png" alt-text="Hybrid scenario":::

## <a name="next-steps"></a>Nästa steg
- [Lär dig om privata slut punkter](private-endpoint-overview.md)
