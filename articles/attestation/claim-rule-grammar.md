---
title: Grammatikkontroll av Azure-attestering för anspråks regel
description: Information om anspråk och anspråks regler för Azure attestering policy.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91285426"
---
# <a name="claim-and-claim-rules"></a>Anspråk och anspråks regler

För att förstå grammatiken i anspråks regler måste du först förstå anspråk i attesterings policyn.

## <a name="claim"></a>Begär

Ett anspråk är en uppsättning egenskaper grupperade tillsammans för att tillhandahålla relevant information. För Azure-attestering innehåller ett anspråk följande egenskaper:

- **typ**: ett sträng värde som representerar typen av anspråk.
- **värde**: ett booleskt värde, heltal eller sträng värde som representerar anspråkets värde.
- **valueType**: data typen för den information som lagras i egenskapen Value. Typer som stöds är sträng, heltal och boolesk. Om detta inte anges blir standardvärdet "String".
- **utfärdare**: information om utfärdaren av anspråket. Utfärdaren är en av följande typer:
  - **AttestationService**: vissa anspråk görs tillgängliga för princip författaren av Azure-attestering, som kan användas av den som skapar policyn för attestering.
  - **AttestationPolicy**: principen (som definieras av administratören) kan lägga till anspråk i inkommande bevis under bearbetningen. Utfärdaren i det här fallet anges till "AttestationPolicy".
  - **CustomClaim**: attesteringen (klienten) kan också lägga till ytterligare anspråk till deklarations beviset. Utfärdaren i det här fallet anges till "CustomClaim".

Om det inte har definierats. Standardvärdet är "CustomClaim".

## <a name="claim-rule"></a>Anspråks regel

Den inkommande anspråks uppsättningen används av princip motorn för att beräkna attesterings resultatet. En anspråks regel är en uppsättning villkor som används för att validera inkommande anspråk och vidta den definierade åtgärden.

```
Conditions list => Action (Claim)
```

Utvärderings versionen av Azure attestering för en anspråks regel omfattar följande steg:

- Om villkors listan inte finns kör du åtgärden med angivet anspråk 
- Annars kan du utvärdera villkoren från listan villkor.
- Om listan villkor utvärderas till falskt, stoppa. Annars fortsätter du.

Villkoren i en anspråks regel används för att avgöra om åtgärden måste utföras. Villkors listan är en följd av villkor som skiljs åt av operatorn "&&".

Villkors listan är strukturerad som:

```
Condition && Condition && ...
```

Villkoret är strukturerat som:

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

Villkors listan består av enskilda villkor för olika egenskaper för ett anspråk. Ett villkor kan ha en valfri identifierare som kan användas för att referera till de anspråk/s som uppfyller villkoret. Den här referensen kan användas i andra villkor eller med åtgärden för samma regel.

Till exempel

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

Följande operatorer kan användas för att kontrol lera villkor:

| ValueType | Åtgärder som stöds |
|--|--|
| Integer | = = (lika med), \! = (inte lika med), <= (mindre än eller lika med), < (mindre än), >= (större än eller lika med), > (större än) |
| Sträng | = = (lika med), \! = (inte lika med) |
| Boolesk | = = (lika med), \! = (inte lika med) |

Utvärdering av villkors lista:

- Förekomsten av operatorn && innebär att en villkors lista utvärderas som sant endast om alla villkor från listan utvärderas till sant.
- Ett villkor representerar filtrerings villkor för anspråks uppsättningen. Villkoret är att utvärdera till sant om det finns minst ett anspråk som uppfyller villkoret.
- Ett anspråk har sagts för att uppfylla det filtrerings villkor som representeras av villkoret om var och en av dess egenskaper uppfyller motsvarande villkor för anspråks egenskaper som finns i villkoret.  

En uppsättning åtgärder som tillåts i en princip beskrivs nedan.

| Åtgärds-verb | Beskrivning | Princip avsnitt som dessa gäller |
|--|--|--|
| Permit () | Den inkommande anspråks uppsättningen kan användas för att beräkna **issuancerules**. Tar inga anspråk som parameter | **authorizationrules** |
| neka () | Den inkommande anspråks uppsättningen ska inte användas för att beräkna **issuancerules** ta inga anspråk som parameter | **authorizationrules** |
| Lägg till (anspråk) | Lägger till anspråk i den inkommande anspråks uppsättningen. Alla anspråk som läggs till i den inkommande anspråks uppsättningen blir tillgängliga för de efterföljande anspråks reglerna. |**authorizationrules**, **issuancerules** |
| problem (anspråk) | Lägger till anspråket i inkommande och utgående anspråks uppsättning | **issuancerules** |
| issueproperty (anspråk) | Lägger till anspråk till anspråks uppsättningen inkommande och egendom | **issuancerules**

## <a name="next-steps"></a>Nästa steg

- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Konfigurera Azure-attestering med PowerShell](quickstart-powershell.md)

