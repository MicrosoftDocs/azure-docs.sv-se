---
title: Etablera i SaaS multi-Tenant
description: Lär dig hur du etablerar och katalogiserar nya klienter i en Azure SQL Database SaaS-app för flera innehavare
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: eddb0c8339069025f0742e9bcbc371efbef094ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793338"
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-azure-sql-database"></a>Etablera och katalogisera nya klienter i ett SaaS-program med shardade för flera innehavare Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här artikeln beskriver etablering och katalogering av nya klienter i en databas modell eller ett mönster för *flera innehavares shardade-databaser* .

Den här artikeln innehåller två huvud delar:

- [Konceptuell diskussion](#goto_2_conceptual) om etablering och katalogering av nya klienter.

- [Självstudie](#goto_1_tutorial) som fokuserar på den PowerShell-skriptfil som utför etableringen och katalogiserar.
  - I självstudien används Wingtip ticks SaaS-programmet, som är anpassat till databas mönstret för flera innehavares shardade.

<a name="goto_2_conceptual"></a>

## <a name="database-pattern"></a>Databas mönster

I det här avsnittet, plus några fler som följer, diskuterar du begreppen för databas mönstret för flera innehavares shardade.

I den här shardade-modellen för flera innehavare innehåller tabell scheman i varje databas en klient nyckel i den primära nyckeln för tabeller som lagrar klient data. Klient nyckeln gör det möjligt för varje enskild databas att lagra 0, 1 eller många klienter. Användningen av shardade-databaser gör det enkelt för program systemet att stödja ett mycket stort antal klienter. Alla data för en klient organisation lagras i en databas. Det stora antalet klienter fördelas i många shardade-databaser. En katalog databas lagrar mappningen av varje klient till databasen.

#### <a name="isolation-versus-lower-cost"></a>Isolering jämfört med lägre kostnader

En klient som har en databas som är helt fristående utnyttjar fördelarna med isolering. Klienten kan återställa databasen till ett tidigare datum utan att begränsas av påverkan på andra klienter. Databas prestanda kan justeras för att optimera för en klient, återigen utan att kompromissa med andra klienter. Problemet är att isoleringen kostar mer än IT-kostnader för att dela en databas med andra klienter.

När en ny klient är etablerad kan den dela en databas med andra klienter, eller så kan den placeras i en egen ny databas. Senare kan du ändra dina saker och flytta databasen till den andra situationen.

Databaser med flera klienter och enskilda klienter är blandade i samma SaaS-program, för att optimera kostnader eller isolering för varje klient.

   ![Shardade för flera klient organisationer med klient katalogen](./media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)

## <a name="tenant-catalog-pattern"></a>Mönster för klient katalog

Om du har två eller flera databaser som varje innehåller minst en klient måste programmet ha ett sätt att identifiera vilken databas som ska lagra innehavaren av aktuellt intresse. En katalog databas lagrar mappningen.

#### <a name="tenant-key"></a>Klient nyckel

Wingtip-programmet kan härleda en unik nyckel för varje klient, vilket är klient nyckeln. Appen extraherar klient namnet från webb sidans URL. Appen hashar namnet för att hämta nyckeln. Appen använder nyckeln för att komma åt katalogen. Katalogen kors referens information om den databas där klienten lagras. Appen använder databas informationen för att ansluta. Andra klient nyckel scheman kan också användas.

Genom att använda en katalog kan du ändra namn eller plats för en klient databas som ska ändras efter etableringen utan att programmet har avbrutits. I en databas modell med flera klient organisationer kan katalogen flytta en klient mellan databaserna.

#### <a name="tenant-metadata-beyond-location"></a>Klientens metadata utanför platsen

Katalogen kan också indikera om en klient organisation är offline för underhåll eller andra åtgärder. Och katalogen kan utökas för att lagra ytterligare klient-eller databas-metadata, till exempel följande objekt:
- Tjänst nivån eller versionen av en databas.
- Databas schemats version.
- Klient organisationens namn och service avtal (service avtal).
- Information för att aktivera program hantering, kund support eller DevOps processer.

Katalogen kan också användas för att aktivera rapportering av flera innehavare, schema hantering och data utdrag i analys syfte.

### <a name="elastic-database-client-library"></a>Elastic Database klient bibliotek

I Wingtip implementeras katalogen i *tenantcatalog* -databasen. *Tenantcatalog* skapas med Shard-hanterings funktionerna i [Elastic Database klient biblioteket (EDCL)](elastic-database-client-library.md). Biblioteket gör det möjligt för ett program att skapa, hantera och använda en *Shard-karta* som lagras i en databas. En Shard-karta kors referenser till klient nyckeln med dess Shard, vilket innebär dess shardade-databas.

Under klient etableringen kan EDCL-funktioner användas från program eller PowerShell-skript för att skapa poster i Shard-kartan. Senare kan EDCL-funktionerna användas för att ansluta till rätt databas. EDCL cachelagrar anslutnings informationen för att minimera trafiken i katalog databasen och påskynda processen med att ansluta.

> [!IMPORTANT]
> Redigera *inte* data i katalog databasen via direkt åtkomst! Direkta uppdateringar stöds inte på grund av hög risk för skadade data. Redigera i stället mappnings data med EDCL-API: er.

## <a name="tenant-provisioning-pattern"></a>Organisations etablerings mönster

#### <a name="checklist"></a>Checklista

När du vill etablera en ny klient i en befintlig delad databas måste du ställa följande frågor i den delade databasen:
- Finns det tillräckligt med utrymme kvar för den nya klienten?
- Har det tabeller med nödvändiga referens data för den nya klienten, eller så kan data läggas till?
- Har den rätt variation för bas schemat för den nya klient organisationen?
- Finns det på rätt geografisk plats nära den nya innehavaren?
- Finns det på rätt tjänst nivå för den nya klient organisationen?

När du vill att den nya klienten ska isoleras i en egen databas kan du skapa den för att uppfylla specifikationerna för klient organisationen.

När etableringen har slutförts måste du registrera klienten i katalogen. Slutligen kan klient mappningen läggas till för att referera till lämpliga Shard.

#### <a name="template-database"></a>Mall databas

Etablera databasen genom att köra SQL-skript, distribuera en BACPAC eller kopiera en mall-databas. Wingtip Apps kopierar en mall för att skapa nya klient databaser.

I likhet med alla program utvecklas Wingtip med tiden. Vid ett tillfälle kräver Wingtip ändringar i databasen. Ändringarna kan innehålla följande objekt:
- Nytt eller ändrat schema.
- Nya eller ändrade referens data.
- Rutinmässig databas underhålls aktiviteter för att säkerställa optimala prestanda för appar.

Med ett SaaS-program, behöver de här ändringarna distribueras på ett samordnat sätt över en potentiellt massiv mängd klientdatabaser. För att dessa ändringar ska vara i framtida klient databaser måste de införlivas i etablerings processen. Den här utmaningen visas ytterligare i [själv studie kursen om schema hantering](saas-tenancy-schema-management.md).

#### <a name="scripts"></a>Skript

Klient etablerings skripten i den här självstudien har stöd för båda följande scenarier:
- Etablering av en klient organisation i en befintlig databas som delas med andra klienter.
- Etablering av en klient organisation i en egen databas.

Klient data initieras sedan och registreras i katalogen Shard-kartan. I exempel appen ges databaser som innehåller flera klienter ett generiskt namn, till exempel *tenants1* eller *tenants2*. Databaser som innehåller en enda klient tilldelas klientens namn. De angivna namngivnings konventionerna som används i exemplet är inte en kritisk del av mönstret, eftersom användningen av en katalog tillåter att alla namn tilldelas till databasen.

<a name="goto_1_tutorial"></a>

## <a name="tutorial-begins"></a>Självstudie börjar

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Etablera en klient organisation i en databas för flera innehavare
> * Etablera en klient organisation i en databas för en enda klient
> * Etablera en batch med klienter i både flera klienter och databaser med en enda klient organisation
> * Registrera en databas och klient mappning i en katalog

#### <a name="prerequisites"></a>Förutsättningar

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps)

- Wingtip biljetter SaaS-appen för flera klient organisationer har distribuerats. Om du vill distribuera på mindre än fem minuter läser du [distribuera och utforska Wingtip-biljetterna SaaS-databas program för flera innehavare](./saas-multitenantdb-get-started-deploy.md)

- Hämta Wingtip-skript och Källkod:
    - Wingtip-biljetterna SaaS-skript för flera klient organisationer och program käll kod är tillgängliga i [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub lagrings platsen.
    - Se den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera Wingtip-skript.

## <a name="provision-a-tenant-into-a-database-shared-with-other-tenants"></a>Etablera en klient i en databas som *delas* med andra klienter

I det här avsnittet visas en lista över de viktigaste åtgärderna för etablering som utförs av PowerShell-skripten. Sedan använder du PowerShell ISE-felsökaren för att gå igenom skripten för att se åtgärder i kod.

#### <a name="major-actions-of-provisioning"></a>Större åtgärder för etablering

Följande är viktiga element i det etablerings arbets flöde som du steg för steg:

- **Beräkna den nya klient nyckeln**: en hash-funktion används för att skapa klient nyckeln från klient namnet.
- **Kontrol lera att klient nyckeln redan finns**: katalogen kontrol leras för att se till att nyckeln inte redan har registrerats.
- **Initiera klienten i standard klient databasen**: klient databasen uppdateras för att lägga till den nya klient informationen.
- **Registrera klient i katalogen**: mappningen mellan den nya klient nyckeln och den befintliga tenants1-databasen läggs till i katalogen.
- **Lägg till klientens namn i en katalog tilläggs tabell**: plats namnet läggs till i tabellen innehavare i katalogen.  Det här tillägget visar hur katalog databasen kan utökas för att stödja ytterligare programspecifika data.
- **Öppna sidan händelser för den nya klienten**: sidan *Bushwillow blåa* händelser öppnas i webbläsaren.

   ![Skärm bild som visar sidan händelser för en ny klient.](./media/saas-multitenantdb-provision-and-catalog/bushwillow.png)

#### <a name="debugger-steps"></a>Fel söknings steg

För att förstå hur Wingtip-appen implementerar ny klient etablering i en delad databas, lägger du till en Bryt punkt och steg i arbets flödet:

1. I *POWERSHELL ISE* öppnar du... \\ \\ProvisionTenants \\ *Demo-ProvisionTenants.ps1* och ange följande parametrar:
   - **$TenantName**  =  **Bushwillow blått**, namnet på ett nytt evenemang.
   - **$VenueType**  =  **blått**, en av de fördefinierade plats typerna: blått, ClassicalMusic, kontrollen åt, jazz, Judo, motorracing, Multipurpose, Opera, rockmusic, fotboll (gemener, inga blank steg).
   - **$DemoScenario**  =  **1** för att etablera en klient i en delad databas med andra klienter.

2. Lägg till en Bryt punkt genom att placera markören var som helst på rad 38, raden med texten: *New-Tenant*, och tryck sedan på **F9**.

   ![Skärm bild som visar den rad som innehåller ny klient.](./media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

3. Kör skriptet genom att trycka på **F5**.

4. När skript körningen stoppas vid Bryt punkten trycker du på **F11** för att stega in i koden.

   ![Skärm bild som visar Windows PowerShell ISE med fel söknings menyn öppen och steget är markerat.](./media/saas-multitenantdb-provision-and-catalog/debug.png)

5. Spåra skript körningen med hjälp av meny alternativen för **fel sökning** , **F10** och **F11**, för att gå över eller till anropade funktioner.

Mer information om hur du felsöker PowerShell-skript finns i [tips om att arbeta med och felsöka PowerShell-skript](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

## <a name="provision-a-tenant-in-its-own-database"></a>Etablera en klient i en *egen* databas

#### <a name="major-actions-of-provisioning"></a>Större åtgärder för etablering

Följande är viktiga element i arbets flödet som du går igenom när du spårar skriptet:

- **Beräkna den nya klient nyckeln**: en hash-funktion används för att skapa klient nyckeln från klient namnet.
- **Kontrol lera att klient nyckeln redan finns**: katalogen kontrol leras för att se till att nyckeln inte redan har registrerats.
- **Skapa en ny klient databas**: databasen skapas genom att du kopierar *basetenantdb* -databasen med hjälp av en Resource Manager-mall.  Det nya databas namnet baseras på klientens namn.
- **Lägg till databas i katalog**: den nya klient organisations databasen registreras som en Shard i katalogen.
- **Initiera klienten i standard klient databasen**: klient databasen uppdateras för att lägga till den nya klient informationen.
- **Registrera klient organisation i katalogen**: mappningen mellan den nya klient nyckeln och *sequoiasoccer* -databasen läggs till i katalogen.
- **Klient organisationens namn läggs till i katalogen**: plats namnet läggs till i tilläggs tabellen innehavare i katalogen.
- **Öppna sidan händelser för den nya klienten**: sidan *Sequoia fotboll* Events öppnas i webbläsaren.

   ![händelser](./media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)

#### <a name="debugger-steps"></a>Fel söknings steg

Gå igenom skript processen när du skapar en klient i en egen databas:

1. Fortfarande i... \\ \\ProvisionTenants \\ *Demo-ProvisionTenants.ps1* ange följande parametrar:
   - **$TenantName**  =  **Sequoia fotboll**, namnet på ett nytt evenemang.
   - **$VenueType**  =  **fotboll**, en av de fördefinierade plats typerna: blått, ClassicalMusic, kontrollen åt, jazz, Judo, motorracing, Multipurpose, Opera, rockmusic, fotboll (gemener, inga blank steg).
   - **$DemoScenario**  =  **2** för att etablera en klient organisation i en egen databas.

2. Lägg till en ny Bryt punkt genom att placera markören var som helst på rad 57, raden med texten: *& &nbsp; $PSScriptRoot \new-tenantanddatabase '* och tryck på **F9**.

   ![brytpunkt](./media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

3. Kör skriptet genom att trycka på **F5**.

4. När skript körningen stoppas vid Bryt punkten trycker du på **F11** för att stega in i koden.  Använd **F10** och **F11** för att gå över och stega till funktioner för att spåra körningen.

## <a name="provision-a-batch-of-tenants"></a>Etablera en batch med klienter

Den här övningen etablerar en batch med 17 klienter. Vi rekommenderar att du etablerar denna batch med klienter innan du påbörjar andra Wingtip ticks-självstudier så att det finns fler databaser att arbeta med.

1. I *POWERSHELL ISE* öppnar du... \\ \\ProvisionTenants \\ *Demo-ProvisionTenants.ps1* och ändra *$DemoScenario* -parametern till 4:
   - **$DemoScenario**  =  **4** för att etablera en batch med klienter i en delad databas.

2. Tryck på **F5** och kör skriptet.

### <a name="verify-the-deployed-set-of-tenants"></a>Verifiera den distribuerade uppsättningen innehavare

I det här skedet har du en blandning av klienter som distribueras i en delad databas och klienter som distribueras till sina egna databaser. Azure Portal kan användas för att kontrol lera de databaser som skapats. Öppna **tenants1-MT \<USER\> -** server i [Azure Portal](https://portal.azure.com)genom att bläddra till listan över SQL-servrar.  Listan **SQL-databaser** bör innehålla den delade **tenants1** -databasen och databaserna för de klienter som finns i en egen databas:

   ![databaslista](./media/saas-multitenantdb-provision-and-catalog/Databases.png)

När Azure Portal visar klient databaserna kan du inte se klienterna *i* den delade databasen. Den fullständiga listan över klienter kan visas på webb sidan **Event Hub** i Wingtip och genom att bläddra i katalogen.

#### <a name="using-wingtip-tickets-events-hub-page"></a>Använda sidan Wingtip Ticket Events Hub

Öppna sidan Events Hub i webbläsaren (http: events. Wingtip- \<USER\> MT. trafficmanager.net)

#### <a name="using-catalog-database"></a>Använda katalog databasen

En fullständig lista över klienter och motsvarande databas för var och en finns i katalogen. En SQL-vy tillhandahålls som ansluter klient namnet till databas namnet. Vyn visar snyggt värdet för att utöka de metadata som lagras i katalogen.
- SQL-vyn är tillgänglig i tenantcatalog-databasen.
- Klient namnet lagras i tabellen klient organisationer.
- Databas namnet lagras i Shard hanterings tabeller.

1. I SQL Server Management Studio (SSMS) ansluter du till klient servern på **katalogen – MT. \<USER\> .. database.windows.net**, med login = **Developer** och Password **= \@ P ssword1**

    ![Dialog ruta för SSMS-anslutning](./media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

2. I SSMS Object Explorer bläddrar du till vyerna i *tenantcatalog* -databasen.

3. Högerklicka på vyn *TenantsExtended* och välj **välj de översta 1000 raderna**. Observera mappningen mellan klient organisations namn och databas för de olika klient organisationerna.

    ![ExtendedTenants-vy i SSMS](./media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)

## <a name="other-provisioning-patterns"></a>Andra etableringsmönster

I det här avsnittet beskrivs andra intressanta etablerings mönster.

#### <a name="pre-provisioning-databases-in-elastic-pools"></a>För etablering av databaser i elastiska pooler

För etablerings mönstret utnyttjar det faktum att när du använder elastiska pooler, är faktureringen för poolen inte databaserna. Därför kan databaser läggas till i en elastisk pool innan de behövs utan att det tillkommer extra kostnad. Den här förhands granskningen minskar avsevärt den tid det tar att etablera en klient i en databas. Antalet företablerade databaser kan justeras efter behov för att behålla en buffert som är lämplig för den förväntade etablerings hastigheten.

#### <a name="auto-provisioning"></a>Automatisk etablering

I det automatiska etablerings mönstret används en dedikerad etablerings tjänst för att etablera servrar, pooler och databaser automatiskt efter behov. Den här automationen inkluderar för etablering av databaser i elastiska pooler. Och om databaserna tas ur bruk och tas bort, kan luckorna som skapas i elastiska pooler fyllas av etablerings tjänsten efter behov.

Den här typen av automatiserad tjänst kan vara enkel eller komplex. Automation kan till exempel hantera etablering över flera geografiska områden och konfigurera geo-replikering för haveri beredskap. Med det automatiska etablerings mönstret skickar ett klient program eller-skript en etablerings förfrågan till en kö som ska bearbetas av en etablerings tjänst. Skriptet avsöker sedan för att identifiera slut för ande. Om för etablering används kan begär Anden hanteras snabbt, medan en bakgrunds tjänst hanterar etableringen av en ersättnings databas.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- - Additional [tutorials that build upon the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
- [Klientbibliotek för elastiska databaser](elastic-database-client-library.md)
- [Felsöka skript i Windows PowerShell ISE](/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise)


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Etablera en enda ny klient i en delad databas för flera innehavare och dess egen databas
> * Etablera en batch med ytterligare klienter
> * Gå igenom detaljerna för etablering av klienter och registrera dem i katalogen

Prova [själv studie kursen om prestanda övervakning](./saas-multitenantdb-performance-monitoring.md).