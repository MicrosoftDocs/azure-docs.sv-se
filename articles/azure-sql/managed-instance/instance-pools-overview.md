---
title: Vad är en Azure SQL-hanterad instans av pool?
titleSuffix: Azure SQL Managed Instance
description: Lär dig mer om Azure SQL Managed instance Pools (för hands version), en funktion som ger ett bekvämt och kostnads effektivt sätt att migrera mindre SQL Server databaser till molnet i stor skala och hantera flera hanterade instanser.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: bc345509db1c2a14afb0ae781eccad8f77395c18
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97347072"
---
# <a name="what-is-an-azure-sql-managed-instance-pool-preview"></a>Vad är en Azure SQL-hanterad instans pool (för hands version)?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Instans pooler i Azure SQL Managed instance är ett bekvämt och kostnads effektivt sätt att migrera mindre SQL Server instanser till molnet i stor skala.

Med instanspooler kan du företablera beräkningsresurser enligt dina totala migreringskrav. Du kan sedan distribuera flera enskilda hanterade instanser upp till din företablerade beräkningsnivå. Om du till exempel företablerar 8-virtuella kärnor kan du distribuera 2 2-vCore och 1 4-vCore-instans och sedan migrera databaserna till dessa instanser. Innan instans pooler var tillgängliga måste mindre och mindre beräknings intensiva arbets belastningar ofta samlas in i en större hanterad instans vid migrering till molnet. Behovet av att migrera grupper av databaser till en stor instans kräver normalt noggrann kapacitets planering och resurs styrning, ytterligare säkerhets överväganden och viss extra data konsoliderings åtgärd på instans nivå.

Dessutom stöder instans-pooler inbyggd VNet-integrering så att du kan distribuera flera instanser av pooler och flera instanser i samma undernät.

## <a name="key-capabilities"></a>De viktigaste funktionerna

Instans pooler ger följande fördelar:

1. Möjlighet att vara värd för 2 vCore instanser. *\* Endast för instanser i instans-poolerna*.
2. Tid för förutsägbar och snabb instans distribution (upp till 5 minuter).
3. Minimal allokering av IP-adress.

Följande diagram illustrerar en instans-pool med flera hanterade instanser som distribueras i ett virtuellt nätverks under nät.

![instans-pool med flera instanser](./media/instance-pools-overview/instance-pools1.png)

Instans pooler möjliggör distribution av flera instanser på samma virtuella dator, där den virtuella datorns beräknings storlek baseras på det totala antalet allokerade virtuella kärnor för poolen. Den här arkitekturen gör det möjligt att *partitionera* den virtuella datorn i flera instanser, vilket kan vara vilken storlek som helst, inklusive 2 virtuella kärnor (2-vCore-instanser är bara tillgängliga för instanser i pooler).

Efter den första distributionen är hanterings åtgärder på instanser i en pool mycket snabbare. Detta beror på att distribution eller tillägg för ett [virtuellt kluster](connectivity-architecture-overview.md#high-level-connectivity-architecture) (dedikerade uppsättning virtuella datorer) inte ingår i etableringen av den hanterade instansen.

Eftersom alla instanser i en pool delar samma virtuella dator, beror den totala IP-allokeringen inte på antalet distribuerade instanser, vilket är praktiskt för distribution i undernät med ett smalt IP-intervall.

Varje pool har en fast IP-allokering av enbart nio IP-adresser (exklusive de fem IP-adresserna i under nätet som är reserverade för egna behov). Mer information finns i [krav på under näts storlek för enskilda instanser](vnet-subnet-determine-size.md).

## <a name="application-scenarios"></a>Programscenarier

I följande lista visas de viktigaste användnings fallen där instanser av instanser bör övervägas:

- Migrering av *en grupp SQL Server instanser* på samma gång, där majoriteten är en mindre storlek (till exempel 2 eller 4 virtuella kärnor).
- Scenarier där *förutsägbar och kort instansen skapas eller skalning* är viktigt. Till exempel distribution av en ny klient i en SaaS program miljö med flera innehavare som kräver funktioner på instans nivå.
- Scenarier där det är viktigt att ha en *fast kostnad* eller en *utgifts gräns* . Du kan till exempel köra delade dev-test-eller demo miljöer med en fast (eller sällan ändrad) storlek, där du regelbundet distribuerar hanterade instanser när det behövs.
- Scenarier där *minimal IP* -adressallokering i ett VNet-undernät är viktigt. Alla instanser i en pool delar en virtuell dator, så antalet allokerade IP-adresser är lägre än för enskilda instanser.

## <a name="architecture"></a>Arkitektur

Instans pooler har liknande arkitektur för vanliga (*enskilda*) hanterade instanser. För att stödja [distributioner i virtuella Azure-nätverk](../../virtual-network/virtual-network-for-azure-services.md) och för att tillhandahålla isolering och säkerhet för kunder, förlitar sig instansen på [virtuella kluster](connectivity-architecture-overview.md#high-level-connectivity-architecture). Virtuella kluster representerar en dedikerad uppsättning isolerade virtuella datorer som distribueras i kundens virtuella nätverks undernät.

Den största skillnaden mellan de två distributions modellerna är att instans pooler tillåter flera SQL Server process distributioner på samma nod för virtuella datorer, som är en resurs som styrs med hjälp av [Windows-jobbobjektet](/windows/desktop/ProcThread/job-objects), medan enskilda instanser alltid är ensamma på en nod för virtuella datorer.

Följande diagram visar en instans-pool och två enskilda instanser som distribueras i samma undernät och illustrerar huvud arkitektur information för båda distributions modellerna:

![Instans-pool och två enskilda instanser](./media/instance-pools-overview/instance-pools2.png)

Varje instans skapar ett separat virtuellt kluster under. Instanser i en pool och enkla instanser som distribueras i samma undernät delar inte ut beräknings resurser som är allokerade till SQL Server processer och gateway-komponenter, vilket garanterar prestanda som är förutsägbara.

## <a name="resource-limitations"></a>Resursbegränsningar

Det finns flera resursbegränsningar för instanspooler och instanser inuti pooler:

- Instans pooler är bara tillgängliga på Gen5-maskinvara.
- Hanterade instanser i en pool har dedikerad processor och RAM-minne, så det sammanställda antalet virtuella kärnor över alla instanser måste vara mindre än eller lika med antalet virtuella kärnor som allokerats till poolen.
- Alla [gränser på instans nivå](resource-limits.md#service-tier-characteristics) gäller för instanser som skapats i en pool.
- Utöver gränser på instans nivå finns det också två begränsningar som gäller för *den instansen av filpool*:
  - Total lagrings storlek per pool (8 TB).
  - Totalt antal användar databaser per pool. Den här begränsningen beror på poolens virtuella kärnor-värde:
    - 8 virtuella kärnor-poolen har stöd för upp till 200 databaser,
    - 16 virtuella kärnor-poolen har stöd för upp till 400 databaser,
    - 24 och större virtuella kärnor-pool har stöd för upp till 500 databaser.
- Det går inte att ange AAD-administratör för de instanser som distribuerats i instansen. Därför går det inte att använda AAD-autentisering.

Den totala lagrings tilldelningen och antalet databaser i alla instanser måste vara lägre än eller lika med de gränser som exponeras av instans-pooler.

- Instans grupper stöder 8, 16, 24, 32, 40, 64 och 80 virtuella kärnor.
- Hanterade instanser i pooler stöder 2, 4, 8, 16, 24, 32, 40, 64 och 80 virtuella kärnor.
- Hanterade instanser i pooler stöder lagrings storlekar mellan 32 GB och 8 TB, förutom:
  - 2 vCore-instanser stöder storlekar mellan 32 GB och 640 GB,
  - 4 vCore-instanser stöder storlekar mellan 32 GB och 2 TB.
- Hanterade instanser i pooler har en gräns på upp till 100 användar databaser per instans, förutom 2 vCore-instanser som stöder upp till 50 användar databaser per instans.

[Egenskapen för tjänst nivå](resource-limits.md#service-tier-characteristics) är associerad med den instansen av den här resursen, så alla instanser i en pool måste vara samma tjänst nivå som poolens tjänst nivå. För närvarande är det bara den Generell användning tjänst nivån tillgänglig (se följande avsnitt om begränsningar i den aktuella för hands versionen).

### <a name="public-preview-limitations"></a>Offentliga begränsningar i förhandsversionen

Den offentliga för hands versionen har följande begränsningar:

- För närvarande är endast den Generell användning tjänst nivån tillgänglig.
- Det går inte att skala instansen av instansen under den offentliga för hands versionen, så noggrann kapacitets planering före distributionen är viktigt.
- Azure Portal stöd för skapande av instans och konfiguration är inte tillgängligt ännu. Alla åtgärder på instans pooler stöds endast via PowerShell. Inledande instans distribution i en förskapad pool stöds också endast via PowerShell. När du har distribuerat till en pool kan hanterade instanser uppdateras med hjälp av Azure Portal.
- Det går inte att flytta hanterade instanser som skapats utanför poolen till en befintlig pool, och instanser som skapats i en pool kan inte flyttas utanför en enskild instans eller till en annan pool.
- Pris för [reserverad kapacitets](../database/reserved-capacity-overview.md) instans är inte tillgängligt.

## <a name="sql-features-supported"></a>SQL-funktioner som stöds

Hanterade instanser som skapats i pooler stöder samma [kompatibilitetsnivå och funktioner som stöds i enskilda hanterade instanser](sql-managed-instance-paas-overview.md#sql-features-supported).

Varje hanterad instans som distribueras i en pool har en separat instans av SQL-agenten.

Valfria funktioner eller funktioner som kräver att du väljer vissa värden (till exempel sortering på instans nivå, tidszon, en offentlig slut punkt för data trafik, Redundansrelationer) har kon figurer ATS på instans nivå och kan vara olika för varje instans i en pool.

## <a name="performance-considerations"></a>Saker att tänka på gällande prestanda

Även om hanterade instanser i pooler har dedikerade vCore och RAM-minne, delar de lokal disk (för tempdb-användning) och nätverks resurser. Det är inte troligt, men det är möjligt att uppleva *störningarnas intilliggande* påverkan om flera instanser i poolen har hög resursförbrukning på samma tid. Om du ser detta beteende bör du överväga att distribuera dessa instanser till en större pool eller som enskilda instanser.

## <a name="security-considerations"></a>Säkerhetsöverväganden

Eftersom instanser som distribueras i en pool delar samma virtuella dator kanske du vill överväga att inaktivera funktioner som ger högre säkerhets risker eller för att kontrol lera åtkomst behörigheterna till dessa funktioner ordentligt. Till exempel CLR-integrering, intern säkerhets kopiering och återställning, Database-e-post osv.

## <a name="instance-pool-support-requests"></a>Support begär Anden för instans pool

Skapa och hantera support förfrågningar för instans-pooler i [Azure Portal](https://portal.azure.com).

Om du har problem som rör distribution av instans-pool (skapa eller ta bort) ska du kontrol lera att du anger **instanstaggar** i fältet **problem under typ** .

![Support förfrågan för instans pooler](./media/instance-pools-overview/support-request.png)

Om du har problem som rör en enda hanterad instans eller databas inom en pool, bör du skapa ett vanligt support ärende för Azure SQL-hanterad instans.

Om du vill skapa större distributioner av SQL-hanterade instanser (med eller utan instans-pooler) kan du behöva skaffa en större regional kvot. Mer information finns i [begäran om kvot ökning för Azure SQL Database](../database/quota-increase-request.md). Distributions logiken för instans pooler jämför total vCore-förbrukning *på Poolnivå* mot kvoten för att avgöra om du får skapa nya resurser utan att ytterligare öka kvoten.

## <a name="instance-pool-billing"></a>Fakturering av instans pool

Med instans pooler kan du skala beräkning och lagring oberoende av varandra. Kunderna betalar för beräkning som är kopplad till den sammanställda virtuella kärnor och lagring som är kopplad till varje instans mätt i gigabyte (de första 32 GB är kostnads fria för varje instans).

vCore-priset för en pool debiteras oavsett hur många instanser som distribueras i poolen.

För beräknings priset (mätt i virtuella kärnor) är två pris alternativ tillgängliga:

  1. *Licens ingår*: pris på SQL Server licenser ingår. Detta gäller för kunder som väljer att inte tillämpa befintliga SQL Server licenser med Software Assurance.
  2. *Azure Hybrid-förmån*: ett reducerat pris som inkluderar Azure Hybrid-förmån för SQL Server. Kunder kan välja det här priset genom att använda sina befintliga SQL Server licenser med Software Assurance. Information om berättigande och annan information finns [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).

Det går inte att ange olika pris alternativ för enskilda instanser i en pool. Alla instanser i den överordnade poolen måste vara antingen på licens priset eller Azure Hybrid-förmån priset. Licens modellen för poolen kan ändras efter att poolen har skapats.

> [!IMPORTANT]
> Om du anger en licens modell för den instans som skiljer sig från poolen, används pool priset och värdet på instans nivå ignoreras.

Om du skapar instans pooler för [prenumerationer som är berättigade till utveckling och testning](https://azure.microsoft.com/pricing/dev-test/)får du automatiskt rabatterat pris på upp till 55 procent på Azure SQL-hanterad instans.

Fullständig information om priser för instans pool finns i avsnittet *instans pooler* på [pris sidan för SQL-hanterade instanser](https://azure.microsoft.com/pricing/details/sql-database/managed/).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång med instans grupper finns [SQL-hanterade instanser med instruktions guide](instance-pools-configure.md).
- Information om hur du skapar din första hanterade instans finns i [snabb starts guide](instance-create-quickstart.md).
- En lista över funktioner och jämförelse finns i [vanliga funktioner i Azure SQL](../database/features-comparison.md).
- Mer information om VNet-konfiguration finns i [konfiguration av SQL Managed instance VNet](connectivity-architecture-overview.md).
- En snabb start som skapar en hanterad instans och återställer en databas från en säkerhets kopia finns i [skapa en hanterad instans](instance-create-quickstart.md).
- En själv studie kurs om hur du använder Azure Database Migration Service för migrering finns i [migrering av SQL-hanterad instans med hjälp av Database migration service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Avancerad övervakning av SQL-hanterad instans databas prestanda med inbyggd fel söknings information finns i [övervaka Azure SQL-hanterad instans med Azure SQL-analys](../../azure-monitor/insights/azure-sql.md).
- Pris information finns i [priser för SQL-hanterad instans](https://azure.microsoft.com/pricing/details/sql-database/managed/).
