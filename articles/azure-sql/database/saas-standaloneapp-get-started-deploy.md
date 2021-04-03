---
title: Själv studie kurs om en enda klient SaaS
description: Distribuera och utforska ett fristående SaaS-program med en enda klient, som använder Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/07/2018
ms.openlocfilehash: 896f4edb1e94c1eca06b046382727a5042375e3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793287"
---
# <a name="deploy-and-explore-a-standalone-single-tenant-application-that-uses-azure-sql-database"></a>Distribuera och utforska ett fristående program med en enda klient som använder Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien distribuerar och utforskar du Wingtip Ticket SaaS-exempelprogrammet som utvecklats med det fristående programmet, eller ett mönster för app-per-innehavare.  Programmet är utformat för att demonstrera funktioner i Azure SQL Database som fören klar aktivering av SaaS-scenarier för flera innehavare.

Det fristående programmet eller appen per klient mönster distribuerar en program instans för varje klient.  Varje program konfigureras för en specifik klient och distribueras i en separat Azure-resurs grupp. Flera instanser av programmet är etablerade för att tillhandahålla en lösning för flera klienter. Det här mönstret lämpar sig bäst för mindre tal, av klienter där klient isolering är en högsta prioritet. Azure har partner program som gör att resurser kan distribueras till en klient organisations prenumeration och hanteras av en tjänst leverantör på klientens vägnar. 

I den här självstudien distribuerar du tre fristående program för tre klienter i din Azure-prenumeration.  Du har fullständig åtkomst till att utforska och arbeta med enskilda program komponenter.

Programmets käll kod och hanterings skript är tillgängliga i [WingtipTicketsSaaS-StandaloneApp GitHub-](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp) lagrings platsen. Programmet skapades med Visual Studio 2015 och kunde inte öppnas och kompileras i Visual Studio 2019 utan att uppdateras.


I den här guiden lär du dig:

> [!div class="checklist"]
> * Så här distribuerar du Wingtip Ticket SaaS standalone-programmet.
> * Var du får program käll koden och hanterings skript.
> * Om de servrar och databaser som utgör appen.

Ytterligare självstudier kommer att släppas. De gör att du kan utforska en mängd olika hanterings scenarier baserat på det här program mönstret.   

## <a name="deploy-the-wingtip-tickets-saas-standalone-application"></a>Distribuera Wingtip Ticket SaaS fristående program

Distribuera appen för de tre tillhandahållna klient organisationerna:

1. Klicka på knappen för varje blå **distribution till Azure** för att öppna distributions mal len i [Azure Portal](https://portal.azure.com). Varje mall kräver två parameter värden. ett namn för en ny resurs grupp och ett användar namn som särskiljer den här distributionen från andra distributioner av appen. Nästa steg innehåller information om hur du anger dessa värden.

   **Contoso konsert Hall**   
   [![Bild som visar en knapp med etiketten "distribuera till Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-contoso)

   **Dogwood Dojo**   
   [![Bild som visar en knapp med etiketten "distribuera till Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-dogwood)

   **Fabrikam jazz-klubb**   
   [![Bild som visar en knapp med etiketten "distribuera till Azure".](media/saas-standaloneapp-get-started-deploy/deploy.png)](https://aka.ms/deploywingtipsa-fabrikam)
 
2. Ange nödvändiga parameter värden för varje distribution.

    > [!IMPORTANT]
    > Vissa autentiserings-och Server brand väggar är avsiktligt oskyddade i demonstrations syfte. **Skapa en ny resurs grupp** för varje program distribution.  Använd inte en befintlig resurs grupp. Använd inte det här programmet eller några resurser som skapas för produktion. Ta bort alla resurs grupper när du är färdig med programmen för att stoppa relaterad fakturering.

    Det är bäst att endast använda gemena bokstäver, siffror och bindestreck i resurs namnen.
    * För **resurs grupp** väljer du skapa ny och anger sedan ett gement namn för resurs gruppen. **Wingtip-sa- \<venueName\> - \<user\>** är det rekommenderade mönstret.  För \<venueName\> ersätter du platsens namn utan blank steg. För \<user\> ersätter du användar värdet nedan.  Med det här mönstret kan resurs grupp namn vara *Wingtip-sa-contosoconcerthall-AF1*, *Wingtip-sa-dogwooddojo-AF1*, *Wingtip-sa-fabrikamjazzclub-AF1*.
    * Välj en **plats** i list rutan.

    * För **användare** – vi rekommenderar ett kort användar värde, till exempel dina initialer plus en siffra: till exempel *AF1*.


3. **Distribuera programmet**.

    * Klicka om du vill godkänna de allmänna villkoren.
    * Klicka på **Köp**.

4. Övervaka status för alla tre distributioner genom att klicka på **aviseringar** (klock ikonen till höger om sökrutan). Det tar cirka fem minuter att distribuera apparna.


## <a name="run-the-applications"></a>Köra programmen

I appen demonstreras platser som är värdar för händelser.  Platserna är klient organisationerna för programmet. Varje plats får en anpassad webbplats som visar sina händelser och säljer biljetter. Plats typerna omfattar konsert salar, Jazz klöver och idrotts klubbar. I exemplet bestämmer typ av plats det bakgrunds fotografi som visas på platsens webbplats.   I den fristående app-modellen har varje plats en separat program instans med sin egen fristående Azure SQL Database.

1. Öppna sidan händelser för var och en av de tre klient organisationerna i separata flikar i webbläsaren:

   - http://events.contosoconcerthall.&lt; User &gt; . trafficmanager.net
   - http://events.dogwooddojo.&lt; User &gt; . trafficmanager.net
   - http://events.fabrikamjazzclub.&lt; User &gt; . trafficmanager.net

     (I varje URL ersätter &lt; du användare &gt; med distributionens användar värde.)

   ![Händelser](./media/saas-standaloneapp-get-started-deploy/fabrikam.png)

Appen använder [*Azure Traffic Manager*](../../traffic-manager/traffic-manager-overview.md)för att kontrol lera distributionen av inkommande begär Anden. Varje klient-/regionsspecifika App-instans innehåller klient namnet som en del av domän namnet i URL: en. Alla klient-URL: er innehåller ditt speciella **användar** värde. URL: erna följer följande format:
- http://events.&lt; venuename &gt; . &lt; User &gt; . trafficmanager.net

Varje klients databas **plats** ingår i appens inställningar för den motsvarande distribuerade appen.

I en produktions miljö kan du vanligt vis skapa en CNAME DNS-post för att [*peka företagets Internet domän*](../../traffic-manager/traffic-manager-point-internet-domain.md) till URL: en för Traffic Manager-profilen.


## <a name="explore-the-servers-and-tenant-databases"></a>Utforska servrar och klient databaser

Nu ska vi titta på några av de resurser som har distribuerats:

1. I [Azure Portal](https://portal.azure.com)bläddrar du till listan över resurs grupper.
2. Du bör se de tre resurs grupperna för klient organisationen.
3. Öppna resurs gruppen **Wingtip-sa-Fabrikam &lt; - &gt; User** som innehåller resurserna för Fabrikam Jazz-distributionen.  **Fabrikamjazzclub- &lt; User &gt; -** servern innehåller **fabrikamjazzclub** -databasen.

Varje klient databas är en *fristående* 50 DTU-databas.

## <a name="additional-resources"></a>Ytterligare resurser

<!--
* Additional [tutorials that build on the Wingtip SaaS application](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](elastic-pool-overview.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](./elastic-jobs-overview.md)
-->

- Mer information om SaaS-program för flera innehavare finns i [design mönster för SaaS-program med flera innehavare](saas-tenancy-app-design-patterns.md).

 
## <a name="delete-resource-groups-to-stop-billing"></a>Ta bort resurs grupper för att stoppa faktureringen ##

När du är färdig med att använda exemplet tar du bort alla resurs grupper som du har skapat för att stoppa den associerade faktureringen.

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> * Så här distribuerar du Wingtip Ticket SaaS standalone-programmet.
> * Om de servrar och databaser som utgör appen.
> * Så här tar du bort exempel resurser för att stoppa relaterad fakturering.

Prova sedan själv studie kursen för [etablering och katalog](saas-standaloneapp-provision-and-catalog.md) där du får utforska användningen av en katalog med klienter som möjliggör flera olika klient scenarier som schema hantering och klient analys.
