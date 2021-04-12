---
title: Azure Event Grid – fel söknings guide
description: Den här artikeln innehåller en lista över felkoder, fel meddelanden, beskrivningar och rekommenderade åtgärder.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 79533918ccc6995f459b39f058de9e01091c0958
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94592999"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Felsöka Azure Event Grid fel
Den här fel söknings guiden innehåller följande information: 

- Azure Event Grid felkoder
- Felmeddelanden
- Beskrivningar av felen
- Rekommenderade åtgärder som du bör vidta när du får dessa fel. 

## <a name="error-code-400"></a>Felkod: 400
| Felkod | Felmeddelande | Beskrivning | Rekommendation |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. BadRequest<br/>400 | Ämnes namnet måste vara mellan 3 och 50 tecken långt. | Namnet på den anpassade ämnes namnet ska vara mellan 3 och 50 tecken långt. Endast alfanumeriska bokstäver, siffror och tecknen '-' är tillåtna i ämnes namnet. Namnet får inte heller börja med följande reserverade ord: <ul><li>Utforskaren</li><li>EventGrid</li><li>Säker</li></ul> | Välj ett annat ämnes namn som följer namn kraven för ämnet. |
| HttpStatusCode. BadRequest<br/>400 | Domän namnet måste vara mellan 3 och 50 tecken långt. | Domän namnets längd måste vara mellan 3 och 50 tecken långt. Endast alfanumeriska bokstäver, siffror och tecknen '-' är tillåtna i domän namnet. Namnet får inte heller börja med följande reserverade ord:<ul><li>Utforskaren</li><li>EventGrid</li><li>Säker</li> | Välj ett annat domän namn som följer domän namns kraven. |
| HttpStatusCode. BadRequest<br/>400 | Ogiltig förfallo tid. | Förfallo tiden för händelse prenumerationen avgör när händelse prenumerationen ska tas ur bruk. Värdet måste vara ett giltigt DateTime-värde i framtiden.| Se till att förfallo tiden för händelse prenumerationen är i ett giltigt DateTime-format och att den har angetts vara i framtiden. |

## <a name="error-code-409"></a>Felkod: 409
| Felkod | Felmeddelande | Beskrivning | Rekommenderad åtgärd |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. konflikt <br/>409 | Det finns redan ett ämne med det angivna namnet. Välj ett annat ämnes namn.   | Namnet på den anpassade artikeln bör vara unikt i en enda Azure-region för att säkerställa en korrekt publicerings åtgärd. Samma namn kan användas i olika Azure-regioner. | Välj ett annat namn för ämnet. |
| HttpStatusCode. konflikt <br/> 409 | Den angivna domänen finns redan. Välj ett annat domän namn. | Domän namnet bör vara unikt i en enda Azure-region för att säkerställa en korrekt publicerings åtgärd. Samma namn kan användas i olika Azure-regioner. | Välj ett annat namn för domänen. |
| HttpStatusCode. konflikt<br/>409 | Kvot gränsen har uppnåtts. Mer information om dessa gränser finns [Azure Event Grid gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Varje Azure-prenumeration har en gräns för antalet Azure Event Grid-resurser som den kan använda. En eller flera av kvoten har överskridits och inga fler resurser kunde skapas. |    Kontrol lera den aktuella resurs användningen och ta bort alla som inte behövs. Om du fortfarande behöver öka kvoten skickar du ett e-postmeddelande till [aeg@microsoft.com](mailto:aeg@microsoft.com) med det exakta antalet resurser som behövs. |

## <a name="error-code-403"></a>Felkod: 403

| Felkod | Felmeddelande | Beskrivning | Rekommenderad åtgärd |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode. förbjuden <br/>403 | Publicering till {Topic/Domain} av klienten {IpAddress} avvisades på grund av IP-filtrerings regler. | Avsnittet eller domänen har konfigurerade regler för IP-brandvägg och åtkomst begränsas endast till konfigurerade IP-adresser. | Lägg till IP-adressen i IP-brandväggens regler, se [Konfigurera IP-brandvägg](configure-firewall.md) |
| HttpStatusCode. förbjuden <br/> 403 | Publicering till {Topic/Domain} av klienten avvisas eftersom begäran kom från privat slut punkt och ingen matchande privat slut punkts anslutning hittades för resursen. | Ämnet eller domänen har privata slut punkter och publicerings förfrågan kom från en privat slut punkt som inte har kon figurer ATS eller godkänts. | Konfigurera en privat slut punkt för ämnet/domänen. [Konfigurera privata slutpunkter](configure-private-endpoints.md) |

Kontrol lera också om webhooken ligger bakom en Azure Application Gateway eller brand vägg för webbaserade program. Om så är fallet inaktiverar du följande brand Väggs regler och gör ett HTTP-inlägg igen:

- 920300 (begäran saknas i ett accept-huvud)
- 942430 (begränsad SQL Character-avvikelse identifiering (argument): # av specialtecken har överskridits (12))
- 920230 (flera URL-kodning identifierade)
- 942130 (SQL-injektering: SQL-Tautology har identifierats.)
- 931130 (möjliga RFI-angrepp (Remote File inkludering) = av-domän referens/länk)



## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du publicera ditt problem i [Stack Overflow-forumet](https://stackoverflow.com/questions/tagged/azure-eventgrid) eller öppna ett [support ärende](https://azure.microsoft.com/support/options/). 
