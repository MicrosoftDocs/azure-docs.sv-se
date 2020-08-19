---
title: Översikt över vCore inköps modell
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Med vCore inköps modell kan du skala beräknings-och lagrings resurser oberoende av varandra, matcha lokala prestanda och optimera priset för Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 08/14/2020
ms.openlocfilehash: 7131ddac840d2854969147da2eeb82a890ce3410
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586825"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Översikt över vCore-modellen – Azure SQL Database och Azure SQL-hanterad instans 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

VCore-modellen (Virtual Core) som används av Azure SQL Database och Azure SQL-hanterad instans ger flera fördelar:

- Högre beräknings-, minnes-, I/O-och lagrings gränser.
- Kontroll över maskin varu genereringen för att bättre matcha beräknings-och minnes kraven för arbets belastningen.
- Pris rabatter för [Azure Hybrid-förmån (AHB)](../azure-hybrid-benefit.md) och [reserverad instans (RI)](reserved-capacity-overview.md).
- Bättre genomskinlighet i maskin varu informationen som används för att hantera data bearbetningen, vilket underlättar planeringen av migreringar från lokala distributioner.

## <a name="service-tiers"></a>Tjänstnivåer

Tjänst nivå alternativ i vCore-modellen omfattar Generell användning, Affärskritisk och storskalig. Tjänste nivån definierar vanligt vis lagrings arkitektur, utrymmes-och I/O-gränser och affärs kontinuitets alternativ relaterade till tillgänglighet och haveri beredskap.

|-|**Generell användning**|**Affärskritisk**|**Hyperskala**|
|---|---|---|---|
|Bäst för|De flesta företags arbets belastningar. Erbjuder budgetorienterade, balanserade och skalbara beräknings- och lagringsalternativ. |Erbjuder affärs program den högsta återhämtningen till problem genom att använda flera isolerade repliker och ger den högsta I/O-prestandan per databas replik.|De flesta företags arbets belastningar med mycket skalbara lagrings-och Läs skalnings krav.  Ger högre återhämtning till problem genom att tillåta konfiguration av mer än en isolerad databas replik. |
|Storage|Använder Fjärrlagring.<br/>**SQL Database etablerings beräkning**:<br/>5 GB – 4 TB<br/>**Server lös beräkning**:<br/>5 GB-3 TB<br/>**SQL-hanterad instans**: 32 GB-8 TB |Använder lokal SSD-lagring.<br/>**SQL Database etablerings beräkning**:<br/>5 GB – 4 TB<br/>**SQL-hanterad instans**:<br/>32 GB – 4 TB |Flexibel automatisk storleks ökning av lagring vid behov. Har stöd för upp till 100 TB lagrings utrymme. Använder lokal SSD-lagring för lokal cache för buffring och lokal data lagring. Använder Azure Fjärrlagring som sista långsiktigt långsiktigt data lager. |
|IOPS och data flöde (ungefärligt)|**SQL Database**: se resurs gränser för [enskilda databaser](resource-limits-vcore-single-databases.md) och [elastiska pooler](resource-limits-vcore-elastic-pools.md).<br/>**SQL-hanterad instans**: se [Översikt över gränser för Azure SQL Managed Instance-resurser](../managed-instance/resource-limits.md#service-tier-characteristics).|Se resurs gränser för [enskilda databaser](resource-limits-vcore-single-databases.md) och [elastiska pooler](resource-limits-vcore-elastic-pools.md).|Hög skalning är en arkitektur med flera nivåer med cachelagring på flera nivåer. Effektiv IOPS och data flöde beror på arbets belastningen.|
|Tillgänglighet|1 replik, inga storskaliga repliker|3 repliker, 1 [storskalig replik](read-scale-out.md),<br/>zon-redundant hög tillgänglighet (HA)|1 Läs-och skriv replik, plus 0-4 storskalig [repliker](read-scale-out.md)|
|Säkerhetskopior|[Geo-redundant lagring med Läs behörighet (RA-GRS)](../../storage/common/geo-redundant-design.md), 7-35 dagar (7 dagar som standard)|[RA-GRS](../..//storage/common/geo-redundant-design.md), 7-35 dagar (7 dagar som standard)|Ögonblicks bilds säkerhets kopieringar i Azure Remote Storage. Återställningar använder dessa ögonblicks bilder för snabb återställning. Säkerhets kopieringar är omedelbara och påverkar inte beräknings-I/O-prestanda. Återställningar är snabba och är inte en storleks data åtgärd (tar några minuter i stället för timmar eller dagar).|
|Minnesintern|Stöds inte|Stöds|Stöds inte|
|||


### <a name="choosing-a-service-tier"></a>Välja tjänstnivå

Information om hur du väljer en tjänst nivå för din specifika arbets belastning finns i följande artiklar:

- [När du ska välja Generell användning tjänst nivå](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [När du ska välja Affärskritisk tjänst nivå](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [När du ska välja den storskaliga tjänst nivån](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Beräknings nivåer

Alternativ för beräknings nivåer i vCore-modellen innehåller etablerade och Server lös beräknings nivåer.


### <a name="provisioned-compute"></a>Allokerad beräkning

Den etablerade beräknings nivån tillhandahåller en viss mängd data bearbetnings resurser som ständigt har allokerats oberoende av arbets belastnings aktiviteten och faktureras för den mängd data som allokeras till ett fast pris per timme.


### <a name="serverless-compute"></a>Serverlös databearbetning

[Server lös beräknings nivån](serverless-tier-overview.md) skalar automatiskt upp beräknings resurser baserat på arbets belastnings aktivitet och fakturerar för mängden data bearbetning som används per sekund.



## <a name="hardware-generations"></a>Maskin varu generationer

Alternativen för att skapa maskin vara i vCore-modellen är generation 4/5, M-serien och Fsv2-serien. Maskin varu generationen definierar vanligt vis beräknings-och minnes gränser och andra egenskaper som påverkar arbets Belastningens prestanda.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5-maskinvara ger balanserade beräknings-och minnes resurser och är lämplig för de flesta databas arbets belastningar som inte har högre minne, högre vCore eller snabbare enskilda vCore-krav enligt Fsv2-serien eller M-serien.

För regioner där Gen4/Gen5 är tillgängligt, se [Gen4/Gen5-tillgänglighet](#gen4gen5-1).

### <a name="fsv2-series"></a>Fsv2-serien

- Fsv2-serien är ett alternativ för att optimera maskin vara som ger låg processor latens och hög klock hastighet för de flesta processor krävande arbets belastningar.
- Beroende på arbets belastningen kan Fsv2-serien leverera mer processor prestanda per vCore än Gen5 och vCore-storleken 72 ger mer processor prestanda för mindre kostnad än 80 virtuella kärnor på Gen5. 
- Fsv2 ger mindre minne och tempdb per vCore än annan maskin vara, så arbets belastningarna som är känsliga för dessa gränser kan vilja överväga Gen5 eller M-serien i stället.  

Fsv2-serien stöds bara i Generell användning nivån. För regioner där Fsv2-serien är tillgänglig, se [tillgänglighet för Fsv2-serien](#fsv2-series-1).


### <a name="m-series"></a>M-serien

- M-serien är ett minnesoptimerade maskin varu alternativ för arbets belastningar som kräver mer minnes-och högre beräknings gränser än vad som anges i Gen5.
- M-serien tillhandahåller 29 GB per vCore och upp till 128 virtuella kärnor, vilket ökar minnes gränsen i förhållande till Gen5 med 8x till nästan 4 TB.

M-serien stöds endast på Affärskritisk nivå och stöder inte zon redundans.  Prenumerationen måste vara en betald erbjudande typ, inklusive betala per användning eller Enterprise-avtal (EA). För regioner där M-serien är tillgänglig, se [tillgänglighet för m-serien](#m-series-1).

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="compute-and-memory-specifications"></a>Beräknings-och minnes specifikationer


|Maskin varu generering  |Compute  |Minne  |
|:---------|:---------|:---------|
|Gen4     |– Intel® E5-2673 v3 (Haswell) 2,4 GHz-processorer<br>-Etablera upp till 24 virtuella kärnor (1 vCore = 1 fysisk kärna)  |– 7 GB per vCore<br>-Etablera upp till 168 GB|
|Gen5     |**Allokerad beräkning**<br>– Intel® E5-2673 v4 (Broadwell) 2,3-GHz, Intel® SP-8160 (Skylake) \* och intel® 8272CL (Cascade Lake) 2,5 GHz- \* processorer<br>-Etablera upp till 80 virtuella kärnor (1 vCore = 1 Hyper-Thread)<br><br>**Serverlös databearbetning**<br>– Intel® E5-2673 v4 (Broadwell) 2,3-GHz och Intel® SP-8160 (Skylake) * processorer<br>-Skala upp till 40 virtuella kärnor (1 vCore = 1 Hyper-Thread) automatiskt|**Allokerad beräkning**<br>– 5,1 GB per vCore<br>-Etablera upp till 408 GB<br><br>**Serverlös databearbetning**<br>-Skala upp till 24 GB per vCore automatiskt<br>-Skala upp till 120 GB max|
|Fsv2-serien     |– Intel® 8168-processorer (Skylake)<br>– Med en hög kärnors klock hastighet på 3,4 GHz och en maximal Turbo Turbo klock hastighet på 3,7 GHz.<br>-Etablera upp till 72 virtuella kärnor (1 vCore = 1 Hyper-Thread)|– 1,9 GB per vCore<br>-Etablera upp till 136 GB|
|M-serien     |– Intel® E7-8890 v3 2,5 GHz och Intel® 8280M 2,7 GHz-processorer (Cascade Lake)<br>-Etablera upp till 128 virtuella kärnor (1 vCore = 1 Hyper-Thread)|– 29 GB per vCore<br>-Etablera upp till 3,7 TB|

\* I vyn [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) dynamisk hantering visas maskin varu generering för databaser som använder Intel® SP-8160-processorer (Skylake) som Gen6, medan maskin varu generering för databaser som använder Intel® 8272CL (Cascade Lake) visas som Gen7. Resurs gränser för alla Gen5-databaser är desamma oavsett processor typ (Broadwell, Skylake eller överlappande sjö).

Mer information om resurs gränser finns i [resurs gränser för enskilda databaser (vCore)](resource-limits-vcore-single-databases.md)eller [resurs gränser för elastiska pooler (vCore)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Välja en maskin varu generation

I Azure Portal kan du välja maskin varu generering för en databas eller pool i SQL Database vid tidpunkten för skapandet, eller så kan du ändra maskin varu generationen för en befintlig databas eller pool.

**Så här väljer du en maskin varu generering när du skapar en SQL Database eller pool**

Detaljerad information finns i [skapa en SQL Database](single-database-create-quickstart.md).

På fliken **grundläggande** väljer du länken **Konfigurera databas** i avsnittet **Compute + Storage** och väljer sedan länken **ändra konfiguration** :

  ![konfigurera databasen](./media/service-tiers-vcore/configure-sql-database.png)

Välj önskad maskin varu generation:

  ![Välj maskin vara](./media/service-tiers-vcore/select-hardware.png)


**Ändra maskin varu genereringen för en befintlig SQL Database eller pool**

För en databas, på sidan Översikt, väljer du länken **pris nivå** :

  ![ändra maskin vara](./media/service-tiers-vcore/change-hardware.png)

Välj **Konfigurera**på sidan Översikt för en pool.

Följ stegen för att ändra konfigurationen och välj maskin varu skapande enligt beskrivningen i föregående steg.

**Så här väljer du en maskin varu generation när du skapar en SQL-hanterad instans**

Detaljerad information finns i [skapa en SQL-hanterad instans](../managed-instance/instance-create-quickstart.md).

På fliken **grundläggande** väljer du länken **Konfigurera databas** i avsnittet **Compute + Storage** och väljer önskad maskin varu generering:

  ![Konfigurera SQL-hanterad instans](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Ändra maskin varu generationen för en befintlig SQL-hanterad instans**

# <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

På sidan SQL-hanterad instans väljer du **pris nivå** länk placerad under avsnittet Inställningar

![ändra maskin vara för SQL-hanterad instans](./media/service-tiers-vcore/change-managed-instance-hardware.png)

På sidan pris nivå kommer du att kunna ändra maskin varu genereringen enligt beskrivningen i föregående steg.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande PowerShell-skript:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Mer information finns i [set-AzSqlInstance-](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) kommandot.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande CLI-kommando:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Mer information finns i [AZ SQL mi Update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) -kommandot.

---

### <a name="hardware-availability"></a>Maskin varu tillgänglighet

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

Gen4-maskinvaran [föråldras](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) och är inte längre tillgänglig för nya distributioner. Alla nya databaser måste distribueras på Gen5 maskin vara.

Gen5 är tillgängligt i de flesta regioner i hela världen.

#### <a name="fsv2-series"></a>Fsv2-serien

Fsv2-serien är tillgänglig i följande regioner: Australien, centrala, Australien, östra, Australien, sydöstra, Brasilien, södra, centrala Kanada, Asien, östra, östra USA, centrala Frankrike, centrala Indien, västra USA, centrala Korea, södra Nord Europa, norra Europa, Sydostasien, Storbritannien, södra, Storbritannien, västra, Västeuropa, västra USA 2.


#### <a name="m-series"></a>M-serien

M-serien är tillgänglig i följande regioner: östra USA, norra Europa, Västeuropa, västra USA 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

## <a name="next-steps"></a>Nästa steg

För att komma igång, se: 
- [Skapa en SQL Database med hjälp av Azure Portal](single-database-create-quickstart.md)
- [Skapa en SQL-hanterad instans med hjälp av Azure Portal](../managed-instance/instance-create-quickstart.md)

Pris information finns på sidan med [Azure SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/single/).

Mer information om de specifika beräknings-och lagrings storlekar som är tillgängliga i nivån generell användning och affärs kritiska tjänster finns i:

- [vCore resurs gränser för Azure SQL Database](resource-limits-vcore-single-databases.md).
- [vCore resurs gränser för Azure SQL Database i pooler](resource-limits-vcore-elastic-pools.md).
- [vCore resurs gränser för Azure SQL-hanterad instans](../managed-instance/resource-limits.md). 

