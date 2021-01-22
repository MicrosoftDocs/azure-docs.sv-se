---
title: Migrera från Azure Media Services v2 till v3-introduktion | Microsoft Docs
description: Den här artikeln är en introduktion till migrering från Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: fb9abd8f3186405edc31b4af48ee98482e080c68
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690584"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Migrera från Media Services v2 till v3 introduktion

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

Den Media Services migrations guiden hjälper dig att migrera från Media Services v2-API: er till v3-API: er baserat på en migrering som drar nytta av de nya funktionerna och funktionerna som nu är tillgängliga. Du bör använda ditt bästa omdöme och ta reda på vad som passar bäst för ditt scenario.

## <a name="how-to-use-this-guide"></a>Så här använder du den här guiden

### <a name="navigating"></a>Navigera

I guiden visas följande bild.

![migrerings steg](./media/migration-guide/steps.svg)<br/>

Det steg du är på visas i en färg förändring i numret för steget, som så här:

![steg 2 i migreringen](./media/migration-guide/steps-2.svg)<br/>

I slutet av varje sida visas länkar till resten av de migreringsjobb som du kan läsa under **Nästa steg** -rubrik.

### <a name="guidance"></a>Vägledning

Rikt linjerna som visas här är *Allmänt*. Den innehåller innehåll för att förbättra din medvetenhet om vad som nu är tillgängligt i v3 samt vad som har ändrats i Media Services arbets flöden.

För mer detaljerad information, inklusive skärm dum par och konceptuell grafik, finns länkar till begrepp, självstudier, snabb starter, exempel och API-referenser i varje scenario-baserat ämne. Vi har också listat exempel som hjälper dig att jämföra v2-API: et med v3-API: et.

## <a name="migration-guidance-overview"></a>Översikt över migrations vägledning

Det finns fyra allmänna steg att följa vid migreringen:

## <a name="step-1-benefits"></a>Steg 1 fördelar

<hr color="#5ea0ef" size="10">

[Lär dig fördelarna](migrate-v-2-v-3-migration-benefits.md) med att migrera till Media Services API v3.

## <a name="step-2-differences"></a>Steg 2 skillnader

<hr color="#5ea0ef" size="10">

Förstå skillnaderna mellan API för Media Services v2 och v3-API: et.

- [API-åtkomst](migrate-v-2-v-3-differences-api-access.md)
- [Funktions luckor](migrate-v-2-v-3-differences-feature-gaps.md)
- [Ändringar av terminologi och enhet](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>Steg 3 SDK-installation

<hr color="#5ea0ef" size="10">

Förstå SDK-skillnader och [Konfigurera att migrera till v3-REST API eller klient-SDK: n](migrate-v-2-v-3-migration-setup.md).

## <a name="step-4-scenario-based-guidance"></a>Steg 4 scenario-baserad vägledning

<hr color="#5ea0ef" size="10">

Ditt program för Media Services v2 kan vara unikt. Vi har därför tillhandahållit scenariobaserade rikt linjer baserade på hur du *kan ha* använt Media Services tidigare och stegen för varje funktion i tjänsten, till exempel:

- [Kodning](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [Liveuppspelning](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [Paketering och leverans](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [Innehålls skydd](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [Reserverade enheter (Mac)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
