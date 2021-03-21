---
title: Vägledning för migrering av innehålls skydd
description: Den här artikeln innehåller rikt linjer för innehålls skydds scenarion som hjälper dig att migrera från Azure Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 9c0040c0ad34b019eaa90bbd1571377ad3539578
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940110"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Vägledning för innehålls skydds scenario-baserad migrering

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-4.svg)

Den här artikeln innehåller rikt linjer för innehålls skydds scenarion som hjälper dig att migrera från Azure Media Services v2 till v3.

## <a name="protect-content-in-v3-api"></a>Skydda innehåll i v3-API

Använd support för [flera viktiga](design-multi-drm-system-with-access-control.md) funktioner i det nya v3-API: et.

Se begrepp för innehålls skydd, självstudier och hur du går till guider nedan för olika steg.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Koncept för innehålls skydd, självstudier och hur du går till guider

### <a name="concepts"></a>Begrepp

- [Skydda ditt innehåll med Media Services dynamisk kryptering](content-protection-overview.md)
- [Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md)
- [Media Services v3 med PlayReady-licens mal len](playready-license-template-overview.md)
- [Översikt över Media Services v3 med Widevine-licens mal len](widevine-license-template-overview.md)
- [Licenskrav för och konfiguration av Apple FairPlay](fairplay-license-overview.md)
- [Strömmande principer](streaming-policy-concept.md)
- [Principer för innehålls nyckel](content-key-policy-concept.md)

### <a name="tutorials"></a>Självstudier

[Snabb start: Använd portalen för att kryptera innehåll](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Guider

- [Hämta en signeringsnyckel från den befintliga principen](get-content-key-policy-dotnet-howto.md)
- [Offline-FairPlay strömning för iOS med Media Services v3](offline-fairplay-for-ios.md)
- [Offline-Widevine strömning för Android med Media Services v3](offline-widevine-for-android.md)
- [Offline PlayReady streaming för Windows 10 med Media Services v3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>Exempel

Du kan också [jämföra v2-och v3-koden i kod exemplen](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]