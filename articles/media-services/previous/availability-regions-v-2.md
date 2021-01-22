---
title: Azure Media Services regional tillgänglighet | Microsoft Docs
description: Den här artikeln är en översikt över Microsoft Azure Media Services funktioner och regional tillgänglighet för tjänster.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: inhenkel
ms.custom: references_regions
ms.openlocfilehash: 458927afc78c46ad365d437a574d0b40b32fc11e
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98695805"
---
# <a name="media-services-regional-availability"></a>Media Services regional tillgänglighet

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. Kolla in den senaste versionen [Media Services v3](../latest/media-services-overview.md). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services (AMS) gör det möjligt att på ett säkert sätt överföra, lagra, koda och paketera video- eller ljudinnehåll för att strömma både på begäran och live till olika klienter (till exempel TV, datorer och mobila enheter).

AMS fungerar i flera regioner runtom i världen, vilket ger dig flexibilitet när du väljer var du vill bygga dina program. Den här artikeln är en översikt över Microsoft Azure Media Services funktioner och regional tillgänglighet för tjänster.

Mer information om hela den globala Azure-infrastrukturen finns i [Azure-geografiska](https://azure.microsoft.com/global-infrastructure/geographies/)områden.

## <a name="ams-accounts"></a>AMS-konton

Använd [Azure-produkter per region](https://azure.microsoft.com/global-infrastructure/services/?products=media-services&regions=all) för att avgöra om Media Services är tillgänglig i en angiven region.

## <a name="streaming-endpoints"></a>Slutpunkter för direktuppspelning

Media Services-kunder kan antingen välja en **Standard**-slutpunkt för direktuppspelning eller en **Premium**-slutpunkt för direktuppspelning.

|Name|Status|Region
|---|---|---|
|Standard|Allmän tillgänglighet (GA)|Alla|
|Premium|Allmän tillgänglighet (GA)|Alla|

## <a name="live-encoding"></a>Live Encoding

Tillgängligt i alla regioner utom: Tyskland, södra Brasilien, västra Indien, södra Indien och centrala Indien.

## <a name="encoding-media-processors"></a>Mediebearbetare för kodning

AMS erbjuder två kodare på begäran: **Media Encoder Standard** och **Media Encoder Premium Workflow**. Mer information finns i [Overview and comparison of Azure on-demand media encoders](media-services-encode-asset.md) (Översikt och jämförelse av Azures mediekodare på begäran).

|Namn på mediebearbetare|Status|Regioner
|---|---|---|
|Media Encoder Standard|Allmän tillgänglighet (GA)|Alla|
|Arbetsflöde för Media Encoder Premium|Allmän tillgänglighet (GA)|Alla utom Kina|

## <a name="analytics-media-processors"></a>Mediebearbetare för analys

Media Analytics är en samling tal- och visionskomponenter som gör det enklare för organisationer och företag att härleda insikter som det går att direkt agera utifrån från sina videofiler. Mer information finns i [Översikt över Azure Media Services Analytics](./legacy-components.md).

> [!NOTE]
> Vissa analys medie processorer kommer att dras tillbaka. Information om datum för indragning finns i avsnittet om [äldre komponenter](legacy-components.md) .

|Namn på mediebearbetare|Status|Region
|---|---|---|
|Azure Media Face Detector|Förhandsgranskning|Alla|
|Azure Media Indexer|Allmän tillgänglighet (GA)|Alla|
|Azure Media Motion Detector|Förhandsgranskning|Alla|
|Azure Media OCR|Förhandsgranskning|Alla|
|Azure Media Redactor|Allmän tillgänglighet (GA)|Alla|
|Azure Media Video Thumbnails|Förhandsgranskning|Alla|

## <a name="protection"></a>Skydd

Med Microsoft Azure Media Services kan du skydda dina mediefiler från att filerna lämnar din dator och medan de lagras, bearbetas och levereras. Mer information finns i avsnittet om att [skydda AMS-innehåll](media-services-content-protection-overview.md).

|Kryptering|Status|Regioner|
|---|---|---| 
|Storage|Allmän tillgänglighet (GA)|Alla|
|128-bitars AES-nycklar|Allmän tillgänglighet (GA)|Alla|
|Fairplay|Allmän tillgänglighet (GA)|Alla|
|PlayReady|Allmän tillgänglighet (GA)|Alla|
|Widevine|Allmän tillgänglighet (GA)|Alla utom Tyskland, federala myndigheter och Kina.

> [!NOTE]
> Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="reserved-units-rus"></a>Reserverade enheter (RU:er)

Antalet etablerade reserverade enheter anger antalet medieuppgifter som kan bearbetas samtidigt i en viss konto.

Tillgängligt i alla regioner.

## <a name="reserved-unit-ru-type"></a>Typ av reserverad enhet (RU)

Ett Media Services konto är associerat med en reserverad enhets typ som avgör hastigheten som dina medie bearbetnings uppgifter slutförs med. Du kan välja mellan följande typer av reserverade enheter: S1, S2 eller S3.

|Namn på RU-typ|Status|Regioner
|---|---|---|
|S1|Allmän tillgänglighet (GA)|Alla|
|S2|Allmän tillgänglighet (GA)|Alla utom Brasilien, södra och Indien, västra|
|S3|Allmän tillgänglighet (GA)|Allt utom Indien, västra|

## <a name="next-steps"></a>Nästa steg

[Migrera till Media Services v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Ge feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]