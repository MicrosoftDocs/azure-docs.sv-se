---
title: Avancerad begränsning av förfrågningar med Azure API Management
description: Lär dig hur du skapar och tillämpar flexibla kvot-och hastighets begränsnings principer med Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 3f91ed2238a7a23b967cd3e3556c8ade88af0044
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449740"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Avancerad begränsning av förfrågningar med Azure API Management
Att kunna begränsa inkommande begär Anden är en viktig roll i Azure API Management. Antingen genom att kontrol lera antalet begär Anden eller de totala begär Anden/data som överförs, tillåter API Management att API-providers skyddar sina API: er från missbruk och skapar värden för olika API-produktsortiment.

## <a name="rate-limits-and-quotas"></a>Hastighets begränsningar och kvoter
Hastighets begränsningar och kvoter används för olika syfte.

### <a name="rate-limits"></a>Hastighetsbegränsningar
Hastighets begränsningar används vanligt vis för att skydda mot korta och intensiva volyms burst. Om du till exempel vet att Server dels tjänsten har en Flask hals i databasen med en hög anrops volym kan du ange en `rate-limit-by-key` princip för att inte tillåta hög anrops volym genom att använda den här inställningen.

### <a name="quotas"></a>Kvoter
Kvoter används vanligt vis för att kontrol lera samtals priser under en längre tids period. De kan till exempel ange det totala antalet samtal som en viss prenumerant kan göra inom en given månad. För att kunna använda ditt API kan kvoter också ställas in på olika sätt för nivåbaserade prenumerationer. Till exempel kan en enkel nivå prenumeration inte längre göra mer än 10 000 samtal per månad, men en Premium nivå kan gå upp till 100 000 000 samtal varje månad.

I Azure API Management sprids hastighets gränserna ofta snabbare över noderna för att skydda mot toppar. Information om användnings kvoten används däremot över en längre period och därför är dess implementering annorlunda.

> [!CAUTION]
> På grund av den distribuerade typen av begränsnings arkitektur är hastighets begränsningen aldrig helt korrekt. Skillnaden mellan det konfigurerade och det verkliga antalet tillåtna begär Anden varierar beroende på volym och hastighet för begäran, Server svars tid och andra faktorer.

## <a name="product-based-throttling"></a>Produkt-baserad begränsning
Hastighets begränsnings funktioner som är begränsade till en viss prenumeration är användbara för API-providern för att tillämpa gränser på de utvecklare som har registrerat sig för att använda API: et. Den kan dock inte hjälpa till exempel att begränsa enskilda slutanvändare av API: et. Det är möjligt för en användare av utvecklarens program att använda hela kvoten och sedan förhindra att andras kunder av utvecklaren kan använda programmet. Dessutom kan flera kunder som kan generera en stor mängd begär Anden begränsa åtkomsten till tillfälliga användare.

## <a name="custom-key-based-throttling"></a>Anpassad nyckelbaserad begränsning

> [!NOTE]
> - `rate-limit-by-key` Och- `quota-by-key` principerna är inte tillgängliga när du arbetar på användnings nivån i Azure API Management. 

Principerna [-gräns-till-nyckel](./api-management-access-restriction-policies.md#LimitCallRateByKey) -och [kvot-för-nyckel-](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) principer ger en mer flexibel lösning för trafik kontroll. Med dessa principer kan du definiera uttryck för att identifiera de nycklar som används för att spåra användning av trafiken. Sättet det här fungerar är enkelt att illustrera med ett exempel. 

## <a name="ip-address-throttling"></a>IP-adress begränsning
Följande principer begränsar en enskild klients IP-adress till endast 10 anrop varje minut, med totalt 1 000 000 anrop och 10 000 KB bandbredd per månad. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Om alla klienter på Internet använder en unik IP-adress kan det vara ett effektivt sätt att begränsa användningen av användare. Det är dock troligt att flera användare delar en enda offentlig IP-adress på grund av att de ansluter till Internet via en NAT-enhet. Trots detta kan det bästa alternativet för API: er som tillåter oautentiserad åtkomst `IpAddress` .

## <a name="user-identity-throttling"></a>Begränsning av användar identitet
Om en slutanvändare autentiseras kan en begränsnings nyckel genereras baserat på information som unikt identifierar användaren.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Det här exemplet visar hur du extraherar Authorization-huvudet, konverterar det till- `JWT` objekt och använder ämnet för token för att identifiera användaren och använda den som begränsnings nyckel. Om användar identiteten lagras i `JWT` som en av de andra anspråken, kan det värdet användas i sitt ställe.

## <a name="combined-policies"></a>Kombinerade principer
Även om de användarspecifika begränsnings principerna ger mer kontroll än de prenumerations begränsnings principerna, finns det fortfarande värde som kombinerar båda funktionerna. Begränsning av produkt prenumerations nyckel ([begränsa anrops frekvensen per](./api-management-access-restriction-policies.md#LimitCallRate) prenumeration och [Ange användnings kvoten för prenumerationen](./api-management-access-restriction-policies.md#SetUsageQuota)) är ett bra sätt att aktivera monetarisering av ett API genom att debiteras baserat på användnings nivåer. Den bättre korniga kontrollen av att kunna begränsas av användaren är kompletterande och förhindrar en användares beteende från att försämra upplevelsen av en annan. 

## <a name="client-driven-throttling"></a>Klient driven begränsning
När begränsnings nyckeln definieras med ett [princip uttryck](./api-management-policy-expressions.md)är det den API-provider som väljer hur begränsningen är begränsad. En utvecklare kan dock vilja styra hur de begränsar sina egna kunder. Detta kan aktive ras av API-providern genom att introducera en anpassad rubrik så att utvecklarens klient program kan kommunicera nyckeln till API: et.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Detta gör det möjligt för utvecklarens klient program att välja hur du vill skapa en hastighets begränsnings nyckel. Klient utvecklarna kan skapa sina egna pris nivåer genom att allokera uppsättningar nycklar till användare och rotera nyckel användningen.

## <a name="summary"></a>Sammanfattning
Azure API Management tillhandahåller hastighets-och kvot begränsningar både för att skydda och lägga till värden i din API-tjänst. Med de nya begränsnings principerna med anpassade omfångs regler kan du få bättre kontroll över principerna så att kunderna kan skapa ännu bättre program. I exemplen i den här artikeln demonstreras användningen av dessa nya principer med hjälp av tillverknings frekvens begränsar nycklar med klientens IP-adresser, användar identitet och klienter som genererar värden. Det finns dock många andra delar av meddelandet som kan användas, till exempel användar agent, fragment för URL-sökvägar, meddelande storlek.

## <a name="next-steps"></a>Nästa steg
Ge oss din feedback som ett GitHub-problem för det här ämnet. Det skulle vara bra att höra om andra potentiella nyckel värden som har varit ett logiskt val i dina scenarier.
