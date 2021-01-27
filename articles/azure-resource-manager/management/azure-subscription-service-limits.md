---
title: Begränsningar och kvoter för Azure-prenumeration
description: Innehåller en lista över vanliga gränser, kvoter och begränsningar för Azure-prenumerationer och tjänster. Den här artikeln innehåller information om hur du ökar gränserna tillsammans med högsta värden.
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 0a20d38d0f8d5be4a19dcdb8b6b846699bac6cc1
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920482"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Azure-prenumeration och tjänstbegränsningar, kvoter och krav

Det här dokumentet innehåller några av de vanligaste Microsoft Azures gränserna, som ibland kallas kvoter.

Mer information om priser för Azure finns i [Översikt över Azure-priser](https://azure.microsoft.com/pricing/). Där kan du beräkna dina kostnader med hjälp av [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/). Du kan också gå till sidan med pris information för en viss tjänst, till exempel [virtuella Windows-datorer](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Tips som hjälper dig att hantera dina kostnader finns i [förebygga oväntade kostnader med fakturering och kostnads hantering i Azure](../../cost-management-billing/cost-management-billing-overview.md).

## <a name="managing-limits"></a>Hantera gränser

> [!NOTE]
> Vissa tjänster har justerbara gränser.
>
> När en tjänst inte har justerbara gränser använder följande tabell huvud **gränsen**. I dessa fall är standard och Max gränserna samma.
>
> När gränsen kan justeras innehåller tabellerna **standard gräns** och **maximal gräns** rubrik. Gränsen kan höjas över standard gränsen, men inte över max gränsen.
>
> Om du vill höja gränsen eller kvoten över standard gränsen kan du [öppna en kund support förfrågan online utan kostnad](../templates/error-resource-quota.md).
>
> Termernas *mjuka gräns* och *hård gräns* används ofta för att beskriva den aktuella, justerbara gränsen (den mjuka gränsen) och den maximala gränsen (hård gräns). Om en gräns inte är justerbar, kommer det inte att finnas någon mjuk gräns, bara en hård gräns.
>

De [kostnads fria utvärderings prenumerationerna](https://azure.microsoft.com/offers/ms-azr-0044p) är inte berättigade till begränsning eller kvot ökningar. Om du har en [kostnads fri utvärderings prenumeration](https://azure.microsoft.com/offers/ms-azr-0044p)kan du uppgradera till en prenumeration [där du betalar per](https://azure.microsoft.com/offers/ms-azr-0003p/) användning. Mer information finns i [uppgradera din kostnads fria utvärderings prenumeration på Azure till en prenumeration där du betalar per](../../cost-management-billing/manage/upgrade-azure-subscription.md) användning och [vanliga frågor och svar om utvärderings prenumerationen](https://azure.microsoft.com/free/free-account-faq).

Vissa gränser hanteras på regional nivå.

Vi använder vCPU-kvoter som exempel. Om du vill begära en kvot ökning med stöd för virtuella processorer, måste du bestämma hur många virtuella processorer du vill använda i vilka regioner. Sedan gör du en speciell begäran för Azures resurs grupp vCPU kvoter för de mängder och regioner som du vill ha. Om du behöver använda 30 virtuella processorer i Västeuropa för Europa för att köra ditt program där, så kan du begära 30 virtuella processorer i Västeuropa. Din vCPU-kvot ökas inte i någon annan region – endast Västeuropa har 30-vCPU-kvoten.

Därför bör du bestämma vilka Azure-resurs grupps kvoter som krävs för din arbets belastning i en region. Sedan kan du begära detta belopp i varje region som du vill distribuera till. Information om hur du fastställer dina aktuella kvoter för vissa regioner finns i [lösa fel för resurs kvoter](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Allmänna gränser

Begränsningar för resurs namn finns i [namngivnings regler och begränsningar för Azure-resurser](resource-name-rules.md).

Information om Läs-och skriv gränser i Resource Manager API finns i [begränsa Resource Manager-begäranden](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Hanterings grupps gränser

Följande begränsningar gäller för [hanterings grupper](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Prenumerationsgränser

Följande begränsningar gäller när du använder Azure Resource Manager och Azure-resurs grupper.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Resurs grupps gränser

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Active Directory gränser

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>API Management gränser

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>App Service gränser

Följande App Service gränser inkluderar gränser för Web Apps, Mobile Apps och API Apps.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Automation-begränsningar

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-app-configuration"></a>Azure App Configuration

[!INCLUDE [app-configuration-limits](../../../includes/app-configuration-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Azure cache för Redis-gränser

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Azure Cloud Services-gränser

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Azure Kognitiv sökning-gränser

Pris nivåer avgör din Sök tjänsts kapacitet och begränsningar. Nivåerna är:

* En **kostnads fri** tjänst för flera innehavare, som delas med andra Azure-prenumeranter, är avsedd för utvärdering och små utvecklings projekt.
* **Basic** tillhandahåller dedikerade data bearbetnings resurser för produktions arbets belastningar i en mindre skala, med upp till tre repliker för arbets belastningar med hög tillgänglighet.
* **Standard**, som omfattar S1, S2, S3 och S3, är för större produktions arbets belastningar. Det finns flera nivåer på standard nivån så att du kan välja en resurs konfiguration som bäst matchar din arbets belastnings profil.

**Gränser per prenumeration**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Gränser per Sök tjänst**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Om du vill veta mer om begränsningar på en mer detaljerad nivå, till exempel dokument storlek, frågor per sekund, nycklar, förfrågningar och svar, se [tjänst begränsningar i Azure kognitiv sökning](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Azure Cognitive Services-gränser

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Azure Cosmos DB gränser

För Azure Cosmos DB gränser, se [gränser i Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Azure Datautforskaren-gränser

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

För Azure Database for MySQL gränser, se [begränsningar i Azure Database for MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

För Azure Database for PostgreSQL gränser, se [begränsningar i Azure Database for PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Azure Functions gränser

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

Mer information finns i [funktions värds planer jämförelse](../../azure-functions/functions-scale.md).

## <a name="azure-kubernetes-service-limits"></a>Service gränser i Azure Kubernetes

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Azure Machine Learning gränser

Du hittar de senaste värdena för Azure Machine Learning Compute-kvoter på sidan för [Azure Machine Learning kvot](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Azure Maps gränser

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Azure Monitor gränser

### <a name="alerts"></a>Aviseringar

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Åtgärdsgrupper

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="autoscale"></a>Automatisk skalning

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-autoscale.md)]

### <a name="log-queries-and-language"></a>Logg frågor och språk

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Log Analytics-arbetsytor

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Azure Policy gränser

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-quantum-limits"></a>Azure-Quantum-gränser

[!INCLUDE [quantum-limits](../../../includes/azure-quantum-limits.md)]

## <a name="azure-role-based-access-control-limits"></a>Azure rollbaserad åtkomst kontroll gränser

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control/limits.md)]

## <a name="azure-signalr-service-limits"></a>Service gränser i Azure SignalR

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Säkerhets kopierings gränser

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Batch-gränser

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Begränsningar för klassisk distributions modell

Om du använder den klassiska distributions modellen i stället för Azure Resource Manager distributions modell gäller följande begränsningar.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Container Instances gränser

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Container Registry gränser

I följande tabell beskrivs funktionerna och begränsningarna för [tjänst nivåerna](../../container-registry/container-registry-skus.md)Basic, standard och Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Content Delivery Network gränser

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Data Factory gränser

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Data Lake Analytics gränser

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-storage-limits"></a>Data Lake Storage gränser

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Data delnings gränser

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Database Migration Service gränser

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Digitala gränser

> [!NOTE]
> Vissa delar av den här tjänsten har justerbara gränser och andra inte. Detta representeras i tabellerna nedan med den *justerbara?* kolumnen. När gränsen kan justeras är värdet för *justerbara?* *Ja*.

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Event Grid gränser

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Event Hubs gränser

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="iot-central-limits"></a>IoT Central gränser
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>IoT Hub gränser

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>IoT Hub Device Provisioning Service gränser

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Key Vault gränser

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="managed-identity-limits"></a>Hanterade identitets gränser

[!INCLUDE [Managed-Identity-Limits](../../../includes/managed-identity-limits.md)]


## <a name="media-services-limits"></a>Media Services gränser

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Media Services v2 (äldre)

För gränser som är begränsade till Media Services v2 (bakåtkompatibelt), se [Media Services v2 (bakåtkompatibelt)](../../media-services/previous/media-services-quotas-and-limitations.md)

## <a name="mobile-services-limits"></a>Mobile Services gränser

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Multi-Factor Authentication gränser

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Begränsningar för nätverk

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>ExpressRoute-gränser

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>Virtual Network Gateway-gränser

[!INCLUDE [virtual-network-gateway-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="nat-gateway-limits"></a>NAT-gateway-gränser

[!INCLUDE [nat-gateway-limits](../../../includes/azure-nat-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>Virtuella WAN-gränser

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Application Gateway gränser

Följande tabell gäller v1, v2, standard och WAF SKU: er om inget annat anges.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Network Watcher gränser

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Private Link-gränser

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

## <a name="purview-limits"></a>Avdelningens kontroll-gränser

De senaste värdena för Azure avdelningens kontroll-kvoter finns på [kvot sidan för Azure avdelningens kontroll](../../purview/how-to-manage-quotas.md)

### <a name="traffic-manager-limits"></a>Traffic Manager gränser

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Azure skydds-gränser

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Azure DNS gränser

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Begränsningar för Azure-brandvägg

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Azure frontend-tjänstens gränser

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Notification Hubs gränser

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="service-bus-limits"></a>Service Bus gränser

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Gränser för Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>SQL Database gränser

För SQL Database gränser, se [SQL Database resurs gränser för enskilda databaser](../../azure-sql/database/resource-limits-vcore-single-databases.md), [SQL Database resurs gränser för elastiska pooler och databaser i pooler](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)och [SQL Database resurs gränser för SQL-hanterad instans](../../azure-sql/managed-instance/resource-limits.md).

## <a name="azure-synapse-analytics-limits"></a>Azure Synapse Analytics-gränser

För Azure Synapse Analytics-gränser, se [resurs gränser för Azure Synapse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="storage-limits"></a>Lagrings gränser

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Mer information om gränser för standard lagrings konton finns i [skalbarhets mål för standard lagrings konton](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Begränsningar för lagrings resurs leverantörer

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Azure Blob Storage-gränser

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Azure Files gränser

Mer information om Azure Files gränser finns i [Azure Files skalbarhets-och prestanda mål](../../storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Azure File Sync gränser

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Lagrings gränser i Azure Queue

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Lagrings gränser i Azure-tabell

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Disk gränser för virtuell dator

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Mer information finns i [storlekar för virtuella datorer](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="disk-encryption-sets"></a>Disk krypterings uppsättningar

Det finns en begränsning på 1000 disk krypterings uppsättningar per region, per prenumeration. Mer information finns i krypterings dokumentationen för virtuella [Linux](../../virtual-machines/disk-encryption.md#restrictions) -eller [Windows](../../virtual-machines/disk-encryption.md#restrictions) -datorer. Kontakta Azure-supporten om du behöver öka kvoten.

### <a name="managed-virtual-machine-disks"></a>Hanterade virtuella dator diskar

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Ohanterade virtuella dator diskar

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>StorSimple system gränser

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Stream Analytics gränser

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Virtual Machines gränser

### <a name="virtual-machines-limits"></a>Virtual Machines gränser

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Virtual Machines gränser – Azure Resource Manager

Följande begränsningar gäller när du använder Azure Resource Manager och Azure-resurs grupper.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Begränsningar för delade avbildnings Galleri

Det finns gränser per prenumeration för att distribuera resurser med hjälp av delade avbildnings gallerier:

- 100 delade avbildnings gallerier, per prenumeration, per region
- 1 000 avbildnings definitioner, per prenumeration, per region
- 10 000 avbildnings versioner, per prenumeration, per region

## <a name="virtual-machine-scale-sets-limits"></a>Begränsningar för skalnings uppsättningar för virtuell dator

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Se även

* [Förstå Azure-gränser och ökningar](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Virtuella datorer och moln tjänst storlekar för Azure](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Storlekar för Azure-Cloud Services](../../cloud-services/cloud-services-sizes-specs.md)
* [Namngivningsregler och begränsningar för Azure-resurser](resource-name-rules.md)
