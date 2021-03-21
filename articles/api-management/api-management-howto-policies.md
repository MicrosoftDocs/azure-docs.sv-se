---
title: Principer i Azure API Management | Microsoft Docs
description: Lär dig hur du skapar, redigerar och konfigurerar principer i API Management. Se kod exempel och Visa fler tillgängliga resurser.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 37ac6369790ed526fd923819558863ae84432aed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94358124"
---
# <a name="policies-in-azure-api-management"></a>Principer i Azure API Management

I Azure API Management (APIM) är principer en kraftfull funktion i systemet som gör att utgivaren kan ändra beteendet för API: et genom konfigurationen. Principer är en samling instruktioner som körs sekventiellt i begäran eller svar på ett API. Vanliga instruktioner är format konvertering från XML till JSON och begränsning av anrops frekvensen för att begränsa antalet inkommande anrop från en utvecklare. Många fler principer är tillgängliga i rutan.

Principer tillämpas i gatewayen som finns mellan API-konsumenten och det hanterade API: t. Gatewayen tar emot alla begär Anden och vidarebefordrar vanligt vis dem oförändrade till det underliggande API: t. En princip kan dock tillämpa ändringar i både inkommande begäran och utgående svar.

Principuttryck kan användas som attributvärden eller textvärden i API Management-principer, under förutsättning att principen tillåter det. Vissa principer som [Kontrollflöde][Control flow] och [Ange variabel][Set variable] baseras på principuttryck. Mer information finns i [Avancerade principer][Advanced policies] och [Principuttryck][Policy expressions].

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Förstå princip konfiguration

Princip definitionen är ett enkelt XML-dokument som beskriver en sekvens med inkommande och utgående instruktioner. XML-filen kan redige ras direkt i definitions fönstret. En lista med instruktioner ges till höger och de instruktioner som gäller för det aktuella omfånget är aktiverade och markerade.

Om du klickar på en aktive rad instruktion läggs lämplig XML till i positionen för markören i definitions vyn. 

> [!NOTE]
> Om den princip som du vill lägga till inte är aktive rad ska du kontrol lera att du har rätt omfattning för principen. Varje princip sats är utformad för användning i vissa områden och princip avsnitt. Om du vill granska princip avsnitten och omfattningarna för en princip, kontrollerar du **användnings** avsnittet för principen i [princip referensen][Policy Reference].
> 
> 

Konfigurationen är indelad i `inbound` ,, `backend` `outbound` och `on-error` . Serien med angivna princip satser körs i ordning för en begäran och ett svar.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Om det uppstår ett fel under bearbetningen av en begäran, hoppas eventuella återstående steg i-,-och- `inbound` `backend` `outbound` avsnitten över och körnings hopp till instruktionerna i `on-error` avsnittet. Genom att placera princip instruktioner i `on-error` avsnittet kan du granska felet genom att använda `context.LastError` -egenskapen, inspektera och anpassa fel svaret med hjälp av `set-body` principen och konfigurera vad som händer om ett fel inträffar. Det finns felkoder för inbyggda steg och för fel som kan uppstå under bearbetningen av princip satser. Mer information finns [i fel hantering i API Management principer](./api-management-error-handling-policies.md).

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Så här konfigurerar du principer

Information om hur du konfigurerar principer finns i [Ange eller redigera principer](set-edit-policies.md).

## <a name="policy-reference"></a>Princip referens

Se [princip referensen](./api-management-policies.md) för en fullständig lista över princip satser och deras inställningar.

## <a name="policy-samples"></a>Principexempel

Se [princip exempel](./policy-reference.md) för fler kod exempel.

## <a name="examples"></a>Exempel

### <a name="apply-policies-specified-at-different-scopes"></a>Använd principer som anges i olika omfång

Om du har en princip på global nivå och en princip som kon figurer ATS för ett API, används båda principerna när det aktuella API: t används. API Management tillåter deterministisk sortering av kombinerade princip satser via bas elementet. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

I exempel princip definitionen ovan `cross-domain` skulle instruktionen köras före eventuella högre principer som i sin tur skulle följas av `find-and-replace` principen. 

### <a name="restrict-incoming-requests"></a>Begränsa inkommande begär Anden

Om du vill lägga till en ny instruktion för att begränsa inkommande begär anden till angivna IP-adresser placerar du markören precis i innehållet i `inbound` XML-elementet och klickar på instruktionen **begränsa anropare IP** -adress.

![Principer för begränsning][policies-restrict]

Detta lägger till ett XML-kodfragment till `inbound` elementet som ger vägledning om hur du konfigurerar instruktionen.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Om du vill begränsa inkommande begär Anden och bara acceptera de från IP-adressen 1.2.3.4 ändrar du XML på följande sätt:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du arbetar med principer finns i:

+ [Transformera API: er](transform-api.md)
+ [Princip referens](./api-management-policies.md) för en fullständig lista över princip satser och deras inställningar
+ [Principexempel](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
