---
title: Löpande programuppgraderingar
description: Lär dig hur du använder Azure SQL Database geo-replikering för att stödja löpande uppgraderingar av ditt moln program
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, sstein
ms.date: 02/13/2019
ms.openlocfilehash: b7d21852ad684782fa1cb917442fee236d3c882b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502152"
---
# <a name="manage-rolling-upgrades-of-cloud-applications-by-using-sql-database-active-geo-replication"></a>Hantera löpande uppgraderingar av moln program med SQL Database aktiv geo-replikering
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Lär dig hur du använder [aktiv geo-replikering](auto-failover-group-overview.md) i Azure SQL Database för att aktivera rullande uppgraderingar av ditt moln program. Eftersom uppgraderingar är störande åtgärder bör de vara en del av din planering och utformning av verksamhets kontinuitet. I den här artikeln tittar vi på två olika metoder för att samordna uppgraderings processen och diskutera fördelarna och kompromisserna med varje alternativ. I den här artikeln hänvisar vi till ett program som består av en webbplats som är ansluten till en enda databas som dess datanivå. Vårt mål är att uppgradera version 1 (v1) av programmet till version 2 (v2) utan någon betydande inverkan på användar upplevelsen.

När du utvärderar uppgraderings alternativ bör du tänka på följande faktorer:

* Påverkan på program tillgänglighet under uppgraderingar, till exempel hur länge programfunktioner kan vara begränsade eller försämrade.
* Möjlighet att återställa igen om uppgraderingen Miss lyckas.
* Sårbarhet av programmet om ett orelaterade oåterkalleligt fel inträffar under uppgraderingen.
* Total dollar kostnad. Den här faktorn inkluderar ytterligare databasens redundans och stegvisa kostnader för de temporära komponenter som används av uppgraderings processen.

## <a name="upgrade-applications-that-rely-on-database-backups-for-disaster-recovery"></a>Uppgradera program som förlitar sig på databas säkerhets kopieringar för haveri beredskap

Om ditt program är beroende av automatisk säkerhets kopiering av databasen och använder geo-återställning för haveri beredskap, distribueras det till en enda Azure-region. Du kan minimera användar avbrott genom att skapa en mellanlagrings miljö i regionen med alla program komponenter som ingår i uppgraderingen. Det första diagrammet illustrerar drift miljön före uppgraderings processen. Slut punkten `contoso.azurewebsites.net` representerar en produktions miljö för webbappen. Om du vill kunna återställa uppgraderingen måste du skapa en mellanlagringsplats med en helt synkroniserad kopia av databasen. Följ de här stegen för att skapa en utvecklings miljö för uppgraderingen:

1. Skapa en sekundär databas i samma Azure-region. Övervaka den sekundära för att se om initierings processen har slutförts (1).
2. Skapa en ny miljö för din webbapp och anropa den "mellanlagringen". Den registreras i Azure DNS med URL `contoso-staging.azurewebsites.net` (2).

> [!NOTE]
> Dessa förberedelse steg påverkar inte produktions miljön, som kan fungera i full åtkomst läge.

![Diagram visar SQL Database geo-replikeringskonfiguration för haveri beredskap för molnet.](./media/manage-application-rolling-upgrade/option1-1.png)

När förberedelse stegen har slutförts är programmet redo för den faktiska uppgraderingen. Nästa diagram illustrerar stegen som ingår i uppgraderings processen:

1. Ställ in den primära databasen till skrivskyddat läge (3). Det här läget garanterar att webbappens produktions miljö (v1) förblir skrivskyddad under uppgraderingen, vilket förhindrar data avvikelser mellan v1-och v2-databas instanserna.
2. Koppla från den sekundära databasen med hjälp av planerat avslutnings läge (4). Den här åtgärden skapar en helt synkroniserad, oberoende kopia av den primära databasen. Den här databasen kommer att uppgraderas.
3. Aktivera den sekundära databasen till Read-Write-läge och kör uppgraderings skriptet (5).

![Diagrammet visar SQL Database geo-replikeringskonfiguration för haveri beredskap för molnet som kör uppgraderings skriptet.](./media/manage-application-rolling-upgrade/option1-2.png)

Om uppgraderingen har slutförts är du nu redo att växla användare till den uppgraderade kopian av programmet, vilket blir en produktions miljö. Växling innebär några fler steg, som du ser i nästa diagram:

1. Aktivera en växlings åtgärd mellan produktions-och mellanlagrings miljöer i webbappen (6). Den här åtgärden växlar URL: er för de två miljöerna. `contoso.azurewebsites.net`Pekar nu på v2-versionen av webbplatsen och databasen (produktions miljön). 
2. Om du inte längre behöver v1-versionen, som blev en mellanlagrings kopia efter växlingen, kan du inaktivera mellanlagrings miljön (7).

![SQL Database geo-replikeringskonfiguration för haveri beredskap för molnet.](./media/manage-application-rolling-upgrade/option1-3.png)

Om uppgraderings processen Miss lyckas (till exempel på grund av ett fel i uppgraderings skriptet) bör du tänka på att mellanlagrings miljön skulle komprometteras. Om du vill återställa programmet till för uppgraderings tillstånd återställer du programmet i produktions miljön till fullständig åtkomst. Nästa diagram visar omversions stegen:

1. Ange databas kopian till Read-Write-läge (8). Med den här åtgärden återställs alla v1-funktioner i produktions kopian.
2. Utför rotor Saks analysen och inaktivera mellanlagrings miljön (9).

I det här läget fungerar programmet fullständigt och du kan upprepa uppgraderings stegen.

> [!NOTE]
> Återställningen kräver inte DNS-ändringar eftersom du ännu inte utförde en växlings åtgärd.

![Diagrammet visar SQL Database geo-replikeringskonfiguration för haveri beredskap i molnet med den mellanlagrade miljön.](./media/manage-application-rolling-upgrade/option1-4.png)

Den främsta fördelen med det här alternativet är att du kan uppgradera ett program i en enda region genom att följa en uppsättning enkla steg. Dollar kostnaden för uppgraderingen är relativt låg. 

Den största kompromissen är att om ett oåterkalleligt fel uppstår under uppgraderingen, innebär återställningen till föruppgraderings statusen att programmet distribueras i en annan region och att databasen återställs från säkerhets kopian med hjälp av Geo-återställning. Den här processen resulterar i betydande stillestånds tid.

## <a name="upgrade-applications-that-rely-on-database-geo-replication-for-disaster-recovery"></a>Uppgradera program som förlitar sig på databas geo-replikering för katastrof återställning

Om programmet använder aktiv geo-replikering eller grupper för automatisk redundans för affärs kontinuitet, distribueras de till minst två olika regioner. Det finns en aktiv, primär databas i en primär region och en skrivskyddad, sekundär databas i en säkerhets kopierings region. Tillsammans med de faktorer som anges i början av den här artikeln måste uppgraderings processen även garantera att:

* Programmet förblir skyddat från oåterkalleliga försök hela tiden under uppgraderings processen.
* De geo-redundanta komponenterna i programmet uppgraderas parallellt med de aktiva komponenterna.

För att uppnå dessa mål kan du, förutom att använda Web Appss miljöer, dra nytta av Azure Traffic Manager genom att använda en växlings profil med en aktiv slut punkt och en slut punkt för säkerhets kopiering. Nästa diagram illustrerar drift miljön före uppgraderings processen. Webbplatser `contoso-1.azurewebsites.net` och `contoso-dr.azurewebsites.net` representerar en produktions miljö för programmet med fullständig geografisk redundans. Produktions miljön innehåller följande komponenter:

* Produktions miljön för webbappen `contoso-1.azurewebsites.net` i den primära regionen (1)
* Den primära databasen i den primära regionen (2)
* En standby-instans av webbappen i säkerhets kopierings regionen (3)
* Den geo-replikerade sekundära databasen i säkerhets kopierings regionen (4)
* En Traffic Manager prestanda profil med en online-slutpunkt `contoso-1.azurewebsites.net` som kallas och en offline-slutpunkt som kallas `contoso-dr.azurewebsites.net`

För att göra det möjligt att återställa uppgraderingen måste du skapa en mellanlagrings miljö med en helt synkroniserad kopia av programmet. Eftersom du måste se till att programmet snabbt kan återställas om det uppstår ett oåterkalleligt fel under uppgraderings processen, måste mellanlagrings miljön vara Geo-redundant också. Följande steg krävs för att skapa en utvecklings miljö för uppgraderingen:

1. Distribuera en mellanlagrings miljö för webbappen i den primära regionen (6).
2. Skapa en sekundär databas i den primära Azure-regionen (7). Konfigurera utvecklings miljön för webbappen för att ansluta till den. 
3. Skapa en annan Geo-redundant, sekundär databas i säkerhets kopierings regionen genom att replikera den sekundära databasen i den primära regionen. (Den här metoden kallas *kedjad geo-replikering*.) (8).
4. Distribuera en mellanlagrings miljö för webb program instansen i säkerhets kopierings regionen (9) och konfigurera den för att ansluta den geo-redundanta sekundära databasen som skapades på (8).

> [!NOTE]
> Dessa förberedelse steg påverkar inte programmet i produktions miljön. Den fungerar fortfarande fullständigt i Läs-och skriv läge.

![Diagrammet visar SQL Database geo-replikeringskonfiguration för haveri beredskap för molnet med en helt synkroniserad kopia av programmet.](./media/manage-application-rolling-upgrade/option2-1.png)

När förberedelse stegen är klara är utvecklings miljön redo för uppgraderingen. Nästa diagram illustrerar dessa uppgraderings steg:

1. Ställ in den primära databasen i produktions miljön i skrivskyddat läge (10). Det här läget garanterar att produktions databasen (v1) inte ändras under uppgraderingen, vilket hindrar data avvikelserna mellan v1-och v2-databas instanserna.

```sql
-- Set the production database to read-only mode
ALTER DATABASE <Prod_DB>
SET (ALLOW_CONNECTIONS = NO)
```

2. Avsluta geo-replikering genom att koppla från den sekundära (11). Den här åtgärden skapar en oberoende men helt synkroniserad kopia av produktions databasen. Den här databasen kommer att uppgraderas. I följande exempel används Transact-SQL men [PowerShell](/powershell/module/az.sql/remove-azsqldatabasesecondary) är också tillgängligt. 

```sql
-- Disconnect the secondary, terminating geo-replication
ALTER DATABASE <Prod_DB>
REMOVE SECONDARY ON SERVER <Partner-Server>
```

3. Kör uppgraderings skriptet mot `contoso-1-staging.azurewebsites.net` , `contoso-dr-staging.azurewebsites.net` och den primära mellanlagrings databasen (12). Databas ändringarna replikeras automatiskt till den sekundära mellanlagringen.

![Diagrammet visar SQL Database geo-replikeringskonfiguration för haveri beredskap i molnet med databas ändringar som repliker ATS till mellanlagring.](./media/manage-application-rolling-upgrade/option2-2.png)

Om uppgraderingen har slutförts är du nu redo att växla användare till version 2 av programmet. Nästa diagram illustrerar stegen som ingår:

1. Aktivera en växlings åtgärd mellan produktions-och mellanlagringsplatserna i webbappen i den primära regionen (13) och i säkerhets kopierings regionen (14). V2 för programmet blir nu en produktions miljö med en redundant kopia i säkerhets kopierings regionen.
2. Om du inte längre behöver v1-programmet (15 och 16) kan du inaktivera mellanlagrings miljön.

![Diagrammet visar SQL Database geo-replikeringskonfiguration för haveri beredskap för molnet med valfri inaktive ring av utvecklings miljön.](./media/manage-application-rolling-upgrade/option2-3.png)

Om uppgraderings processen Miss lyckas (till exempel på grund av ett fel i uppgraderings skriptet) bör du tänka på att mellanlagrings miljön är i ett inkonsekvent tillstånd. Återgå till att använda v1 av programmet i produktions miljön för att återställa programmet till tillstånd för för uppgradering. De steg som krävs visas i nästa diagram:

1. Ange den primära databas kopian i produktions miljön till Read-Write-läge (17). Med den här åtgärden återställs fullständiga v1-funktioner i produktions miljön.
2. Utför rotor Saks analysen och reparera eller ta bort mellanlagrings miljön (18 och 19).

I det här läget fungerar programmet fullständigt och du kan upprepa uppgraderings stegen.

> [!NOTE]
> Återställningen kräver inte DNS-ändringar eftersom du inte genomförde en växlings åtgärd.

![Diagrammet visar SQL Database geo-replikeringskonfiguration för haveri beredskap för molnet med uppgraderings processen återställd.](./media/manage-application-rolling-upgrade/option2-4.png)

Fördelen med det här alternativet är att du kan uppgradera både programmet och dess geo-redundanta kopia parallellt utan att kompromissa med affärs kontinuiteten under uppgraderingen.

Den största kompromissen är att den kräver dubbel redundans av varje program komponent och därmed debiteras högre dollar kostnad. Det innebär också ett mer komplicerat arbets flöde.

## <a name="summary"></a>Sammanfattning

De två uppgraderings metoder som beskrivs i artikeln skiljer sig från komplexitets-och kronornas kostnader, men båda fokuserar på att minimera hur länge användaren är begränsad till skrivskyddade åtgärder. Den tiden definieras direkt av uppgraderings skriptets varaktighet. Den är inte beroende av databasens storlek, den tjänst nivå du valde, webbplats konfigurationen eller andra faktorer som du inte kan styra. Alla förberedelse steg frigörs från uppgraderings stegen och påverkar inte produktions programmet. Effektiviteten i uppgraderings skriptet är en viktig faktor som avgör användar upplevelsen under uppgraderingar. Det bästa sättet att förbättra den här upplevelsen är att fokusera på dina ansträngningar för att göra uppgraderings skriptet så effektivt som möjligt.

## <a name="next-steps"></a>Nästa steg

* En översikt över kontinuitet och scenarier för affärs kontinuitet finns i [Översikt över verksamhets kontinuitet](business-continuity-high-availability-disaster-recover-hadr-overview.md).
* Mer information om Azure SQL Database aktiv geo-replikering finns i [skapa läsbara sekundära databaser med aktiv geo-replikering](active-geo-replication-overview.md).
* Läs mer om Azure SQL Database grupper för automatisk redundans i [Använd grupper för automatisk redundans för att aktivera transparent och koordinerad redundansväxling av flera databaser](auto-failover-group-overview.md).
* Information om mellanlagrings miljöer i Azure App Service finns i [Konfigurera mellanlagrings miljöer i Azure App Service](../../app-service/deploy-staging-slots.md).
* Mer information om Azure Traffic Manager-profiler finns i [hantera en Azure Traffic Manager-profil](../../traffic-manager/traffic-manager-manage-profiles.md).
