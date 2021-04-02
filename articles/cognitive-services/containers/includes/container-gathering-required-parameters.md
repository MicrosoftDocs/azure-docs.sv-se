---
title: Nödvändiga parametrar samlas in
services: cognitive-services
author: aahill
manager: nitinme
description: Parametrar för alla Cognitive Services behållare
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 85c67384c38376dc6f5701cf26e879d6e1b76c7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95996504"
---
## <a name="gathering-required-parameters"></a>Nödvändiga parametrar samlas in

Det finns tre primära parametrar för alla Cognitive Services behållare som krävs. Licens avtalet för slutanvändare (EULA) måste vara närvarande med värdet `accept` . Dessutom behövs både en slut punkts-URL och API-nyckel.

### <a name="endpoint-uri-endpoint_uri"></a>Slut punkts-URI `{ENDPOINT_URI}`

URI-värdet för **slut punkten** är tillgängligt på sidan Azure Portal *Översikt* för motsvarande kognitiva tjänst resurs. Gå till sidan *Översikt* , Hovra över slut punkten och en `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikon visas. Kopiera och använd vid behov.

![Samla in slut punkts-URI för senare användning](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Nyckel `{API_KEY}`

Den här nyckeln används för att starta behållaren och är tillgänglig på sidan Azure Portals nycklar för motsvarande kognitiva tjänst resurser. Gå till sidan *nycklar* och klicka på `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> ikonen.

![Hämta en av de två nycklarna för senare användning](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Dessa prenumerations nycklar används för att få åtkomst till ditt kognitiva tjänst-API. Dela inte dina nycklar. Lagra dem på ett säkert sätt, till exempel med hjälp av Azure Key Vault. Vi rekommenderar också att du återskapar nycklarna regelbundet. Endast en nyckel krävs för att göra ett API-anrop. När du återskapar den första nyckeln kan du använda den andra nyckeln för fortsatt åtkomst till tjänsten.