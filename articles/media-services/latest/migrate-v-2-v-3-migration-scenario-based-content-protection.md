---
title: Vägledning för migrering av innehålls skydd
description: Den här artikeln innehåller en scenario-baserad vägledning för innehålls skydd som hjälper dig att migrera från Azure Media Services v2 till v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 74f15fc302a8499e41a1413dd8915e6442d4bbe7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064502"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Vägledning för innehålls skydds scenario-baserad migrering

![logo typ för migrations guide](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![steg 2 i migreringen](./media/migration-guide/steps-4.svg)

Den här artikeln innehåller information och vägledning om migrering av innehålls skydds användnings fall från v2 API till det nya Azure Media Services v3-API: et.

## <a name="protect-content-in-v3-api"></a>Skydda innehåll i v3-API

Använd support för [flera viktiga](architecture-design-multi-drm-system.md) funktioner i det nya v3-API: et.

Se begrepp för innehålls skydd, självstudier och hur du går till guider nedan för olika steg.

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>Synlighet för v2-tillgångar, StreamingLocators och egenskaper i v3 API för innehålls skydds scenarier

Under migreringen till v3-API: et kommer du att se att du behöver komma åt vissa egenskaper eller innehålls nycklar från dina v2-tillgångar. En viktig skillnad är att v2-API: et skulle använda **AssetID** som primär identifierings nyckel och det nya v3-API: et använder Azures resurs hanterings namn för entiteten som den primära identifieraren.  Egenskapen v2 **Asset.name** används vanligt vis inte som en unik identifierare, så när du migrerar till v3 kommer du att se att dina v2-assets namn nu visas i fältet **till gång. Description** .

Om du till exempel tidigare hade en v2-till gång med ID: t **"OBS! CID: UUID: 8cb39104-122c-496e-9ac5-7f9e2c2547b8"** kommer du att se när du registrerar de gamla v2-resurserna via v3-API: et. namnet kommer nu att vara GUID-delen i slutet (i det här fallet **"8cb39104-122c-496e-9ac5-7f9e2c2547b8"**.)

Du kan fråga de **StreamingLocators** som är kopplade till till gångarna som skapats i v2-API: et med hjälp av den nya v3-metoden [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) på till gångs enheten.  Referera också till .NET-klientens SDK-version av [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

Resultatet av **ListStreamingLocators** -metoden ger dig **namnet** och **StreamingLocatorId** för lokaliseraren tillsammans med **StreamingPolicyName**.

Om du vill hitta **ContentKeys** som används i din **StreamingLocators** för innehålls skydd kan du anropa metoden [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) .  

Alla **till gångar** som har skapats och publicerats med v2-API: et har både en [innehålls nyckel princip](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) och en innehålls nyckel som har definierats på dem i v3-API: n, i stället för att använda en standard princip för innehålls nycklar i [streaming-principen](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept).

Mer information om innehålls skydd i v3-API: n finns i artikeln [skydda ditt innehåll med Media Services dynamisk kryptering.](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>Visa en lista över v2-till gångar och inställningar för innehålls skydd med hjälp av v3-API: et

I v2-API: t använder du vanligt vis **till gångar**, **StreamingLocators** och **ContentKeys** för att skydda ditt strömmande innehåll.
När du migrerar till v3-API: t kommer dina v2 API-till gångar, StreamingLocators och ContentKeys att visas automatiskt i v3-API: et och alla data som finns tillgängliga för dig kan du komma åt.

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>Kan jag uppdatera v2-egenskaper med hjälp av v3-API: et?

Nej, du kan inte uppdatera egenskaper på v2-entiteter via v3-API: et som skapades med StreamingLocators, StreamingPolicies, innehålls nyckel principer och innehålls nycklar i v2.
Om du behöver uppdatera, ändra eller ändra innehåll som lagras i v2-entiteter måste du uppdatera det via v2-API: et eller skapa nya v3-API-entiteter för att migrera dem framåt.

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>Hur gör jag för att ändra ContentKeyPolicy som används för en v2-till gång som publiceras och behåll samma innehålls nyckel?

I så fall bör du först avpublicera (ta bort alla strömmande positionerare) på till gången via v2 SDK: n (ta bort lokaliseraren, ta bort länken för innehålls nyckelns auktoriseringsprincip, ta bort länk till till gångs leverans principen, ta bort länk till innehålls nyckeln, ta bort innehålls nyckeln) och skapa en ny **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** i v3 med hjälp av v3 [StreamingPolicy](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) och [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept).

Du måste ange den angivna innehålls nyckel identifieraren och det nyckel värde som krävs när du skapar **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)**.

Observera att det är möjligt att ta bort v2-lokaliseraren med hjälp av v3-API: et, men det tar inte bort innehålls nyckeln eller innehålls nyckel principen som används om de skapades i v2-API: et.  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>Använda AMSE v2 och AMSE v3 sida vid sida

När du migrerar innehållet från v2 till v3 uppmanas du att installera [verktyget v2 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) tillsammans med v3- [Azure Media Services Explorer-verktyget](https://github.com/Azure/Azure-Media-Services-Explorer) för att jämföra de data som visas sida vid sida för en till gång som har skapats och publicerats via v2-API: er. Egenskaperna bör vara synliga, men på olika platser nu.  


## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Koncept för innehålls skydd, självstudier och hur du går till guider

### <a name="concepts"></a>Begrepp

- [Skydda ditt innehåll med Media Services dynamisk kryptering](drm-content-protection-concept.md)
- [Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](architecture-design-multi-drm-system.md)
- [Media Services v3 med PlayReady-licens mal len](drm-playready-license-template-concept.md)
- [Översikt över Media Services v3 med Widevine-licens mal len](drm-widevine-license-template-concept.md)
- [Licenskrav för och konfiguration av Apple FairPlay](drm-fairplay-license-overview.md)
- [Strömmande principer](streaming-policy-concept.md)
- [Principer för innehålls nyckel](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Självstudier

[Snabb start: Använd portalen för att kryptera innehåll](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Guider

- [Hämta en signeringsnyckel från den befintliga principen](drm-get-content-key-policy-dotnet-how-to.md)
- [Offline-FairPlay strömning för iOS med Media Services v3](drm-offline-fairplay-for-ios-concept.md)
- [Offline-Widevine strömning för Android med Media Services v3](drm-offline-widevine-for-android.md)
- [Offline PlayReady streaming för Windows 10 med Media Services v3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>Exempel

Du kan också [jämföra v2-och v3-koden i kod exemplen](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>Verktyg

- [verktyget v3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)
- [verktyget v2 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
