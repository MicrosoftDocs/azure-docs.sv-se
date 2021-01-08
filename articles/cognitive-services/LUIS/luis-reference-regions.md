---
title: Publicerings regioner &-slutpunkter – LUIS
description: Den region som anges i Azure Portal är samma där du kommer att publicera LUIS-appen och en slut punkts-URL genereras för samma region.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: bb2aa2954ab4aed5466858226e385155b9da8ec4
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97977038"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Redigerings-och publicerings regioner och tillhör ande nycklar

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Tre redigerings regioner stöds av motsvarande LUIS-portaler. Om du vill publicera en LUIS-app till fler än en region behöver du minst en nyckel per region.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS redigerings regioner
Det finns tre LUIS redigerings portaler, baserat på region. Du måste skapa och publicera i samma region.

|LUIS|Redigerings region|Namn på Azure-region|
|--|--|--|
|[www.luis.ai][www.luis.ai] |USA<br>inte Europa<br>inte Australien| `westus`|
|[au.luis.ai][au.luis.ai] |Australien| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] |Europa|`westeurope`|

Redigerings regioner har [parat på växlings bara regioner](../../best-practices-availability-paired-regions.md).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publicera regioner och Azure-resurser
Appen publiceras till alla regioner som är associerade med de LUIS-resurser som lagts till i LUIS-portalen. För en app som skapas på [www.Luis.AI][www.luis.ai], om du till exempel skapar en Luis-eller kognitiv tjänst resurs i **väst** och [lägger till den i appen som en resurs](luis-how-to-azure-subscription.md), publiceras appen i den regionen.

## <a name="public-apps"></a>Offentliga appar
En offentlig app publiceras i alla regioner så att en användare med en regions-baserad LUIS resurs nyckel kan komma åt appen i den region som är kopplad till deras resurs nyckel.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publicerings regioner är knutna till redigerings regioner

Det går bara att publicera appen för redigerings region i en motsvarande publicerings region. Om din app för närvarande är i fel redigeringsregion exporterar du appen och importerar den till rätt redigeringsregion för din publiceringsregion.

LUIS-appar som skapas på https://www.luis.ai kan publiceras till alla slut punkter förutom de [Europeiska](#publishing-to-europe) och [australiska](#publishing-to-australia) regionerna.

## <a name="publishing-to-europe"></a>Publicera till Europa

För att publicera till de europeiska regionerna skapar du LUIS-appar https://eu.luis.ai endast på. Om du försöker publicera någon annan stans med en nyckel i regionen Europa, visar LUIS ett varnings meddelande. Använd i stället https://eu.luis.ai . LUIS-appar som skapats med [https://eu.luis.ai][eu.luis.ai] migreras inte automatiskt till andra regioner. Exportera och importera LUIS-appen för att migrera den.

## <a name="europe-publishing-regions"></a>Publicerings regioner för Europa

 Global region | Redigera API-region & redigerings webbplats| Publicerar & fråge område<br>`API region name`   |  URL-format för slut punkt   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Frankrike, centrala<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Norra Europa<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa, västra<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Storbritannien, södra<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publicera till Australien

För att publicera till australiensiska regioner skapar du LUIS-appar https://au.luis.ai endast på. Om du försöker publicera någon annan stans med hjälp av en nyckel i regionen australisk, visar LUIS ett varnings meddelande. Använd i stället https://au.luis.ai . LUIS-appar som skapats med [https://au.luis.ai][au.luis.ai] migreras inte automatiskt till andra regioner. Exportera och importera LUIS-appen för att migrera den.

## <a name="australia-publishing-regions"></a>Australien, publicerings regioner

 Global region | Redigera API-region & redigerings webbplats| Publicerar & fråge område<br>`API region name`   |  URL-format för slut punkt   |
|-----|------|------|------|
| [Australien](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australien, östra<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Publicera till andra regioner

Om du vill publicera till andra regioner skapar du LUIS-appar [https://www.luis.ai](https://www.luis.ai) endast på.

## <a name="other-publishing-regions"></a>Andra publicerings regioner

 Global region | Redigera API-region & redigerings webbplats| Publicerar & fråge område<br>`API region name`   |  URL-format för slut punkt   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Sydafrika, norra<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Indien, centrala<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Asien, östra<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Japan, östra<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Japan, västra<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Sydkorea, centrala<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Sydostasien<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asien | `westus`<br>[www.luis.ai][www.luis.ai]| Norra Förenade Arabemiraten<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Kanada, centrala<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | Central US<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | East US<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA, östra 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA, norra centrala<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA, södra centrala<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, västra centrala<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Nordamerika | `westus`<br>[www.luis.ai][www.luis.ai] | USA, västra<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Nordamerika |`westus`<br>[www.luis.ai][www.luis.ai] | USA, västra 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Sydamerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brasilien, södra<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Slutpunkter

Läs mer om hur du [skapar och förutsäger slut punkter](developer-reference-resource.md).

## <a name="failover-regions"></a>Redundansregioner

Varje region har en sekundär region att redundansväxla till. Europa växlar över i Europa och Australien växlar över inom Australien.

Redigerings regioner har [parat på växlings bara regioner](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för fördefinierade entiteter](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
