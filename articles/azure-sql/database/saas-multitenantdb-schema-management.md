---
title: Hantera schema i en app för flera klienter
description: Hantera scheman för flera klienter i ett program för flera klienter som använder Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: d222234cd6ff3d910e6dbc51a394695ce467edce
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011872"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-databases"></a>Hantera schema i ett SaaS-program som använder shardade-databaser för flera klienter
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Den här självstudien undersöker utmaningarna med att underhålla en flotta av databaser i ett SaaS-program (program vara som en tjänst). Lösningar visas för Fanning av schema ändringar över flottan av databaser.

Precis som med alla program utvecklas Wingtip ticks SaaS-appen över tid och kräver ändringar i databasen. Ändringar kan påverka schema-eller referens data eller använda databas underhålls aktiviteter. Med ett SaaS-program som använder en databas per klient mönster måste ändringarna koordineras över en potentiellt enorm flotta av klient databaserna. Dessutom måste du inkludera dessa ändringar i databas etablerings processen för att se till att de ingår i nya databaser när de skapas.

#### <a name="two-scenarios"></a>Två scenarier

I den här självstudien utforskas följande två scenarier:
- Distribuera referens data uppdateringar för alla klienter.
- Återskapa ett index för den tabell som innehåller referens data.

Funktionen [elastiska jobb](./elastic-jobs-overview.md) i Azure SQL Database används för att utföra dessa åtgärder över klient databaser. Jobben körs också på klient databasen ' Template '. I exempel appen Wingtip biljetter kopieras den här mallfilen för att etablera en ny klient databas.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Skapa en jobb agent.
> * Kör en T-SQL-fråga på flera klient databaser.
> * Uppdatera referens data i alla klient databaser.
> * Skapa ett index i en tabell i alla klient databaser.

## <a name="prerequisites"></a>Förutsättningar

- Wingtip Ticket-appen för flera klient organisationer måste redan vara distribuerad:
    - Mer information finns i den första självstudien som presenterar Wingtip-biljetterna SaaS för flera klient organisationer:<br />[Distribuera och utforska ett shardade-program för flera innehavare som använder Azure SQL Database](./saas-multitenantdb-get-started-deploy.md).
        - Distributions processen körs i mindre än fem minuter.
    - Du måste ha *shardade multi-Tenant-* versionen av Wingtip installerat. Versionerna för *fristående* och *databas per klient* har inte stöd för den här självstudien.

- Den senaste versionen av SQL Server Management Studio (SSMS) måste vara installerad. [Hämta och installera SSMS](/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell måste vara installerat. Mer information finns i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).

> [!NOTE]
> I den här självstudien används funktioner i Azure SQL Databases tjänsten som är i en begränsad förhands granskning ([Elastic Database-jobb](elastic-database-client-library.md)). Om du vill göra den här själv studie kursen anger du ditt prenumerations-ID till *SaaSFeedback \@ Microsoft.com* med subject = för hands version av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, [ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna](https://github.com/jaredmoo/azure-powershell/releases). Den här för hands versionen är begränsad, så kontakta *SaaSFeedback \@ Microsoft.com* för relaterade frågor eller support.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduktion till SaaS schema hanterings mönster

Shardade-modellen för flera klient organisationer som används i det här exemplet gör det möjligt för en klient databas att innehålla en eller flera klienter. Det här exemplet utforskar potentialen att använda en blandning av en många-klient och en-klient databas, vilket möjliggör en hanterings modell för *hybrid* klienter. Det kan vara svårt att hantera ändringar i dessa databaser. [Elastiska jobb](./elastic-jobs-overview.md) underlättar administration och hantering av stora mängder databaser. Med jobb kan du på ett säkert och tillförlitligt sätt köra Transact-SQL-skript som uppgifter, mot en grupp klient databaser. Aktiviteterna är oberoende av användar interaktion eller indata. Den här metoden kan användas för att distribuera ändringar i schemat eller vanliga referens data för alla klienter i ett program. Elastiska jobb kan också användas för att underhålla en mal kopia av databasen. Mallen används för att skapa nya klienter, och säkerställer alltid att det senaste schemat och referens data används.

![skärmen](./media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Elastiska jobb begränsad förhandsvisning

Det finns en ny version av elastiska jobb som nu är en integrerad funktion i Azure SQL Database. Den här nya versionen av elastiska jobb är för närvarande i begränsad förhandsvisning. Den begränsade för hands versionen stöder för närvarande användning av PowerShell för att skapa en jobb agent och T-SQL för att skapa och hantera jobb.
> [!NOTE]
> Den här guiden använder funktioner för SQL Database-tjänsten som är i en begränsad förhandsgranskning (Elastic Database-jobb). Om du vill göra den här själv studie kursen anger du ditt prenumerations-ID till SaaSFeedback@microsoft.com med ämne = för hands version av elastiska jobb. När du fått en bekräftelse att din prenumeration har aktiverats kan du, ladda ned och installera den senaste förhandsversionen av jobs-cmdletarna. Den här för hands versionen är begränsad, så kontakta dig SaaSFeedback@microsoft.com för relaterade frågor eller support.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta Wingtip-biljetterna SaaS källkod och skript för databas program för flera innehavare

Wingtip-biljetterna SaaS databas skript för flera innehavare och program käll kod är tillgängliga i [WingtipTicketsSaaS-MultitenantDB-](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) lagringsplatsen på GitHub. Se den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera Wingtip Ticket SaaS-skript.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Skapa en jobb agent databas och en ny jobb agent

I den här självstudien krävs att du använder PowerShell för att skapa jobb Agent databasen och jobb agenten. Precis som MSDB-databasen som används av SQL Agent använder en jobb agent en databas i Azure SQL Database för att lagra jobb definitioner, jobb status och historik. När jobb agenten har skapats kan du skapa och övervaka jobb direkt.

1. I **POWERSHELL ISE** öppnar du *... \\ Modulerna \\ schema hantering \\ för inlärningDemo-SchemaManagement.ps1*.
2. Tryck **F5** för att köra skriptet.

*Demo-SchemaManagement.ps1* skriptet anropar *Deploy-SchemaManagement.ps1* -skriptet för att skapa en databas med namnet _JobAgent_ på katalog servern. Skriptet skapar sedan jobb agenten och skickar _JobAgent_ -databasen som en parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Skapa ett jobb för att distribuera nya referensdata till alla klienter

#### <a name="prepare"></a>Förbereda

Varje innehavares databas innehåller en uppsättning plats typer i **VenueTypes** -tabellen. Varje platstyp definierar typ av händelser som kan finnas på en plats. Dessa typer av platser motsvarar de bakgrunds bilder som visas i appen klient organisations händelser.  I den här övningen distribuerar du en uppdatering till alla databaser för att lägga till två ytterligare plats typer: *motorcykel tävling* och *SIM-klubb*.

Börja med att granska de plats typer som ingår i varje klient databas. Anslut till en av klient databaserna i SQL Server Management Studio (SSMS) och granska VenueTypes-tabellen.  Du kan också fråga den här tabellen i Frågeredigeraren i Azure Portal, som öppnas från databas sidan.

1. Öppna SSMS och Anslut till klient servern: *tenants1-DPT- &lt; user &gt; . Database.Windows.net*
1. För att bekräfta att *motorcykelns racing* -och *SIM-klubb* **inte** ingår, bläddrar du till *contosoconcerthall* -databasen på *Tenants1-DPT- &lt; &gt; User-* servern och frågar *VenueTypes* -tabellen.



#### <a name="steps"></a>Steg

Nu skapar du ett jobb för att uppdatera **VenueTypes** -tabellen i varje klient databas genom att lägga till de två nya plats typerna.

Om du vill skapa ett nytt jobb använder du uppsättningen med jobb system lagrade procedurer som skapades i _JobAgent_ -databasen. De lagrade procedurerna skapades när jobb agenten skapades.

1. I SSMS ansluter du till klient servern: tenants1-MT- &lt; User &gt; . Database.Windows.net

2. Bläddra till *tenants1* -databasen.

3. Fråga *VenueTypes* -tabellen för att bekräfta att *motorcykelns racing* -och *SIM-klubb* ännu inte finns i resultat listan.

4. Anslut till katalog servern, som är *Catalog-MT- &lt; user &gt; . Database.Windows.net*.

5. Anslut till _JobAgent_ -databasen på katalog servern.

6. Öppna filen i SSMS *... \\ Inlärnings moduler \\ schema hantering \\ DeployReferenceData. SQL*.

7. Ändra instruktionen: set @User = &lt; User &gt; och ersätt User Value som används när du distribuerade Wingtip Ticket SaaS-databasprogram för flera innehavare.

8. Tryck **F5** för att köra skriptet.

#### <a name="observe"></a>Närmare

Observera följande objekt i *DeployReferenceData. SQL* -skriptet:

- **SP \_ Add \_ target \_ Group** skapar mål grupp namnet *DemoServerGroup* och lägger till mål medlemmar i gruppen.

- **SP \_ Lägg till \_ mål \_ grupp \_ medlem** lägger till följande objekt:
    - En medlems typ för *Server* mål.
        - Detta är den *tenants1-MT- &lt; User &gt; -* server som innehåller klienternas databaser.
        - Inklusive-servern omfattar de klient databaser som finns när jobbet körs.
    - En *databas* mål medlems typ för den mall databas (*basetenantdb*) som finns på *katalogen – MT- &lt; User &gt;* Server
    - En medlems typ av *databas* mål som innehåller *AdHocReporting* -databasen som används i en senare själv studie kurs.

- **SP \_ Add \_ Job** skapar ett jobb med namnet *referens data distribution*.

- **SP \_ Add \_ Jobstep** skapar det jobb steg som innehåller T-SQL-kommando texten för att uppdatera referens tabellen, VenueTypes.

- De återstående vyerna i skriptet visar att jobbet finns och övervakar jobbkörningen. Använd de här frågorna för att granska status värdet i kolumnen **livs cykel** för att fastställa när jobbet har avslut ATS. Jobbet uppdaterar klient databasen och uppdaterar de två ytterligare databaser som innehåller referens tabellen.

I SSMS bläddrar du till klient databasen på *tenants1-MT- &lt; User &gt; -* servern. Fråga *VenueTypes* -tabellen för att bekräfta att *motorcykelns racing* och *SIM-klubb* nu läggs till i tabellen. Det totala antalet typer av platser bör ha ökat med två.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Skapa ett jobb för att hantera referenstabellindexet

Den här övningen skapar ett jobb för att återskapa indexet för primär nyckeln för referens tabellen på alla klient databaser. En index återskapning är en typisk databas hanterings åtgärd som en administratör kan köra efter att ha läst in en stor mängd data belastning för att förbättra prestandan.

1. I SSMS ansluter du till _JobAgent_ Database i *katalogen-MT- &lt; User &gt; . Database.Windows.net-* servern.

2. Öppna i SSMS *... \\ Inlärnings moduler \\ schema hantering \\ OnlineReindex. SQL*.

3. Tryck **F5** för att köra skriptet.

#### <a name="observe"></a>Närmare

Observera följande objekt i *OnlineReindex. SQL* -skriptet:

* **SP \_ Add \_ Job** skapar ett nytt jobb med namnet *online Reindex PK \_ \_ VenueTyp \_ \_ 265E44FD7FD4C885*.

* **SP \_ Add \_ Jobstep** skapar det jobb steg som innehåller kommando texten T-SQL för att uppdatera indexet.

* Återstående vyer i skript övervaknings jobb körningen. Använd de här frågorna för att granska status värdet i kolumnen **livs cykel** för att fastställa när jobbet har slutförts på alla mål grupps medlemmar.

## <a name="additional-resources"></a>Ytterligare resurser

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Hantera utskalade molndatabaser](./elastic-jobs-overview.md)

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en jobb agent för att köra T-SQL-jobb över flera databaser
> * Uppdatera referens data i alla klient databaser
> * Skapar ett index i en tabell i alla klientdatabaser

Prova sedan [själv studie kursen om ad hoc-rapportering](./saas-multitenantdb-adhoc-reporting.md) för att utforska köra distribuerade frågor över klient databaser.