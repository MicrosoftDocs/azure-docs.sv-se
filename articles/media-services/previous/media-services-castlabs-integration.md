---
title: Använda castLabs för att leverera Widevine-licenser till Azure Media Services | Microsoft Docs
description: 'I den här artikeln beskrivs hur du kan använda Azure Media Services (AMS) för att leverera en ström som är dynamiskt krypterad av AMS med både PlayReady-och Widevine-DRM: er.'
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: willzhan
ms.openlocfilehash: 576ac636f166e2daebbb9919d6666fea913a17be
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017060"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>Använd castLabs för att leverera Widevine-licenser till Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>Översikt

I den här artikeln beskrivs hur du kan använda Azure Media Services (AMS) för att leverera en ström som är dynamiskt krypterad av AMS med både PlayReady-och Widevine-DRM: er. PlayReady-licensen kommer från Media Services PlayReady licens Server och Widevine-licensen levereras av **castLabs** -licensservern.

Om du vill spela upp strömmande innehåll som skyddas av CENC (PlayReady och/eller Widevine) kan du använda  [Azure Media Player](https://aka.ms/azuremediaplayer). Mer information finns i [amp Document](https://amp.azure.net/libs/amp/latest/docs/) .

Följande diagram visar en övergripande Azure Media Services-och castLabs integrerings arkitektur.

![integration](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>Standard system konfiguration

* Medie innehåll lagras i AMS.
* Nyckel-ID: n för innehålls nycklar lagras i både castLabs och AMS.
* både castLabs och AMS har inbyggd token-autentisering. I följande avsnitt diskuteras autentiseringstoken. 
* När en klient begär att strömma videon krypteras innehållet dynamiskt med **common Encryption** (Cenc) och dynamiskt paketeras av AMS till Smooth Streaming och tank streck. Vi levererar även PlayReady M2TS-dataström-kryptering för HLS Streaming Protocol.
* PlayReady-licensen hämtas från AMS licens Server och Widevine-licensen hämtas från castLabs-licensservern. 
* Media Player bestämmer automatiskt vilken licens som ska hämtas baserat på klientens plattforms funktion. 

## <a name="authentication-token-generation-for-getting-a-license"></a>Generering av autentiseringstoken för att hämta en licens

Både castLabs-och AMS stöder JWT-formatet (JSON Web Token) som används för att auktorisera en licens. 

### <a name="jwt-token-in-ams"></a>JWT-token i AMS

I följande tabell beskrivs JWT-token i AMS. 

| Utfärdare | Issuer-sträng från den valda STS (Secure token service) |
| --- | --- |
| Målgrupp |Mål grupp sträng från den använda STS |
| Anspråk |En uppsättning anspråk |
| NotBefore |Startens giltighet för token |
| Upphör |Slut giltighet för token |
| SigningCredentials |Nyckeln som delas mellan PlayReady-licensserver, castLabs licens Server och STS, kan vara antingen symmetrisk eller asymmetrisk nyckel. |

### <a name="jwt-token-in-castlabs"></a>JWT-token i castLabs

I följande tabell beskrivs JWT-token i castLabs. 

| Name | Beskrivning |
| --- | --- |
| optData |En JSON-sträng som innehåller information om dig. |
| CRT |En JSON-sträng som innehåller information om till gången, dess licens information och uppspelnings rättigheter. |
| IAT |Aktuellt datetime i epok. |
| jti |En unik identifierare för denna token (varje token kan endast användas en gång i castLabs-systemet). |

## <a name="sample-solution-setup"></a>Exempel på lösnings installation

[Exempel lösningen](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) består av två projekt:

* En konsol app som kan användas för att ange DRM-begränsningar för en redan inmatad till gång, för både PlayReady och Widevine.
* Ett webb program som använder token, som kan ses som en mycket förenklad version av en STS.

Så här använder du konsol programmet:

1. Ändra app.config till att konfigurera AMS-autentiseringsuppgifter, castLabs-autentiseringsuppgifter, STS-konfiguration och delad nyckel.
2. Ladda upp en till gång i AMS.
3. Hämta UUID från den överförda till gången och ändra rad 32 i filen program. CS:
   
      var objIAsset = _context. Till gångar. där (x => x.Id = = "OBS: CID: UUID: dac53a5d-1500-80bd-b864-f1e4b62594cf"). FirstOrDefault ();
4. Använd en AssetId för att namnge till gången i castLabs-systemet (rad 44 i filen program. CS).
   
   Du måste ange AssetId för **castLabs**; Det måste vara en unik alfanumerisk sträng.
5. Kör programmet.

Så här använder du webb programmet (STS):

1. Ändra web.config till att konfigurera castlabs för handlare, STS-konfigurationen och den delade nyckeln.
2. Distribuera till Azure Websites.
3. Navigera till webbplatsen.

## <a name="playing-back-a-video"></a>Spela upp en video

Om du vill spela upp en video som är krypterad med vanlig kryptering (PlayReady och/eller Widevine) kan du använda [Azure Media Player](https://aka.ms/azuremediaplayer). När du kör-konsol programmet visas innehålls nyckel-ID: t och manifest-URL: en.

1. Öppna en ny flik och starta STS: http://[yourStsName]. azurewebsites. NET/API/token/AssetID/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid].
2. Gå till [Azure Media Player](https://aka.ms/azuremediaplayer).
3. Klistra in i strömnings-URL: en.
4. Klicka på kryss rutan **Avancerade alternativ** .
5. I list rutan **skydd** väljer du PlayReady och/eller Widevine.
6. Klistra in den token som du fick från din STS i text rutan token. 
   
   CastLab licens servern behöver inte prefixet "Bearer =" framför token. Ta bort det innan du skickar in token.
7. Uppdatera spelaren.
8. Videon ska spelas upp.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

