---
title: Etablera nya klienter i en app för flera klienter
description: Lär dig att etablera och katalogisera nya klienter i en Azure SQL Database SaaS-app med flera innehavare
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 26add03929551c912b4d7b7cf10741d53333689a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780571"
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Lär dig hur du etablerar nya klienter och registrerar dem i katalogen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien får du lära dig hur du etablerar och katalogiserar SaaS mönster. Du får också lära dig hur de implementeras i Wingtip Ticket SaaS Database-användarspecifika program. Du skapar och initierar nya klient databaser och registrerar dem i programmets klient katalog. Katalogen är en databas som upprätthåller mappningen mellan SaaS-programmets många klienter och deras data. Katalogen spelar en viktig roll för att dirigera program-och hanterings begär anden till rätt databas.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Etablera en enda ny klient.
> * Etablera en batch med ytterligare klienter.


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip-biljetterna SaaS Database-per-klient-app distribueras. Information om hur du distribuerar den på mindre än fem minuter finns i [distribuera och utforska Wingtip Ticket SaaS Database-användarspecifika program](./saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introduktion till SaaS Catalog-mönstret

I ett SaaS program med en databas som har stöd för flera innehavare är det viktigt att veta var informationen för varje klient är lagrad. I SaaS Catalog-mönstret används en katalog databas för att lagra mappningen mellan varje klient organisation och databasen där deras data lagras. Det här mönstret gäller när klient data distribueras över flera databaser.

Varje klient identifieras av en nyckel i katalogen som mappas till platsen för databasen. I Wingtip biljetter-appen skapas nyckeln från en hash av klientens namn. Det här schemat tillåter att appen skapar nyckeln från klient namnet som ingår i programmets URL. Andra klient nyckel scheman kan användas.

Katalogen gör det möjligt att ändra namnet eller platsen för databasen med minimal påverkan på programmet. I en databas modell med flera innehavare kan den här funktionen också flytta en klient mellan-databaser. Katalogen kan också användas för att ange om en klient eller databas är offline för underhåll eller andra åtgärder. Den här funktionen finns i [själv studie kursen för att återställa en enda klient](saas-dbpertenant-restore-single-tenant.md).

Katalogen kan också lagra ytterligare klient-eller databas-metadata, till exempel schema version, service plan eller service avtal som erbjuds till klienter. Katalogen kan lagra annan information som möjliggör program hantering, kund support eller DevOps.

Katalogen kan aktivera databas verktyg utanför SaaS-programmet. I Wingtip Ticket SaaS-databas per klient exempel används katalogen för att aktivera frågor över flera klienter, som visas i [självstudien om ad hoc-rapportering](saas-tenancy-cross-tenant-reporting.md). Jobb hantering i flera databaser utforskas i självstudierna [schema hantering](saas-tenancy-schema-management.md) och [klient analys](saas-tenancy-tenant-analytics.md) .

I Wingtip Ticket SaaS-exempel implementeras katalogen med hjälp av Shard hanterings funktioner i [Elastic Database klient bibliotek (EDCL)](elastic-database-client-library.md). EDCL är tillgänglig i Java och .NET Framework. EDCL gör det möjligt för ett program att skapa, hantera och använda en Shard-karta för databas.

En Shard-karta innehåller en lista över Shards (databaser) och mappningen mellan nycklar (klienter) och Shards. EDCL-funktioner används vid klient etablering för att skapa poster i Shard-kartan. De används vid körnings tillfället av program för att ansluta till rätt databas. EDCL cachelagrar anslutnings information för att minimera trafik till katalog databasen och påskynda programmet.

> [!IMPORTANT]
> Mappnings data är tillgängliga i katalog databasen, men *du kan inte redigera den*. Redigera mappnings data genom att endast använda Elastic Database klient biblioteks-API: er. Direkt manipulering av mappnings data risker som skadar katalogen och stöds inte.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introduktion till SaaS-etablerings mönster

När du lägger till en ny klient i ett SaaS-program som använder en databas modell med en enda klient måste du etablera en ny klient databas. Databasen måste skapas på rätt plats och på tjänst nivå. Det måste också initieras med lämpligt schema-och referens data. Och måste registreras i katalogen under lämplig klient nyckel.

Olika metoder för databas etablering kan användas. Du kan köra SQL-skript, distribuera en BACPAC eller kopiera en mall-databas.

Databas etableringen måste ingå i din strategi för schema hantering. Du måste se till att nya databaser är etablerade med det senaste schemat. Det här kravet har upptäckts i [självstudien om schema hantering](saas-tenancy-schema-management.md).

Programmet Wingtip ticks databas-per-klient etablerar nya klienter genom att kopiera en mall-databas med namnet _basetenantdb_, som distribueras på katalog servern. Etableringen kan integreras i programmet som en del av registrerings upplevelsen. Det kan också stödjas offline med hjälp av skript. I den här självstudien utforskas etablering med hjälp av PowerShell.

Med etablerings skript kopieras _basetenantdb_ -databasen för att skapa en ny klient databas i en elastisk pool. Klient databasen skapas i klient servern som är mappad till _newtenant_ DNS-alias. Det här aliaset innehåller en referens till den server som används för att etablera nya klienter och uppdateras för att peka på en återställnings klient server i självstudierna om haveri beredskap ([Dr med hjälp av omåterställ](./saas-dbpertenant-dr-geo-restore.md), [Dr med hjälp av replikering](./saas-dbpertenant-dr-geo-replication.md)). Skripten initierar sedan databasen med klient information och registrerar den i katalogen Shard Map. Klient databaser får namn baserat på klient organisationens namn. Detta namngivnings schema är inte en kritisk del av mönstret. Katalogen mappar klient nyckeln till databas namnet, så att alla namngivnings konventioner kan användas.


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip-biljetterna SaaS Database-användarspecifika program skript

Wingtip Ticket SaaS-skript och program käll kod är tillgängliga i [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub lagrings platsen. Ta en titt på den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera Wingtip Ticket SaaS-skript.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Etablera och katalogisera detaljerad genomgång

Om du vill förstå hur Wingtip biljetter-programmet implementerar ny klient etablering, lägger du till en Bryt punkt och följer arbets flödet medan du etablerar en klient.

1. I PowerShell ISE öppnar du... \\ \\ProvisionAndCatalog \\ _Demo-ProvisionAndCatalog.ps1_ och ange följande parametrar:

   * **$TenantName** = namnet på den nya platsen (till exempel *Bushwillow Blues*).
   * **$VenueType** = en av de fördefinierade plats typerna: _blått, ClassicalMusic, kontrollen åt, jazz, Judo, bil racing, Multipurpose, Opera, rockmusic, fotboll_.
   * **$DemoScenario**  =  **1**, *etablera en enda klient*.

2. Om du vill lägga till en Bryt punkt placerar du markören var som helst på den rad som står för *ny klient*. Tryck sedan på F9.

   ![Skärm bild som visar ett skript med New-Tenant markerat för att lägga till en Bryt punkt.](./media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Tryck på F5 för att köra skriptet.

4. När skript körningen stoppas vid Bryt punkten trycker du på F11 för att stega in i koden.

   ![Felsökning](./media/saas-dbpertenant-provision-and-catalog/debug.png)



Spåra skript körningen med hjälp av meny alternativen för **fel sökning** . Tryck på F10 och F11 för att gå över eller till de anropade funktionerna. Mer information om hur du felsöker PowerShell-skript finns i [tips om att arbeta med och felsöka PowerShell-skript](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).


Du behöver inte uttryckligen följa det här arbets flödet. Den förklarar hur du felsöker skriptet.

* **Importera modulen CatalogAndDatabaseManagement. psm1.** Det ger en abstraktion av katalogen och klient nivån över [hanterings](elastic-scale-shard-map-management.md) funktionerna för Shard. Den här modulen kapslar in mycket av katalog mönstret och är värt att utforska.
* **Importera modulen SubscriptionManagement. psm1.** Den innehåller funktioner för att logga in på Azure och välja den Azure-prenumeration som du vill arbeta med.
* **Hämta konfigurations information.** Gå till Get-Configuration med hjälp av F11 och se hur appens konfiguration anges. Resurs namn och andra AppData-värden definieras här. Ändra inte värdena förrän du är van vid skripten.
* **Hämta katalogobjektet.** Stega in i get-Catalog, som skapar och returnerar ett katalog objekt som används i skriptet på högre nivå. Den här funktionen använder Shard hanterings funktioner som importeras från **AzureShardManagement. psm1**. Katalogobjektet består av följande element:

   * $catalogServerFullyQualifiedName konstrueras med hjälp av standard skaftet plus ditt användar namn: _katalog- \<user\> . databas. Windows .net_.
   * $catalogDatabaseName hämtas från konfigurationen: *tenantcatalog*.
   * $shardMapManager-objektet initieras från katalogdatabasen.
   * $shardMap-objektet initieras från fragmentkartan _tenantcatalog_ i katalogdatabasen. Ett katalog objekt består och returneras. Den används i skriptet på högre nivå.
* **Beräkna den nya klient nyckeln.** En hash-funktion används för att skapa klientnyckeln från klientnamnet.
* **Kontrol lera om klient nyckeln redan finns.** Katalogen kontrol leras för att se till att nyckeln är tillgänglig.
* **Klientdatabasen etableras med New-TenantDatabase.** Använd F11 för att gå till hur databasen är etablerad genom att använda en [Azure Resource Manager mall](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    Databasnamnet skapas från klientnamnet för att klargöra vilket fragment som tillhör vilken klient. Du kan också använda andra namngivnings konventioner för databaser. En Resource Manager-mall skapar en klient databas genom att kopiera en mall databas (_baseTenantDB_) på katalog servern. Alternativt kan du skapa en databas och initiera den genom att importera en BACPAC. Eller så kan du köra ett initierings skript från en välkänd plats.

    Resource Manager-mallen finns i mappen. ..\Learning Modules\Common\: *tenantdatabasecopytemplate.jspå*

* **Klient databasen initieras ytterligare.** Platsens namn och plats typen läggs till. Du kan också göra andra initieringar här.

* **Klient databasen är registrerad i katalogen.** Den har registrerats med *Add-TenantDatabaseToCatalog* med hjälp av klient nyckeln. Använd F11 för att stega in i detaljerna:

    * Katalogdatabasen läggs till i fragmentkartan (listan över kända databaser).
    * Mappningen som länkar nyckelvärdet till fragmentet skapas.
    * Ytterligare metadata om innehavaren (platsens namn) läggs till i tabellen innehavare i katalogen. Tabellen innehavare är inte en del av Shard Management schema och installeras inte av EDCL. Den här tabellen illustrerar hur katalog databasen kan utökas för att stödja ytterligare programspecifika data.


När etableringen har slutförts återgår körningen till det ursprungliga *demo-ProvisionAndCatalog-* skriptet. Sidan **händelser** öppnas för den nya klienten i webbläsaren.

   ![Sidan händelser](./media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Etablera en batch med klienter

Den här övningen etablerar en batch med 17 klienter. Vi rekommenderar att du etablerar den här batchen av klienter innan du påbörjar andra Wingtip-biljetter SaaS-självstudier för databas per klient. Det finns mer än bara några få databaser att arbeta med.

1. I PowerShell ISE öppnar du... \\ ProvisionAndCatalogDemo-ProvisionAndCatalog.ps1för inlärnings moduler \\ \\ **. Ändra parametern *$DemoScenario* till 3:

   * **$DemoScenario**  =  **3**, *etablera en batch med klienter*.
2. Tryck på F5 för att köra skriptet.

Skriptet etablerar en batch med ytterligare klienter. Den använder en [Azure Resource Manager-mall](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) som styr batchen och delegerar etablering av varje databas till en länkad mall. Om du använder mallar på det här sättet, kan Azure Resource Manager mäkla etableringsprocessen för ditt skript. Mallarna etablerar databaser parallellt och hanterar nya försök, om det behövs. Skriptet är idempotenta, så om det Miss lyckas eller stoppas av någon anledning kan du köra det igen.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Verifiera batchen av de klienter som har distribuerats

* I [Azure Portal](https://portal.azure.com)bläddrar du till listan med servrar och öppnar *tenants1* -servern. Välj **SQL-databaser** och kontrol lera att batchen för 17 ytterligare databaser finns nu i listan.

   ![Lista över databaser](./media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

Andra etablerings mönster som inte ingår i den här självstudien:

**För etablering av databaser**: för för etablerings mönster utnyttjar faktum att databaser i en elastisk pool inte lägger till extra kostnad. Faktureringen är för den elastiska poolen, inte databaserna. Inaktiva databaser förbrukar inga resurser. Genom att förkonfigurera databaser i en pool och tilldela dem vid behov kan du minska tiden för att lägga till klienter. Antalet företablerade databaser kan justeras efter behov för att behålla en buffert som är lämplig för den förväntade etablerings hastigheten.

**Automatisk etablering**: i det automatiska etablerings mönstret etablerar en etablerings tjänst servrar, pooler och databaser automatiskt efter behov. Om du vill kan du inkludera för etablering av databaser i elastiska pooler. Om databaserna tas ur bruk och tas bort kan luckor i elastiska pooler fyllas av etablerings tjänsten. En sådan tjänst kan vara enkel eller komplex, till exempel hanterings etablering över flera geografiska områden och konfigurera geo-replikering för haveri beredskap.

Med det automatiska etablerings mönstret skickar ett klient program eller-skript en etablerings förfrågan till en kö som ska bearbetas av etablerings tjänsten. Tjänsten avsöker sedan tjänsten för att fastställa slut för ande. Om för etablering används, hanteras förfrågningar snabbt. Tjänsten etablerar en ersättnings databas i bakgrunden.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Etablera en enda ny klient.
> * Etablera en batch med ytterligare klienter.
> * Stega in i informationen om att etablering av klienter och registrera dem i katalogen.

Prova [själv studie kursen om prestanda övervakning](./saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip-biljetter SaaS-program för databas per klient](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Klientbibliotek för elastiska databaser](elastic-database-client-library.md)
* [Felsöka skript i Windows PowerShell ISE](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)