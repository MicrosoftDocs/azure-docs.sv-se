---
title: Återställa en databas i en SaaS-app med flera innehavare
description: Lär dig hur du återställer en enskild klients Azure SQL Database efter att ha tagit bort data av misstag
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 88496a39b0186cefb7c64e227530b5d73e693094
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780486"
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>Återställa en enskild klient med ett SaaS-program för databas per klient
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Modellen för databas per klient gör det enkelt att återställa en enskild klient till en tidigare tidpunkt utan att påverka andra klienter.

I den här självstudien får du lära dig två data återställnings mönster:

> [!div class="checklist"]
> * Återställ en databas till en parallell databas (sida vid sida).
> * Återställ en databas på plats och ersätt den befintliga databasen.

| Mönster | Beskrivning |
|:--|:--|
| Återställa till en parallell databas | Det här mönstret kan användas för uppgifter som granskning, granskning och efterlevnad för att tillåta en klient att inspektera sina data från en tidigare tidpunkt. Klient organisationens aktuella databas förblir online och oförändrad. |
| Återställning på plats | Det här mönstret används vanligt vis för att återställa en klient till en tidigare tidpunkt när en klient av misstag tar bort eller skadar data. Den ursprungliga databasen tas bort och ersätts med den återställda databasen. |
|||

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip SaaS-appen har distribuerats. Information om hur du distribuerar på mindre än fem minuter finns i [distribuera och utforska Wingtip SaaS-programmet](./saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>Introduktion till återställnings mönstren för SaaS-klienten

Det finns två enkla mönster för att återställa en enskild klients data. Eftersom klient databaserna är isolerade från varandra, har återställningen av en klient ingen inverkan på någon annan klients data. Funktionen Azure SQL Database PITR (Point-in-Time-Restore) används i båda mönstren. PITR skapar alltid en ny databas.

* **Återställ parallellt**: i det första mönstret skapas en ny parallell databas tillsammans med klientens aktuella databas. Klienten får sedan skrivskyddad åtkomst till den återställda databasen. Återställda data kan granskas och eventuellt användas för att skriva över aktuella data värden. Det är upp till app designer som avgör hur klienten får åtkomst till den återställda databasen och vilka alternativ som finns för återställning. Att bara tillåta klienten att granska sina data vid en tidigare tidpunkt kan vara allt som krävs i vissa scenarier.

* **Återställ på plats**: det andra mönstret är användbart om data har förlorats eller skadats och klienten vill återställa till en tidigare tidpunkt. Klienten tas bort från linjen medan databasen återställs. Den ursprungliga databasen tas bort och den återställda databasen får ett nytt namn. Säkerhets kopierings kedjan för den ursprungliga databasen är fortfarande tillgänglig efter borttagningen, så du kan återställa databasen till en tidigare tidpunkt, om det behövs.

Om databasen använder [aktiv geo-replikering](active-geo-replication-overview.md) och återställning parallellt, rekommenderar vi att du kopierar alla nödvändiga data från den återställda kopian till den ursprungliga databasen. Om du ersätter den ursprungliga databasen med den återställda databasen måste du konfigurera om och synkronisera om geo-replikeringen.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip-biljetterna SaaS Database-användarspecifika program skript

Wingtip biljetter SaaS-skript för flera klient organisationer och program käll kod är tillgängliga i [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub lagrings platsen. Anvisningar för hur du hämtar och avblockerar Wingtip Ticket SaaS-skript finns i den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="before-you-start"></a>Innan du börjar

När en databas har skapats kan det ta 10 till 15 minuter innan den första fullständiga säkerhets kopieringen är tillgänglig att återställa från. Om du precis har installerat programmet kan du behöva vänta några minuter innan du testar det här scenariot.

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>Simulera en klient som oavsiktligt tar bort data

För att demonstrera dessa återställnings scenarier måste du först ta bort en händelse i en av klient databaserna. 

### <a name="open-the-events-app-to-review-the-current-events"></a>Öppna appen Events om du vill granska aktuella händelser

1. Öppna Events Hub ( http://events.wtp.&lt ; User &gt; . trafficmanager.net) och välj **contoso konsert Hall**.

   ![Events Hub](./media/saas-dbpertenant-restore-single-tenant/events-hub.png)

2. Bläddra i listan över händelser och anteckna den sista händelsen i listan.

   ![Senaste händelsen visas](./media/saas-dbpertenant-restore-single-tenant/last-event.png)

### <a name="accidentally-delete-the-last-event"></a>"Ta bort den senaste händelsen" av misstag

1. I PowerShell ISE öppnar du... \\ Inlärnings moduler \\ affärs kontinuitet och haveri beredskap \\ RestoreTenant \\ *Demo-RestoreTenant.ps1* och ange följande värde:

   * **$DemoScenario**  =  **1**, *ta bort sista händelsen (utan biljett försäljning)*.
2. Tryck på F5 för att köra skriptet och ta bort den sista händelsen. Följande bekräftelse meddelande visas:

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

3. Sidan contoso-händelser öppnas. Rulla nedåt och kontrol lera att händelsen är borta. Om händelsen fortfarande finns i listan väljer du **Uppdatera** och kontrollerar att den är borta.
   ![Senaste händelse borttagen](./media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)

## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>Återställa en klient databas parallellt med produktions databasen

I den här övningen återställs contoso konsert Hall-databasen till en tidpunkt innan händelsen togs bort. Det här scenariot förutsätter att du vill granska borttagna data i en parallell databas.

 *Restore-TenantInParallel.ps1* -skriptet skapar en parallell klient databas med namnet *ContosoConcertHall \_ Old*, med en parallell katalog post. Det här återställnings mönstret passar bäst för att återställa från en mindre data förlust. Du kan också använda det här mönstret om du behöver granska data för efterlevnad eller gransknings syfte. Det är den rekommenderade metoden när du använder [aktiv geo-replikering](active-geo-replication-overview.md).

1. Slutför avsnittet [simulera en klient som tar bort data av misstag](#simulate-a-tenant-accidentally-deleting-data) .
2. I PowerShell ISE öppnar du... \\ Inlärnings moduler \\ affärs kontinuitet och haveri beredskap \\ RestoreTenant \\ _Demo-RestoreTenant.ps1_.
3. Ange **$DemoScenario**  =  **2**, *återställa klient organisationen parallellt*.
4. Tryck på F5 för att köra skriptet.

Skriptet återställer klient databasen till en tidpunkt innan du tog bort händelsen. Databasen återställs till en ny databas med namnet _ContosoConcertHall \_ Old_. Katalogens metadata som finns i den här återställda databasen tas bort och databasen läggs sedan till i katalogen med hjälp av en nyckel som skapats från det *ContosoConcertHall \_ gamla* namnet.

Demo skriptet öppnar sidan händelser för den här nya klient databasen i webbläsaren. Observera från URL: en ```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` som den här sidan visar data från den återställda databasen där *_old* läggs till i namnet.

Bläddra igenom händelserna som visas i webbläsaren för att bekräfta att händelsen som togs bort i föregående avsnitt har återställts.

Att exponera den återställda innehavaren som en ytterligare klient, med en egen Events-app, är sannolikt inte att vara hur du ger klient åtkomst till återställda data. Den används för att illustrera återställnings mönstret. Normalt ger du skrivskyddad åtkomst till gamla data och behåller den återställda databasen under en definierad period. I exemplet kan du ta bort den återställda klient posten när du är klar genom att köra scenariot _ta bort återställd klient_ .

1. Ange **$DemoScenario**  =  **4**, *ta bort återställd klient*.
2. Tryck på F5 för att köra skriptet.
3. Den *\_ gamla ContosoConcertHall* -posten tas nu bort från katalogen. Stäng sidan händelser för den här klienten i webbläsaren.

## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>Återställ en klient på plats och ersätt den befintliga klient databasen

I den här övningen återställs contoso konsert Hall-klienten till en punkt innan händelsen togs bort. *Restore-TenantInPlace-* skriptet återställer en klient databas till en ny databas och tar bort originalet. Detta återställnings mönster lämpar sig bäst för att återställa från allvarliga skadade data och klienten kan behöva hantera betydande data förlust.

1. Öppna den **Demo-RestoreTenant.ps1** filen i PowerShell ISE.
2. Ange **$DemoScenario**  =  **5**, *Återställ klient organisation på plats*.
3. Tryck på F5 för att köra skriptet.

Skriptet återställer klient databasen till en plats innan händelsen togs bort. Det tar först contoso konsert Hall-arbetsinnehavare att förhindra ytterligare uppdateringar. Sedan skapas en parallell databas genom att återställa från återställnings punkten. Den återställda databasen heter med en tidsstämpel för att kontrol lera att databas namnet inte står i konflikt med namnet på den befintliga klient organisations databasen. Sedan tas den gamla klient databasen bort och den återställda databasen byter namn till det ursprungliga databas namnet. Slutligen är contoso konsert Hall online så att appen får åtkomst till den återställda databasen.

Du har återställt databasen till en tidpunkt innan händelsen togs bort. När sidan **händelser** öppnas bekräftar du att den sista händelsen återställdes.

När du har återställt databasen tar det ytterligare 10 till 15 minuter innan den första fullständiga säkerhets kopieringen är tillgänglig att återställas igen.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Återställ en databas till en parallell databas (sida vid sida).
> * Återställ en databas på plats.

Prova själv studie kursen [Hantera klient organisations databas schema](saas-tenancy-schema-management.md) .

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på Wingtip SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Översikt över affärskontinuitet med Azure SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Lär dig mer om SQL Database säkerhets kopieringar](automated-backups-overview.md)