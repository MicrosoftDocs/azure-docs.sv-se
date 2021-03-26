---
title: Vägledning för migrering av Media Services Live Streaming
description: Den här artikeln ger dig till gång till Live-scenarier med direkt uppspelning som hjälper dig att migrera från Azure Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 322a599d5ad02ddcf19c61261d968d098b6c9871
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105563135"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Vägledning för Live streaming-scenario-baserad migrering

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-4.svg)

Azure Portal har nu stöd för installation och hantering av Live-händelser.  Du uppmanas att testa din v2-till v3-migrering.

## <a name="test-the-v3-live-event-workflow"></a>Testa v3 live event-arbetsflödet

> [!NOTE]
> Kanaler och program som skapats med v2 (som mappas till Live-händelser och Live-utdata i v3) hanteras med v3-API: et. Vägledningen är att växla över till v3 Live-händelser och Live-utdata vid en lämplig tidpunkt när du kan stoppa din befintliga v2-kanal. Det finns för närvarande inget stöd för att sömlöst migrera en Live Live-kanal dygnet runt till nya v3 Live-händelser. Därför måste underhålls stillestånden koordineras på bästa möjliga sätt för din mål grupp och ditt företag.

Testa det nya sättet att leverera Live-händelser med Media Services innan du flyttar innehållet från v2 till v3. Här är v3-funktionerna för att arbeta med och fundera på migrering.

- Skapa en ny v3 [Live-händelse](live-events-outputs-concept.md#live-events) för kodning. Du kan aktivera [för inställningar för 1080p-och 720p-kodning](live-event-types-comparison.md#system-presets).
- Använd entiteten [Live output](live-events-outputs-concept.md#live-outputs) i stället för program
- Skapa [strömmande positionerare](streaming-locators-concept.md).
- Överväg ditt behov av [HLS och tank streck](dynamic-packaging-overview.md) direkt uppspelning.
- Om du behöver snabb start av Live-händelser går du igenom de nya funktionerna i [vänte läge](live-events-outputs-concept.md#standby-mode) .
- Om du vill skriva över din Live-händelse medan den inträffar kan du utforska den nya funktionen för [direkt avskriftning](live-transcription.md) .
- Skapa 24x7x365 Live-händelser i v3 om du behöver en längre strömnings tid.
- Använd [Event Grid](monitoring/monitor-events-portal-how-to.md) för att övervaka dina Live-händelser.

Se begrepp för Live-händelser, självstudier och hur du vägleder dig nedan för olika steg.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Live Events-koncept, självstudier och guider

### <a name="concepts"></a>Begrepp

- [Direktsänd strömning med Azure Media Services v3](live-streaming-overview.md)
- [Live-händelser och Live-utdata i Media Services](live-events-outputs-concept.md)
- [Verifierade lokala direkt uppspelnings kodare](recommended-on-premises-live-encoders.md)
- [Använd tids växling och Live-utdata för att skapa video uppspelning på begäran](live-event-cloud-dvr.md)
- [Live-avskrift (för hands version)](live-transcription.md)
- [Jämförelse av händelse typer i real tid](live-event-types-comparison.md)
- [Live händelse tillstånd och fakturering](live-event-states-billing.md)
- [Inställningar för låg latens för live event](live-event-latency.md)
- [Fel koder för Media Services Live-händelse](live-event-error-codes.md)

### <a name="tutorials-and-quickstarts"></a>Självstudier och snabb starter

- [Självstudie: strömma live med Media Services](stream-live-tutorial-with-api.md)
- [Skapa en Azure Media Services Live Stream med ONLINEBANKSYSTEM](live-events-obs-quickstart.md)
- [Snabb start: Ladda upp, koda och strömma innehåll med portalen](manage-assets-quickstart.md)
- [Snabb start: skapa en Azure Media Services Live Stream med Wirecast](live-events-wirecast-quickstart.md)

## <a name="samples"></a>Exempel

Du kan också [jämföra v2-och v3-koden i kod exemplen](migrate-v-2-v-3-migration-samples.md).
