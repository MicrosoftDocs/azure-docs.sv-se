---
title: Azure Media Services felkoder | Microsoft Docs
description: Du kan få HTTP-felkoder från tjänsten beroende på problem som token för autentisering som går ut till åtgärder som inte stöds i Media Services. Den här artikeln ger en översikt över Azure Media Services v2 API-felkoder.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: d3a62a64-7608-4b17-8667-479b26ba0d6c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 10ade5c8f34d806ca5706ccaaf4b7ff2e05ba657
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013422"
---
# <a name="azure-media-services-error-codes"></a>Azure Media Services-felkoder

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

När du använder Microsoft Azure Media Services kan du få HTTP-felkoder från tjänsten, beroende på problem som token för autentisering som går ut till åtgärder som inte stöds i Media Services. Följande är en lista över **http-felkoder** som kan returneras av Media Services och möjliga orsaker till dem.  

## <a name="400-bad-request"></a>400 – Felaktig begäran
Begäran innehåller ogiltig information och avvisas på grund av en av följande orsaker:

* En API-version som inte stöds har angetts. Den senaste versionen finns i [installations programmet för Media Services REST API utveckling](media-services-rest-how-to-use.md).
* API-versionen för Media Services har inte angetts. Information om hur du anger API-versionen finns [Media Services åtgärder REST API referens](/rest/api/media/operations/azure-media-services-rest-api-reference).
  
  > [!NOTE]
  > Om du använder .NET eller Java SDK: er för att ansluta till Media Services, anges API-versionen åt dig när du försöker och utför en åtgärd mot Media Services.
  > 
  > 
* En odefinierad egenskap har angetts. Egenskaps namnet visas i fel meddelandet. Endast de egenskaper som är medlemmar i en viss entitet kan anges. Se [Azure Media Services REST API referens](/rest/api/media/operations/azure-media-services-rest-api-reference) för en lista över entiteter och deras egenskaper.
* Ett ogiltigt egenskaps värde har angetts. Egenskaps namnet visas i fel meddelandet. Se föregående länk för giltiga egenskaps typer och deras värden.
* Ett egenskaps värde saknas och måste anges.
* En del av den angivna URL: en innehåller ett felaktigt värde.
* Ett försök gjordes att uppdatera en WriteOnce-egenskap.
* Ett försök gjordes att skapa ett jobb som har en inmatad till gång med en primär AssetFile som inte har angetts eller som inte kunde fastställas.
* Ett försök gjordes att uppdatera en SAS-lokaliserare. SAS-positionerare kan bara skapas eller tas bort. Du kan uppdatera strömmande positionerare. Mer information finns i [positionerare](/rest/api/media/operations/locator).
* En åtgärd som inte stöds eller fråga skickades.

## <a name="401-unauthorized"></a>401 – Ej behörig
Det gick inte att autentisera begäran (innan den kan auktoriseras) på grund av en av följande orsaker:

* Authentication-huvud saknas.
* Felaktigt värde för Authentication-huvud.
  * Token har upphört att gälla. 
  * Token innehåller en ogiltig signatur.

## <a name="403-forbidden"></a>403 – Förbjuden
Begäran tillåts inte på grund av en av följande orsaker:

* Det går inte att hitta Media Services kontot eller så har det tagits bort.
* Media Servicess kontot är inaktiverat och typen av begäran är inte HTTP GET. Tjänst åtgärder returnerar också ett 403-svar.
* Autentiseringstoken innehåller inte användarens autentiseringsinformation: AccountName och/eller SubscriptionId. Du hittar den här informationen i Media Services UI-tillägget för ditt Media Services-konto i Azure-Hanteringsportal.
* Det går inte att komma åt resursen.
  
  * Ett försök gjordes att använda en MediaProcessor som inte är tillgänglig för ditt Media Services-konto.
  * Ett försök gjordes att uppdatera en JobTemplate som definierats av Media Services.
  * Det gjordes ett försök att skriva över någon annan Media Servicess kontots positionerare.
  * Det gjordes ett försök att skriva över en annan Media Services kontots ContentKey.
* Det gick inte att skapa resursen på grund av en tjänst kvot som nåtts för det Media Services kontot. Mer information om tjänst kvoter finns i [kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="404-not-found"></a>404 – Hittades inte
Begäran tillåts inte för en resurs på grund av en av följande orsaker:

* Ett försök gjordes att uppdatera en entitet som inte finns.
* Ett försök gjordes att ta bort en entitet som inte finns.
* Ett försök gjordes att skapa en entitet som länkar till en entitet som inte finns.
* Ett försök gjordes att hämta en entitet som inte finns.
* Ett försök gjordes att ange ett lagrings konto som inte är kopplat till Media Services kontot.  

## <a name="409-conflict"></a>409 – Konflikt
Begäran tillåts inte på grund av en av följande orsaker:

* Fler än ett AssetFile har det angivna namnet inom till gången.
* Ett försök gjordes att skapa en andra primär AssetFile inom till gången.
* Ett försök gjordes att skapa en ContentKey med angivet ID som redan används.
* Ett försök gjordes att skapa en positionerare med angivet ID som redan används.
* Fler än ett IngestManifestFile har det angivna namnet i IngestManifest.
* Ett försök gjordes att länka en andra lagrings krypterings ContentKey till den lagrings krypterade till gången.
* Ett försök gjordes att länka samma ContentKey till till gången.
* Ett försök gjordes att skapa en positionerare till en till gång vars lagrings behållare saknas eller inte längre är kopplad till till gången.
* Ett försök gjordes att skapa en positionerare till en till gång som redan har 5 lokaliserare som används. (Azure Storage tillämpar gränsen för fem principer för delad åtkomst på en lagrings behållare.)
* Länkning av lagrings konto för en till gång till en IngestManifestAsset är inte samma som det lagrings konto som används av den överordnade IngestManifest.  

## <a name="500-internal-server-error"></a>500 internt serverfel
Under bearbetningen av begäran upptäcker Media Services fel som förhindrar bearbetningen från att fortsätta. Detta kan bero på något av följande:

* Det går inte att skapa en till gång eller ett jobb eftersom Media Services kontots tjänst kvot information är tillfälligt otillgänglig.
* Det går inte att skapa en till gång eller IngestManifest Blob Storage-behållare eftersom kontots lagrings konto information inte är tillgänglig för tillfället.
* Andra oväntade fel.

## <a name="503-service-unavailable"></a>503 Tjänsten är inte tillgänglig
Servern kan för närvarande inte ta emot begär Anden. Det här felet kan orsakas av för många förfrågningar till tjänsten. Begränsningsfunktionen i Media Services begränsar resursanvändningen för program som skickar för många förfrågningar till tjänsten.

> [!NOTE]
> Kontrollera felmeddelandet och felkodsträngen för att få mer detaljerad information om orsaken till 503-felet. Det här felet medför inte alltid begränsning.
> 
> 

Beskrivningar av möjliga status är:

* "Servern är upptagen. Tidigare körningar av den här typen av begäran tog mer än {0} sekunder. "
* "Servern är upptagen. Fler än {0} begär Anden per sekund kan begränsas. "
* "Servern är upptagen. Fler än {0} begär Anden inom {1} några sekunder kan begränsas. "

För att hantera det här felet rekommenderar vi att du använder exponentiella omprövnings logik. Det innebär att användningen av progressivt väntar mellan upprepade försök för efterföljande fel svar.  Mer information finns i [program block för tillfälligt fel hantering](/previous-versions/msp-n-p/hh680905(v=pandp.50)).

> [!NOTE]
> Om du använder [Azure Media Services SDK för .net](https://github.com/Azure/azure-sdk-for-media-services/tree/master)har omprövnings logiken för 503-felet implementerats av SDK: n.  
> 
> 

## <a name="see-also"></a>Se även
[Fel koder för Media Services hantering](/rest/api/media/)

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
