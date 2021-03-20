---
title: Vanliga parametrar och sidhuvuden
description: De parametrar och sidhuvud som är gemensamma för alla åtgärder som du kan göra relaterade till Key Vault resurser.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83005819"
---
# <a name="common-parameters-and-headers"></a>Vanliga parametrar och sidhuvuden

Följande information är gemensam för alla åtgärder som du kan göra relaterade till Key Vault resurser:

- HTTP- `Host` huvudet måste alltid finnas och måste ange valvets värdnamn. Exempel: `Host: contoso.vault.azure.net`. Observera att de flesta klient tekniker fyller i `Host` sidhuvudet från URI: n. Till exempel `GET https://contoso.vault.azure.net/secrets/mysecret{...}` ställer in `Host` as `contoso.vault.azure.net` . Det innebär att om du kommer åt Key Vault att använda RAW IP-adress som är `GET https://10.0.0.23/secrets/mysecret{...}` det automatiska värdet för `Host` rubriken fel och du måste manuellt se till att `Host` sidhuvudet innehåller valv-värdnamnet.
- Ersätt `{api-version}` med API-versionen i URI: n.
- Ersätt `{subscription-id}` med prenumerations-ID: t i URI: n
- Ersätt `{resource-group-name}` med resurs gruppen. Mer information finns i använda resurs grupper för att hantera dina Azure-resurser.
- Ersätt `{vault-name}` med nyckel valvets namn i URI: n.
- Ange rubrik för innehålls typ till Application/JSON.
- Ange Authorization-huvudet till en JSON Web Token som du får från Azure Active Directory (AAD). Mer information finns i [autentisera Azure Resource Manager](authentication-requests-and-responses.md) begär Anden.

## <a name="common-error-response"></a>Vanliga fel svar
Tjänsten kommer att använda HTTP-statuskod för att indikera att det lyckades eller misslyckades. Dessutom innehåller felen ett svar i följande format:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Elementnamn | Typ | Beskrivning |
|---|---|---|
| kod | sträng | Den typ av fel som inträffat.|
| meddelande | sträng | En beskrivning av vad som orsakat felet. |



## <a name="see-also"></a>Se även
 [Azure Key Vault REST API referens](/rest/api/keyvault/)
