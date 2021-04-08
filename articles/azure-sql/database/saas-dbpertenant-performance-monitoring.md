---
title: 'SaaS-app: övervaka prestanda för många databaser'
description: Övervaka och hantera prestanda för Azure SQL Database i en SaaS-app med flera innehavare
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
ms.openlocfilehash: 21c0a7a3fe6d5be9d99ea53dbfa74cf72e163272
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780673"
---
# <a name="monitor-and-manage-performance-of-azure-sql-database-in-a-multi-tenant-saas-app"></a>Övervaka och hantera prestanda för Azure SQL Database i en SaaS-app med flera innehavare
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien får du utforska flera scenarier för viktiga prestanda hantering i SaaS-program. Genom att använda en belastnings generator för att simulera aktivitet i alla klient databaser visas de inbyggda funktionerna för övervakning och avisering i SQL Database och elastiska pooler.

Wingtip biljetter SaaS-databasen per klient-app använder en data modell med en enda klient, där varje plats (klient) har sin egen databas. Precis som för flera SaaS-program så är de förväntade belastningsmönstren för klienterna oberäkneliga och sporadiska. Biljettförsäljningar kan med andra ord ske när som helst. För att dra nytta av detta vanliga mönster för databas användning distribueras klient databaserna i elastiska pooler. Elastiska pooler optimerar kostnaderna för en lösning genom att dela resurser över flera databaser. Med den här typen av mönster är det viktigt att övervaka användningen av databas- och poolresurser för att försäkra att belastningarna balanseras över poolerna. Du behöver också se till att enskilda databaser har tillräckliga resurser och att pooler inte träffar sina [eDTU](purchasing-models.md#dtu-based-purchasing-model)-gränser. Den här guiden går igenom sätt att övervaka och hantera databaser och pooler och hur man vidtar åtgärder i respons på belastningsvariationer.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> 
> * Simulerar användning på klientdatabaserna genom att köra en angiven belastningsgenerator
> * Övervakar klientdatabaserna när de svarar på belastningsökningar
> * Skalar upp den elastiska poolen i svar på den ökade databasbelastningen
> * Etablerar en andra elastisk pool för att belastningsutjämna databasaktiviteten


Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip biljetter SaaS-databasen per klient-app distribueras. Om du vill distribuera på mindre än fem minuter, se [distribuera och utforska Wingtip-biljetter SaaS-databas per klient program](./saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduktion till SaaS prestanda hanterings mönster

Hantering av databasprestanda innebär kompilering och analys av prestandadata för att sedan reagera på den genom att justera parametrarna för att bibehålla en acceptabel svarstid för programmet. När du är värd för flera klienter är elastiska pooler ett kostnads effektivt sätt att tillhandahålla och hantera resurser för en grupp databaser med oförutsägbara arbets belastningar. Med vissa arbetsbelastningmönster, kan så få som två S3-databaser dra nytta av att hanteras i en pool.

![program diagram](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Pooler och databaser i pooler bör övervakas för att se till att de ligger inom acceptabla prestanda intervall. Finjustera konfigurationen för poolen för att uppfylla behoven för den sammanställda arbets belastningen för alla databaser, vilket säkerställer att poolens eDTU: er är lämpliga för den övergripande arbets belastningen. Justera per databas min och max eDTU-värden till lämpliga värden för dina specifika programkrav.

### <a name="performance-management-strategies"></a>Strategier för prestandahantering

* För att undvika att behöva övervaka prestanda manuellt är det mest effektivt att **Ange aviseringar som utlöses när databaser eller pooler avviker utanför de normala intervallen**.
* För att svara på kortsiktiga variationer i en Pools sammanlagda beräknings storlek **kan poolens eDTU-nivå skalas upp eller ned**. Om de här variationerna inträffar på regelbunden eller förutsägbar basis, **kan skalning av poolen schemaläggas att ske automatiskt**. Du kan exempelvis skala ned när du vet att din arbetsbelastning är lätt, på nätter eller helger till exempel.
* För att svara på mer långsiktiga variationer eller förändringar av antalet databaser, **kan enskilda databaser flyttas till andra pooler**.
* För att svara på kortsiktiga ökningar i *enskilda* **databaser kan enskilda databaser tas bort från en pool och tilldelas en individuell beräknings storlek**. När belastningen minskar, kan databasen returneras till poolen. När detta är känt i förväg kan databaser flyttas förebyggande syfte för att säkerställa att databasen alltid har de resurser som krävs, och för att undvika påverkan på andra databaser i poolen. Om det här kravet är förutsägbart, till exempel en plats där ett populärt evenemang skapar en rusning efter biljetter, kan det här hanteringsbeteendet integreras i programmet.

[Azure-portalen](https://portal.azure.com) tillhandahåller inbyggd övervakning och avisering för de flesta resurser. Övervakning och avisering är tillgängligt på databaser och pooler. Den här inbyggda övervakningen och aviseringen är resurs bestämd, så det är praktiskt att använda för ett fåtal resurser, men är inte särskilt användbart när du arbetar med många resurser.

För stora volymer, där du arbetar med många resurser, kan [Azure Monitor loggar](./saas-dbpertenant-log-analytics.md) användas. Det här är en separat Azure-tjänst som ger analys över utgivna loggar som samlas in i en Log Analytics-arbetsyta. Azure Monitor loggar kan samla in telemetri från många tjänster och använda för att fråga och ange aviseringar.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip-biljetterna SaaS-databas per klient program skript

Wingtip-biljetterna SaaS-skript för flera klient organisationer och program käll kod är tillgängliga i [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub lagrings platsen. Ta en titt på den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera Wingtip Ticket SaaS-skript.

## <a name="provision-additional-tenants"></a>Etablera ytterligare klienter

Pooler kan vara kostnadseffektiva med bara två S3 databaser, men ju fler databaser som finns i poolen, desto mer kostnadseffektiv blir genomsnittseffekten. För att få en bra förståelse för hur prestandaövervakning och hantering fungerar i stor skala, kräver den här kursen du har distribuerat minst 20 databaser.

Om du redan har skapat en batch med klienter i en tidigare självstudie går du vidare till avsnittet [simulera användning på alla klient databaser](#simulate-usage-on-all-tenant-databases) .

1. I **POWERSHELL ISE** öppnar du... \\ Learning-moduler \\ prestanda övervakning och hantering \\ *Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ange **$DemoScenario**  =  **1**, **etablera en batch med klienter**
1. Tryck **F5** för att köra skriptet.

Skriptet distribuerar 17 klienter på mindre än fem minuter.

Det *nya-TenantBatch-* skriptet använder en kapslad eller länkad uppsättning [Resource Manager](../../azure-resource-manager/index.yml) -mallar som skapar en batch med klienter, som som standard kopierar databasen **basetenantdb** på katalog servern för att skapa nya klient databaser, registrerar dem i katalogen och sedan initierar dem med klient organisations namn och platstyp. Detta är konsekvent med hur appen etablerar en ny klient. Ändringar som görs i *basetenantdb* tillämpas på alla nya klienter som tillhandahålls därefter. I [själv studie kursen om schema hantering](saas-tenancy-schema-management.md) kan du se hur du gör schema ändringar i *befintliga* klient databaser (inklusive *basetenantdb* -databasen).

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulera användning på alla klientdatabaser

*Demo-PerformanceMonitoringAndManagement.ps1* -skriptet tillhandahålls som simulerar en arbets belastning som körs mot alla klient databaser. Belastningen genereras med något av de tillgängliga inläsnings scenarierna:

| Demo | Scenario |
|:--|:--|
| 2 | Generera normal intensitets belastning (cirka 40 DTU) |
| 3 | Generera belastning med längre och mer frekventa toppar per databas|
| 4 | Generera belastning med högre DTU-burst per databas (cirka 80 DTU)|
| 5 | Generera en normal belastning plus en hög belastning på en enskild klient (cirka 95 DTU)|
| 6 | Generera obalanserad belastning över flera pooler|

Belastningsgeneratorn tillämpar en *syntetisk* enbart-CPU-belastning på varje klientdatabas. Generatorn startar ett jobb för varje klientdatabas som anropar en lagrad procedur med jämna mellanrum, vilket genererar belastningen. Belastningsnivåer (i eDTU:er), varaktighet och intervaller varierar över alla databaser, vilket simulerar oförutsägbar klientaktivitet.

1. I **POWERSHELL ISE** öppnar du... \\ Learning-moduler \\ prestanda övervakning och hantering \\ *Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ange **$DemoScenario**  =  **2**, *generera normal intensitets belastning*.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.

Wingtip biljetter SaaS Database per klient är en SaaS-app och den verkliga belastningen på en SaaS-app är vanligt vis sporadisk och oförutsägbar. För att simulera det så skapar belastningsgeneratorn en slumpmässig belastning som distribueras över alla klienterna. Det krävs flera minuter för att inläsnings mönstret ska visas, så kör belastnings generatorn i 3-5 minuter innan du försöker övervaka belastningen i följande avsnitt.

> [!IMPORTANT]
> Belastningsgeneratorn körs som en serie jobb i din lokala PowerShell-session. Behåll fliken *Demo-PerformanceMonitoringAndManagement.ps1* öppen! Om du stänger fliken eller pausar din dator, stannar belastningsgeneratorn. Belastnings generatorn förblir i ett *jobb som anropar* ett tillstånd där den genererar belastning på alla nya klienter som tillhandahålls efter att generatorn har startats. Använd *CTRL-C* för att stoppa att anropa nya jobb och avsluta skriptet. Belastnings generatorn fortsätter att köras, men bara på befintliga klienter.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Övervaka resursanvändningen med hjälp av Azure Portal

Om du vill övervaka resursanvändningen som beror på att belastningen används öppnar du portalen till poolen som innehåller klient databaserna:

1. Öppna [Azure Portal](https://portal.azure.com) och bläddra till servern *tenants1-DPT- &lt; User &gt;* .
1. Bläddra ned och hitta elastiska pooler och klicka på **Pool1**. Den här poolen innehåller alla klientdatabaser som skapats än så länge.

Observera övervakningen av **elastiska pooler** och övervaknings diagram för **Elastic Database** .

Poolens resursutnyttjande är den sammanställda databas användningen för alla databaser i poolen. Databas diagrammet visar de fem hetaste databaserna:

![databas diagram](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Eftersom det finns fler databaser i poolen än de fem översta, visar pool användningen aktivitet som inte avspeglas i de översta fem databas diagram. Om du vill ha mer information klickar du på **databas resursutnyttjande**:

![resursanvändning för databas](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Ställ in prestandaaviseringar för poolen

Ange en avisering för poolen som utlöser \> 75%-användning enligt följande:

1. Öppna *Pool1* (på *tenants1-DPT \<user\> -* Server) i [Azure Portal](https://portal.azure.com).
1. Klicka på **aviseringsregler** och därefter på **+ lägg till avisering**:

   ![lägg till avisering](./media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Ange ett namn, exempelvis **hög DTU**,
1. Ställ in följande värden:
   * **Mått = eDTU procent**
   * **Villkor = större än**
   * **Tröskelvärde = 75**
   * **Period = under de senaste 30 minuterna**
1. Lägg till en e-postadress i rutan *ytterligare administratörs-e-post (er)* och klicka på **OK**.

   ![ange varning](./media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Skala upp en upptagen pool

Om den sammanställda belastningsnivån ökar på en pool till den punkt där poolen maxas ut och den når 100% eDTU-användning så påverkas individuell databasprestanda, vilket kan sakta ner frågesvarstiderna för alla databaser i poolen.

På **kort sikt** kan du skala upp poolen för att tillhandahålla ytterligare resurser eller ta bort databaser från poolen (flytta dem till andra pooler eller från poolen till en fristående tjänst nivå).

**Längre tid** bör du överväga att optimera frågor eller använda index användningen för att förbättra databasens prestanda. Beroende på programmets känslighet för prestandaproblem, är det praxis att skala upp en pool innan den når 100% eDTU-användning. Använd en avisering för att varna dig i förväg.

Du kan simulera en upptagen pool genom att öka belastningen som generatorn skapar. Orsaka att databaserna överförs oftare och för längre tid, ökar den sammanställda belastningen på poolen utan att ändra kraven för de enskilda databaserna. Det är enkelt att skala upp poolen i portalen eller från PowerShell. Den här övningen använder portalen.

1. Ange *$DemoScenario*  =  **3**, _generera belastning med längre och mer frekventa burst per databas_ för att öka intensiteten för den sammanställda belastningen på poolen utan att ändra den högsta belastning som krävs av varje databas.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.

1. Gå till **Pool1** i Azure Portal.

Övervaka den utökade användningen av pool-eDTU i det övre diagrammet. Det tar några minuter för den nya högre belastningen att sätta igång, men du bör snabbt se poolen starta för att få maximal användning, och eftersom belastningen blir jämnare i det nya mönstret överbelastas poolen snabbt.

1. Om du vill skala upp poolen klickar du på **Konfigurera pool** överst på **Pool1** -sidan.
1. Justera **pool-eDTU** -inställningen till **100**. Ändringar av pool-eDTU:er, ändrar inte inställningarna per databas (som fortfarande är 50 eDTU max per databas). Du kan se inställningarna per databas på höger sida av sidan **Konfigurera pool** .
1. Klicka på **Spara** för att skicka begäran om att skala poolen.

Gå tillbaka till **Pool1**-  >  **översikten** om du vill visa övervaknings diagrammen. Övervaka effekterna av att tillhandahålla poolen med fler resurser (även om det finns några databaser och en slumpmässig inläsning är det inte alltid lätt att se avgörande tills du kör en stund). När du tittar på diagrammen, tänk på att 100% i det övre diagrammet nu representerar 100 eDTU:er, medan 100% i det lägre diagrammet fortfarande är 50 eDTU:er eftersom max per databas fortfarande är 50 eDTU:er.

Databaserna är online och fullt tillgängliga under hela processen. Under det sista ögonblicket när varje databas är redo att aktiveras med de nya pool-eDTU:erna, bryts alla aktiva anslutningar. Du ska alltid skriva programkod för att återförsöka brutna anslutningar, vilken då kommer att återansluta till databasen i den uppskalade poolen.

## <a name="load-balance-between-pools"></a>Belastnings utjämning mellan pooler

Som ett alternativ till att skala upp poolen, kan du skapa en andra pool och flytta databaserna till den för att balansera belastningen mellan de två poolerna. Det gör du genom att skapa den nya poolen på samma server som den första.

1. Öppna **tenants1-DPT- &lt; User &gt; -** servern i [Azure Portal](https://portal.azure.com).
1. Klicka på **+ ny pool** för att skapa en pool på den aktuella servern.
1. I mallen för **elastisk pool** :

   1. Ange **namnet** till *Pool2*.
   1. Lämna prisnivån som **standardpool**.
   1. Klicka på **Konfigurera pool**,
   1. Set **pool-eDTU** till *50 eDTU*.
   1. Klicka på **Lägg till databaser** för att se en lista över databaser på servern som kan läggas till i *Pool2*.
   1. Välj 10 databaser för att flytta dem till den nya poolen och klicka sedan på **Välj**. Om du har kört belastnings generatorn vet tjänsten redan att prestanda profilen kräver en större pool än standard storleken 50 eDTU och rekommenderar att du börjar med en 100 eDTU-inställning.

      ![rekommenderade](./media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. I den här självstudien lämnar du standardvärdet 50 eDTU: er och klickar på **Välj** igen.
   1. Välj **OK** för att skapa den nya poolen och flytta de markerade databaserna till den.

Det tar några minuter att skapa poolen och flytta databaserna. När databaserna flyttas är de fortfarande online och fullständigt tillgängliga tills det allra sista tillfället, där alla öppna anslutningar är stängda. Så länge som du har en del omprövnings logik kommer klienterna att ansluta till databasen i den nya poolen.

Bläddra till **Pool2** (på *tenants1-DPT- \<user\>* Server) för att öppna poolen och övervaka dess prestanda. Om du inte ser det väntar du tills etableringen av den nya poolen har slutförts.

Nu ser du att resursanvändningen på *Pool1* har släppts och att *Pool2* nu har lästs in på samma sätt.

## <a name="manage-performance-of-an-individual-database"></a>Hantera prestanda för en enskild databas

Om en enskild databas i en pool får en hållbar hög belastning, beroende på konfiguration av poolen, kan det krävas att de dominerar resurserna i poolen och påverkar andra databaser. Om aktiviteten troligt vis fortsätter under en tid kan databasen tillfälligt flyttas från poolen. Detta gör att databasen får de extra resurser som krävs och isolerar den från de andra databaserna.

Den här övningen simulerar effekten av att Contosos konserthall upplever en hög belastning när biljetter börjar säljas för ett populärt evenemang.

1. I **POWERSHELL ISE** öppnar du... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skript.
1. Ange **$DemoScenario = 5, generera en normal belastning plus en hög belastning på en enskild klient (cirka 95 DTU).**
1. Ställ in **$SingleTenantDatabaseName = contosoconcerthall**
1. Kör skriptet med **F5**.


1. I [Azure Portal](https://portal.azure.com)bläddrar du till listan över databaser på *tenants1-DPT- \<user\>* Server. 
1. Klicka på **contosoconcerthall** -databasen.
1. Klicka på den pool som **contosoconcerthall** finns i. Leta upp poolen i avsnittet **elastisk pool** .

1. Granska övervaknings diagrammet för **elastisk pool** och leta efter den ökade användningen av pool-eDTU. Efter någon minut sätter den högre belastningen in och du borde snabbt se att poolen når 100% användning.
2. Kontrol lera visningen av **Elastic Database Monitoring** , som visar de hetaste databaserna under den senaste timmen. *Contosoconcerthall* -databasen bör snart visas som en av de fem hetaste databaserna.
3. **Klicka på diagrammet för övervakning av elastiska databaser**  så öppnas sidan **databas resurs användning** där du kan övervaka någon av databaserna. På så sätt kan du isolera visningen för *contosoconcerthall* -databasen.
4. Klicka på **contosoconcerthall** i listan över databaser.
5. Klicka på **pris nivå (skala DTU: er)** för att öppna sidan **Konfigurera prestanda** där du kan ange en fristående beräknings storlek för databasen.
6. Klicka på **Standard**-fliken för att öppna skalningsalternativen på standardnivån.
7. Dra **skjutreglaget DTU** till höger för att välja **100** DTU: er. Observera att detta motsvarar tjänst målet, **S3**.
8. Klicka på **Använd** för att flytta ut databasen från poolen och göra den till *standard S3* -databas.
9. När skalningen är klar kan du övervaka effekterna på contosoconcerthall-databasen och Pool1 på bladet elastisk pool och databaser.

När den höga belastningen på contosoconcerthall-databasen är på-sidan bör du snabbt returnera den till poolen för att minska dess kostnad. Om det är oklart när det sker kan du ange en avisering för den databas som ska utlöses när DTU-användningen sjunker under max gränsen per databas i poolen. Övrning 5 beskriver hur du flyttar en databas till en pool.

## <a name="other-performance-management-patterns"></a>Andra mönster för prestanda hantering

**För ogiltiga skalning** I övningen ovan där du utforskade hur du skalar en isolerad databas, kände du till vilken databas du vill söka efter. Om hanteringen av contoso konsert Hall hade informerat wingtips om den förestående biljett försäljningen, kan databasen ha flyttats från poolen förebyggande syfte. Annars hade det sannolikt behövts en avisering på poolen eller databasen för att upptäcka vad som hände. Du vill inte få reda på sånt här genom att andra klienter i poolen klagar på försämrad prestanda. Och om klienten kan förutse hur länge de behöver ytterligare resurser, kan du ställa in en Azure Automation-runbook för att flytta ut databasen från poolen och sedan tillbaka igen enligt ett definierat schema.

**Klientskalning som självservice** eftersom skalning är en aktivitet som enkelt kan anropas via hanterings-API:t, kan du enkelt bygga in möjligheten att skala klientdatabaser i ditt klientriktade program och erbjuda de som en funktion av din SaaS-tjänst. Exempelvis kan du låta klienter självhantera upp- och nedskalning, kanske kopplat direkt till sin fakturering!

**Skala en pool upp och ned enligt ett schema för att matcha användningsmönster**

I de fall där sammanställd klientanvändning följer förutsägbara mönster, kan du använda Azure Automation för att skala upp en pool efter ett schema. Skala exempelvis ned en pool efter 18.00 och upp igen innan 06.00 på veckodagar, när du vet att kraven på resursanvändning går ned.



## <a name="next-steps"></a>Nästa steg

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Simulerar användning på klientdatabaserna genom att köra en angiven belastningsgenerator
> * Övervakar klientdatabaserna när de svarar på belastningsökningar
> * Skalar upp den elastiska poolen i svar på den ökade databasbelastningen
> * Etablerar en andra elastisk pool för att belastningsutjämna databasaktiviteten

[Guiden återställ en enskild klient](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Ytterligare resurser

* Ytterligare [självstudier som bygger på Wingtip-biljetter SaaS-databas per klient program distribution](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastiska SQL-pooler](elastic-pool-overview.md)
* [Azure Automation](../../automation/automation-intro.md)
* [Azure Monitor loggar](./saas-dbpertenant-log-analytics.md) – skapa och använda Azure Monitor loggar självstudie