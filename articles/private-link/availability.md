---
title: Tillgänglighet för Azure Private-länkar
description: I den här artikeln får du lära dig om vilka Azure-tjänster som stöder privat länk.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: 26485c84749b7d4c91159476b3f683c2b0f3831b
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555775"
---
# <a name="azure-private-link-availability"></a>Tillgänglighet för Azure Private-länkar

Med Azures privata länk kan du få åtkomst till Azure PaaS-tjänster (till exempel Azure Storage och SQL Database) och Azure-värdbaserade/partner tjänster som ägs av en [privat slut punkt](private-endpoint-overview.md) i det virtuella nätverket. 

> [!IMPORTANT]
> Azures privata länk är nu allmänt tillgänglig. Både privat och privat länk tjänst (tjänsten bakom en standard belastningsutjämnare) är allmänt tillgängliga. Olika Azure-PaaS kommer att publiceras på en privat Azure-länk vid olika scheman. Information om kända begränsningar finns i [privat slut punkt](private-endpoint-overview.md#limitations) och [privat länk tjänst](private-link-service-overview.md#limitations). 

## <a name="service-availability"></a>Tjänst tillgänglighet

I följande tabeller visas de privata länk tjänsterna och de regioner där de är tillgängliga. 

### <a name="ai--machine-learning"></a>AI + Machine Learning

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Machine Learning | Alla offentliga regioner    |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Machine Learning.](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>Analys

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| Alla offentliga regioner <br/> Alla myndighets regioner |  Stöd för [anslutnings princip](../azure-sql/database/connectivity-architecture.md#connection-policy) för proxy |Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Azure Synapse Analytics.](../azure-sql/database/private-endpoint-overview.md)|
|Azure Event Hub | Alla offentliga regioner<br/>Alla myndighets regioner      |   | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Event Hub.](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>(Log Analytics & Application Insights) | Alla offentliga regioner      |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Monitor.](../azure-monitor/logs/private-link-security.md)   |
|Azure Data Factory | Alla offentliga regioner<br/> Alla myndighets regioner<br/>Alla regioner i Kina    | Autentiseringsuppgifter måste lagras i ett Azure Key Vault| Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Data Factory.](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>Compute

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure App Configuration | Alla offentliga regioner      |  | Förhandsgranskning  </br> [Lär dig hur du skapar en privat slut punkt för Azure App konfiguration](../azure-app-configuration/concept-private-endpoint.md) |
|Azure-hanterade diskar | Alla offentliga regioner<br/> Alla myndighets regioner<br/>Alla regioner i Kina    | [Välj för kända begränsningar](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Managed Disks.](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>Containers

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Container Registry | Alla offentliga regioner<br/> Alla myndighets regioner    | Stöds med Premium nivån i behållar registret. [Välj för nivåer](../container-registry/container-registry-skus.md)| Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Container Registry.](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes-tjänst – Kubernetes-API | Alla offentliga regioner      |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Kubernetes-tjänsten.](../aks/private-clusters.md)   |

### <a name="databases"></a>Databaser

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | Alla offentliga regioner <br/> Alla myndighets regioner<br/>Alla regioner i Kina      |  Stöd för [anslutnings princip](../azure-sql/database/connectivity-architecture.md#connection-policy) för proxy | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Azure SQL](create-private-endpoint-portal.md)      |
|Azure Cosmos DB|  Alla offentliga regioner<br/> Alla myndighets regioner</br> Alla regioner i Kina | |Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Cosmos DB.](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL-enskild server         | Alla offentliga regioner <br/> Alla myndighets regioner<br/>Alla regioner i Kina     | Stöds för Generell användning och minnesoptimerade pris nivåer | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Azure Database for PostgreSQL.](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | Alla offentliga regioner<br/> Alla myndighets regioner<br/>Alla regioner i Kina      |  | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Azure Database for MySQL.](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure-databas för MariaDB         | Alla offentliga regioner<br/> Alla myndighets regioner<br/>Alla regioner i Kina     |  | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Azure Database for MariaDB.](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>Integrering

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Event Grid| Alla offentliga regioner<br/> Alla myndighets regioner       |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Event Grid.](../event-grid/network-security.md) |
|Azure Service Bus | Alla offentliga regioner<br/>Alla myndighets regioner  | Stöds med Premium-nivån av Azure Service Bus. [Välj för nivåer](../service-bus-messaging/service-bus-premium-messaging.md) | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Service Bus.](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>Sakernas Internet (IoT)

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT Hub | Alla offentliga regioner    |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure IoT Hub.](../iot-hub/virtual-network-support.md) |
|  Azure Digital Twins         | Alla offentliga regioner som stöds av digitala Azure-platser     |  | Förhandsgranskning <br/> [Lär dig hur du skapar en privat slut punkt för Azures digitala dubbla.](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>Hantering och styrning

| Tjänster som stöds | Tillgängliga regioner | Ytterligare överväganden | Status  |
| ------------ | ----------------| ------------| ----------------|
| Azure Automation  | Alla offentliga regioner<br/> Alla myndighets regioner |  | Förhandsgranskning </br> [Lär dig hur du skapar en privat slut punkt för Azure Automation.](../automation/how-to/private-link-security.md)|
|Azure Backup | Alla offentliga regioner<br/> Alla myndighets regioner   |  | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Azure Backup.](../backup/private-endpoints.md)   |

### <a name="security"></a>Säkerhet

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | Alla offentliga regioner<br/> Alla myndighets regioner      |  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Key Vault.](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>Storage
|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob Storage (inklusive Data Lake Storage Gen2)       |  Alla offentliga regioner<br/> Alla myndighets regioner       |  Stöds på konto typ Generell användning v2 | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Blob Storage.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Alla offentliga regioner<br/> Alla myndighets regioner      | |   Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar Azure Files nätverks slut punkter.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Alla offentliga regioner      | |   Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar Azure Files nätverks slut punkter.](../storage/files/storage-sync-files-networking-endpoints.md)   |
| Azure Queue Storage       |  Alla offentliga regioner<br/> Alla myndighets regioner       |  Stöds på konto typ Generell användning v2 | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Queue Storage.](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  Alla offentliga regioner<br/> Alla myndighets regioner       |  Stöds på konto typ Generell användning v2 | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Table Storage.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | Alla offentliga regioner utom: Tyskland, centrala, Tyskland NORDÖSTRA <br/> Alla myndighets regioner  | | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat slut punkt för Azure Batch.](../batch/private-connectivity.md) |

### <a name="web"></a>Webb
|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | ÖSTRA USA, SÖDRA CENTRALA USA,<br/>VÄSTRA USA 2, alla regioner i Kina      |  | Förhandsgranskning   <br/> [Lär dig hur du skapar en privat slut punkt för Azure-Signalerare.](../azure-signalr/howto-private-endpoints.md)   |
|Azure Web Apps | Alla offentliga regioner<br/> Kina, norra 2 & öst 2    | Stöds med PremiumV2, PremiumV3 eller Function Premium plan  | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Web Apps.](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure Search | Alla offentliga regioner <br/> Alla myndighets regioner | Stöds med tjänsten i privat läge | Allmän tillgänglighet (GA)   <br/> [Lär dig hur du skapar en privat slut punkt för Azure Search.](../search/service-create-private-endpoint.md)    |
|Azure Relay | Alla offentliga regioner      |  | Förhandsgranskning <br/> [Lär dig hur du skapar en privat slut punkt för Azure Relay.](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>Privat länk tjänst med en standard belastningsutjämnare

|Tjänster som stöds  |Tillgängliga regioner | Ytterligare överväganden | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Privata länk tjänster bakom standard Azure Load Balancer | Alla offentliga regioner<br/> Alla myndighets regioner<br/>Alla regioner i Kina  | Stöds på Standard Load Balancer | Allmän tillgänglighet (GA) <br/> [Lär dig hur du skapar en privat länk-tjänst.](create-private-link-service-portal.md) |

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Private Link service:
- [Vad är Azure Privat Link?](private-link-overview.md)
- [Skapa en privat slutpunkt med hjälp av Azure-portalen](create-private-endpoint-portal.md)
