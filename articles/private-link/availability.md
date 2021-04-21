---
title: Azure Private Link tillgänglighet
description: I den här artikeln får du lära dig mer om vilka Azure-tjänster som Private Link.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 866eb9feb152c0094cd5281fe4820ccc4589386f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778327"
---
# <a name="azure-private-link-availability"></a>Azure Private Link tillgänglighet

Azure Private Link kan du komma åt Azure PaaS-tjänster (till exempel Azure Storage och SQL Database) och Azure-värdbaserade kundägda/partnertjänster via en privat [slutpunkt](private-endpoint-overview.md) i ditt virtuella nätverk. 

> [!IMPORTANT]
> Azure Private Link är nu allmänt tillgänglig. Både privat slutpunkt och Private Link (tjänsten bakom standardlastbalanserare) är allmänt tillgängliga. Olika Azure PaaS publiceras för att Azure Private Link enligt olika scheman. Kända begränsningar finns i [Privat slutpunkt och](private-endpoint-overview.md#limitations) Private Link [Service](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Tjänsttillgänglighet

I följande tabeller visas Private Link tjänster och de regioner där de är tillgängliga. 

### <a name="ai--machine-learning"></a>AI + Machine Learning

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Alla offentliga regioner    |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analys

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Alla offentliga regioner <br/> Alla myndighetsregioner |  Stöds för [proxyanslutningsprincip](../azure-sql/database/connectivity-architecture.md#connection-policy) |Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | Alla offentliga regioner<br/>Alla myndighetsregioner      |   | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Event Hub.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics-& Application Insights) | Alla offentliga regioner      |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Monitor.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Alla offentliga regioner<br/> Alla myndighetsregioner<br/>Alla regioner i Kina    | Autentiseringsuppgifter måste lagras i ett Azure-nyckelvalv| Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Data Factory.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Compute

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | Alla offentliga regioner      |  | Förhandsgranskning  </br> [Lär dig hur du skapar en privat slutpunkt för Azure App Configuration](../azure-app-configuration/concept-private-endpoint.md) |
|Azure-hanterade diskar | Alla offentliga regioner<br/> Alla myndighetsregioner<br/>Alla regioner i Kina    | [Välj för kända begränsningar](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Managed Disks.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Containers

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Alla offentliga regioner<br/> Alla myndighetsregioner    | Stöds med premiumnivån för containerregister. [Välj för nivåer](../container-registry/container-registry-skus.md)| Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Container Registry.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes Service – Kubernetes API | Alla offentliga regioner      |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Kubernetes Service.](../aks/private-clusters.md)   |

### <a name="databases"></a>Databaser

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | Alla offentliga regioner <br/> Alla myndighetsregioner<br/>Alla regioner i Kina      |  Stöds för [proxyanslutningsprincip](../azure-sql/database/connectivity-architecture.md#connection-policy) | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för Azure SQL](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Alla offentliga regioner<br/> Alla myndighetsregioner</br> Alla regioner i Kina | |Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL – enskild server         | Alla offentliga regioner <br/> Alla myndighetsregioner<br/>Alla regioner i Kina     | Stöds för Generell användning och minnesoptimerade prisnivåer | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Database for PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Alla offentliga regioner<br/> Alla myndighetsregioner<br/>Alla regioner i Kina      |  | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Database for MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | Alla offentliga regioner<br/> Alla myndighetsregioner<br/>Alla regioner i Kina     |  | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Database for MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integrering

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Alla offentliga regioner<br/> Alla myndighetsregioner       |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Event Grid.](../event-grid/network-security.md) |
|Azure Service Bus | Alla offentliga regioner<br/>Alla myndighetsregioner  | Stöds med premiumnivån för Azure Service Bus. [Välj för nivåer](../service-bus-messaging/service-bus-premium-messaging.md) | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Sakernas Internet (IoT)

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Alla offentliga regioner    |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure IoT Hub.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Alla offentliga regioner som stöds av Azure Digital Twins     |  | Förhandsgranskning <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Digital Twins.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Hantering och styrning

| Tjänster som stöds | Tillgängliga regioner | Ytterligare överväganden | Status  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | Alla offentliga regioner<br/> Alla myndighetsregioner |  | Förhandsgranskning </br> [Lär dig hur du skapar en privat slutpunkt för Azure Automation.](../automation/how-to/private-link-security.md)|
|Azure Backup | Alla offentliga regioner<br/> Alla myndighetsregioner   |  | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Backup.](../backup/private-endpoints.md)   |

### <a name="security"></a>Säkerhet

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Alla offentliga regioner<br/> Alla myndighetsregioner      |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Storage
|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob Storage (inklusive Data Lake Storage Gen2)       |  Alla offentliga regioner<br/> Alla myndighetsregioner       |  Stöds på konto kind Generell användning V2 | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för bloblagring.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Alla offentliga regioner<br/> Alla myndighetsregioner      | |   Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar Azure Files nätverksslutpunkter.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Alla offentliga regioner      | |   Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar Azure Files nätverksslutpunkter.](../storage/file-sync/file-sync-networking-endpoints.md)   |
| Azure Queue Storage       |  Alla offentliga regioner<br/> Alla myndighetsregioner       |  Stöds på konto kind Generell användning V2 | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för kölagring.](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  Alla offentliga regioner<br/> Alla myndighetsregioner       |  Stöds på konto kind Generell användning V2 | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för tabellagring.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Alla offentliga regioner utom: Tyskland, centrala, Tyskland NORDÖSTRA <br/> Alla myndighetsregioner  | | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Batch.](../batch/private-connectivity.md) |

### <a name="web"></a>Webb
|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | USA, ÖSTRA, USA, SÖDRA CENTRALA,<br/>USA, VÄSTRA 2, Alla regioner i Kina      |  | Förhandsgranskning   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure SignalR.](../azure-signalr/howto-private-endpoints.md)   |
|Azure Web Apps | Alla offentliga regioner<br/> Kina, norra 2 & östra 2    | Stöds med PremiumV2, PremiumV3 eller Function Premium-plan  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Web Apps.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Alla offentliga regioner <br/> Alla myndighetsregioner | Stöds med tjänsten i privat läge | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Search.](../search/service-create-private-endpoint.md)    |
|Azure Relay | Alla offentliga regioner      |  | Förhandsgranskning <br/> [Lär dig hur du skapar en privat slutpunkt för Azure Relay.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Private Link med en standardlastbalanserare

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Private Link tjänster bakom Azure Load Balancer | Alla offentliga regioner<br/> Alla myndighetsregioner<br/>Alla regioner i Kina  | Stöds på Standard Load Balancer | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat länktjänst.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Private Link tjänsten:
- [Vad är Azure Privat Link?](private-link-overview.md)
- [Skapa en privat slutpunkt med hjälp av Azure-portalen](create-private-endpoint-portal.md)
