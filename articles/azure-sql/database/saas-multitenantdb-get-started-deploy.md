---
title: Distribuera en shardade-app med flera klient databaser SaaS
description: Distribuera och utforska shardade Wingtip ticks-SaaS för flera klient organisationer, som demonstrerar SaaS mönster med hjälp av Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/16/2018
ms.openlocfilehash: 4de7e428bff0feaafdec00b0c0014bbaf6acb917
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790975"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Distribuera och utforska ett shardade-program för flera innehavare
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien får du distribuera och utforska ett exempel på ett SaaS-program med flera innehavare som heter Wingtip-biljetter. Wingtip biljetter-appen är utformad för att demonstrera funktioner i Azure SQL Database som fören klar implementeringen av SaaS-scenarier.

Den här implementeringen av Wingtip biljetters-appen använder ett shardade databas mönster för flera innehavare. Horisontell partitionering är av klient-ID. Klient data distribueras till en viss databas enligt klient-ID-värdena. 

Med det här databas mönstret kan du lagra en eller flera klienter i varje Shard eller databas. Du kan optimera för lägsta kostnad genom att varje databas delas av flera klienter. Eller så kan du optimera för isolering genom att bara låta varje databas lagra en klient. Optimerings valet kan göras oberoende för varje enskild klient. Du kan välja att göra det när klienten är lagrad första gången eller ändra dig senare. Programmet är utformat för att fungera på ett bra sätt.

## <a name="app-deploys-quickly"></a>Appen distribueras snabbt

Appen körs i Azure-molnet och använder Azure SQL Database. Avsnittet distribution som följer innehåller knappen blå **distribution till Azure** . När knappen trycks ned, distribueras appen fullständigt till din Azure-prenumeration inom fem minuter. Du har fullständig åtkomst för att arbeta med de enskilda program komponenterna.

Programmet distribueras med data för tre exempel klienter. Klienterna lagras tillsammans i en databas med flera innehavare.

Vem som helst kan ladda ned C#-och PowerShell-källkoden för Wingtip-biljetter från [sin GitHub-lagringsplats][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Lär dig i den här självstudien

> [!div class="checklist"]
> - Så här distribuerar du Wingtip Ticket SaaS-program.
> - Var du får program käll koden och hanterings skript.
> - Om de servrar och databaser som utgör appen.
> - Hur klienter mappas till sina data med *katalogen*.
> - Så här etablerar du en ny klient.
> - Övervaka klient aktivitet i appen.

Det finns en serie relaterade självstudier som bygger på den här första distributionen. Självstudierna utforskar en rad SaaS design-och hanterings mönster. När du arbetar med självstudierna rekommenderar vi att du stega igenom de tillhandahållna skripten för att se hur de olika SaaS-mönstren implementeras.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

- Den senaste Azure PowerShell är installerad. Mer information finns i [komma igång med Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Distribuera Wingtip Ticket-appen

### <a name="plan-the-names"></a>Planera namnen

I stegen i det här avsnittet anger du ett *användar* värde som används för att säkerställa att resurs namn är globalt unika och ett namn för *resurs gruppen* som innehåller alla resurser som har skapats av en distribution av appen. För en person med namnet *Ann Finley* föreslår vi:
- *Användare:* **AF1**  *(deras initialer, plus en siffra. Använd ett annat värde (t. ex. af2) om du distribuerar appen en andra gång.)*
- *Resurs grupp:* **Wingtip-MT-AF1** *(Wingtip-MT indikerar att detta är shardade för flera klient organisationer. om du lägger till användar namnet AF1 korreleras resurs gruppens namn med namnen på de resurser som den innehåller.)*

Välj dina namn nu och skriv ned dem. 

### <a name="steps"></a>Steg

1. Klicka på knappen följande blå **distribution till Azure** .
   - Den öppnar Azure Portal med distributions mal len Wingtip biljetter SaaS.

     [![Knapp för att distribuera till Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Ange de parameter värden som krävs för distributionen.

    > [!IMPORTANT]
    > I den här demonstrationen ska du inte använda några befintliga resurs grupper, servrar eller pooler. Välj i stället **skapa en ny resurs grupp**. Ta bort den här resursgruppen när du är klar med att programmet för att stoppa relaterad fakturering.
    > Använd inte det här programmet eller några resurser som skapas för produktion. Vissa aspekter av autentiseringen och inställningarna för Server brand väggen är avsiktligt oskyddade i appen för att under lätta demonstrationen.

    - För **resurs grupp** – Välj **Skapa ny** och ange ett **namn** för resurs gruppen (Skift läges känsligt).
        - Välj en **plats** i list rutan.
    - För **användare** – vi rekommenderar att du väljer ett kort **användar** värde.

1. **Distribuera programmet**.

    - Klicka om du vill godkänna de allmänna villkoren.
    - Klicka på **Köp**.

1. Övervaka distributions status genom att klicka på **meddelanden**, som är klock ikonen till höger om sökrutan. Distribution av Wingtip-appen tar cirka fem minuter.

   ![distributionen lyckades](./media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Ladda ned och avblockera hanterings skript

När programmet distribueras laddar du ned program käll koden och hanterings skripten.

> [!NOTE]
> Körbart innehåll (skript, DLL-filer) kan blockeras av Windows när zip-filer laddas ned från en extern källa och extraheras. När du extraherar skripten från en zip-fil kan du använda följande steg för att avblockera zip-filen innan du extraherar. Genom att avblockera. zip-filen ser du till att skripten tillåts köra.

1. Bläddra till [WingtipTicketsSaaS-MultiTenantDb GitHub lagrings platsen](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Klicka på **klona eller hämta**.
3. Klicka på **Hämta zip** och spara filen.
4. Högerklicka på **WingtipTicketsSaaS-MultiTenantDb-master.zip** -filen och välj **Egenskaper**.
5. På fliken **Allmänt** väljer du **avblockera** och klickar på **Använd**.
6. Klicka på **OK**.
7. Extrahera filerna.

Skripten finns i *... \\ WingtipTicketsSaaS – MultiTenantDb-Master \\ Learning moduless \\ -* mappen.

## <a name="update-the-configuration-file-for-this-deployment"></a>Uppdatera konfigurations filen för den här distributionen

Ange *resurs grupp* och *användar* värden i **userconfig. psm1** innan du kör skript. Ange dessa variabler till samma värden som du angav under distributionen.

1. Öppna... \\ Learning-moduler \\ *userconfig. Psm1* i *PowerShell ISE*.
2. Uppdatera *ResourceGroupName* och *namn* med de angivna värdena för din distribution (endast på raderna 10 och 11).
3. Spara ändringarna.

Värdena som anges i den här filen används av alla skript, så det är viktigt att de är korrekta. Om du distribuerar om appen måste du välja olika värden för användar-och resurs grupp. Uppdatera sedan filen UserConfig. psm1 igen med de nya värdena.

## <a name="run-the-application"></a>Kör programmet

I Wingtip-appen är klienterna platser. En plats kan vara konsert Hall, en idrotts klubb eller annan plats som är värd för händelser. Platserna registreras i Wingtip som kunder och ett klient-ID genereras för varje plats. Varje plats visar sina kommande händelser i Wingtip, så att allmänheten kan köpa biljetter till händelserna.

Varje plats får en anpassad webbapp som visar sina händelser och säljer biljetter. Varje webbapp är oberoende och isolerad från andra klienter. Internt i Azure SQL Database lagras varje data för varje klient i en shardade-databas som standard. Alla data är taggade med klient-ID: n.

En webb sida för central **evenemangs hubb** innehåller en lista över länkar till klienterna i din specifika distribution. Använd följande steg för att uppleva webb sidan för **Events Hub** och en enskild webbapp:

1. Öppna **Events-hubben** i webbläsaren:
   - http://events.wingtip-mt.&lt; User &gt; . trafficmanager.net &nbsp; *(Ersätt &lt; användare &gt; med distributionens användar värde.)*

     ![evenemangshubben](./media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Klicka på **Fabrikam Jazzklubb** i **evenemangshubben**.

   ![Händelser](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

Wingtip-appen använder [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)för att kontrol lera distributionen av inkommande begär Anden. Sidan händelser för varje klient innehåller klient namnet i URL: en. Varje URL innehåller också ditt speciella användar värde. Varje URL lyder under det visade formatet med hjälp av följande steg:

- http://events.wingtip-mt.&lt; User &gt; . trafficmanager.net/*fabrikamjazzclub*

1. I appen händelser parsas klient namnet från URL: en. Klient namnet är *fabrikamjazzclub* i föregående exempel-URL.
2. Appen hashar sedan klient namnet för att skapa en nyckel för att få åtkomst till en katalog med hjälp av [Shard Map Management](elastic-scale-shard-map-management.md).
3. Appen hittar nyckeln i katalogen och hämtar motsvarande plats för klientens databas.
4. Appen använder plats informationen för att hitta och komma åt den databas som innehåller alla data för klienten.

### <a name="events-hub"></a>Events Hub

1. **Events-hubben** visar en lista över alla klienter som är registrerade i katalogen och deras platser.
2. **Events Hub** använder utökade metadata i katalogen för att hämta klient organisationens namn som är associerat med varje mappning för att skapa URL: erna.

I en produktions miljö skapar du vanligt vis en CNAME DNS-post för att [peka företagets Internet domän](../../traffic-manager/traffic-manager-point-internet-domain.md) mot Traffic Manager-profilen.

## <a name="start-generating-load-on-the-tenant-databases"></a>Börja generera belastningar på klient-databaserna

Nu när appen har distribuerats kan vi placera den i arbetet! PowerShell *-skriptet demo-LoadGenerator* startar en arbets belastning som körs för varje klient. Den verkliga belastningen på många SaaS-appar är vanligt vis sporadisk och oförutsägbar. För att simulera den här typen av belastning ger generatorn en belastning fördelad över alla klienter. Belastningen inkluderar slumpmässiga burst-överföringar på varje klient som inträffar med slumpmässiga intervall. Det tar flera minuter för inläsnings mönstret att visas, så det är bäst att låta generatorn köras i minst tre minuter innan belastningen övervakas.

1. I *POWERSHELL ISE* öppnar du... \\ Verktyg för inlärnings moduler \\ \\ *Demo-LoadGenerator.ps1* skript.
2. Tryck **F5** för att köra skriptet och starta belastningsgeneratorn (Lämna standardvärdet för parametern för tillfället).

*Demo-LoadGenerator.ps1* -skriptet öppnar en annan PowerShell-session där belastnings generatorn körs. Belastnings generatorn körs i den här sessionen som en förgrunds aktivitet som anropar bakgrunds jobb för inläsnings jobb, en för varje klient.

När förgrunds aktiviteten startar är den kvar i ett jobb som anropas. Aktiviteten startar ytterligare bakgrunds jobb för alla nya klienter som senare har allokerats.

Om du stänger PowerShell-sessionen stoppas alla jobb.

Du kanske vill starta om belastnings Generator-sessionen för att använda olika parameter värden. I så fall, Stäng PowerShell-sessionen och kör sedan *Demo-LoadGenerator.ps1* igen.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Etablera en ny klient i shardade-databasen

Den första distributionen innehåller tre exempel klienter i *Tenants1* -databasen. Nu ska vi skapa en annan klient och observera dess effekter på det distribuerade programmet. I det här steget trycker du på en nyckel för att skapa en ny klient:

1. Öppna... \\ Inlärnings moduler \\ tillhandahåller och katalogiserar \\ *Demo-ProvisionTenants.ps1* i *PowerShell ISE*.
2. Tryck på **F5** (inte **F8**) för att köra skriptet (lämna standardvärdena för tillfället).

   > [!NOTE]
   > Du måste bara köra PowerShell-skripten genom att trycka på **F5** , inte genom att trycka på **F8** för att köra en markerad del av skriptet. Problemet med **F8** är att variabeln *$PSScriptRoot* inte utvärderas. Den här variabeln krävs av många skript för att navigera bland mappar, anropa andra skript eller importera moduler.

Den nya röda Tenants1-klienten läggs till i  -databasen och registreras i katalogen. Den nya klient organisationens biljett försäljnings **evenemang** öppnas i webbläsaren:

![Ny klient](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Uppdatera **händelsehubben** och den nya klient organisationen visas nu i listan.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Etablera en ny klient i en egen databas

Med shardade för flera klienter kan du välja om du vill etablera en ny klient i en databas som innehåller andra klienter eller i en egen databas. En klient som är isolerad i en egen databas har följande fördelar:

- Prestanda för klient organisationens databas kan hanteras utan att behöva kompromissa med behoven hos andra klienter.
- Vid behov kan databasen återställas till en tidigare tidpunkt, eftersom inga andra klienter påverkas.

Du kan välja att publicera kostnads fria utvärderings kunder eller ekonomi kunder i databaser med flera klienter. Du kan ställa in varje Premium-klient i sin egen dedikerade databas. Om du skapar många databaser som bara innehåller en klient kan du hantera dem tillsammans i en elastisk pool för att optimera resurs kostnaderna.

Sedan etablerar vi en annan klient, den här gången i en egen databas:

1. I... \\ Lär dig \\ att etablera och katalogisera \\ *Demo-ProvisionTenants.ps1*, ändra *$TenantName* till **Salix salsa**, *$VenueType* till **kontrollen åt** och *$scenario* till **2**.

2. Tryck på **F5** för att köra skriptet igen.
    - Den här **F5** -tryckningen etablerar den nya klienten i en separat databas. Databasen och klient organisationen är registrerade i katalogen. Sedan öppnas webbläsaren på sidan händelser för klient organisationen.

   ![Sidan Salix salsa-händelser](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Rulla längst ned på sidan. I informations texten ser du databas namnet där klient data lagras.

3. Uppdatera **Event Hub** och de två nya klient organisationerna visas nu i listan.

## <a name="explore-the-servers-and-tenant-databases"></a>Utforska servrar och klient databaser

Nu tittar vi på några av de resurser som har distribuerats:

1. I [Azure Portal](https://portal.azure.com)bläddrar du till listan över resurs grupper. Öppna resurs gruppen som du skapade när du distribuerade programmet.

   ![Resursgrupp](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Klicka på **katalog- &lt; MT &gt; användar** Server. Katalog servern innehåller två databaser med namnet *tenantcatalog* och *basetenantdb*. *Basetenantdb* -databasen är en tom mall databas. Den kopieras för att skapa en ny klient databas, oavsett om den används för många klienter eller bara en klient.

   ![katalogserver](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Gå tillbaka till resurs gruppen och välj den *tenants1-MT-* server som innehåller klient databaserna.
    - Tenants1-databasen är en databas för flera innehavare där de tre ursprungliga klienterna, plus den första klient organisationen som du lade till, lagras. Den är konfigurerad som en 50 DTU standard-databas.
    - **Salixsalsa** -databasen innehåller Salix salsa kontrollen åt-platsen som sin enda klient. Den konfigureras som en standard-versions databas med 50 DTU: er som standard.

   ![klient organisations Server](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Övervaka databasens prestanda

Om belastnings generatorn har körts i flera minuter finns tillräckligt med telemetri för att titta på de databas övervaknings funktioner som är inbyggda i Azure Portal.

1. Bläddra till **tenants1-MT- &lt; användar &gt;** servern och klicka på **tenants1** för att Visa resursutnyttjande för den databas som har fyra klienter. Varje klient är underkastad sporadiskt hög belastning från belastnings generatorn:

   ![övervaka tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Användnings diagrammet för DTU illustrerar snyggt hur en databas med flera innehavare kan stödja en oförutsägbar arbets belastning över flera klienter. I det här fallet använder belastnings generatorn en sporadisk belastning på ungefär 30 DTU: er till varje klient. Den här belastningen motsvarar 60% användning av en 50 DTU-databas. Toppar som överstiger 60% är resultatet av belastningen som tillämpas på fler än en klient på samma tidpunkt.

2. Bläddra till **tenants1-MT- &lt; användar &gt;** servern och klicka på **salixsalsa** -databasen. Du kan se resursutnyttjande för den här databasen som bara innehåller en klient.

   ![salixsalsa-databas](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Belastnings generatorn använder en liknande belastning för varje klient, oavsett vilken databas varje klient är i. Med bara en klient i **salixsalsa** -databasen kan du se att databasen kan hantera en mycket högre belastning än databasen med flera klienter. 

### <a name="resource-allocations-vary-by-workload"></a>Resurs tilldelningar varierar mellan arbets belastning

Ibland kräver en databas för flera innehavare fler resurser för att få bättre prestanda än en enda klient databas, men inte alltid. Den optimala fördelningen av resurser beror på arbets Belastningens egenskaper för klienterna i systemet.

Arbets belastningarna som genereras av belastnings Generator skriptet är endast avsedda som illustration.

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information om SaaS-program för flera innehavare finns i [design mönster för SaaS-program med flera innehavare](saas-tenancy-app-design-patterns.md).

- Information om elastiska pooler finns i:

  - [Elastiska pooler hjälper dig att hantera och skala flera databaser i Azure SQL Database](elastic-pool-overview.md)
  - [Skala ut med Azure SQL Database](elastic-scale-introduction.md)

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> - Så här distribuerar du Wingtip-biljetterna SaaS-databasen för flera klient organisationer.
> - Om de servrar och databaser som utgör appen.
> - Klienter mappas till sina data med *katalogen*.
> - Hur du etablerar nya klienter i en databas för flera klienter och en databas med en enda klient.
> - Så här visar du användningen av poolen för att övervaka klient aktivitet.
> - Så här tar du bort exempel resurser för att stoppa relaterad fakturering.

Prova nu [själv studie kursen om etablering och katalog](saas-multitenantdb-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: /powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Knapp för att distribuera till Azure."