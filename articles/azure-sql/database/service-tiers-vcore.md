---
title: Översikt över köpmodell för virtuella kärnor
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Med köpmodellen för virtuella kärnor kan du oberoende skala beräknings- och lagringsresurser, matcha lokala prestanda och optimera priset för Azure SQL Database och Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 3bd617f052d52339ae35e5a088c6ee85b797fb48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779191"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Översikt över vCore-modell – Azure SQL Database och Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Köpmodellen för virtuell kärna (vCore) som används av Azure SQL Database Azure SQL Managed Instance ger flera fördelar:

- Högre gränser för beräkning, minne, I/O och lagring.
- Kontroll över maskinvarugenereringen för att bättre matcha beräknings- och minneskraven för arbetsbelastningen.
- Prisrabatter [för Azure Hybrid-förmån (AHB)](../azure-hybrid-benefit.md) och [reserverad instans (RI).](reserved-capacity-overview.md)
- Större transparens i maskinvaruinformationen som driver beräkningen, vilket underlättar planering för migreringar från lokala distributioner.

## <a name="service-tiers"></a>Tjänstnivåer

Tjänstnivåalternativen i modellen med virtuella kärnor omfattar Generell användning, Affärskritisk och Hyperskala. Tjänstnivån definierar vanligtvis lagringsarkitektur, utrymmes- och I/O-gränser samt alternativ för affärskontinuering som rör tillgänglighet och haveriberedskap.

|-|**Generell användning**|**Affärskritisk**|**Hyperskala**|
|---|---|---|---|
|Bäst för|De flesta företagsarbetsbelastningar. Erbjuder budgetorienterade, balanserade och skalbara beräknings- och lagringsalternativ. |Erbjuder affärsprogram den högsta återhämtningsförmågan vid fel genom att använda flera isolerade repliker och ger högsta I/O-prestanda per databasreplik.|De flesta företagsarbetsbelastningar med mycket skalbar lagring och krav på lässkalning.  Erbjuder högre återhämtning vid fel genom att tillåta konfiguration av fler än en isolerad databasreplik. |
|Storage|Använder fjärrlagring.<br/>**SQL Database etablerade beräkning**:<br/>5 GB –4 TB<br/>**Serverlös beräkning:**<br/>5 GB–3 TB<br/>**SQL Managed Instance:** 32 GB–8 TB |Använder lokal SSD-lagring.<br/>**SQL Database etablerade beräkning:**<br/>5 GB –4 TB<br/>**SQL Managed Instance**:<br/>32 GB–4 TB |Flexibel automatisk tillväxt av lagringen efter behov. Stöder upp till 100 TB lagringsutrymme. Använder lokal SSD-lagring för lokal buffertpoolcache och lokal datalagring. Använder Azure Remote Storage som slutligt långsiktigt datalager. |
|IOPS och dataflöde (ungefärligt)|**SQL Database: Se** resursgränser för enskilda [databaser och](resource-limits-vcore-single-databases.md) [elastiska pooler.](resource-limits-vcore-elastic-pools.md)<br/>**SQL Managed Instance:** Se [Översikt Azure SQL Managed Instance resursbegränsningar.](../managed-instance/resource-limits.md#service-tier-characteristics)|Se resursgränser för [enkla databaser och](resource-limits-vcore-single-databases.md) [elastiska pooler.](resource-limits-vcore-elastic-pools.md)|Hyperskala är en arkitektur med flera nivåer med cachelagring på flera nivåer. Effektiv IOPS och dataflöde beror på arbetsbelastningen.|
|Tillgänglighet|1 replik, inga skrivskyddade repliker|3 repliker, 1 [skrivskyddade repliker](read-scale-out.md),<br/>zonredundant hög tillgänglighet (HA)|1 skrivskyddade repliker, plus 0–4 [skrivskyddade repliker](read-scale-out.md)|
|Säkerhetskopior|[Geo-redundant lagring med läsbehörighet (RA-GRS),](../../storage/common/geo-redundant-design.md)1–35 dagar (7 dagar som standard)|[RA-GRS](../..//storage/common/geo-redundant-design.md), 1–35 dagar (7 dagar som standard)|Ögonblicksbildsbaserade säkerhetskopieringar i Azure Remote Storage. Återställningar använder dessa ögonblicksbilder för snabb återställning. Säkerhetskopieringar är omedelbara och påverkar inte beräknings-I/O-prestanda. Återställningar är snabba och är inte en datastorleksåtgärd (tar minuter i stället för timmar eller dagar).|
|Minnesintern|Stöds inte|Stöds|Stöds inte|
|||


### <a name="choosing-a-service-tier"></a>Välja tjänstnivå

Information om hur du väljer en tjänstnivå för din specifika arbetsbelastning finns i följande artiklar:

- [När du ska välja Generell användning tjänstnivå](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [När du ska välja Affärskritisk tjänstnivå](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [När du ska välja tjänstnivån Hyperskala](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Beräkningsnivåer

Alternativen för beräkningsnivå i vCore-modellen inkluderar etablerade och serverlösa beräkningsnivåer.


### <a name="provisioned-compute"></a>Etablerad beräkning

Den etablerade beräkningsnivån tillhandahåller en viss mängd beräkningsresurser som kontinuerligt etableras oberoende av arbetsbelastningsaktiviteten och fakturerar för den mängd beräkning som etableras till ett fast pris per timme.


### <a name="serverless-compute"></a>Serverlös databearbetning

Den [serverlösa beräkningsnivån](serverless-tier-overview.md) skalar automatiskt beräkningsresurser baserat på arbetsbelastningsaktivitet och debiterar för den mängd beräkning som används per sekund.



## <a name="hardware-generations"></a>Maskinvarugenerationer

Alternativen för maskinvarugenerering i vCore-modellen är Gen 4/5, M-serien, Fsv2-serien och DC-serien. Maskinvarugenereringen definierar vanligtvis beräknings- och minnesgränserna och andra egenskaper som påverkar arbetsbelastningens prestanda.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5-maskinvaran ger balanserade beräknings- och minnesresurser och är lämplig för de flesta databasarbetsbelastningar som inte har högre minne, högre virtuella kärnor eller snabbare krav för en enskild vCore som tillhandahålls av Fsv2-serien eller M-serien.

För regioner där Gen4/Gen5 är tillgängligt, se [Gen4/Gen5-tillgänglighet.](#gen4gen5-1)

### <a name="fsv2-series"></a>Fsv2-serien

- Fsv2-serien är ett beräkningsoptimerat maskinvarualternativ som levererar låg CPU-svarstid och hög klockhastighet för de mest CPU-krävande arbetsbelastningarna.
- Beroende på arbetsbelastningen kan Fsv2-serien leverera mer CPU-prestanda per vCore än Gen5, och storleken på 72 virtuella kärnor kan ge mer processorprestanda till en lägre kostnad än 80 virtuella kärnor på Gen5. 
- Fsv2 ger mindre minne och tempdb per vCore än annan maskinvara, så arbetsbelastningar som är känsliga för dessa gränser kan vilja överväga Gen5- eller M-serien i stället.  

Fsv2-serien i stöds endast på Generell användning nivå. För regioner där Fsv2-serien är tillgänglig, se [Fsv2-seriens tillgänglighet.](#fsv2-series-1)

### <a name="m-series"></a>M-serien

- M-serien är ett minnesoptimerat maskinvarualternativ för arbetsbelastningar som kräver mer minne och högre beräkningsgränser än gen5.
- M-serien tillhandahåller 29 GB per vCore och upp till 128 virtuella kärnor, vilket ökar minnesgränsen i förhållande till Gen5 med 8x till nästan 4 TB.

M-serien stöds endast på Affärskritisk och stöder inte zonredundans.  Information om regioner där M-serien är tillgänglig finns i [M-seriens tillgänglighet.](#m-series-1)

#### <a name="azure-offer-types-supported-by-m-series"></a>Azure-erbjudandetyper som stöds av M-serien

För att få åtkomst till M-serien måste prenumerationen vara en betald erbjudandetyp, inklusive Betala per prenumeration eller Enterprise-avtal (EA).  En fullständig lista över typer av Azure-erbjudanden som stöds av M-serien finns i [aktuella erbjudanden utan utgiftsgränser.](https://azure.microsoft.com/support/legal/offer-details)

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>DC-serien

> [!NOTE]
> DC-serien finns för närvarande i **offentlig förhandsversion.**

- DC-seriens maskinvara använder Intel-processorer med Software Guard Extensions (Intel SGX)-teknik.
- DC-serien krävs för [Always Encrypted med säkra enklaver](/sql/relational-databases/security/encryption/always-encrypted-enclaves), som inte stöds med andra maskinvarukonfigurationer.
- DC-serien är utformad för arbetsbelastningar som bearbetar känsliga data och kräver funktioner för konfidentiell frågebearbetning, som tillhandahålls av Always Encrypted med säkra enklaver.
- Maskinvara i DC-serien ger balanserade beräknings- och minnesresurser.

DC-serien stöds endast för den etablerade beräkningen (serverlös stöds inte) och stöder inte zonredundans. Information om regioner där DC-serien är tillgänglig finns i [Tillgänglighet för DC-serien.](#dc-series-1)

#### <a name="azure-offer-types-supported-by-dc-series"></a>Typer av Azure-erbjudanden som stöds av DC-serien

För att få åtkomst till DC-serien måste prenumerationen vara en betald erbjudandetyp, inklusive Betala per du-go eller Enterprise-avtal (EA).  En fullständig lista över typer av Azure-erbjudanden som stöds av DC-serien finns i [aktuella erbjudanden utan utgiftsgränser.](https://azure.microsoft.com/support/legal/offer-details)

### <a name="compute-and-memory-specifications"></a>Specifikationer för beräkning och minne


|Maskinvarugenerering  |Compute  |Minne  |
|:---------|:---------|:---------|
|Gen4     |– Intel® E5-2673 v3 (Haswell) 2,4 GHz-processorer<br>– Etablera upp till 24 virtuella kärnor (1 vCore = 1 fysisk kärna)  |– 7 GB per vCore<br>– Etablera upp till 168 GB|
|Gen5     |**Etablerad beräkning**<br>– Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) \* och Intel® 8272CL (Cascade Lake) 2,5 GHz-processorer \*<br>– Etablera upp till 80 virtuella kärnor (1 vCore = 1 hypertråd)<br><br>**Serverlös databearbetning**<br>– Intel® E5-2673 v4 (Broadwell) 2,3 GHz- och Intel® SP-8160-processorer (Skylake)*<br>– Skala automatiskt upp till 40 virtuella kärnor (1 vCore = 1 hypertråd)|**Etablerad beräkning**<br>– 5,1 GB per vCore<br>– Etablera upp till 408 GB<br><br>**Serverlös databearbetning**<br>– Skala automatiskt upp till 24 GB per vCore<br>– Skala upp till 120 GB max automatiskt|
|Fsv2-serien     |– Intel® 8168-processorer (Skylake)<br>– Visar en varaktig turboklocka på 3,4 GHz och en högsta enkärnig turboklocka på 3,7 GHz.<br>– Etablera upp till 72 virtuella kärnor (1 vCore = 1 hypertråd)|– 1,9 GB per vCore<br>– Etablera upp till 136 GB|
|M-serien     |– Intel® E7-8890 v3 2,5 GHz- och Intel® 8280M 2,7 GHz-processorer (Cascade Lake)<br>– Etablera upp till 128 virtuella kärnor (1 vCore = 1 hypertråd)|– 29 GB per vCore<br>– Etablera upp till 3,7 TB|
|DC-serien     | – Intel XEON E-2288G-processorer<br>– Med Intel Software Guard-tillägget (Intel SGX))<br>– Etablera upp till 8 virtuella kärnor (1 vCore = 1 fysisk kärna) | 4,5 GB per vCore |

\* I [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) dynamisk hanteringsvy visas maskinvarugenereringen för databaser som använder Intel® SP-8160-processorer (Skylake) som Gen6, medan maskinvarugenerering för databaser som använder Intel® 8272CL (Cascade Lake) visas som Gen7. Resursgränserna för alla Gen5-databaser är desamma oavsett processortyp (Broadwell, Skylake eller Cascade Lake).

Mer information om resursbegränsningar finns i Resursgränser för enkla databaser [(vCore)](resource-limits-vcore-single-databases.md)eller Resursgränser [för elastiska pooler (vCore).](resource-limits-vcore-elastic-pools.md)

### <a name="selecting-a-hardware-generation"></a>Välja en maskinvarugeneration

I Azure Portal kan du välja maskinvarugenerering för en databas eller pool i SQL Database när den skapas, eller så kan du ändra maskinvarugenereringen för en befintlig databas eller pool.

**Så här väljer du en maskinvarugeneration när du skapar en SQL Database eller pool**

Detaljerad information finns i [Skapa en SQL Database](single-database-create-quickstart.md).

På **fliken Grundläggande inställningar** väljer du **länken Konfigurera databas** i avsnittet Beräkning **+** lagring och väljer sedan länken **Ändra** konfiguration:

  ![konfigurera databasen](./media/service-tiers-vcore/configure-sql-database.png)

Välj önskad maskinvarugeneration:

  ![välj maskinvara](./media/service-tiers-vcore/select-hardware.png)


**Ändra maskinvarugenereringen för en befintlig SQL Database eller pool**

För en databas går du till sidan Översikt och väljer **länken Prisnivå:**

  ![ändra maskinvara](./media/service-tiers-vcore/change-hardware.png)

För en pool går du till sidan Översikt och väljer **Konfigurera**.

Följ stegen för att ändra konfigurationen och välj maskinvarugenerering enligt beskrivningen i föregående steg.

**Så här väljer du en maskinvarugeneration när du skapar en SQL Managed Instance**

Detaljerad information finns i [Skapa en SQL Managed Instance](../managed-instance/instance-create-quickstart.md).

På fliken **Grundläggande inställningar** väljer du länken **Konfigurera databas** i avsnittet Beräkning **+** lagring och väljer sedan önskad maskinvarugenerering:

  ![konfigurera SQL Managed Instance](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Ändra maskinvarugenereringen för en befintlig SQL Managed Instance**

# <a name="the-azure-portal"></a>[Azure-portalen](#tab/azure-portal)

På sidan SQL Managed Instance väljer du **länken Prisnivå** under avsnittet Inställningar

![ändra SQL Managed Instance maskinvara](./media/service-tiers-vcore/change-managed-instance-hardware.png)

På sidan Prisnivå kan du ändra maskinvarugenerering enligt beskrivningen i föregående steg.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande PowerShell-skript:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Mer information finns i [Kommandot Set-AzSqlInstance.](/powershell/module/az.sql/set-azsqlinstance)

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd följande CLI-kommando:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Mer information finns i [kommandot az sql mi update.](/cli/azure/sql/mi#az_sql_mi_update)

---

### <a name="hardware-availability"></a>Maskinvarutillgänglighet

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

Gen4-maskinvaran [håller på att fasas](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) ut och är inte längre tillgänglig för nya distributioner. Alla nya databaser måste distribueras på Gen5-maskinvara.

Gen5 är tillgängligt i alla offentliga regioner över hela världen.

#### <a name="fsv2-series"></a>Fsv2-serien

Fsv2-serien finns i följande regioner: Australien, centrala, Australien, centrala 2, Australien, östra, Australien, sydöstra, Brasilien, södra, Kanada, centrala, Asien, östra, USA, östra, Frankrike, centrala, Indien, centrala, Sydkorea, centrala, Sydkorea, södra, Europa, norra, Sydafrika, norra, Sydostasien, Storbritannien, södra, Storbritannien, västra, Europa, västra, USA, västra 2.


#### <a name="m-series"></a>M-serien

M-serien är tillgänglig i följande regioner: USA, östra, Europa, norra, Europa, västra, USA, västra 2.
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

#### <a name="dc-series"></a>DC-serien

> [!NOTE]
> DC-serien finns för närvarande i **offentlig förhandsversion.**

DC-serien är tillgänglig i följande regioner: Kanada, centrala, Kanada, östra, USA, östra, Europa, norra, Storbritannien, södra, Europa, västra, USA, västra.

Om du behöver DC-serien i en region som för närvarande [inte](quota-increase-request.md)stöds skickar du en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) enligt anvisningarna i Begär kvotökningar för Azure SQL Database och SQL Managed Instance .

## <a name="next-steps"></a>Nästa steg

Kom igång genom att gå till: 
- [Skapa en SQL Database med hjälp av Azure Portal](single-database-create-quickstart.md)
- [Skapa en SQL Managed Instance med hjälp av Azure Portal](../managed-instance/instance-create-quickstart.md)

Prisinformation finns på Azure SQL Database [sidan med priser.](https://azure.microsoft.com/pricing/details/sql-database/single/)

Mer information om de specifika beräknings- och lagringsstorlekar som är tillgängliga på tjänstnivåer för generell användning och affärskritisk finns i:

- [vCore-baserade resursbegränsningar för Azure SQL Database](resource-limits-vcore-single-databases.md).
- [vCore-baserade resursgränser för poolbaserade Azure SQL Database](resource-limits-vcore-elastic-pools.md).
- [vCore-baserade resursbegränsningar för Azure SQL Managed Instance](../managed-instance/resource-limits.md).
