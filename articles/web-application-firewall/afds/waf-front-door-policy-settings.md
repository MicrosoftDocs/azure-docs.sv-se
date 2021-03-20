---
title: Princip inställningar för brand vägg för webbaserade program med Azures front dörr
description: Lär dig mer om brand vägg för webbaserade program (WAF).
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 08/21/2019
ms.author: victorh
ms.openlocfilehash: 08b21ccd7f7958f00546583f680ecb8cde4a20c8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75932606"
---
# <a name="policy-settings-for-web-application-firewall-on-azure-front-door"></a>Princip inställningar för brand vägg för webbaserade program på Azures front dörr

Med en princip för brand vägg för webbaserade program (WAF) kan du styra åtkomsten till dina webb program med en uppsättning anpassade och hanterade regler. WAF-Principens namn måste vara unikt. Du kommer att få ett verifierings fel om du försöker använda ett befintligt namn. Det finns flera princip nivå inställningar som gäller för alla regler som anges för principen enligt beskrivningen i den här artikeln.

## <a name="waf-state"></a>WAF-tillstånd

En WAF-princip för front dörren kan vara i något av följande två tillstånd:
- **Aktive rad:** När en princip är aktive rad kontrollerar WAF aktivt inkommande begär Anden och vidtar motsvarande åtgärder enligt regel definitioner
- **Inaktive rad:** – när en princip är inaktive rad pausas WAF-kontrollen. Inkommande begär Anden kringgår WAF och skickas till backend-platser baserat på routning av dirigerad dörr.

## <a name="waf-mode"></a>WAF-läge

WAF-principen kan konfigureras att köras i följande två lägen:

- **Identifierings läge** Vid körning i identifierings läge vidtar WAF inga åtgärder förutom övervaka och logga begäran och dess matchade WAF-regel till WAF-loggar. Aktivera Logging Diagnostics för front dörr (när du använder portalen kan du göra det genom att gå till avsnittet **diagnostik** i Azure Portal).

- **Skydds läge** När WAF har kon figurer ATS för att köras i förebyggande läge, tar den angivna åtgärden om en begäran matchar en regel. Alla matchade begär Anden loggas också i WAF-loggarna.

## <a name="waf-response-for-blocked-requests"></a>WAF-svar för blockerade begär Anden

Som standard när WAF blockerar en begäran på grund av en matchad regel returnerar den en 403-status kod med- **begäran är blockerad** . En referens sträng returneras också för loggning.

Du kan definiera en anpassad svars status kod och ett svars meddelande när en begäran blockeras av WAF. Följande anpassade status koder stöds:

- 200 OK
- 403 förbud
- metoden 405 tillåts inte
- 406 är inte acceptabelt
- 429 för många begär Anden

Anpassad svars status kod och svars meddelande är en princip nivå inställning. När den har kon figurer ATS får alla blockerade begär Anden samma anpassade svars status och svars meddelande.

## <a name="uri-for-redirect-action"></a>URI för omdirigerings åtgärd

Du måste definiera en URI för att omdirigera begär anden till om åtgärden **omdirigera** har valts för någon av de regler som finns i en WAF-princip. Den här omdirigerings-URI: n måste vara en giltig HTTP (S)-plats och när den har kon figurer ATS omdirigeras alla regler för begär Ande som matchar en åtgärd för att OMDIRIGERA till den angivna platsen.


## <a name="next-steps"></a>Nästa steg
- Lär dig hur du definierar [anpassade svar](waf-front-door-configure-custom-response-code.md) för WAF
