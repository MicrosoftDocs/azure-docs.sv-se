---
title: Hämta erbjuder API – Azure Marketplace
description: API för att hämta en sammanfattande lista med erbjudanden under ett utgivar namn område.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 39d07751c708d5555799ecbb3b3bc66d3f44f43a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87271968"
---
# <a name="retrieve-offers"></a>Hämta erbjudanden

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. CPP-API: er bör endast användas för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Hämtar en summerad lista med erbjudanden under ett utgivar namn område.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-parametrar

| **Namn**         |  **Beskrivning**                         |  **Datatyp** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Utgivar identifierare, till exempel `contoso` |   Sträng    |
|  api-version     | Senaste versionen av API                    |    Datum        |
|  |  |

## <a name="header"></a>Huvud

|  **Namn**        |         **Värde**       |
|  --------------- |       ----------------  |
|  Content-Type    | `application/json`      |
|  Auktorisering   | `Bearer YOUR_TOKEN`     |
|  |  |

## <a name="body-example"></a>Body-exempel

### <a name="response"></a>Svarsåtgärder

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Egenskaper för svars text

|  **Namn**       |       **Beskrivning**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifierar typen av erbjudande                                                                                           |
|  publisherId    | Identifierare som unikt identifierar utgivaren                                                                      |
|  status         | Status för erbjudandet. En lista över möjliga värden finns i [erbjudande status](#offer-status) nedan.                         |
|  id             | GUID som unikt identifierar erbjudandet i utgivarens namnrymd.                                                    |
|  version        | Aktuell version av erbjudandet. Det går inte att ändra versions egenskapen av klienten. Den ökar efter varje publicering. |
|  definition     | Innehåller en summerad vy av den faktiska definitionen av arbets belastningen. Om du vill ha en detaljerad definition använder du API: et [Hämta särskilda erbjudande](./cloud-partner-portal-api-retrieve-specific-offer.md) . |
|  changedTime    | UTC-tid när erbjudandet senast ändrades                                                                              |
|  |  |

### <a name="response-status-codes"></a>Svars status koder

| **Kod**  |  **Beskrivning**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -Begäran har bearbetats och alla erbjudanden under utgivaren returnerades till klienten.  |
|  400      | `Bad/Malformed request` – Fel svars texten kan innehålla mer information.                                    |
|  403      | `Forbidden` -Klienten har inte åtkomst till den angivna namn rymden.                                          |
|  404      | `Not found` -Den angivna entiteten finns inte.                                                                 |
|  |  |

### <a name="offer-status"></a>Erbjudande status

|  **Namn**                    | **Beskrivning**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | Erbjudandet har aldrig publicerats.                  |
|  NotStarted                  | Erbjudandet är nytt men har inte startats.                 |
|  WaitingForPublisherReview   | Erbjudandet väntar på godkännande av utgivare.         |
|  Körs                     | Överföring av erbjudande bearbetas.             |
|  Lyckades                   | Bearbetningen av erbjudandet har slutförts.       |
|  Avbrutna                    | Överföring av erbjudande avbröts.                   |
|  Misslyckad                      | Det gick inte att skicka erbjudandet.                         |
|  |  |
