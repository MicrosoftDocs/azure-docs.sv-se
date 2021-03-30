---
title: Övervaka migrering av aktivitet – Azure Database Migration Service
description: Lär dig att använda Azure Database Migration Service för att övervaka migreringen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: dc9123735ef65bec89e99a14d25cba881be306fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91297478"
---
# <a name="monitor-migration-activity-using-the-azure-database-migration-service"></a>Övervaka migrering av aktiviteter med hjälp av Azure Database Migration Service
I den här artikeln får du lära dig hur du övervakar förloppet för en migrering på både en databas nivå och en tabell nivå.

## <a name="monitor-at-the-database-level"></a>Övervaka på databas nivå
Om du vill övervaka aktiviteten på databas nivå kan du Visa bladet på databas nivå:

![Bladet på databas nivå](media/how-to-monitor-migration-activity/dms-database-level-blade.png)

> [!NOTE]
> Om du väljer Database-hyperlänken visas listan över tabeller och deras migrerings förlopp.

I följande tabell visas fälten på bladet på databas nivå och en beskrivning av de olika status värden som är associerade med var och en.

<table id='overview' class='overview'>
  <thead>
    <tr>
      <th class="x-hidden-focus"><strong>Fältnamn</strong></th>
      <th><strong>Fält under status</strong></th>
      <th><strong>Beskrivning</strong></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="3" class="ActivityStatus"><strong>Aktivitets status</strong></td>
      <td>Körs</td>
      <td>Migreringen körs.</td>
    </tr>
    <tr>
      <td>Lyckades</td>
      <td>Migreringen lyckades utan problem.</td>
    </tr>
    <tr>
      <td>Felaktig</td>
      <td>Migreringen misslyckades. Välj länken "se fel information" under information om migreringen för det fullständiga fel meddelandet.</td>
    </tr>
    <tr>
      <td rowspan="4" class="Status"><strong>Status</strong></td>
      <td>Initierar</td>
      <td>DMS konfigurerar pipelinen för migrering.</td>
    </tr>
    <tr>
      <td>Körs</td>
      <td>DMS-pipeline kör och utför migrering.</td>
    </tr>
    <tr>
      <td>Klart</td>
      <td>Migreringen har slutförts.</td>
    </tr>
    <tr>
      <td>Misslyckad</td>
      <td>Migreringen misslyckades. Klicka på information om migrering för att se migrerings fel.</td>
    </tr>
    <tr>
      <td rowspan="5" class="migration-details"><strong>Information om migrering</strong></td>
      <td>Initierar pipelinen för migrering</td>
      <td>DMS konfigurerar pipelinen för migrering.</td>
    </tr>
    <tr>
      <td>Fullständig data inläsning pågår</td>
      <td>DMS utför den första belastningen.</td>
    </tr>
    <tr>
      <td>Redo för start punkt</td>
      <td>När den första inläsningen har slutförts markerar DMS-databasen som klar för start punkt. Användaren bör kontrol lera om data har hämtats vid kontinuerlig synkronisering.</td>
    </tr>
    <tr>
      <td>Alla ändringar har tillämpats</td>
      <td>Den inledande inläsningen och den kontinuerliga synkroniseringen är klar. Den här statusen inträffar även efter att databasen har start punkt lyckats.</td>
    </tr>
    <tr>
      <td>Se fel information</td>
      <td>Klicka på länken för att visa fel information.</td>
    </tr>
    <tr>
      <td rowspan="1" class="duration"><strong>Varaktighet</strong></td>
      <td>Ej tillämpligt</td>
      <td>Total tid från migrerings aktivitet som initieras för migrering slutförd eller migrering misslyckades.</td>
    </tr>
     </tbody>
</table>

## <a name="monitor-at-table-level--quick-summary"></a>Övervaka på tabell nivå – snabb sammanfattning
Om du vill övervaka aktiviteten på tabell nivå visar du bladet tabell nivå. Den övre delen av bladet visar det detaljerade antalet rader som migrerats vid fullständig belastning och stegvisa uppdateringar. 

Den nedre delen av bladet listar tabellerna och visar en snabb sammanfattning av migreringens förlopp.

![Blad-snabb sammanfattning på tabell nivå](media/how-to-monitor-migration-activity/dms-table-level-blade-summary.png)

I följande tabell beskrivs fälten som visas i informationen på tabell nivå.

| Fältnamn        | Beskrivning       |
| ------------- | ------------- |
| **Fullständig inläsning slutförd**      | Antal tabeller slutförd fullständig data inläsning. |
| **Fullständig belastning i kö**      | Antal tabeller som ställts i kö för fullständig belastning.      |
| **Fullständig belastnings inläsning** | Antalet tabeller som misslyckades.      |
| **Stegvisa uppdateringar**      | Antalet uppdateringar av registrering av ändrings data (CDC) i rader som tillämpas på målet. |
| **Stegvisa infogningar**      | Antal CDC-infogningar i rader som tillämpas på målet.      |
| **Stegvisa borttagningar** | Antal CDC-borttagningar i rader som tillämpas på målet.      |
| **Väntande ändringar**      | Antal CDC i rader som fortfarande väntar på att tillämpas på målet. |
| **Tillämpade ändringar**      | Totalt antal CDC-uppdateringar, infogningar och borttagningar i rader som tillämpas på målet.      |
| **Tabeller med fel tillstånd** | Antalet tabeller som är i fel tillstånd under migreringen. Några exempel på tabeller kan hamna i fel tillstånd när det finns dubbletter som identifierats i målet eller data inte är kompatibla med inläsning i mål tabellen.      |

## <a name="monitor-at-table-level--detailed-summary"></a>Övervaka på tabell nivå – detaljerad sammanfattning
Det finns två flikar som visar migreringens förlopp vid fullständig belastning och stegvis data synkronisering.
    
![Fliken fullständig inläsning](media/how-to-monitor-migration-activity/dms-full-load-tab.png)

![Fliken stegvis synkronisering av data](media/how-to-monitor-migration-activity/dms-incremental-data-sync-tab.png)

I följande tabell beskrivs fälten som visas i migreringen av tabell nivå.

| Fältnamn        | Beskrivning       |
| ------------- | ------------- |
| **Status-synkronisering**      | Kontinuerlig synkronisering körs. |
| **Infoga**      | Antal CDC-infogningar i rader som tillämpas på målet.      |
| **Uppdatera** | Antal CDC-uppdateringar i rader som tillämpas på målet.      |
| **Ta bort**      | Antal CDC-borttagningar i rader som tillämpas på målet. |
| **Totalt antal tillämpade**      | Totalt antal CDC-uppdateringar, infogningar och borttagningar i rader som tillämpas på målet. |
| **Data fel** | Antalet data fel som har inträffat i den här tabellen. Några exempel på fel är *511: det går inte att skapa en rad med storleken% d, vilket är större än den maximalt tillåtna rad storleken på% d, 8114: det gick inte att konvertera data typen% LS till% ls.*  Kunden ska fråga från dms_apply_exceptions-tabellen i Azure Target för att se fel informationen.    |

> [!NOTE]
> CDC-värden för INSERT-, Update-och Delete-och total applicerad kan minska när databasen är start punkt eller om migreringen startas om.

## <a name="next-steps"></a>Nästa steg
- Läs igenom vägledningen för migrering i Microsoft [Database migration guide](https://datamigration.microsoft.com/).