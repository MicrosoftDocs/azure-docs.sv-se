---
title: Återställa en borttagen Azure Database for MariaDB Server
description: I den här artikeln beskrivs hur du återställer en borttagen server i Azure Database for MariaDB med hjälp av Azure Portal.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 4/12/2021
ms.openlocfilehash: 7cf2ce61239c7f2320f7b789acbb8f340e6de471
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315653"
---
# <a name="restore-a-deleted-azure-database-for-mariadb-server"></a>Återställa en borttagen Azure Database for MariaDB Server

När en server tas bort kan säkerhets kopian av databas servern behållas upp till fem dagar i tjänsten. Säkerhets kopian av databasen kan nås och endast återställas från den Azure-prenumeration där servern ursprungligen befanns. Följande rekommenderade steg kan följas för att återställa en borttagen MariaDB Server-resurs inom 5 dagar från tidpunkten för borttagning av servern. De rekommenderade stegen fungerar bara om säkerhetskopian fortfarande är tillgänglig och inte har tagits bort från systemet. 

## <a name="pre-requisites"></a>Förutsättningar
Om du vill återställa en borttagen Azure Database for MariaDB Server, behöver du följande:
- Namn på Azure-prenumeration som är värd för den ursprungliga servern
- Plats där servern skapades

## <a name="steps-to-restore"></a>Steg för att återställa

1. Gå till bladet [aktivitets logg](https://ms.portal.azure.com/#blade/Microsoft_Azure_ActivityLog/ActivityLogBlade) från övervaknings blad i Azure Portal. 

2. I aktivitets loggen klickar du på **Lägg till filter** som visas och anger följande filter för 

    - **Prenumeration** = prenumerationen som är värd för den borttagna servern
    - **Resurs typ** = Azure Database for MariaDB servrar (Microsoft. DBForMariaDB/servers) 
    - **Åtgärd** = ta bort MariaDB-Server (Microsoft. DBForMariaDB/servers/Delete) 
 
     [![Aktivitets logg filtrerad för borttagning av MariaDB-server åtgärd](./media/howto-restore-dropped-server/activity-log.png)](./media/howto-restore-dropped-server/activity-log.png#lightbox)
   
 3. Dubbelklicka på händelsen ta bort MariaDB-Server och klicka på fliken JSON och notera attributen "resourceId" och "submissionTimestamp" i JSON-utdata. ResourceId har följande format:/subscriptions/ffffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/TargetResourceGroup/providers/Microsoft.DBForMariaDB/servers/deletedserver.
 
 4. Gå till [skapa Server REST API sidan](/rest/api/mariadb/servers/create) och klicka på fliken "prova", markerad i grönt och logga in med ditt Azure-konto.
 
 5. Ange resourceGroupName, serverName (borttaget Server namn), subscriptionId som härletts från det resourceId-attribut som samlats in i steg 3, medan API-versionen är förifylld som visas i avbildningen.
 
     [![Skapa server med REST API](./media/howto-restore-dropped-server/create-server-from-rest-api.png)](./media/howto-restore-dropped-server/create-server-from-rest-api.png#lightbox)
  
 6. Rulla nedan och klistra in följande i avsnittet om brödtext:
     
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

7. Ersätt följande värden i begär ande texten ovan:
   * "Borttagen Server plats" med den Azure-region där den borttagna servern ursprungligen skapades
   * "submissionTimestamp" och "resourceId" med värdena som registrerades i steg 3. 
   * För "restorePointInTime" anger du värdet "submissionTimestamp" minus **15 minuter** för att se till att kommandot inte fel uppstår.

8. Om du ser svars koden 201 eller 202 har återställnings förfrågan skickats. 

9. Det kan ta tid att skapa servern beroende på databasens storlek och de beräknings resurser som har allokerats på den ursprungliga servern. Återställnings statusen kan övervakas från aktivitets loggen genom filtrering för 
   - **Prenumeration** = din prenumeration
   - **Resurs typ** = Azure Database for MariaDB servrar (Microsoft. DBForMariaDB/servers) 
   - **Åtgärd** = uppdatera MariaDB-Server skapa

## <a name="next-steps"></a>Nästa steg
- Om du försöker återställa en server inom fem dagar och fortfarande får ett fel efter att ha åtgärdat de steg som beskrivs ovan, öppnar du en support incident för att få hjälp. Om du försöker återställa en borttagen Server efter fem dagar förväntas ett fel eftersom det inte går att hitta säkerhets kopierings filen. Öppna inte ett support ärende i det här scenariot. Support teamet kan inte ge någon hjälp om säkerhets kopian tas bort från systemet. 
- För att förhindra oavsiktlig borttagning av servrar rekommenderar vi starkt att du använder [resurs lås](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/preventing-the-disaster-of-accidental-deletion-for-your-mysql/ba-p/825222).
