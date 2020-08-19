---
title: Minska tjänste kostnaderna med Azure Advisor
description: Använd Azure Advisor för att optimera kostnaden för dina Azure-distributioner.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 15f357248a6174de1e0890a4b45e9fa3930a06fe
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258528"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Minska service kostnaderna genom att använda Azure Advisor

Azure Advisor hjälper dig att optimera och minska dina totala Azure-utgifter genom att identifiera inaktiva och underutnyttjade resurser.Du kan få kostnads rekommendationer från fliken **kostnad** på Advisor-instrumentpanelen.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Optimera utgifterna för den virtuella datorn genom att ändra storlek på eller stänga av underutnyttjade instanser 

Även om vissa program scenarier kan resultera i låg användning genom design, kan du ofta Spara pengar genom att hantera storleken och antalet virtuella datorer. 

De rekommenderade åtgärderna stängs av eller ändras, beroende på den resurs som utvärderas.

Den avancerade utvärderings modellen i Advisor ser till att stänga av virtuella datorer när båda dessa påståenden är sanna: 
- P95th av det maximala värdet för processor belastningen är mindre än 3%. 
- Nätverks användningen är mindre än 2% under en sju dagars period.
- Minnes belastningen är lägre än tröskelvärdena

Advisor ser till att ändra storlek på virtuella datorer när det är möjligt att anpassa den aktuella belastningen på en mindre SKU (inom samma SKU-familj) eller ett mindre antal instanser, till exempel:
- Den aktuella inläsningen går inte över 80% användning för arbets belastningar som inte är riktade mot användaren. 
- Inläsningen går inte över 40% för användarspecifika arbets belastningar. 

Här bestämmer Advisor typ av arbets belastning genom att analysera arbets Belastningens processor användnings egenskaper.

Advisor visar de uppskattade kostnaderna för antingen Rekommenderad åtgärd: ändra storlek eller Stäng av. För storleks ändring tillhandahåller Advisor aktuell och riktad SKU-information.

Om du vill vara mer aggressivare att identifiera underutnyttjade virtuella datorer kan du justera processor användnings regeln per prenumeration.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>Optimera utgifter för MariaDB-, MySQL-och PostgreSQL-servrar genom rätt storleks ändring 
Advisor analyserar din användning och utvärderar om dina MariaDB-, MySQL-eller PostgreSQL-databas server resurser har underutnyttjats under de senaste sju dagarna. Låg resursutnyttjande ger oönskade utgifter som du kan åtgärda utan betydande prestanda påverkan. För att minska kostnaderna och hantera dina resurser effektivt, rekommenderar vi att du minskar beräknings storleken (virtuella kärnor) med hälften.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Minska kostnaderna genom att ta bort unetablerad ExpressRoute-kretsar

Advisor identifierar Azure ExpressRoute-kretsar som har varit i leverantörs statusen **inte etablerad** i mer än en månad. Vi rekommenderar att du tar bort kretsen om du inte planerar att etablera kretsen med anslutnings leverantören.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Minska kostnaderna genom att ta bort eller konfigurera om inaktiva virtuella nätverksgatewayer

Advisor identifierar virtuella nätverksgateway som har varit inaktiva i mer än 90 dagar. Eftersom dessa gatewayer faktureras per timme bör du överväga att konfigurera om eller ta bort dem om du inte tänker använda dem längre. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Köp reserverade instanser för virtuella datorer och spara pengar jämfört med om du betalar per användning

Advisor granskar din användning av den virtuella datorn under de senaste 30 dagarna för att avgöra om du kan spara pengar genom att köpa en Azure-reservation. Advisor visar de regioner och storlekar där potentiella besparingar är högsta och de beräknade besparingarna från inköps reservationer. Med Azure-reservationer kan du i förväg köpa bas kostnaderna för dina virtuella datorer. Rabatter gäller automatiskt för nya eller befintliga virtuella datorer som har samma storlek och region som dina reservationer. [Läs mer om Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor meddelar dig även om dina reserverade instanser som upphör att gälla inom de närmaste 30 dagarna. Vi rekommenderar att du köper nya reserverade instanser för att undvika priser enligt principen betala per användning.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Köp reserverade instanser för flera resurs typer för att spara över din betala per användning-kostnad

Advisor analyserar användnings mönster under de senaste 30 dagarna för följande resurser och rekommenderar reserverade kapacitets inköp som optimerar kostnaderna.

### <a name="azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB reserverad kapacitet
Advisor analyserar Azure Cosmos DB användnings mönster under de senaste 30 dagarna och rekommenderar reserv kapacitets inköp för att optimera kostnaderna. Med reserverad kapacitet kan du förköpa Azure Cosmos DB per timme och spara över dina kostnader enligt principen betala per användning. Reserverad kapacitet är en fakturerings förmån och gäller automatiskt för nya och befintliga distributioner. Advisor beräknar besparingar för enskilda prenumerationer med hjälp av 3 års reservations priser och genom att extrapolera de användnings mönster som observerats under de senaste 30 dagarna. Delade omfattnings rekommendationer är tillgängliga för reserverade kapacitets inköp och kan öka besparingarna.

### <a name="sql-paas-reserved-capacity"></a>Reserverad SQL PaaS-kapacitet
Advisor analyserar SQL-PaaS Elastic Database-pooler och användnings mönster för SQL-hanterade instanser under de senaste 30 dagarna. Sedan rekommenderar vi reserverade kapacitets inköp som optimerar kostnaderna. Med reserverad kapacitet kan du förköpa användningen av SQL DB per timme och spara över dina SQL Compute-kostnader. Din SQL-licens debiteras separat och debiteras inte av reservationen. Reserverad kapacitet är en fakturerings förmån och gäller automatiskt för nya och befintliga distributioner. Advisor beräknar besparingar för enskilda prenumerationer med hjälp av 3 års reservations priser och genom att extrapolera de användnings mönster som observerats under de senaste 30 dagarna. Delade omfattnings rekommendationer är tillgängliga för reserverade kapacitets inköp och kan öka besparingarna.

### <a name="app-service-stamp-fee-reserved-capacity"></a>App Service stämpel avgift reserverad kapacitet
Advisor analyserar användnings mönstret för Stamp-avgift för din Azure App Service isolerade miljö under de senaste 30 dagarna och rekommenderar reserverade kapacitets inköp som optimerar kostnaderna. Med reserverad kapacitet kan du förköpa varje timmes användning per timme för den isolerade miljö stämpel avgiften och spara över dina kostnader enligt principen betala per användning. Observera att den reserverade kapaciteten endast gäller för stämplings avgiften och inte App Service instanser. Reserverad kapacitet är en fakturerings förmån och gäller automatiskt för nya och befintliga distributioner. Advisor beräknar sparande av uppskattningar för enskilda prenumerationer med hjälp av 3 års reservations pris baserat på användnings mönster under de senaste 30 dagarna.

### <a name="blob-storage-reserved-capacity"></a>Reserverad Blob Storage-kapacitet
Advisor analyserar Azure Blob Storage och Azure Data Lake lagrings användning under de senaste 30 dagarna. Sedan beräknar den reserverade kapacitets inköp som optimerar kostnaderna. Med reserverad kapacitet kan du förköpa varje timmes användning och spara över dina nuvarande kostnader på begäran. Reserverad Blob Storage-kapacitet gäller endast för data som lagras i Azure Blob General-Purpose v2 och Azure Data Lake Storage Gen2 konton. Reserverad kapacitet är en fakturerings förmån och gäller automatiskt för nya och befintliga distributioner. Advisor beräknar besparingar för enskilda prenumerationer med hjälp av 3 års reservations priser och användnings mönster som observerats under de senaste 30 dagarna. Delade omfattnings rekommendationer är tillgängliga för reserverade kapacitets inköp och kan öka besparingarna.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>MariaDB, MySQL och PostgreSQL reserverad kapacitet
Advisor analyserar användnings mönstren för Azure Database for MariaDB, Azure Database for MySQL och Azure Database for PostgreSQL de senaste 30 dagarna. Sedan rekommenderar vi reserverade kapacitets inköp som optimerar kostnaderna. Med reserverad kapacitet kan du förköpa MariaDB, MySQL och PostgreSQL per timme och spara över dina nuvarande kostnader. Reserverad kapacitet är en fakturerings förmån och gäller automatiskt för nya och befintliga distributioner. Advisor beräknar besparingar för enskilda prenumerationer med hjälp av 3 års reservations priser och användnings mönster som observerats under de senaste 30 dagarna. Delade omfattnings rekommendationer är tillgängliga för reserverade kapacitets inköp och kan öka besparingarna.

### <a name="synapse-analytics-formerly-sql-data-warehouse-reserved-capacity"></a>Synapse-analys (tidigare SQL Data Warehouse) reserverad kapacitet
Advisor analyserar användnings mönstren för Azure Synapse-analys under de senaste 30 dagarna och rekommenderar reserverade kapacitets inköp som optimerar kostnaderna. Med reserverad kapacitet kan du förköpa Synapse för analys per timme och spara över dina kostnader på begäran. Reserverad kapacitet är en fakturerings förmån och gäller automatiskt för nya och befintliga distributioner. Advisor beräknar besparingar för enskilda prenumerationer med hjälp av 3 års reservations priser och användnings mönster som observerats under de senaste 30 dagarna. Delade omfattnings rekommendationer är tillgängliga för reserverade kapacitets inköp och kan öka besparingarna.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Ta bort ej associerade offentliga IP-adresser för att spara pengar

Advisor identifierar offentliga IP-adresser som inte är associerade med Azure-resurser som belastningsutjämnare och virtuella datorer. En nominell kostnad är kopplad till dessa offentliga IP-adresser. Om du inte planerar att använda dem kan du spara pengar genom att ta bort dem.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Ta bort Azure Data Factory-pipelines som inte fungerar

Advisor identifierar Azure Data Factory pipelines som inte fungerar upprepade gånger. Vi rekommenderar att du löser problemen eller tar bort pipelinen om du inte behöver dem. Du debiteras för de här pipelinen även om de inte betjänar dig när de inte är det.

## <a name="use-standard-snapshots-for-managed-disks"></a>Använd standard ögonblicks bilder för hanterade diskar
För att spara 60% av kostnaden rekommenderar vi att du lagrar dina ögonblicks bilder i standard lagring, oavsett lagrings typ för den överordnade disken. Det här alternativet är standard alternativet för ögonblicks bilder av hanterade diskar. Advisor identifierar ögonblicks bilder som lagras i Premium Storage och rekommenderar att du migrerar från Premium till standard Storage. [Läs mer om priser för hanterade diskar.](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>Använda livs cykel hantering
Genom att använda information om antalet Azure Blob Storage-objekt, Total storlek och transaktioner, identifierar Advisor om du ska aktivera livs cykel hantering för att skikta data på ett eller flera av dina lagrings konton. Du uppmanas att skapa livs cykel hanterings regler för att automatiskt hantera dina data till låg frekvent lagring eller Arkiv lag ring för att optimera dina lagrings kostnader samtidigt som du behåller dina data i Azure Blob Storage för programkompatibilitet.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Rekommendation om skapande av en tillfällig OS-disk
Med en [tillfällig OS-disk](../virtual-machines/ephemeral-os-disks.md) kan du: 
- Spara pengar på lagrings kostnader för OS-diskar. 
- Få mindre Läs-/skriv fördröjning till OS-diskar. 
- Få snabbare avbildnings åtgärder för virtuella datorer genom att återställa operativ systemet (och temporär disk) till dess ursprungliga tillstånd.

Det är bättre att använda en tillfällig OS-disk för IaaS virtuella datorer eller virtuella datorer med tillstånds lösa. Advisor ger rekommendationer för resurser som kan dra nytta av en tillfällig OS-disk.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>Minska Azure Datautforskaren Table cache-period (princip) för kluster kostnads optimering (för hands version)
Advisor identifierar resurser där en minskning av Table cache-principen frigör Azure Datautforskaren klusternoder som har låg processor användning, minne och en konfiguration med hög cachestorlek.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Få åtkomst till kostnads rekommendationer i Azure Advisor

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Sök efter och välj [**Advisor**](https://aka.ms/azureadvisordashboard) från vilken sida som helst.

1. På instrument panelen för **Advisor** väljer du fliken **kostnad** .

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer om rekommendationer för hög tillgänglighet](advisor-high-availability-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
