---
title: Mappa en mappstruktur till en Azure File Sync-topologi
description: Mappa en befintlig fil-och mappstruktur till Azure-filresurser för användning med Azure File Sync. Ett vanligt textblock som delas mellan migreringsjobb.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: cde85e245c8cc6ae8c55b24270f125bacc111737
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547566"
---
```console
Robocopy /MT:16 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Switch              | Innebörd |
|---------------------|---------|
| /MT                 | Tillåter att RoboCopy körs med flera trådar. Standardvärdet är 8 och det högsta värdet är 128. Matcha det här värdet med antalet processor kärnor och antalet trådar per kärna. Överväg om kärnor måste reserveras för andra uppgifter som en produktions server kan ha. |
| /NP                 | Förloppet för kopian för varje fil och mapp visas inte. Om du visar förloppet sänks kopierings prestanda betydligt. |
| /NFL                | Anger att filnamn inte ska loggas. Förbättrar kopierings prestanda. |
| /NDL                | Anger att katalognamn inte ska loggas. Förbättrar kopierings prestanda. |
| /B                  | Kör RoboCopy i samma läge som ett säkerhets kopierings program använder. Det gör att RoboCopy kan flytta filer som den aktuella användaren inte har behörighet till. |
| /MIR                | *Med speglad källa till mål* kan Robocopy bara kopiera delta mellan källa och mål. Tomma under kataloger kommer att kopieras. Objekt (filer eller mappar) som har ändrats eller som inte finns på målet kommer att kopieras. Objekt som finns på målet men inte på källan rensas (tas bort) från målet. När du använder den här växeln är det absolut nödvändigt att du matchar källans och målets mappstruktur exakt. "Matchning" innebär att du kopierar från rätt källa och mapp till motsvarande mappnivå på målet. Sedan kan en "fånga upp"-kopia lyckas. När källan och målet är felaktigt matchade kan du använda dem `/MIR` för att ta bort och kopiera om stor skala. |
| /IT                 | Ser till att åter givningen bevaras i vissa speglings scenarier. </br>*Exempel* : om två Robocopy kör en fil som upplever en ACL-ändring och en attribut uppdatering: till exempel är den *dold*. Utan/IT kan ACL-ändringen missas av RoboCopy och inte överföras till mål platsen. |
|Exemplar`[copyflags]`  | Åter givningen av fil kopian (standard om inget anges `/COPY:DAT` ), kopierings flaggor: `D` = data, `A` = attribut, `T` = tidsstämplar, `S` = säkerhet = NTFS ACL: er, `O` = ägar information, `U` = en<u>u</u>tokendata-information. Det går inte att lagra gransknings information i en Azure-filresurs. |
| /DCOPY:`[copyflags]`| Åter givning för kopian av kataloger (standard om inget anges `/DCOPY:DA` ), kopierings flaggor: `D` = data, `A` = attribut, `T` = tidsstämplar. |
| /UNILOG:<file name> | Matar ut status till logg filen som UNICODE (skriver över befintlig logg). |
| /LFSM               | **endast för mål med skiktad lagring** </br>Användning av/LFSM-begäranden RoboCopy för att köras i läget för låg ledigt utrymme. Den här växeln är bara användbar för mål med skiktad lagring som kan få slut på lokal kapacitet innan RoboCopy kan slutföras. Den här växeln lades till specifikt för användning med ett Azure File Sync-aktiverat moln skikts mål. Den kan användas oberoende av Azure File Sync. I det här läget kommer RoboCopy att pausas när en fil kopia skulle orsaka att mål volymens lediga utrymme går under ett "golv"-värde. Det här värdet kan anges med `/LFSM:n` flaggans form. Parametern `n` anges i bas 2: `nKB` , `nMB` , eller `nGB` . Om anges `/LFSM` utan ett explicit golv värde, är golvet inställt på 10 procent av storleken på mål volymen. Läget för ont om ledigt utrymme är inkompatibelt med/MT,/EFSRAW,/B och/ZB. |
| /Z                  | **försiktig användning** </br>Kopierar filer i läget omstart, användningen rekommenderas endast i en instabil nätverks miljö. Det här alternativet minskar kopierings prestanda avsevärt på grund av extra loggning. |
| /ZB                 | **försiktig användning** </br>Använder restart-läge. Om åtkomst nekas använder det här alternativet säkerhets kopierings läge. Det här alternativet minskar kopierings prestanda avsevärt på grund av kontroll punkter. |
   