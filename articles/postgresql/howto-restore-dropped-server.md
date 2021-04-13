---
title: Återställa en bort Azure Database for PostgreSQL server
description: Den här artikeln beskriver hur du återställer en bort ignorerad server i Azure Database for PostgreSQL med hjälp av Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 5b5bb9fd6e3d34fc4a6b0ae90a2cd76fc84e9ce1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366529"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Återställa en bort Azure Database for PostgreSQL server

När en server tas bort kan säkerhetskopian av databasservern behållas i upp till fem dagar i tjänsten. Säkerhetskopian av databasen kan endast nås och återställas från den Azure-prenumeration där servern ursprungligen fanns. Följande rekommenderade steg kan följas för att återställa en borttagen PostgreSQL-serverresurs inom 5 dagar från tidpunkten för serverborttagningen. De rekommenderade stegen fungerar bara om säkerhetskopian fortfarande är tillgänglig och inte har tagits bort från systemet. 

## <a name="pre-requisites"></a>Förutsättningar
Om du vill återställa en Azure Database for PostgreSQL server behöver du följande:
- Azure-prenumerationsnamn som är värd för den ursprungliga servern
- Plats där servern skapades

## <a name="steps-to-restore"></a>Steg för att återställa

1. Bläddra till [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Välj **Azure Monitor** och sedan **Aktivitetslogg.**

2. I aktivitetsloggen klickar du **på Lägg till filter** så som det visas och anger följande filter för följande

    - **Prenumeration** = din prenumeration som är värd för den borttagna servern
    - **Resurstyp** = Azure Database for PostgreSQL servrar (Microsoft.DBforPostgreSQL/servers)
    - **Åtgärd** = Ta bort PostgreSQL-server (Microsoft.DBforPostgreSQL/servers/delete)
 
    ![Aktivitetslogg filtrerad för att ta bort PostgreSQL-serveråtgärd](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Välj händelsen Delete PostgreSQL Server (Ta bort **PostgreSQL-server)** och välj sedan **fliken JSON**. Kopiera `resourceId` attributen `submissionTimestamp` och i JSON-utdata. resourceId har följande format: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Bläddra till sidan Skapa server för PostgreSQL [REST API och](/rest/api/PostgreSQL/servers/create) välj **fliken Prova** markerad i grönt. Logga in med ditt Azure-konto.

 5. Ange **resourceGroupName**, **serverName** (borttagna servernamn), **subscriptionId-egenskaper,** baserat på resourceId-attributets JSON-värde som avbildades i föregående steg 3. Api-versionsegenskapen är förifylld och kan lämnas som den är, som du ser i följande bild.

    ![Skapa server med REST API](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Rulla nedan i avsnittet Begärandetext och klistra in följande ersätter "Dropped server Location" (t.ex. CentralUS, EastUS osv.), "submissionTimestamp" och "resourceId". För "restorePointInTime" anger du värdet "submissionTimestamp" minus **15** minuter för att se till att kommandot inte får fel.
    
    ```json
    {
      "location": "Dropped Server Location",  
      "properties": 
      {
        "restorePointInTime": "submissionTimestamp - 15 minutes",
        "createMode": "PointInTimeRestore",
        "sourceServerId": "resourceId"
      }
    }
    ```

    Om den aktuella tiden till exempel är 2020-11-02T23:59:59.0000000Z rekommenderar vi minst 15 minuter före återställningspunkten i tid 2020-11-02T23:44:59.0000000Z.

    > [!Important]
    > Det finns en tidsgräns på fem dagar efter att servern togs bort. Efter fem dagar förväntas ett fel eftersom det inte går att hitta säkerhetskopian.
    
7. Om svarskoden 201 eller 202 visas skickas återställningsbegäran. 

    Det kan ta tid att skapa servern beroende på databasens storlek och beräkningsresurser som etablerats på den ursprungliga servern. Återställningsstatusen kan övervakas från aktivitetsloggen genom filtrering för 
   - **Prenumeration** = din prenumeration
   - **Resurstyp** = Azure Database for PostgreSQL servrar (Microsoft.DBforPostgreSQL/servers) 
   - **Åtgärd** = Uppdatera PostgreSQL Server Create

## <a name="next-steps"></a>Nästa steg
- Om du försöker återställa en server inom fem dagar och fortfarande får ett felmeddelande efter att ha följt stegen som beskrivs ovan, kan du öppna ett supportincident för att få hjälp. Om du försöker återställa en bort ignorerad server efter fem dagar förväntas ett fel eftersom det inte går att hitta säkerhetskopian. Öppna inte en supportbiljett i det här scenariot. Supportteamet kan inte ge någon hjälp om säkerhetskopian tas bort från systemet. 
- För att förhindra oavsiktlig borttagning av servrar rekommenderar vi starkt att du använder [resurslås.](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222)
