---
title: Diagnostisera vanliga kod paket fel med hjälp av Service Fabric
description: Lär dig hur du felsöker vanliga kod paket fel med Azure Service Fabric
author: grzuber
ms.topic: article
ms.date: 05/09/2019
ms.author: grzuber
ms.openlocfilehash: 400651b240f0518a85b9deb7a7293a77a88b0861
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100591702"
---
# <a name="diagnose-common-code-package-errors-by-using-service-fabric"></a>Diagnostisera vanliga kod paket fel med hjälp av Service Fabric

I den här artikeln beskrivs vad det innebär att ett kod paket avslutas utan förvarning. Den ger inblick i möjliga orsaker till vanliga felkoder, tillsammans med fel söknings steg.

## <a name="when-does-a-process-or-container-terminate-unexpectedly"></a>När avslutas en process eller behållare oväntad?

När Azure Service Fabric tar emot en begäran om att starta ett kod paket börjar den förbereda miljön på det lokala systemet enligt de alternativ som anges i app-och tjänst manifesten. Dessa förberedelser kan vara att reservera nätverks slut punkter eller resurser, konfigurera brand Väggs regler eller konfigurera resurs styrnings begränsningar. 

När miljön har kon figurer ATS korrekt försöker Service Fabric att ta fram kod paketet. Det här steget anses vara slutfört om operativ systemet eller behållar körningen rapporterar att processen eller containern har Aktiver ATS. Om aktiveringen Miss lyckas bör du se ett hälso meddelande i SFX som liknar följande:

```
There was an error during CodePackage activation. Service host failed to activate. Error: 0xXXXXXXXX
```

När kod paketet har Aktiver ATS börjar Service Fabric att övervaka dess livs längd. I det här läget kan en process eller container avslutas när som helst av flera orsaker. Det kan till exempel hända att det inte gick att initiera en DLL eller att operativ systemet har slut på heap-utrymmet på Skriv bordet. Om ditt kod paket har avslut ATS bör du se följande hälso meddelande i SFX:

```
The process/container terminated with exit code: XXXXXXXX. Please look at your application logs/dump or debug your code package for more details. For information about common termination errors, please visit https://aka.ms/service-fabric-termination-errors
```

Avslutnings koden i det här hälso meddelandet är den enda LED texten som processen eller behållaren tillhandahåller om varför den avslutades. Den kan genereras av vilken nivå som helst i stacken. Den här avsluts koden kan till exempel vara relaterad till ett OS-fel eller ett .NET-problem, eller så kan den ha Aktiver ATS av din kod. Använd den här artikeln som utgångs punkt för att diagnosticera källan till avslutnings koderna och möjliga lösningar. Men kom ihåg att dessa är allmänna lösningar på vanliga scenarier och kanske inte gäller för det fel som du ser.

## <a name="how-can-i-tell-if-service-fabric-terminated-my-code-package"></a>Hur kan jag avgöra om Service Fabric avslutat mitt kod paket?

Service Fabric kan vara ansvarig för att avsluta ditt kod paket av olika orsaker. Det kan till exempel bestämmas att placera kod paketet på en annan nod för belastnings utjämning. Du kan kontrol lera att Service Fabric avslutat kod paketet om du ser någon av slut koderna i följande tabell.

>[!NOTE]
> Om din process eller behållare slutar med en annan slutkod än koden i följande tabell, är Service Fabric inte ansvarig för att avsluta den.

Slutkod | Beskrivning
--------- | -----------
7147 | Indikerar att Service Fabric stänga av processen eller behållaren korrekt genom att skicka en CTRL + C-signal.
7148 | Anger att Service Fabric avslutade processen eller containern. Den här felkoden indikerar ibland att processen eller containern inte svarade i tid efter att ha skickat en CTRL + C-signal och måste avslutas.


## <a name="other-common-error-codes-and-their-potential-fixes"></a>Andra vanliga felkoder och eventuella korrigeringar

Slutkod | HEXADECIMALT värde | Kort beskrivning | Rotorsak | Möjlig åtgärd
--------- | --------- | ----------------- | ---------- | -------------
3221225794 | 0xc0000142 | STATUS_DLL_INIT_FAILED | Det här felet innebär ibland att datorn har slut på heap-utrymmet på Skriv bordet. Den här orsaken är särskilt sannolik om du har flera processer som hör till ditt program som körs på noden. | Om programmet inte har skapats för att svara på CTRL + C-signaler kan du aktivera inställningen **EnableActivateNoWindow** i kluster manifestet. Att aktivera den här inställningen innebär att kod paketet körs utan ett GUI-fönster och inte får CTRL + C-signaler. Den här åtgärden minskar också mängden Skriv bords utrymme som varje process förbrukar. Om ditt kod paket måste ta emot CTRL + C-signaler kan du öka storleken på nodens Skriv bords heap.
3762504530 | 0xe0434352 | Ej tillämpligt | Det här värdet representerar felkoden för ett ohanterat undantag från hanterad kod (det vill säga .NET). | Den här avslutnings koden visar att ditt program utlöste ett undantag som inte hanteras och som avbröt processen. I det första steget när du avgör vad som utlöste det här felet kan du felsöka programmets loggar och dumpfiler.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att [diagnostisera andra vanliga scenarier](service-fabric-diagnostics-common-scenarios.md).
* Få en mer detaljerad översikt över Azure Monitor loggar och vad de erbjuder genom att läsa [Azure Monitor översikt](../azure-monitor/overview.md).
* Lär dig mer om Azure Monitor loggar [varningar](../azure-monitor/alerts/alerts-overview.md) för hjälp vid identifiering och diagnostik.
* Bekanta dig med [loggs ökningen och fråge](../azure-monitor/logs/log-query-overview.md) funktionerna som ingår i Azure Monitor loggar.
