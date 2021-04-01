---
title: Publicera ett erbjudande – Azure Marketplace
description: API för att publicera det angivna erbjudandet.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 60e75aff79913896bdf1dcdc8754b6ecf5620b06
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87272053"
---
# <a name="publish-an-offer"></a>Publicera ett erbjudande

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. CPP-API: er bör endast användas för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Startar publicerings processen för det angivna erbjudandet. Det här anropet är en tids krävande åtgärd.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |    **Beskrivning**                               |  **Datatyp** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Utgivar identifierare, till exempel `contoso`      |   Sträng       |
|  offerId       | Erbjudande-ID                                 |   Sträng       |
|  api-version   | Senaste versionen av API: et                        |   Datum         |
|  |  |

## <a name="header"></a>Huvud
------

|  **Namn**        |    **Värde**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Auktorisering   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Body-exempel
------------

### <a name="request"></a>Förfrågan

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Egenskaper för begär ande brödtext

|  **Namn**               |   **Beskrivning**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  meddelande – e-post    | Kommaavgränsad lista med e-postadresser som ska meddelas om förloppet för publicerings åtgärden. |
|  |  |

### <a name="response"></a>Svarsåtgärder

#### <a name="migrated-offers"></a>Migrerade erbjudanden

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ej migrerade erbjudanden

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Svarshuvud

|  **Namn**             |    **Värde**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Location    | Den relativa sökvägen för att hämta den här åtgärdens status     |
|  |  |

### <a name="response-status-codes"></a>Svars status koder

| **Kod** |  **Beskrivning**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` -Begäran har godkänts. Svaret innehåller en plats som kan användas för att spåra den åtgärd som startas. |
| 400   | `Bad/Malformed request` – Fel svars texten kan innehålla mer information.                                                               |
| 422   | `Un-processable entity` -Anger att entiteten som ska publiceras misslyckades valideringen.                                                        |
| 404   | `Not found` -Den entitet som anges av klienten finns inte.                                                                              |
|  |  |
