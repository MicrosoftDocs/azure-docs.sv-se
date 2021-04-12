---
title: ta med fil
description: ta med fil
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 01512f0e37761915fcb6eeba8c162e1a3db62c48
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491699"
---
```console
Robocopy /MT:16 /R:5 /W:5 /B /MIR /IT /COPY:DATSO /DCOPY:DAT /NP /NFL /NDL /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Switch                | Innebörd |
|-----------------------|---------|
| `/MT:n`               | Tillåter att RoboCopy körs med flera trådar. Standardvärdet för n = 8 och det högsta antalet är 128 trådar. Matcha det här värdet med antalet processor kärnor och antalet trådar per kärna. Överväg om kärnor måste reserveras för andra uppgifter som en produktions server kan ha. |
| `/R:n`                | Hastigheten för en RoboCopy-körning kan förbättras genom att ange ett maximalt antal återförsök för en fil som inte kan kopieras vid första försöket. n = maximalt antal försök innan filen inte kan kopieras i den här RoboCopy-körningen. Den här växeln fungerar bäst i scenarier där det redan är klart att det kommer att finnas fler RoboCopy-körningar. Om filen inte kan kopieras i den här körningen försöker nästa RoboCopy-jobb att försöka igen. Ofta filer som misslyckades eftersom de användes eller på grund av timeout-problem kan komma att kopieras till den här metoden. |
| `/W:n`                | Den här växeln anger hur lång tid RoboCopy väntar innan ett försök görs att kopiera en fil som inte kunde kopieras vid det senaste försöket. n = antalet sekunder att vänta mellan återförsök. `/W:n` används ofta tillsammans med `/R:n` . |
| `/B`                  | Kör RoboCopy i samma läge som ett säkerhets kopierings program använder. Det gör att RoboCopy kan flytta filer som den aktuella användaren inte har behörighet till. |
| `/MIR`                | *Med speglad källa till mål* kan Robocopy bara kopiera delta mellan källa och mål. Tomma under kataloger kommer att kopieras. Objekt (filer eller mappar) som har ändrats eller som inte finns på målet kommer att kopieras. Objekt som finns på målet men inte på källan rensas (tas bort) från målet. När du använder den här växeln är det absolut nödvändigt att du matchar källans och målets mappstruktur exakt. "Matchning" innebär att du kopierar från rätt källa och mapp till motsvarande mappnivå på målet. Sedan kan en "fånga upp"-kopia lyckas. När källan och målet är felaktigt matchade kan du använda dem `/MIR` för att ta bort och kopiera om stor skala. |
| `/IT`                 | Ser till att åter givningen bevaras i vissa speglings scenarier. </br>*Exempel* : om två Robocopy kör en fil som upplever en ACL-ändring och en attribut uppdatering: till exempel är den *dold*. Utan/IT kan ACL-ändringen missas av RoboCopy och inte överföras till mål platsen. |
|`/COPY:[copyflags]`    | Åter givningen av fil kopian (standard om inget anges `/COPY:DAT` ), kopierings flaggor: `D` = data, `A` = attribut, `T` = tidsstämplar, `S` = säkerhet = NTFS ACL: er, `O` = ägar information, `U` = en<u>u</u>tokendata-information. Det går inte att lagra gransknings information i en Azure-filresurs. |
| `/DCOPY:[copyflags]`  | Åter givning för kopian av kataloger (standard om inget anges `/DCOPY:DA` ), kopierings flaggor: `D` = data, `A` = attribut, `T` = tidsstämplar. |
| `/NP`                 | Förloppet för kopian för varje fil och mapp visas inte. Om du visar förloppet sänks kopierings prestanda betydligt. |
| `/NFL`                | Anger att filnamn inte ska loggas. Förbättrar kopierings prestanda. |
| `/NDL`                | Anger att katalognamn inte ska loggas. Förbättrar kopierings prestanda. |
| `/UNILOG:<file name>` | Matar ut status till logg filen som UNICODE (skriver över befintlig logg). |
| `/LFSM`               | **endast för mål med skiktad lagring** </br>Användning av/LFSM-begäranden RoboCopy för att köras i läget för låg ledigt utrymme. Den här växeln är bara användbar för mål med skiktad lagring som kan få slut på lokal kapacitet innan RoboCopy kan slutföras. Den här växeln lades till specifikt för användning med ett Azure File Sync-aktiverat moln skikts mål. Den kan användas oberoende av Azure File Sync. I det här läget kommer RoboCopy att pausas när en fil kopia skulle orsaka att mål volymens lediga utrymme går under ett "golv"-värde. Det här värdet kan anges med `/LFSM:n` flaggans form. Parametern `n` anges i bas 2: `nKB` , `nMB` , eller `nGB` . Om anges `/LFSM` utan ett explicit golv värde, är golvet inställt på 10 procent av storleken på mål volymen. Läget för ont om ledigt utrymme är inkompatibelt med/MT,/EFSRAW,/B och/ZB. |
| `/Z`                  | **försiktig användning** </br>Kopierar filer i läget omstart, användningen rekommenderas endast i en instabil nätverks miljö. Det här alternativet minskar kopierings prestanda avsevärt på grund av extra loggning. |
| `/ZB`                 | **försiktig användning** </br>Använder restart-läge. Om åtkomst nekas använder det här alternativet säkerhets kopierings läge. Det här alternativet minskar kopierings prestanda avsevärt på grund av kontroll punkter. |
   