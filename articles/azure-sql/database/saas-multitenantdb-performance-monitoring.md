---
title: Övervaka prestanda för en shardade-databas för flera innehavare
description: Övervaka och hantera prestanda för shardade Azure SQL Database flera innehavare i en SaaS för flera innehavare
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: d37bf2c84b74dba76e5d1921ed67072af7f6c328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790907"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Övervaka och hantera prestanda för shardade Azure SQL Database flera innehavare i en SaaS för flera innehavare
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien får du utforska flera scenarier för viktiga prestanda hantering i SaaS-program. Genom att använda en belastnings generator för att simulera aktivitet i shardade-databaser visas de inbyggda övervaknings-och aviserings funktionerna i Azure SQL Database.

Wingtip-biljetterna SaaS för flera klient organisationer använder en shardade för flera klient organisationer, där plats data distribueras efter klient-ID över flera databaser. Precis som för flera SaaS-program så är de förväntade belastningsmönstren för klienterna oberäkneliga och sporadiska. Biljettförsäljningar kan med andra ord ske när som helst. För att kunna dra nytta av detta typiska databas användnings mönster kan databaser skalas upp och ned för att optimera kostnaden för en lösning. Med den här typen av mönster är det viktigt att övervaka användningen av databas resurser för att säkerställa att belastningar är rimligen balanserade över potentiellt flera databaser. Du måste också se till att enskilda databaser har tillräckligt med resurser och inte når deras [DTU](purchasing-models.md#dtu-based-purchasing-model) -gränser. I den här självstudien får du lära dig hur du övervakar och hanterar databaser och hur du vidtar korrigerings åtgärder som svar på variationer i arbets belastningen.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> 
> * Simulera användning på en shardade-databas med flera innehavare genom att köra en angiven belastnings Generator
> * Övervaka-databasen när den svarar på ökningen av belastningen
> * Skala upp databasen som svar på den ökade databas belastningen
> * Etablera en klient organisation i en databas för en enda klient

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS-appen för flera klient organisationer har distribuerats. Om du vill distribuera på mindre än fem minuter läser du [distribuera och utforska Wingtip-biljetterna SaaS-databas program för flera innehavare](./saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduktion till SaaS prestanda hanterings mönster

Hantering av databasprestanda innebär kompilering och analys av prestandadata för att sedan reagera på den genom att justera parametrarna för att bibehålla en acceptabel svarstid för programmet. 

### <a name="performance-management-strategies"></a>Strategier för prestandahantering

* För att undvika att behöva övervaka prestanda manuellt är det mest effektivt att **Ange aviseringar som utlöses när databaser avviker utanför de normala intervallen**.
* För att kunna svara på kortsiktiga variationer i beräknings storleken för en databas **kan DTU-nivån skalas upp eller ned**. Om denna variation inträffar regelbundet eller förutsägbart, **kan skalning av databasen schemaläggas att ske automatiskt**. Du kan exempelvis skala ned när du vet att din arbetsbelastning är lätt, på nätter eller helger till exempel.
* För att svara på långsiktiga variationer eller ändringar i klient organisationerna **kan enskilda klienter flyttas till en annan databas**.
* För att svara på kortsiktiga ökningar i en *enskild* klient belastning **kan enskilda klienter tas bort från en databas och tilldelas en individuell beräknings storlek**. När belastningen har minskat kan klienten sedan returneras till databasen för flera innehavare. När detta är känt i förväg, kan klienterna flyttas förebyggande syfte för att säkerställa att databasen alltid har de resurser som krävs, och för att undvika påverkan på andra klienter i databasen med flera innehavare. Om det här kravet är förutsägbart, till exempel en plats där ett populärt evenemang skapar en rusning efter biljetter, kan det här hanteringsbeteendet integreras i programmet.

[Azure-portalen](https://portal.azure.com) tillhandahåller inbyggd övervakning och avisering för de flesta resurser. För SQL Database, övervakning och avisering är tillgängligt i databaser. Den här inbyggda övervakningen och aviseringen är resurs bestämd, så det är praktiskt att använda för ett fåtal resurser, men det är inte praktiskt när du arbetar med många resurser.

För stora volymer, där du arbetar med många resurser, kan [Azure Monitor loggar](https://azure.microsoft.com/services/log-analytics/) användas. Det här är en separat Azure-tjänst som ger analys över utgivna loggar som samlas in i en Log Analytics-arbetsyta. Azure Monitor loggar kan samla in telemetri från många tjänster och använda för att fråga och ange aviseringar.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta Wingtip-biljetterna SaaS källkod och skript för databas program för flera innehavare

Wingtip-biljetterna SaaS-skript för flera klient organisationer och program käll kod är tillgängliga i [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub lagrings platsen. Ta en titt på den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera Wingtip Ticket SaaS-skript.

## <a name="provision-additional-tenants"></a>Etablera ytterligare klienter

För en god förståelse för hur prestanda övervakning och hantering fungerar i stor skala, kräver den här självstudien att du har flera klienter i en shardade-databas för flera innehavare.

Om du redan har skapat en batch med klienter i en tidigare självstudie går du vidare till avsnittet [simulera användning på alla klient databaser](#simulate-usage-on-all-tenant-databases) .

1. I **POWERSHELL ISE** öppnar du... \\ Learning-moduler \\ prestanda övervakning och hantering \\ *Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ange **$DemoScenario**  =  **1**, _etablera en batch med klienter_
1. Tryck **F5** för att köra skriptet.

Skriptet distribuerar 17 klienter i databasen för flera klienter på några minuter. 

Skriptet *New-TenantBatch* skapar nya klienter med unika klient nycklar i shardade-databasen för flera klient organisationer och initierar dem med klient organisations namn och platstyp. Detta är konsekvent med hur appen etablerar en ny klient. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulera användning på alla klientdatabaser

*Demo-PerformanceMonitoringAndManagement.ps1* -skriptet tillhandahålls som simulerar en arbets belastning som körs mot databasen med flera innehavare. Belastningen genereras med något av de tillgängliga inläsnings scenarierna:

| Demo | Scenario |
|:--|:--|
| 2 | Generera normal intensitets belastning (cirka 30 DTU) |
| 3 | Generera belastning med längre burst per klient|
| 4 | Generera belastning med högre DTU-burst per klient (cirka 70 DTU)|
| 5 | Generera en hög intensitet (cirka 90 DTU) på en enskild klient och en normal belastning på alla andra klienter |

Belastningsgeneratorn tillämpar en *syntetisk* enbart-CPU-belastning på varje klientdatabas. Generatorn startar ett jobb för varje klientdatabas som anropar en lagrad procedur med jämna mellanrum, vilket genererar belastningen. Belastnings nivåerna (i DTU: er), varaktighet och intervall varierar i alla databaser, vilket simulerar oförutsägbar klient aktivitet.

1. I **POWERSHELL ISE** öppnar du... \\ Learning-moduler \\ prestanda övervakning och hantering \\ *Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ange **$DemoScenario**  =  **2**, _generera normal intensitets belastning_
1. Tryck på **F5** för att använda en belastning för alla klienter.

Wingtip biljetter SaaS-databas för flera innehavare är en SaaS-app och den verkliga belastningen på en SaaS-app är vanligt vis sporadisk och oförutsägbar. För att simulera det så skapar belastningsgeneratorn en slumpmässig belastning som distribueras över alla klienterna. Det krävs flera minuter för att inläsnings mönstret ska visas, så kör belastnings generatorn i 3-5 minuter innan du försöker övervaka belastningen i följande avsnitt.

> [!IMPORTANT]
> Belastnings generatorn körs som en serie jobb i ett nytt PowerShell-fönster. Om du stänger sessionen stoppas belastnings generatorn. Belastnings generatorn förblir i ett *jobb som anropar* ett tillstånd där den genererar belastning på alla nya klienter som tillhandahålls efter att generatorn har startats. Använd *CTRL-C* för att stoppa att anropa nya jobb och avsluta skriptet. Belastnings generatorn fortsätter att köras, men bara på befintliga klienter.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Övervaka resursanvändningen med hjälp av Azure Portal

Om du vill övervaka resursanvändningen som beror på att belastningen används öppnar du portalen till databasen för flera klient organisationer **tenants1**, som innehåller klienterna:

1. Öppna [Azure Portal](https://portal.azure.com) och bläddra till servern *tenants1-MT- &lt; &gt; User*.
1. Bläddra nedåt och leta upp databaser och klicka på **tenants1**. Denna shardade-databas för flera innehavare innehåller alla klienter som skapats hittills.

![databas diagram](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Observera **DTU** -diagrammet.

## <a name="set-performance-alerts-on-the-database"></a>Ange prestanda varningar för databasen

Ange en avisering för den databas som utlöser \> 75%-användning enligt följande:

1. Öppna *tenants1* -databasen (på *tenants1-MT- &lt; User &gt; -* servern) i [Azure Portal](https://portal.azure.com).
1. Klicka på **aviseringsregler** och därefter på **+ lägg till avisering**:

   ![lägg till avisering](./media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Ange ett namn, exempelvis **hög DTU**,
1. Ställ in följande värden:
   * **Mått = DTU-procent**
   * **Villkor = större än**
   * **Tröskelvärdet = 75**.
   * **Period = under de senaste 30 minuterna**
1. Lägg till en e-postadress i rutan *ytterligare administratörs-e-post (er)* och klicka på **OK**.

   ![ange varning](./media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Skala upp en upptagen databas

Om belastnings nivån ökar i en databas till den tidpunkt då den maxas ut databasen och når 100% DTU-användning, kommer databas prestanda att påverkas, vilket potentiellt långsamma svars tider för frågor.

**Kortsiktigt**, Överväg att skala upp databasen för att tillhandahålla ytterligare resurser eller ta bort klienter från databasen med flera innehavare (flytta ut dem från databasen med flera innehavare till en fristående databas).

**Längre tid** bör du överväga att optimera frågor eller använda index användningen för att förbättra databasens prestanda. Beroende på programmets känslighet för prestanda problem är det bästa sättet att skala upp en databas innan den når 100% DTU-användning. Använd en avisering för att varna dig i förväg.

Du kan simulera en upptagen databas genom att öka belastningen som alstras av generatorn. Gör att klienterna kan överföra oftare och att öka belastningen på databasen med flera klienter utan att ändra kraven för enskilda klienter. Det är enkelt att skala upp databasen i portalen eller från PowerShell. Den här övningen använder portalen.

1. Ange *$DemoScenario*  =  **3**, _generera belastning med längre och mer frekventa burst per databas_ för att öka intensiteten för den sammanställda belastningen på databasen utan att ändra den högsta belastning som krävs av varje klient.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.
1. Gå till **tenants1** -databasen i Azure Portal.

Övervaka den ökade användningen av databas-DTU i det övre diagrammet. Det tar några minuter för den nya högre belastningen att användas, men du bör snabbt se databasen från början för att få maximal användning, och när belastningen blir jämnare i det nya mönstret överbelastas databasen snabbt.

1. Om du vill skala upp databasen klickar du på **pris nivå (skala DTU: er)** på bladet inställningar.
1. Justera **DTU** -inställningen till **100**. 
1. Klicka på **tillämpa** för att skicka begäran om att skala databasen.

Gå tillbaka till **tenants1**-  >  **översikten** om du vill visa övervaknings diagrammen. Övervaka effekterna av att tillhandahålla databasen med fler resurser (även om det finns några klienter och en slumpmässig inläsning är det inte alltid lätt att se något avgörande tills du kör en stund). När du tittar på diagrammen är det viktigt att 100% i det övre diagrammet nu representerar 100 DTU: er, medan i det nedre diagrammet 100% fortfarande 50 DTU: er.

Databaserna är online och fullt tillgängliga under hela processen. Program koden bör alltid skrivas för att försöka igen, och ansluter sedan till databasen igen.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Etablera en ny klient i en egen databas 

Med shardade för flera klienter kan du välja om du vill etablera en ny klient i en databas för flera klienter tillsammans med andra klienter, eller för att etablera klienten i en egen databas. Genom att tillhandahålla en klient i sin egen databas fördelar den från isoleringen i den separata databasen, så att du kan hantera klientens prestanda oberoende av andra, återställa denna klient oberoende av andra, osv. Du kan till exempel välja att ställa in en kostnads fri utvärderings version eller vanliga kunder i en databas med flera innehavare och Premium-kunder i enskilda databaser.  Om isolerade en enskild klient databas skapas kan de fortfarande hanteras tillsammans i en elastisk pool för att optimera resurs kostnaderna.

Om du redan har skapat en ny klient i en egen databas, hoppar du över nästa steg.

1. I **POWERSHELL ISE** öppnar du... \\ ProvisionTenantsDemo-ProvisionTenants.ps1för inlärnings moduler \\ \\ **. 
1. Ändra **$TenantName = "Salix salsa"** och **$VenueType = "kontrollen åt"**
1. Ange **$scenario**  =  **2**, _etablera en klient i en ny databas för en enda klient_
1. Tryck **F5** för att köra skriptet.

Skriptet etablerar den här klienten i en separat databas, registrerar databasen och klienten med katalogen och öppnar sedan klientens händelse sida i webbläsaren. Uppdatera sidan Events Hub och se att "Salix salsa" har lagts till som plats.

## <a name="manage-performance-of-an-individual-database"></a>Hantera prestanda för en enskild databas

Om en enskild klient i en databas med flera innehavare upplever en varaktig hög belastning kan det vara en risk att dominera databas resurserna och påverka andra klienter i samma databas. Om aktiviteten sannolikt fortsätter under en viss tid kan klienten tillfälligt flyttas från databasen och till en egen databas med en enda klient. Detta gör det möjligt för innehavaren att ha de extra resurser som behövs, och helt isolera dem från de andra klienterna.

I den här övningen simuleras effekterna av Salix salsa som har hög belastning när biljetterna går till försäljning för en populär händelse.

1. Öppna... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skript.
1. Ange **$DemoScenario = 5**, _Generera en normal belastning plus en hög belastning på en enskild klient (cirka 90 DTU)._
1. Ange **$SingleTenantName = Salix salsa**
1. Kör skriptet med **F5**.

Gå till portalen och navigera till **salixsalsa**-  >  **Översikt** för att Visa övervaknings diagrammen. 

## <a name="other-performance-management-patterns"></a>Andra mönster för prestanda hantering

**Själv tjänst skalning för klient organisation**

Eftersom skalning är en uppgift som enkelt anropas via hanterings-API: et, kan du enkelt bygga ut möjligheten att skala klient databaser till klient programmet och erbjuda det som en funktion i din SaaS-tjänst. Exempelvis kan du låta klienter självhantera upp- och nedskalning, kanske kopplat direkt till sin fakturering!

**Skala upp och ned en databas enligt ett schema för att matcha användnings mönster**

Om den sammanställda klient användningen följer förutsägbara användnings mönster kan du använda Azure Automation för att skala upp och ned en databas enligt ett schema. Du kan t. ex. skala en databas nedåt efter. 18:00 och upp igen innan 06:00 på vardagar när du vet att det finns en släpp resurs krav.

## <a name="next-steps"></a>Nästa steg

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Simulera användning på en shardade-databas med flera innehavare genom att köra en angiven belastnings Generator
> * Övervaka-databasen när den svarar på ökningen av belastningen
> * Skala upp databasen som svar på den ökade databas belastningen
> * Etablera en klient organisation i en databas för en enda klient

## <a name="additional-resources"></a>Ytterligare resurser

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../../automation/automation-intro.md)