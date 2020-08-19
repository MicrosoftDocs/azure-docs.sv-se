---
title: Visa status för Azure import/export-jobb | Microsoft Docs
description: Lär dig hur du visar statusen för Azure import/export-jobb och de enheter som används. Förstå de faktorer som påverkar hur lång tid det tar att bearbeta ett jobb.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 8110b98c055a211203fb937990e860fc8dea74f4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520470"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Visa status för Import/Export-jobb i Azure

Den här artikeln innehåller information om hur du visar enhets-och jobb status för Azure import/export-jobb. Azure import/export-tjänsten används för att på ett säkert sätt överföra stora mängder data till Azure-blobbar och Azure Files. Tjänsten används också för att exportera data från Azure Blob Storage.  

## <a name="view-job-and-drive-status"></a>Visa jobb-och enhets status
Du kan spåra statusen för dina import-eller export jobb från Azure Portal genom att välja fliken **Importera/exportera** . En lista över dina jobb visas på sidan.

![Visa jobb status](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Visa jobbstatus

Du ser något av följande jobb statusar beroende på var enheten finns i processen.

| Jobb status | Description |
|:--- |:--- |
| Skapar | När ett jobb har skapats är dess tillstånd inställt på att **skapa**. När jobbet har statusen **skapande** , antar import/export-tjänsten att enheterna inte har levererats till data centret. Ett jobb kan finnas kvar i det här läget i upp till två veckor, och det tas bort automatiskt av tjänsten. |
| Frakt | När du har levererat ditt paket bör du uppdatera spårnings informationen i Azure Portal.  Om du gör det blir jobbet i **leverans** tillstånd. Jobbet förblir i **leverans** tillstånd i upp till två veckor. 
| Mottaget | När alla enheter har tagits emot i data centret är jobbets tillstånd inställt på **mottaget**. |
| Överföring | När minst en enhet har börjat bearbeta är jobbets tillstånd inställt på **överföring**. Mer information finns i [enhets tillstånd](#view-drive-status). |
| Paketering | När alla enheter har slutfört bearbetningen placeras jobbet i **förpacknings** tillstånd tills enheterna skickas tillbaka till dig. |
| Slutförd | När alla enheter har skickats tillbaka till dig, om jobbet har slutförts utan fel, anges jobbet som **slutfört**. Jobbet tas bort automatiskt efter 90 dagar i **slutfört** tillstånd. |
| Stängda | När alla enheter har skickats tillbaka till dig, om det uppstod några fel under jobb bearbetningen, anges jobbet till **stängt**. Jobbet tas bort automatiskt efter 90 dagar i **stängt** tillstånd. |

## <a name="view-drive-status"></a>Visa enhetsstatus

I tabellen nedan beskrivs livs cykeln för en enskild enhet som den övergår via ett import-eller export jobb. Det aktuella läget för varje enhet i ett jobb visas i Azure Portal.

I följande tabell beskrivs varje tillstånd som varje enhet i ett jobb kan passera.

| Enhets tillstånd | Description |
|:--- |:--- |
| Ange | För ett import jobb **anges**det ursprungliga läget för en enhet när jobbet skapas från Azure Portal. För ett export jobb, eftersom ingen enhet har angetts när jobbet skapas, tas det första enhets läget **emot**. |
| Mottaget | Enheten övergår till **mottaget** tillstånd när tjänsten import/export har bearbetat de enheter som togs emot från transport företaget för ett import jobb. För ett export jobb är det första enhets läget **mottaget** . |
| NeverReceived | Enheten flyttas till **NeverReceived** -tillstånd när paketet för ett jobb kommer, men paketet innehåller inte enheten. En enhet flyttas också till det här läget om data centret ännu inte har tagit emot paketet och tjänsten har tagit emot skeppnings informationen minst två veckor sedan. |
| Överföring | En enhet flyttas till **överförings** tillstånd när tjänsten börjar överföra data från enheten till Azure Storage. |
| Slutförd | En enhet flyttas till **slutfört** tillstånd när tjänsten har överfört alla data utan fel.
| CompletedMoreInfo | En enhet flyttar till **CompletedMoreInfo** -tillstånd när tjänsten har problem med att kopiera data från eller till enheten. Informationen kan innehålla fel, varningar eller informations meddelanden om att skriva över blobbar.
| ShippedBack | En enhet flyttas till **ShippedBack** -tillstånd när den har levererats från data centret tillbaka till retur adressen. |

Den här bilden från Azure Portal visar enhets status för ett exempel jobb:

![Visa enhets status](./media/storage-import-export-service/drivestate.png)

I följande tabell beskrivs enhets felen och de åtgärder som vidtas för varje tillstånd.

| Enhets tillstånd | Händelse | Lösning/nästa steg |
|:--- |:--- |:--- |
| NeverReceived | En enhet som marker ATS som **NeverReceived** (eftersom den inte togs emot som en del av jobbets försändelse) kommer in i en annan försändelse. | Drift teamet flyttar enheten till **mottagen**. |
| Ej tillämpligt | En enhet som inte är en del av något jobb kommer till data centret som en del av ett annat jobb. | Enheten är markerad som en extra enhet. Den returneras till dig när jobbet som är associerat med det ursprungliga paketet har slutförts. |

## <a name="time-to-process-job"></a>Tid för att bearbeta jobb
Hur lång tid det tar att bearbeta ett import/export-jobb varierar beroende på ett antal faktorer, till exempel:

-  Leverans tid
-  Läs in i data centret
-  Jobbtyp och storlek för de data som kopieras
-  Antal diskar i ett jobb. 

Import/export-tjänsten har inget service avtal, men tjänsten försöker slutföra kopian i 7 till 10 dagar efter att diskarna har tagits emot. Förutom statusen som publicerats på Azure Portal kan du använda REST-API: er för att spåra jobb förloppet. Använd parametern procent klart i [list jobbets](/previous-versions/azure/dn529083(v=azure.100)) API-anrop för att Visa procent kopierings förloppet.


## <a name="next-steps"></a>Nästa steg

* [Överföra data med kommando rads verktyget AzCopy](storage-use-azcopy.md)
* [Exempel på Azure import-export REST API](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
