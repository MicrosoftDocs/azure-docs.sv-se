---
title: Översikt över Media Services åtgärder REST API | Microsoft Docs
description: Media Services åtgärder REST API används för att skapa jobb, till gångar, Live Channels och andra resurser i ett Media Services-konto. Den här artikeln innehåller en Azure Media Services v2 REST API översikt.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a5f1c5e7-ec52-4e26-9a44-d9ea699f68d9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.reviewer: johndeu
ms.openlocfilehash: 9f147e333e4d1b95a14dd3121d7ab304b6166248
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103010056"
---
# <a name="media-services-operations-rest-api-overview"></a>Översikt över Media Services åtgärder REST API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

REST-API: et **Media Services Operations** används för att skapa jobb, till gångar, direktsända kanaler och andra resurser i ett Media Services konto. Mer information finns i [Media Services åtgärder REST API referens](/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services tillhandahåller en REST API som accepterar både JSON-eller Atom + pub XML-format. Media Services REST API kräver vissa HTTP-huvuden som varje klient måste skicka när de ansluter till Media Services, samt en uppsättning valfria huvuden. I följande avsnitt beskrivs de huvuden och HTTP-verb som du kan använda när du skapar förfrågningar och tar emot svar från Media Services.

Autentisering till Media Services REST API görs genom Azure Active Directory autentisering som beskrivs i artikeln [Använd Azure AD-autentisering för att få åtkomst till Azure Media Services API med rest](media-services-rest-connect-with-aad.md)

## <a name="considerations"></a>Överväganden

Följande överväganden gäller när du använder REST.

* När du frågar entiteter, finns det en gräns på 1000 entiteter som returneras vid en tidpunkt eftersom offentliga REST v2 begränsar frågeresultat till 1000-resultat. Du måste använda **hoppa över** och **ta** (.net)/ **Top** (rest) enligt beskrivningen i [det här .net-exemplet](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) och [REST API exemplet](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). 
* När du använder JSON och anger att ska använda **__metadata** nyckelordet i begäran (t. ex. för att referera till ett länkat objekt) måste du ange formatet för att **ta emot** sidhuvud till [JSON](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/) (se följande exempel). OData förstår inte **__metadata** egenskapen i begäran, om du inte anger den som utförlig.  

    ```console
    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.19
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net

    {
        "Name" : "NewTestJob", 
        "InputMediaAssets" : 
            [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aba5356eb-30ff-4dc6-9e5a-41e4223540e7')"}}]
    . . . 
   ```

## <a name="standard-http-request-headers-supported-by-media-services"></a>Standard-HTTP-begärandehuvuden som stöds av Media Services
För varje anrop du gör i Media Services finns det en uppsättning obligatoriska huvuden som du måste inkludera i din begäran och även en uppsättning valfria huvuden som du kanske vill inkludera. I tabellen nedan visas de huvuden som krävs:

| Huvud | Typ | Värde |
| --- | --- | --- |
| Auktorisering |Bearer |Bearer är den enda godkända mekanismen för auktorisering. Värdet måste också innehålla den åtkomsttoken som tillhandahålls av Azure Active Directory. |
| x-MS-version |Decimal |2,17 (eller senaste versionen)|
| DataServiceVersion |Decimal |3.0 |
| MaxDataServiceVersion |Decimal |3.0 |

> [!NOTE]
> Eftersom Media Services använder OData för att exponera REST-API: erna, ska DataServiceVersion-och MaxDataServiceVersion-huvuden tas med i alla begär Anden. men om de inte är det antar Media Services det DataServiceVersion-värde som används är 3,0.
> 
> 

Följande är en uppsättning valfria huvuden:

| Huvud | Typ | Värde |
| --- | --- | --- |
| Datum |RFC 1123-datum |Tidsstämpel för begäran |
| Acceptera |Innehållstyp |Begärd innehålls typ för svaret, till exempel följande:<p> -Application/JSON; OData = verbose<p> -Application/Atom + XML<p> Svar kan ha en annan innehålls typ, till exempel en BLOB Fetch, där ett lyckat svar innehåller BLOB-dataströmmen som nytto lasten. |
| Accept-Encoding |Gzip, deflate |GZIP och DEFLATE-kodning, om tillämpligt. Obs! för stora resurser kan Media Services ignorera detta sidhuvud och returnera data som inte är komprimerade. |
| Accept-Language |"sv", "es" och så vidare. |Anger det språk som ska besvaras. |
| Accept-Charset |Teckenuppsättnings typ som "UTF-8" |Standardvärdet är UTF-8. |
| X-HTTP-metod |HTTP-metod |Tillåter att klienter eller brand väggar som inte stöder HTTP-metoder som att lägga till eller ta bort använder dessa metoder, tunnlade via ett GET-anrop. |
| Content-Type |Innehållstyp |Innehålls typ för begär ande texten i begäran om att skicka eller publicera. |
| klient-begärande-ID |Sträng |Ett callation-definierat värde som identifierar den angivna begäran. Om det här värdet anges tas det här värdet med i svarsmeddelandet som ett sätt att mappa begäran. <p><p>**Viktigt**<p>Värdena bör vara tak vid 2096b (2 000). |

## <a name="standard-http-response-headers-supported-by-media-services"></a>Standard-HTTP-svarshuvuden som stöds av Media Services
Följande är en uppsättning huvuden som kan returneras till dig, beroende på vilken resurs du begärde och vilka åtgärder du avsåg att utföra.

| Huvud | Typ | Värde |
| --- | --- | --- |
| begärande-ID |Sträng |En unik identifierare för den aktuella åtgärden, genererad tjänst. |
| klient-begärande-ID |Sträng |En identifierare som anges av anroparen i den ursprungliga begäran, om sådan finns. |
| Datum |RFC 1123-datum |Datum/tid då begäran bearbetades. |
| Content-Type |Det varierar |Innehålls typen för svars texten. |
| Content-Encoding |Det varierar |Gzip eller deflatera efter behov. |

## <a name="standard-http-verbs-supported-by-media-services"></a>Standard-HTTP-verb som stöds av Media Services
Följande är en fullständig lista över HTTP-verb som kan användas när du gör HTTP-förfrågningar:

| Verb | Beskrivning |
| --- | --- |
| GET |Returnerar det aktuella värdet för ett objekt. |
| POST |Skapar ett objekt baserat på angivna data eller skickar ett kommando. |
| PUT |Ersätter ett objekt, eller skapar ett namngivet objekt (om tillämpligt). |
| DELETE |Tar bort ett objekt. |
| Katalog |Uppdaterar ett befintligt objekt med namngivna egenskaps ändringar. |
| HEAD |Returnerar metadata för ett objekt för GET-svar. |

## <a name="discover-and-browse-the-media-services-entity-model"></a>Identifiera och bläddra i Media Services Entity-modellen
För att göra Media Services entiteter mer synliga kan $metadatas åtgärden användas. Du kan hämta alla giltiga entitetstyper, entitets egenskaper, associationer, funktioner, åtgärder och så vidare. Genom att lägga till åtgärden $metadata i slutet av Media Services REST API slut punkten kan du komma åt den här identifierings tjänsten.

 /API/$metadata.

Du bör lägga till "? API-version = 2. x" i slutet av URI: n om du vill visa metadata i en webbläsare eller inte innehåller huvudet x-MS-version i din begäran.

## <a name="authenticate-with-media-services-rest-using-azure-active-directory"></a>Autentisera med Media Services REST med Azure Active Directory

Autentisering på REST API görs via Azure Active Directory (AAD).
Mer information om hur du hämtar nödvändig autentiseringsinformation för ditt Media Services-konto från Azure-portalen finns i [få åtkomst till Azure Media Services-API med Azure AD-autentisering](media-services-use-aad-auth-to-access-ams-api.md).

Mer information om hur du skriver kod som ansluter till REST API med Azure AD-autentisering finns i artikeln [använda Azure AD-autentisering för att få åtkomst till Azure Media Services-API: et med rest](media-services-rest-connect-with-aad.md).

## <a name="next-steps"></a>Nästa steg
Information om hur du använder Azure AD-autentisering med Media Services REST API finns i [använda Azure AD-autentisering för att få åtkomst till Azure Media Services API med rest](media-services-rest-connect-with-aad.md).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
