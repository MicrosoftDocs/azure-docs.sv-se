---
title: Vanliga parametrar och sidhuvuden
description: De parametrar och huvuden som är gemensamma för alla åtgärder som du kan göra relaterade till Key Vault resurser.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 616b6061b08258d465b09902556de6903b873199
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749878"
---
# <a name="common-parameters-and-headers"></a>Vanliga parametrar och sidhuvuden

Följande information är gemensam för alla åtgärder som du kan göra relaterade till Key Vault resurser:

- `Host`HTTP-huvudet måste alltid finnas och måste ange värdnamnet för valvet. Exempel: `Host: contoso.vault.azure.net`. Observera att de flesta klienttekniker fyller i `Host` -huvudet från URI:en. till exempel `GET https://contoso.vault.azure.net/secrets/mysecret{...}` anger som `Host` `contoso.vault.azure.net` . Det innebär att om du använder Key Vault rå IP-adress som , kommer det automatiska värdet för rubriken att vara fel och du måste manuellt försäkra dig om att huvudet innehåller `GET https://10.0.0.23/secrets/mysecret{...}` `Host` `Host` valvets värdnamn.
- Ersätt `{api-version}` med API-versionen i URI:n.
- Ersätt `{subscription-id}` med din prenumerations-ID i URI:en
- Ersätt `{resource-group-name}` med resursgruppen. Mer information finns i Hantera Azure-resurser med hjälp av resursgrupper.
- Ersätt `{vault-name}` med ditt nyckelvalvsnamn i URI:en.
- Ange Content-Type-rubriken till application/json.
- Ange auktoriseringsrubriken till en JSON Web Token som du får från Azure Active Directory (AAD). Mer information finns i [Autentisera Azure Resource Manager](authentication-requests-and-responses.md) begäranden.

## <a name="common-error-response"></a>Vanligt felsvar
Tjänsten använder HTTP-statuskoder för att ange att det lyckades eller misslyckades. Dessutom innehåller fel ett svar i följande format:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Elementnamn | Typ | Description |
|---|---|---|
| kod | sträng | Typ av fel som inträffade.|
| meddelande | sträng | En beskrivning av vad som orsakade felet. |



## <a name="see-also"></a>Se även
 [Azure Key Vault REST API referens](/rest/api/keyvault/)
