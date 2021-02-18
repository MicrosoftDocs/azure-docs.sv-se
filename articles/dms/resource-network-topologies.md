---
title: Nätverkstopologier för migrering av SQL-hanterade instanser
titleSuffix: Azure Database Migration Service
description: Lär dig mer om käll-och mål konfigurationerna för migrering av Azure SQL-hanterade instanser med hjälp av Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 0799e8c76bc5d3969943d766aa83de40659a236a
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093377"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Nätverkstopologier för migrering av Azure SQL-hanterade instanser med hjälp av Azure Database Migration Service

I den här artikeln beskrivs olika nätverkstopologier som Azure Database Migration Service kan arbeta med för att tillhandahålla en omfattande migrering från SQL-servrar till Azure SQL-hanterad instans.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL-hanterad instans konfigurerad för Hybrid arbets belastningar 

Använd den här topologin om din Azure SQL-hanterade instans är ansluten till ditt lokala nätverk. Den här metoden ger den mest förenklade nätverks dirigeringen och ger högsta data genom strömning under migreringen.

![Nätverkstopologi för Hybrid arbets belastningar](media/resource-network-topologies/hybrid-workloads.png)

**Krav**

- I det här scenariot skapas den SQL-hanterade instansen och Azure Database Migration Service-instansen i samma Microsoft Azure Virtual Network, men de använder olika undernät.  
- Det virtuella nätverk som används i det här scenariot är också anslutet till det lokala nätverket med hjälp av antingen [ExpressRoute](../expressroute/expressroute-introduction.md) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>SQL-hanterad instans isolerad från det lokala nätverket

Använd den här nätverks sto pol Ogin om din miljö kräver ett eller flera av följande scenarier:

- SQL-hanterad instans är isolerad från lokal anslutning, men Azure Database Migration Service-instansen är ansluten till det lokala nätverket.
- Om Azure RBAC-principer (rollbaserad åtkomst kontroll) är på plats och du behöver begränsa användarna till att komma åt samma prenumeration som är värd för SQL-hanterad instans.
- De virtuella nätverk som används för SQL-hanterad instans och Azure Database Migration Service finns i olika prenumerationer.

![Nätverkstopologi för en hanterad instans som är isolerad från det lokala nätverket](media/resource-network-topologies/mi-isolated-workload.png)

**Krav**

- Det virtuella nätverk som Azure Database Migration Service använder för det här scenariot måste också vara anslutet till det lokala nätverket med hjälp av antingen ( https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- Konfigurera [VNet-nätverks-peering](../virtual-network/virtual-network-peering-overview.md) mellan det virtuella nätverk som används för SQL-hanterad instans och Azure Database migration service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrering från moln till moln: delat virtuellt nätverk

Använd den här topologin om käll SQL Server finns i en virtuell Azure-dator och delar samma virtuella nätverk med SQL-hanterad instans och Azure Database Migration Service.

![Nätverkstopologi för moln-till-moln-migrering med ett delat VNet](media/resource-network-topologies/cloud-to-cloud.png)

**Krav**

- Inga ytterligare krav.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Moln-till-moln-migreringar: isolerat virtuellt nätverk

Använd den här nätverks sto pol Ogin om din miljö kräver ett eller flera av följande scenarier:

- SQL-hanterad instans är etablerad i ett isolerat virtuellt nätverk.
- Om Azure RBAC-principer (rollbaserad åtkomst kontroll) är på plats och du behöver begränsa användarna till att komma åt samma prenumeration som är värd för SQL-hanterad instans.
- De virtuella nätverk som används för SQL-hanterad instans och Azure Database Migration Service finns i olika prenumerationer.

![Nätverkstopologi för moln-till-moln-migrering med ett isolerat VNet](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Krav**

- Konfigurera [VNet-nätverks-peering](../virtual-network/virtual-network-peering-overview.md) mellan det virtuella nätverk som används för SQL-hanterad instans och Azure Database migration service.

## <a name="inbound-security-rules"></a>Ingående säkerhetsregler

| **NAMN**   | **LASTNING** | **PROTOKOLLHANTERARE** | **KÄLLICENSSERVERN** | **MÅL** | **TGÄRD** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Valfri      | Valfri          | DMS-UNDERNÄT | Valfri             | Tillåt      |

## <a name="outbound-security-rules"></a>Säkerhetsregler för utgående trafik

| **NAMN**                  | **LASTNING**                                              | **PROTOKOLLHANTERARE** | **KÄLLICENSSERVERN** | **MÅL**           | **TGÄRD** | **Orsak för regel**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceBus                | 443, ServiceTag: Service Bus                           | TCP          | Valfri        | Valfri                       | Tillåt      | Hanterings Plans kommunikation via Service Bus. <br/>(Om Microsoft-peering har Aktiver ATS kanske du inte behöver den här regeln.)                                                             |
| Storage                   | 443, ServiceTag: lagring                              | TCP          | Valfri        | Valfri                       | Tillåt      | Hanterings plan med Azure Blob Storage. <br/>(Om Microsoft-peering har Aktiver ATS kanske du inte behöver den här regeln.)                                                             |
| Diagnostik               | 443, ServiceTag: AzureMonitor                         | TCP          | Valfri        | Valfri                       | Tillåt      | DMS använder den här regeln för att samla in diagnostikinformation i fel söknings syfte. <br/>(Om Microsoft-peering har Aktiver ATS kanske du inte behöver den här regeln.)                                                  |
| SQL-webbserver         | 1433 (eller TCP IP-port som SQL Server lyssnar på) | TCP          | Valfri        | Lokalt adressutrymme | Tillåt      | SQL Server käll anslutning från DMS <br/>(Om du har plats-till-plats-anslutning kanske du inte behöver den här regeln.)                                                                                       |
| SQL Server namngiven instans | 1434                                                  | UDP          | Valfri        | Lokalt adressutrymme | Tillåt      | SQL Server namngiven instans källa anslutning från DMS <br/>(Om du har plats-till-plats-anslutning kanske du inte behöver den här regeln.)                                                                        |
| SMB-resurs                 | 445 (om scenariot neeeds)                             | TCP          | Valfri        | Lokalt adressutrymme | Tillåt      | SMB-nätverks resurs för DMS för lagring av säkerhetskopierade databasfiler för migrering till Azure SQL Database MI-och SQL-servrar på den virtuella Azure-datorn <br/>(Om du har plats-till-plats-anslutning kanske du inte behöver den här regeln). |
| DMS_subnet                | Valfri                                                   | Valfri          | Valfri        | DMS_Subnet                | Tillåt      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Se även

- [Migrera SQL Server till SQL-hanterad instans](./tutorial-sql-server-to-managed-instance.md)
- [Översikt över krav för att använda Azure Database Migration Service](./pre-reqs.md)
- [Skapa ett virtuellt nätverk med Azure Portal](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure Database Migration Service finns i artikeln [Vad är Azure Database migration service?](dms-overview.md).
- Aktuell information om regional tillgänglighet för Azure Database Migration Service finns på sidan [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) .