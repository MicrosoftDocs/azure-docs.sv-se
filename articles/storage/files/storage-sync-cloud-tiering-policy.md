---
title: Azure File Sync principer för moln skikt | Microsoft Docs
description: Information om hur datum-och volymens principer för ledigt utrymme fungerar tillsammans för olika scenarier.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3020737e91e1fe5c4af3e23a147fa0ea16037d3b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204436"
---
# <a name="cloud-tiering-policies"></a>Principer för moln skikt

Moln nivåer har två principer som avgör vilka filer som är på nivå av molnet: den **lediga disk utrymmes principen** och **datum principen**.

**Principen för ledigt utrymme på volymen** ser till att en angiven procent andel av den lokala volymen som server slut punkten finns på alltid hålls kostnads fri. 

**Datum princip** nivå filer som senast användes för x dagar sedan eller senare. Principen för ledigt utrymme på volymen prioriteras alltid. om det inte finns tillräckligt med ledigt utrymme på volymen för att lagra så många dagar filer som det beskrivs i datum principen, åsidosätter Azure File Sync datum principen och fortsätter att placera på nivå av coldest-filerna tills procent andelen ledigt utrymme är uppfyllt.

## <a name="how-both-policies-work-together"></a>Hur båda principerna fungerar tillsammans

Vi ska använda ett exempel för att illustrera hur dessa principer fungerar: Låt oss säga att du har konfigurerat Azure File Sync på en lokal volym på 500 GB och att moln nivåer aldrig har Aktiver ATS. Detta är filerna i fil resursen:

|Filnamn |Senaste åtkomst tid  |Filstorlek  |Lagrat i |
|----------|------------------|-----------|----------|
|Fil 1    | 2 dagar sedan  | 10 GB | Server och Azure-filresurs
|Fil 2    | 10 dagar sedan | 30 GB | Server och Azure-filresurs
|Fil 3    | för 1 år sedan | 200 GB | Server och Azure-filresurs
|Fil 4    | 1 år, 2 dagar sedan | 130 GB | Server och Azure-filresurs
|Fil 5    | 2 år, 1 dag sedan | 140 GB | Server och Azure-filresurs

**Ändra 1:** Du har aktiverat moln skiktning, angett en princip för ledigt utrymme på volymen på 20% och inaktive rad datum princip. Med den konfigurationen ser moln nivå att 20% (i det här fallet 100 GB) utrymme hålls kostnads fritt och tillgängligt på den lokala datorn. Det innebär att den totala kapaciteten för det lokala cacheminnet är 400 GB. Att 400 GB lagrar de senaste filer som används ofta på den lokala volymen.

Med den här konfigurationen lagras endast filer 1 till och med 4 i det lokala cacheminnet. filen 5 ska därför ligga i nivå. Detta är bara 370 GB av 400 GB som kan användas. Filen 5 är 140 GB och överskrider gränsen på 400 GB om den cachelagrades lokalt. 

**Ändring 2:** Säg att en användare kommer åt filen 5. Detta gör filen 5 till den senast öppnade filen i resursen. Därför lagras filen 5 i den lokala cachen och får plats under gränsen på 400 GB. filen 4 kommer att ha en nivå. Följande tabell visar var filerna lagras, med följande uppdateringar:

|Filnamn |Senaste åtkomst tid  |Filstorlek  |Lagrat i |
|----------|------------------|-----------|----------|
|Fil 5    | 2 timmar sedan | 140 GB | Server och Azure-filresurs
|Fil 1    | 2 dagar sedan  | 10 GB | Server och Azure-filresurs
|Fil 2    | 10 dagar sedan | 30 GB | Server och Azure-filresurs
|Fil 3    | för 1 år sedan | 200 GB | Server och Azure-filresurs
|Fil 4    | 1 år, 2 dagar sedan | 130 GB | Azure-filresurs, lokalt på nivån

**Ändra 3:** Anta att du har uppdaterat principerna så att den datumbaserade nivå principen är 60 dagar och att principen för ledigt utrymme på volymen är 70%. Nu kan bara upp till 150 GB lagras i det lokala cacheminnet. Även om fil 2 har åtkomst till mindre än 60 dagar sedan, åsidosätter den lediga utrymmes principen datum principen och fil 2 är i nivå av att underhålla 70% lokalt ledigt utrymme.

**Ändra 4:** Om du har ändrat principen för ledigt utrymme på volymen till 20% och sedan använt `Invoke-StorageSyncFileRecall` för att återkalla alla filer som får plats på den lokala enheten samtidigt som du följer principerna för moln skikt, skulle tabellen se ut så här:

|Filnamn |Senaste åtkomst tid  |Filstorlek  |Lagrat i |
|----------|------------------|-----------|----------|
|Fil 5    | för 1 timme sedan  | 140 GB | Server och Azure-filresurs
|Fil 1    | 2 dagar sedan  | 10 GB | Server och Azure-filresurs
|Fil 2    | 10 dagar sedan | 30 GB | Server och Azure-filresurs
|Fil 3    | för 1 år sedan | 200 GB | Azure-filresurs, lokalt på nivån
|Fil 4    | 1 år, 2 dagar sedan | 130 GB | Azure-filresurs, lokalt på nivån

I det här fallet kommer filer 1, 2 och 5 att vara lokalt cachelagrade och filer 3 och 4 ska nivåas. Eftersom datum principen är 60 dagar skiktas filer 3 och 4, även om principen för ledigt utrymme på volymen tillåter upp till 400 GB lokalt.

> [!NOTE] 
> Filer återkallas inte automatiskt när kunderna ändrar ledigt utrymme på volymen till ett mindre värde (till exempel från 20% till 10%) eller ändra datum principen till ett större värde (till exempel från 20 dagar till 50 dagar).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Flera Server slut punkter på en lokal volym

Det går att aktivera moln skiktning för flera Server slut punkter på en enda lokal volym. För den här konfigurationen ska du ställa in ledigt utrymme på volymen på samma belopp för alla Server slut punkter på samma volym. Om du anger olika principer för ledigt utrymme på volymen för flera Server slut punkter på samma volym, prioriteras procent andelen ledigt utrymme på volymen. Detta kallas för den **effektiva mängden ledigt utrymme på volymen**. Om du till exempel har tre Server slut punkter på samma lokala volym, en uppsättning till 15%, en till 20% och en tredje uppsättning på 30%, kommer alla att börja på nivå av coldest-filerna när de har mindre än 30% ledigt utrymme.

## <a name="next-steps"></a>Nästa steg
* [Övervaka moln nivåer](storage-sync-monitor-cloud-tiering.md)
