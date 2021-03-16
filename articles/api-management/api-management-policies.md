---
title: Azure API Management-principer | Microsoft Docs
description: Läs mer om principerna som är tillgängliga för användning i Azure API Management. Med principer kan utgivare ändra API-beteendet genom konfigurationen.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/17/2021
ms.author: apimpm
ms.openlocfilehash: e809efa9da32da5fe9ca296608c602e770f78265
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562356"
---
# <a name="api-management-policies"></a>API Management-principer
Det här avsnittet innehåller en referens för följande API Managements principer. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](api-management-howto-policies.md).

 Principer är en kraftfull funktion i systemet som gör att utgivaren kan ändra beteendet för API: et genom konfigurationen. Principer är en samling instruktioner som körs sekventiellt i begäran eller svar på ett API. Vanliga instruktioner är format konvertering från XML till JSON och begränsning av anrops frekvensen för att begränsa antalet inkommande anrop från en utvecklare. Många fler principer är tillgängliga i rutan.

 Principuttryck kan användas som attributvärden eller textvärden i API Management-principer, under förutsättning att principen tillåter det. Vissa principer som [Kontrollflöde](api-management-advanced-policies.md#choose) och [Ange variabel](api-management-advanced-policies.md#set-variable) baseras på principuttryck. Mer information finns i [Avancerade principer](api-management-advanced-policies.md#AdvancedPolicies) och [Principuttryck](api-management-policy-expressions.md).

##  <a name="policies"></a><a name="ProxyPolicies"></a> Rikt

-   [Principer för begränsning av åtkomst](api-management-access-restriction-policies.md#AccessRestrictionPolicies)
    -   [Kontrol lera http-huvud](api-management-access-restriction-policies.md#CheckHTTPHeader) -framtvingar förekomst och/eller värde för ett HTTP-huvud.
    -   [Begränsa anrops frekvensen av prenumeration](api-management-access-restriction-policies.md#LimitCallRate) – förhindrar att API-användnings toppar begränsas genom att begränsa anrops frekvensen, per prenumeration.
    -   [Begränsa anrops frekvensen per nyckel](api-management-access-restriction-policies.md#LimitCallRateByKey) – förhindrar att API-användnings toppar begränsas genom att begränsa anrops frekvensen, per nyckel.
    -   [Begränsa anroparen IP](api-management-access-restriction-policies.md#RestrictCallerIPs) -filter (tillåter/nekar) anrop från vissa IP-adresser och/eller adress intervall.
    -   [Ange användnings kvot per prenumeration](api-management-access-restriction-policies.md#SetUsageQuota) – gör att du kan tvinga en förnyad eller livs längds samtals volym och/eller bandbredds kvot, per prenumeration.
    -   [Ange användnings kvot per nyckel](api-management-access-restriction-policies.md#SetUsageQuotaByKey) – gör att du kan framtvinga en förnyad eller livs längds samtals volym och/eller bandbredds kvot, per nyckel.
    -   [Verifiera att JWT](api-management-access-restriction-policies.md#ValidateJWT) -framtvingar förekomst och giltighet av ett JWT extraheras från antingen ett angivet http-huvud eller en angiven frågeparameter.
-   [Avancerade principer](api-management-advanced-policies.md#AdvancedPolicies)
    -   [Kontroll flöde](api-management-advanced-policies.md#choose) – villkorligt tillämpar princip uttryck baserat på utvärderingen av booleska uttryck.
    -   [Vidarebefordra begäran](api-management-advanced-policies.md#ForwardRequest) – vidarebefordrar begäran till backend-tjänsten.
    -   [Begränsning av samtidighet](api-management-advanced-policies.md#LimitConcurrency) – förhindrar att omslutna principer körs av fler än det angivna antalet begär anden i taget.
    -   [Logga till Event Hub](api-management-advanced-policies.md#log-to-eventhub) – skickar meddelanden i angivet format till ett meddelande mål som definierats av en loggad entitet.
    -   [Skiss-svar](api-management-advanced-policies.md#mock-response) – avbryter pipeline-körningen och returnerar ett skissat svar direkt till anroparen.
    -   [Försök igen](api-management-advanced-policies.md#Retry) – nya försök att köra de omslutna princip instruktionerna, om och tills villkoret är uppfyllt. Körningen upprepas vid de angivna tidsintervallen och upp till det angivna antalet försök.
    -   [RETUR svar](api-management-advanced-policies.md#ReturnResponse) – avbryter pipeline-körningen och returnerar det angivna svaret direkt till anroparen.
    -   [Skicka en enkelriktad förfrågan](api-management-advanced-policies.md#SendOneWayRequest) – skickar en begäran till angiven URL utan att vänta på ett svar.
    -   [Skicka förfrågan](api-management-advanced-policies.md#SendRequest) – skickar en begäran till angiven URL.
    -   [Ange http-proxy](api-management-advanced-policies.md#SetHttpProxy) – tillåter att du dirigerar vidarebefordrade begär Anden via en http-proxy.
    -   [Ange variabel](api-management-advanced-policies.md#set-variable) – Behåll ett värde i en namngiven kontext variabel för senare åtkomst.
    -   [Ange metod för begäran](api-management-advanced-policies.md#SetRequestMethod) – gör att du kan ändra http-metoden för en begäran.
    -   [Ange status kod](api-management-advanced-policies.md#SetStatus) – ändrar HTTP-statuskoden till det angivna värdet.
    -   [Trace](api-management-advanced-policies.md#Trace) – lägger till anpassade spårningar i [API-kontrollens](./api-management-howto-api-inspector.md) utdata, Application Insights telemetrivärden och resurs loggar.
    -   [Vänta](api-management-advanced-policies.md#Wait) – väntar på en bifogad [sändnings förfrågan](api-management-advanced-policies.md#SendRequest), [hämtar värdet från cachen](api-management-caching-policies.md#GetFromCacheByKey)eller [styr flödes](api-management-advanced-policies.md#choose) principer som ska slutföras innan du fortsätter.
-   [Autentiseringsprinciper](api-management-authentication-policies.md#AuthenticationPolicies)
    -   [Autentisera med Basic](api-management-authentication-policies.md#Basic) -autentisera med en backend-tjänst med grundläggande autentisering.
    -   [Autentisera med klient certifikat](api-management-authentication-policies.md#ClientCertificate) – autentisera med en backend-tjänst med hjälp av klient certifikat.
    -   [Autentisera med hanterad identitet](api-management-authentication-policies.md#ManagedIdentity) – autentisera med en backend-tjänst med hjälp av en [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md).
-   [Cachelagringsprinciper](api-management-caching-policies.md#CachingPolicies)
    -   [Hämta från cache](api-management-caching-policies.md#GetFromCache) – utför cache leta upp och returnera ett giltigt cachelagrat svar när det är tillgängligt.
    -   [Lagra i cache](api-management-caching-policies.md#StoreToCache) – cachelagrar svar enligt den angivna konfigurationen för cache-kontroll.
    -   [Hämta värde från cache](api-management-caching-policies.md#GetFromCacheByKey) – hämta ett cachelagrat objekt efter nyckel.
    -   [Lagra värdet i cache](api-management-caching-policies.md#StoreToCacheByKey) – lagra ett objekt i cacheminnet efter nyckel.
    -   [Ta bort värdet från cache](api-management-caching-policies.md#RemoveCacheByKey) – ta bort ett objekt i cache by-nyckeln.
-   [Korsdomänprinciper](api-management-cross-domain-policies.md#CrossDomainPolicies)
    -   [Tillåt kors domän anrop](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – gör API: et tillgängligt från webbläsarbaserade Adobe Flash-och Microsoft Silverlight-baserade klienter.
    -   [CORS](api-management-cross-domain-policies.md#CORS) – lägger till CORS-stöd (Cross-Origin Resource Sharing) till en åtgärd eller ett API för att tillåta kors domän anrop från webbläsarbaserade klienter.
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) – lägger till JSON med JSONP-stöd (JSON with utfyllnad) till en åtgärd eller ett API för att tillåta kors domän anrop från Java Script browser-baserade klienter.
-   [Omvandlingsprinciper](api-management-transformation-policies.md#TransformationPolicies)
    -   [Konvertera JSON till XML](api-management-transformation-policies.md#ConvertJSONtoXML) – konverterar begäran eller svars text från JSON till XML.
    -   [Konvertera XML till JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – konverterar begäran eller svars text från XML till JSON.
    -   [Sök och ersätt sträng i brödtext](api-management-transformation-policies.md#Findandreplacestringinbody) – söker efter en begäran eller svars under sträng och ersätter den med en annan under sträng.
    -   [Maskera URL: er i](api-management-transformation-policies.md#MaskURLSContent) Länkar för innehålls skrivning (masker) i svars texten så att de pekar på motsvarande länk via gatewayen.
    -   [Ange server dels tjänst](api-management-transformation-policies.md#SetBackendService) – ändrar backend-tjänsten för en inkommande begäran.
    -   [Ange brödtext](api-management-transformation-policies.md#SetBody) – anger meddelande texten för inkommande och utgående begär Anden.
    -   [Ange HTTP-huvud](api-management-transformation-policies.md#SetHTTPheader) – tilldelar ett värde till ett befintligt svar och/eller begär ande huvud eller lägger till ett nytt svar och/eller begär ande huvud.
    -   [Ange frågesträngparametern-parameter](api-management-transformation-policies.md#SetQueryStringParameter) – lägger till, ersätter värdet eller tar bort förfrågan om frågesträngparametern.
    -   [Skriv om URL](api-management-transformation-policies.md#RewriteURL) – KONVERTERAR en URL för begäran från det offentliga formuläret till det formulär som förväntas av webb tjänsten.
    -   [TRANSFORMERA XML med hjälp av en XSLT](api-management-transformation-policies.md#XSLTransform) – använder en XSL-omvandling till XML i begäran eller svars texten.
- [Dapr-integrerings principer](api-management-dapr-policies.md)
    - [Skicka begäran till en tjänst](api-management-dapr-policies.md#invoke) – använder Dapr runtime för att hitta och tillförlitligt kommunicera med en Dapr-mikrotjänst.
    -  [Skicka meddelande till pub/sub-avsnittet](api-management-dapr-policies.md#pubsub) – använder Dapr runtime för att publicera ett meddelande till ett publicerings-/prenumerations ämne.
    -  [Utlös utmatnings bindning](api-management-dapr-policies.md#bind) – använder Dapr runtime för att anropa ett externt system via utgående bindning.
- [Validerings principer](validation-policies.md)
    - [Validera innehåll](validation-policies.md#validate-content) – verifierar storleken eller JSON-schemat för en begäran eller en svars text mot API-schemat.
. 
    - [Validate Parameters](validation-policies.md#validate-parameters) – verifierar parametrarna för begärans huvud, fråga eller sökväg mot API-schemat.
    - [Verifiera huvuden](validation-policies.md#validate-headers) – verifierar svars huvudena mot API-schemat.
    - [Validera status kod](validation-policies.md#validate-status-code) – validerar HTTP-statuskoden i svar mot API-schemat.

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med principer finns i:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principexempel](./policy-reference.md)
