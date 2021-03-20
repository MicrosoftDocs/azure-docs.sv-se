---
title: Återställa en borttagen Azure Database for PostgreSQL Server
description: Den här artikeln beskriver hur du återställer en borttagen server i Azure Database for PostgreSQL att använda Azure Portal.
author: Bashar-MSFT
ms.author: bahusse
ms.service: postgresql
ms.topic: how-to
ms.date: 11/03/2020
ms.openlocfilehash: 591f01004cfba247112f702625ab05ddc0aaede3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652933"
---
# <a name="restore-a-dropped-azure-database-for-postgresql-server"></a>Återställa en borttagen Azure Database for PostgreSQL Server

När en server släpps kan säkerhets kopian av databas servern behållas upp till fem dagar i tjänsten. Säkerhets kopian av databasen kan nås och endast återställas från den Azure-prenumeration där servern ursprungligen befanns. Följande rekommenderade steg kan följas för att återställa en släppt PostgreSQL-Server-resurs inom 5 dagar från tidpunkten för borttagning av servern. De rekommenderade stegen fungerar bara om säkerhetskopian fortfarande är tillgänglig och inte har tagits bort från systemet. 

## <a name="pre-requisites"></a>Förutsättningar
Om du vill återställa en borttagen Azure Database for PostgreSQL Server, behöver du följande:
- Namn på Azure-prenumeration som är värd för den ursprungliga servern
- Plats där servern skapades

## <a name="steps-to-restore"></a>Steg för att återställa

1. Bläddra till [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade). Välj tjänsten **Azure Monitor** och välj sedan **aktivitets logg**.

2. I aktivitets loggen klickar du på **Lägg till filter** som visas och anger följande filter för följande

    - **Prenumeration** = prenumerationen som är värd för den borttagna servern
    - **Resurs typ** = Azure Database for PostgreSQL servrar (Microsoft. DBforPostgreSQL/servers)
    - **Åtgärd** = ta bort postgresql-server (Microsoft. DBforPostgreSQL/servers/Delete)
 
    ![Aktivitets logg filtrerad för borttagning av PostgreSQL-server åtgärd](./media/howto-restore-dropped-server/activity-log-azure.png)

3. Välj händelsen **ta bort postgresql-server** och välj sedan **fliken JSON**. Kopiera `resourceId` `submissionTimestamp` attributen och i JSON-utdata. ResourceId har följande format: `/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/deletedserver` .


 4. Bläddra till sidan PostgreSQL [skapa Server REST API](/rest/api/PostgreSQL/servers/create) och välj fliken **testa den** markerad i grönt. Logga in med ditt Azure-konto.

 5. Ange **resourceGroupName**, **servername** (borttaget Server namn), **SUBSCRIPTIONID** -egenskaper baserat på JSON-attributvärdet resourceId som samlats in i föregående steg 3. Egenskapen API-version är förifylld och kan vara kvar, som du ser i följande bild.

    ![Skapa server med REST API](./media/howto-restore-dropped-server/create-server-from-rest-api-azure.png)
  
 6. Rulla nedan och klistra in följande och ersätt den "borttagna Server platsen", "submissionTimestamp" och "resourceId". För "restorePointInTime" anger du värdet "submissionTimestamp" minus **15 minuter** för att se till att kommandot inte fel uppstår.
    
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

    Om den aktuella tiden till exempel är 2020-11-02T23:59:59.0000000 Z rekommenderar vi minst 15 minuter innan återställnings punkt i tid 2020-11-02T23:44:59.0000000 Z.

    > [!Important]
    > Det finns en tids gräns på fem dagar efter att servern släpptes. Efter fem dagar förväntas ett fel eftersom det inte går att hitta säkerhets kopierings filen.
    
7. Om du ser svars koden 201 eller 202 har återställnings förfrågan skickats. 

    Det kan ta tid att skapa servern beroende på databasens storlek och de beräknings resurser som har allokerats på den ursprungliga servern. Återställnings statusen kan övervakas från aktivitets loggen genom filtrering för 
   - **Prenumeration** = din prenumeration
   - **Resurs typ** = Azure Database for PostgreSQL servrar (Microsoft. DBforPostgreSQL/servers) 
   - **Åtgärd** = uppdatera postgresql-server skapa

## <a name="next-steps"></a>Nästa steg
- Om du försöker återställa en server inom fem dagar och fortfarande får ett fel efter att ha åtgärdat de steg som beskrivs ovan, öppnar du en support incident för att få hjälp. Om du försöker återställa en borttagen Server efter fem dagar förväntas ett fel eftersom det inte går att hitta säkerhets kopierings filen. Öppna inte ett support ärende i det här scenariot. Support teamet kan inte ge någon hjälp om säkerhets kopian tas bort från systemet. 
- För att förhindra oavsiktlig borttagning av servrar rekommenderar vi starkt att du använder [resurs lås](https://techcommunity.microsoft.com/t5/azure-database-for-PostgreSQL/preventing-the-disaster-of-accidental-deletion-for-your-PostgreSQL/ba-p/825222).
