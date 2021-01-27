---
title: MRUs (Media reservered units) vägledning för att migrera
description: Den här artikeln ger dig MRU-baserad vägledning som hjälper dig att migrera från Azure Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 375b4ecdc6153162b3a819b831af152ce390a5dc
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898332"
---
# <a name="media-reserved-units-mrus-scenario-based-migration-guidance"></a>MRUs (Media reservered units) vägledning för att migrera

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-4.svg)

Den här artikeln ger dig MRU-baserad vägledning som hjälper dig att migrera från Azure Media Services v2 till v3.

- För nya v3-konton som skapats i Azure Portal, eller med 2020-05-01-versionen av v3-API: et, behöver du inte längre ange reserverade enheter (MRUs). Systemet kommer nu automatiskt att skalas upp och ned baserat på belastningen.
- Om du har ett v3-eller v2-konto som skapades före 2020-05-01-versionen av API: et kan du fortfarande kontrol lera samtidigheten och prestandan för dina jobb med hjälp av reserverade enheter. Mer information finns i Skala mediebearbetning. Du kan hantera MRUs med CLI 2,0 för Media Services v3 eller med hjälp av Azure Portal.  
- Om du inte ser alternativet för att hantera MRUs i Azure Portal kör du ett konto som har skapats med 2020-05-01-API: et eller senare.
- Om du är bekant med att ställa in din MRU-typ på S3, kommer prestandan att förbättras eller vara oförändrad.
- Om du är en befintlig v2-kund måste du skapa ett nytt v2-konto som stöder ditt befintliga program innan migreringen har slutförts. 
- Indexerare v1 eller andra medie processorer som inte är helt inaktuella kan behöva aktive ras igen. 

Mer information om MRUs finns i [enheter med reserverat medium](concept-media-reserved-units.md) och [hur du skalar enheternas reserverade enheter](media-reserved-units-cli-how-to.md).

## <a name="mru-concepts-tutorials-and-how-to-guides"></a>MRU-koncept, självstudier och instruktioner

### <a name="concepts"></a>Begrepp

[Enheter med reserverat medium](concept-media-reserved-units.md)

### <a name="how-to-guides"></a>Guider

[Skala enhets reserverade enheter](media-reserved-units-cli-how-to.md)

## <a name="samples"></a>Exempel

Du kan också [jämföra v2-och v3-koden i kod exemplen](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
