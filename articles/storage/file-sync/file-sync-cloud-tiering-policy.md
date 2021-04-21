---
title: Azure File Sync principer för molnnivåindelad | Microsoft Docs
description: Information om hur principerna för datum och ledigt utrymme på volymen fungerar tillsammans för olika scenarier.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4376a57b677b95b2de7d261b30ac4c0ad24956cc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797366"
---
# <a name="cloud-tiering-policies"></a>Principer för molnnivåindelad lagring

Molnnivåindelad har två principer som avgör vilka filer som är nivåindelade i molnet: principen för **ledigt utrymme på** volymen och **datumprincipen**.

Principen **för ledigt utrymme på** volymen säkerställer att en angiven procentandel av den lokala volymen som serverslutpunkten finns på alltid är kostnadsfri. 

Datum **för principnivåfiler** som senast fick åtkomst x dagar sedan eller senare. Principen för ledigt utrymme på volymen har alltid företräde. Om det inte finns tillräckligt med ledigt utrymme på volymen för att lagra så många dagars filer som beskrivs i datumprincipen åsidosätter Azure File Sync datumprincipen och fortsätter att nivåindelatera de kallaste filerna tills procentandelen ledigt utrymme för volymen har uppnåtts.

## <a name="how-both-policies-work-together"></a>Så här fungerar båda principerna tillsammans

Vi använder ett exempel för att illustrera hur dessa principer fungerar: Anta att du har konfigurerat Azure File Sync på en lokal volym på 500 GB och att molnnivåindelad nivå aldrig har aktiverats. Det här är filerna i filresursen:

|Filnamn |Senaste åtkomsttid  |Filstorlek  |Lagras i |
|----------|------------------|-----------|----------|
|Fil 1    | 2 dagar sedan  | 10 GB | Server och Azure-filresurs
|Fil 2    | 10 dagar sedan | 30 GB | Server och Azure-filresurs
|Fil 3    | 1 år sedan | 200 GB | Server och Azure-filresurs
|Fil 4    | 1 år, 2 dagar sedan | 130 GB | Server- och Azure-filresurs
|Fil 5    | 2 år, 1 dag sedan | 140 GB | Server- och Azure-filresurs

**Ändra 1:** Du har aktiverat molnnivåindelad lagring, angett en princip för ledigt volymutrymme på 20 % och inaktiverat datumprincipen. Med den konfigurationen säkerställer molnnivåindelad lagring att 20 % (i det här fallet 100 GB) utrymme hålls ledigt och tillgängligt på den lokala datorn. Det innebär att den totala kapaciteten för den lokala cachen är 400 GB. 400 GB kommer att lagra de senast använda filerna på den lokala volymen.

Med den här konfigurationen skulle endast filer 1 till 4 lagras i det lokala cacheminnet och fil 5 nivåindelade. Det här är bara 370 GB av de 400 GB som kan användas. Fil 5 är 140 GB och skulle överskrida gränsen på 400 GB om den cachelagrades lokalt. 

**Ändra 2:** Säg att en användare har åtkomst till fil 5. Detta gör fil 5 till den senast använda filen i resursen. Därför skulle fil 5 lagras i den lokala cachen och för att få plats under gränsen på 400 GB skulle fil 4 nivåindelats. I följande tabell visas var filerna lagras, med dessa uppdateringar:

|Filnamn |Senaste åtkomsttid  |Filstorlek  |Lagras i |
|----------|------------------|-----------|----------|
|Fil 5    | 2 timmar sedan | 140 GB | Server och Azure-filresurs
|Fil 1    | 2 dagar sedan  | 10 GB | Server och Azure-filresurs
|Fil 2    | 10 dagar sedan | 30 GB | Server och Azure-filresurs
|Fil 3    | 1 år sedan | 200 GB | Server och Azure-filresurs
|Fil 4    | 1 år, 2 dagar sedan | 130 GB | Azure-filresurs, nivåindelad lokalt

**Ändra 3:** Anta att du har uppdaterat principerna så att den datumbaserade nivåprincipen är 60 dagar och att principen för ledigt volymutrymme är 70 %. Nu kan endast upp till 150 GB lagras i den lokala cachen. Även om fil 2 har använts för mindre än 60 dagar sedan åsidosätter principen för ledigt utrymme för volymer datumprincipen och fil 2 nivåindelade för att upprätthålla 70 % lokalt ledigt utrymme.

**Ändra 4:** Om du ändrade principen för ledigt utrymme på volymen till 20 % och sedan använde för att återkalla alla filer som passar på den lokala enheten samtidigt som du följa principerna för molnnivåindelad lagring, skulle tabellen se ut `Invoke-StorageSyncFileRecall` så här:

|Filnamn |Senaste åtkomsttid  |Filstorlek  |Lagras i |
|----------|------------------|-----------|----------|
|Fil 5    | 1 timme sedan  | 140 GB | Server- och Azure-filresurs
|Fil 1    | 2 dagar sedan  | 10 GB | Server- och Azure-filresurs
|Fil 2    | 10 dagar sedan | 30 GB | Server- och Azure-filresurs
|Fil 3    | 1 år sedan | 200 GB | Azure-filresurs, nivåindelad lokalt
|Fil 4    | 1 år, 2 dagar sedan | 130 GB | Azure-filresurs, nivåindelad lokalt

I det här fallet cachelagras filerna 1, 2 och 5 lokalt och filerna 3 och 4 nivåindelade. Eftersom datumprincipen är 60 dagar är filerna 3 och 4 nivåindelade, även om principen för ledigt utrymme för volymer tillåter upp till 400 GB lokalt.

> [!NOTE] 
> Filer återkallas inte automatiskt när kunder ändrar principen för ledigt volymutrymme till ett mindre värde (till exempel från 20 % till 10 %) eller ändra datumprincipen till ett större värde (till exempel från 20 dagar till 50 dagar).

## <a name="multiple-server-endpoints-on-a-local-volume"></a>Flera serverslutpunkter på en lokal volym

Molnnivåindelad lagring kan aktiveras för flera serverslutpunkter på en enda lokal volym. För den här konfigurationen bör du ställa in volymens lediga utrymme på samma mängd för alla serverslutpunkter på samma volym. Om du anger olika principer för ledigt utrymme för flera serverslutpunkter på samma volym har den största procentandelen ledigt utrymme företräde. Detta kallas för den gällande **principen för ledigt utrymme på volymen.** Om du till exempel har tre serverslutpunkter på samma lokala volym, en inställd på 15 %, en annan inställd på 20 % och en tredje inställd på 30 %, börjar alla att nivåindelat de kallaste filerna när de har mindre än 30 % ledigt utrymme tillgängligt.

## <a name="next-steps"></a>Nästa steg

* [Övervaka molnnivåindelad](file-sync-monitor-cloud-tiering.md)
