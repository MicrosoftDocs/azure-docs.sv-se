---
title: Azure App konfiguration REST API-rubriker
description: Referens sidor för rubriker som används med Azure App konfigurations REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 80b20b0b55219766872166685c0b1257f3c39c55
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932582"
---
# <a name="headers"></a>Sidhuvuden

Den här artikeln innehåller länkar till referens sidor för rubriker som används med Azure App konfigurations REST API.

## <a name="request-headers"></a>Begärandehuvuden

I följande tabell beskrivs vanliga begärandehuvuden som används i Azure App-konfigurationen.

| Huvud | Beskrivning | Exempel |
|--|--|--|
| **Auktorisering** | Används för att [autentisera](./rest-api-authentication-index.md) en begäran till tjänsten. Se [avsnitt 14,8](https://tools.ietf.org/html/rfc2616#section-14.8) | `Authorization: HMAC-SHA256 Credential=<Credential>&SignedHeaders=Host;x-ms-date;x-ms-content-sha256&Signature=<Signature>` |
| **Godkänn** | Informerar servern vilken medietyp som klienten kommer att acceptera i ett HTTP-svar. Se [avsnitt 14,1](https://tools.ietf.org/html/rfc2616#section-14.1) | `Accept: application/vnd.microsoft.appconfig.kv+json;` |
| **Acceptera-datetime** | Begär att servern returnerar sitt innehåll som en representation av sitt tidigare tillstånd. Värdet för den här rubriken är det begärda datetime-värdet för det aktuella läget. Se [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **Innehålls typ** | Innehåller medie typen för innehållet i innehållet i HTTP-begäran. Se [avsnitt 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **Datum** | Det datum/tid-värde som HTTP-begäran utfärdades. Den här rubriken används i [HMAC-autentisering](./rest-api-authentication-hmac.md). Se [avsnitt 14,18](https://tools.ietf.org/html/rfc2616#section-14.18) | `Date: Fri, 11 May 2018 18:48:36 GMT` |
| **Värd** | Anger den klient organisation som begäran har utfärdats för. Den här rubriken används i [HMAC-autentisering](./rest-api-authentication-hmac.md). Se [avsnitt 14,23](https://tools.ietf.org/html/rfc2616#section-14.23) | `Host: contoso.azconfig.io` |
| **If-Match** | Används för att göra en HTTP-begäran villkorlig. Den här begäran bör bara lyckas om mål resursens ETag matchar värdet för den här rubriken. Värdet "*" matchar alla ETag. Se [avsnitt 14,24](https://tools.ietf.org/html/rfc2616#section-14.24) | `If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **If-None-Match** | Används för att göra en HTTP-begäran villkorlig. Den här begäran bör bara lyckas om mål resursens ETag inte matchar värdet för den här rubriken. Värdet "*" matchar alla ETag. Se [avsnitt 14,26](https://tools.ietf.org/html/rfc2616#section-14.26) | `If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Sync-token** | Används för att möjliggöra konsekvens i real tid under en sekvens med begär Anden. | `Sync-Token: jtqGc1I4=MDoyOA==;sn=28` |
| **x-MS-client-Request-ID** | Ett unikt ID som tillhandahålls av klienten som används för att spåra en förfrågans tur och retur. | `x-ms-client-request-id: 00000000-0000-0000-0000-000000000000` |
| **x-MS-Content-SHA256** | En SHA256 sammandrag av innehållet i HTTP-begäran. Den här rubriken används i [HMAC-autentisering](./rest-api-authentication-hmac.md). | `x-ms-content-sha256: 47DEQpj8HBSa+/TImW+5JCeuQeRkm5NMpJWZG3hSuFU=` |
| **x-MS-date** | Rubriken kan anges och användas i stället för `Date` sidhuvudet om det inte går att komma åt datumets rubrik. Den här rubriken används i [HMAC-autentisering](./rest-api-authentication-hmac.md). | `x-ms-date: Fri, 11 May 2018 18:48:36 GMT` |
| **x-MS-retur-client-Request-ID** | Används tillsammans med `x-ms-client-request-id` rubriken. Om värdet för den här rubriken är "sant" instrueras servern att returnera värdet för `x-ms-client-request-id` begär ande huvudet. | `x-ms-return-client-request-id: true` |

## <a name="response-headers"></a>Svarsrubriker

Servern kan innehålla följande HTTP-huvuden i sina svar.

| Huvud | Beskrivning | Exempel |
|--|--|--|
| **Innehålls typ** | Innehåller medie typen för innehållet i HTTP-svarets brödtext. Se [avsnitt 14,17](https://tools.ietf.org/html/rfc2616#section-14.17) | `Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;` |
| **ETag** | En ogenomskinlig token som representerar statusen för en specifik resurs. Kan användas i villkorliga åtgärder. Se [avsnitt 14,19](https://tools.ietf.org/html/rfc2616#section-14.19) | `ETag: "4f6dd610dd5e4deebc7fbaef685fb903"` |
| **Senast ändrad** | Beskriver när den begärda resursen senast ändrades. Formaterat som ett [http-datum](https://tools.ietf.org/html/rfc2616#section-3.3.1). Se [avsnitt 14,29](https://tools.ietf.org/html/rfc2616#section-14.29) | `Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT` |
| **Länk** | Innehåller länkar till resurser som är relaterade till svaret. Den här rubriken används för växling med hjälp av _Nästa_ länk. Se [RFC 5988](https://tools.ietf.org/html/rfc5988) | `Link: </kv?after={token}>; rel="next"` |
| **Påminnelse-datetime** | Anger att innehållet i ett svar representerar ett tidigare tillstånd. Värdet för den här rubriken är datum/tid för detta tillstånd. Se [RFC 7089](https://tools.ietf.org/html/rfc7089#section-2.1.1) | `Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT` |
| **försök igen – efter-MS** | Innehåller en föreslagen period (i millisekunder) som klienten ska vänta innan den försöker köra en misslyckad begäran igen. | `retry-after-ms: 10` |
| **x-MS-Request-ID** | Ett unikt ID som genereras av servern som används för att spåra begäran inom tjänsten. | `x-ms-request-id: 00000000-0000-0000-0000-000000000000` |
| **WWW – autentisera** | Används för att anropa klienter för autentisering och ange en orsak till varför ett autentiseringsförsök har misslyckats. Se [avsnitt 14,47](https://tools.ietf.org/html/rfc2616#section-14.47) | `WWW-Authenticate: HMAC-SHA256 error="invalid_token" error_description="Invalid Signature"` |
