---
title: Nyckel principer för innehåll i Media Services – Azure
description: Den här artikeln innehåller en förklaring av vilka principer för innehålls nycklar som är och hur de används av Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 1333bbcc7e2ea9219d28fcc1e63f21a833c2bc6f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709571"
---
# <a name="content-key-policies"></a>Viktiga innehållsprinciper

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med Media Services kan du leverera direktsänd och innehåll på begäran som krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre större Digital Rights Management-systemen (DRM): Microsoft PlayReady, Google Widevine och Apple FairPlay. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM-licenser (PlayReady, Widevine och FairPlay) till auktoriserade klienter. 

Om du vill ange krypterings alternativ för data strömmen måste du skapa en [strömmande princip](streaming-policy-concept.md) och koppla den till din [strömmande positionerare](streaming-locators-concept.md). Du skapar en [innehålls nyckel princip](/rest/api/media/contentkeypolicies) för att konfigurera hur innehålls nyckeln (som ger säker åtkomst till [till gångar](assets-concept.md)) levereras till slut klienter. Du måste ange kraven (begränsningar) för den innehålls nyckel princip som måste uppfyllas för att nycklar med den angivna konfigurationen ska skickas till klienter. Innehålls nyckel principen behövs inte för att rensa strömma eller ladda ned. 

Vanligt vis associerar du din innehålls nyckel princip med din [streaming Locator](streaming-locators-concept.md). Du kan också ange innehålls nyckel principen i en [strömmande princip](streaming-policy-concept.md) (när du skapar en anpassad strömnings princip för avancerade scenarier). 

## <a name="best-practices-and-considerations"></a>Metod tips och överväganden

> [!IMPORTANT]
> Kontrol lera följande rekommendationer.

* Du bör utforma en begränsad uppsättning principer för ditt Media Service-konto och återanvända dem för dina strömmande positionerare när samma alternativ behövs. Mer information finns i [kvoter och begränsningar](limits-quotas-constraints.md).
* Innehålls nyckel principer kan uppdateras. Det kan ta upp till 15 minuter för nyckel leverans-cachen att uppdatera och hämta den uppdaterade principen. 

   Genom att uppdatera principen skriver du över din befintliga CDN-cache som kan orsaka uppspelnings problem för kunder som använder cachelagrat innehåll.  
* Vi rekommenderar att du inte skapar en ny innehålls nyckel princip för varje till gång. De största fördelarna med att dela samma innehålls nyckel princip mellan till gångar som behöver samma princip alternativ är:
   
   * Det är enklare att hantera ett litet antal principer.
   * Om du behöver göra uppdateringar av innehålls nyckel principen, kommer ändringarna att börja gälla på alla nya licens förfrågningar nästan direkt.
* Om du behöver skapa en ny princip måste du skapa en ny plats för direkt uppspelning för till gången.
* Vi rekommenderar att Media Services automatiskt genererar innehålls nyckeln. 

   Normalt använder du en lång livs längds nyckel och kontrollerar om det finns en innehålls nyckel princip med [Get](/rest/api/media/contentkeypolicies/get). För att hämta nyckeln måste du anropa en separat åtgärds metod för att få hemligheter eller autentiseringsuppgifter, se exemplet nedan.

## <a name="example"></a>Exempel

För att komma till nyckeln använder du `GetPolicyPropertiesWithSecretsAsync` , som du ser i [Hämta en signerings nyckel från den befintliga princip](get-content-key-policy-dotnet-howto.md#get-contentkeypolicy-with-secrets) exemplet.

## <a name="filtering-ordering-paging"></a>Filtrering, sortering, växling

Se [filtrering, sortering, sid indelning för Media Services entiteter](entities-overview.md).

## <a name="additional-notes"></a>Ytterligare information

* Egenskaperna för de innehålls nyckel principer som är av `Datetime` typen är alltid i UTC-format.
* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="next-steps"></a>Nästa steg

* [Använd AES-128 dynamisk kryptering och Key Delivery Service](protect-with-aes128.md)
* [Använda dynamisk DRM-kryptering och tjänsten för licensleverans](protect-with-drm.md)
* [Basic AES-rensning av kryptering och exempel kod för strömning](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)
