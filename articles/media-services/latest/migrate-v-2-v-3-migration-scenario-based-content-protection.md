---
title: Vägledning för migrering av innehålls skydd
description: Den här artikeln innehåller en scenario-baserad vägledning för innehålls skydd som hjälper dig att migrera från Azure Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: a481759da3f1e7d67accdca7b4322db53abbcb0c
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490955"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Vägledning för innehålls skydds scenario-baserad migrering

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-4.svg)

Den här artikeln innehåller information och vägledning om migrering av innehålls skydds användnings fall från v2 API till det nya Azure Media Services v3-API: et.

## <a name="protect-content-in-v3-api"></a>Skydda innehåll i v3-API

Använd support för [flera viktiga](architecture-design-multi-drm-system.md) funktioner i det nya v3-API: et.

Se begrepp för innehålls skydd, självstudier och rikt linjer i slutet av den här artikeln för särskilda steg.

> [!NOTE]
> Resten av den här artikeln beskriver hur du kan migrera ditt v2-innehålls skydd till v3 med .NET.  Om du behöver instruktioner eller exempel kod för ett annat språk eller en annan metod kan du skapa ett GitHub-problem för den här sidan.

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>v3 synlighet för v2-tillgångar, StreamingLocators och egenskaper

I v2 API, `Assets` ,, `StreamingLocators` och `ContentKeys` användes för att skydda ditt strömmande innehåll. När du migrerar till v3-API: t, så exponeras v2-API: n, `Assets` `StreamingLocators` och alla `ContentKeys` data visas automatiskt i v3-API: et och alla data på dem är tillgängliga så att du kan komma åt dem.

Du kan dock inte *Uppdatera* egenskaper på v2-entiteter via v3-API: et som skapades i v2.

Om du behöver uppdatera, ändra eller ändra innehåll som lagras i v2-entiteter uppdaterar du dem med v2-API: et eller skapar nya v3-API-entiteter för att migrera dem.

## <a name="asset-identifier-differences"></a>Skillnader mellan till gångs identifierare

För att migrera måste du ha åtkomst till egenskaper eller innehålls nycklar från v2-till gångar.  Det är viktigt att förstå att v2-API: et använder `AssetId` som primär identifierings nyckel men det nya v3-API: et använder *Azures resurs hanterings namn* för entiteten som primär identifierare.  (Egenskapen v2 `Asset.Name` används inte som en unik identifierare.) Med v3-API: t visas ditt v2-Asset namn som `Asset.Description` .

Om du till exempel tidigare hade en v2-till gång med ID: t `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8` är identifieraren nu i slutet av GUID `8cb39104-122c-496e-9ac5-7f9e2c2547b8` . Du ser detta när du visar dina v2-tillgångar via v3-API: et.

Alla till gångar som har skapats och publicerats med v2-API: et har både en `ContentKeyPolicy` och en `ContentKey` i v3-API: n i stället för en standard princip för innehålls nycklar på `StreamingPolicy` .

Mer information finns i dokumentationen för [innehålls nyckel principer](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) och dokumentationen för [strömnings principen](https://docs.microsoft.com/azure/media-services/latest/stream-streaming-policy-concept) .

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>Använda Azure Media Services Explorer (AMSE) v2-och AMSE v3-verktyg sida vid sida

Använd [verktyget v2 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) tillsammans med [verktyget v3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) för att jämföra data sida vid sida för en till gång som skapats och publicerats via v2-API: er. Egenskaperna bör vara synliga, men på olika platser.

## <a name="use-the-net-content-protection-migration-sample"></a>Använd exemplet för migrering av .NET-innehålls skydd

Du kan hitta ett kod exempel för att jämföra skillnaderna i till gångs identifierare med hjälp av [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) under ContentProtection i Media Services kod exempel.

## <a name="list-the-streaming-locators"></a>Visa en lista över strömmande positionerare

Du kan fråga `StreamingLocators` associerat med de till gångar som har skapats i v2-API: et med den nya v3-metoden [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) i till gångs enheten.  Referera också till .NET-klientens SDK-version av [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

Med resultatet av `ListStreamingLocators` metoden får du `Name` och `StreamingLocatorId` lokaliseraren tillsammans med `StreamingPolicyName` .

## <a name="find-the-content-keys"></a>Sök efter innehålls nycklar

För att hitta det som `ContentKeys` används med kan `StreamingLocators` du anropa metoden [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) .  

Mer information om innehålls skydd i v3-API: n finns i artikeln [skydda ditt innehåll med Media Services dynamisk kryptering.](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>Ändra den v2-ContentKeyPolicy som behåller samma ContentKey

Du bör först avpublicera (ta bort alla strömmande positionerare) på till gången via v2 SDK. Gör så här:

1. Ta bort lokaliseraren.
1. Ta bort länken till `ContentKeyAuthorizationPolicy` .
1. Ta bort länken till `AssetDeliveryPolicy` .
1. Ta bort länken till `ContentKey` .
1. Ta bort `ContentKey` .
1. Skapa en ny `StreamingLocator` i v3 med hjälp av en v3 `StreamingPolicy` och `ContentKeyPolicy` Ange den angivna innehålls nyckel identifieraren och det nyckel värde som behövs.

> [!NOTE]
> Det går att ta bort v2-lokaliseraren med hjälp av v3-API: et, men den tar inte bort innehålls nyckeln eller innehålls nyckel principen om de skapades i v2-API: et.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Koncept för innehålls skydd, självstudier och hur du går till guider

### <a name="concepts"></a>Begrepp

- [Skydda ditt innehåll med Media Services dynamisk kryptering](drm-content-protection-concept.md)
- [Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](architecture-design-multi-drm-system.md)
- [Media Services v3 med PlayReady-licens mal len](drm-playready-license-template-concept.md)
- [Översikt över Media Services v3 med Widevine-licens mal len](drm-widevine-license-template-concept.md)
- [Licenskrav för och konfiguration av Apple FairPlay](drm-fairplay-license-overview.md)
- [Strömmande principer](stream-streaming-policy-concept.md)
- [Principer för innehålls nyckel](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Självstudier

[Snabb start: Använd portalen för att kryptera innehåll](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Guider

- [Hämta en signeringsnyckel från den befintliga principen](drm-get-content-key-policy-dotnet-how-to.md)
- [Offline-FairPlay strömning för iOS med Media Services v3](drm-offline-fairplay-for-ios-concept.md)
- [Offline-Widevine strömning för Android med Media Services v3](drm-offline-widevine-for-android.md)
- [Offline PlayReady streaming för Windows 10 med Media Services v3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Exempel

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- Du kan också [jämföra v2-och v3-koden i kod exemplen](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Verktyg

- [verktyget v3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)
- [verktyget v2 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
