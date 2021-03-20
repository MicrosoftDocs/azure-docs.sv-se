---
title: Hämta erbjudande status – Azure Marketplace
description: API för att hämta erbjudandets aktuella status.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 336f23f83c33bcee1887d0e41710e686b794a663
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87272019"
---
# <a name="retrieve-offer-status"></a>Hämta erbjudandestatus

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. CPP-API: er bör endast användas för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Hämtar aktuell status för erbjudandet.

  `GET  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/status?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI-parametrar

|  **Namn**       |   **Beskrivning**                            |  **Datatyp** |
|  -------------  |  ------------------------------------------  |  ------------  |
|  publisherId    | Utgivar identifierare, till exempel `Contoso`  |     Sträng     |
|  offerId        | GUID som unikt identifierar erbjudandet      |     Sträng     |
|  api-version    | Senaste versionen av API                        |     Datum       |
|  |  |

## <a name="header"></a>Huvud


|  Name           |  Värde               |
|  -------------  | -------------------  |
|  Content-Type   |  `application/json`  |
|  Auktorisering  | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Body-exempel

### <a name="response"></a>Svarsåtgärder

``` json
  {
      "status": "succeeded",
      "messages": [],
      "steps": [
      {
          "estimatedTimeFrame": "< 15 min",
          "id": "displaydummycertify",
          "stepName": "Validate Pre-Requisites",
          "description": "Offer settings provided are validated.",
          "status": "complete",
          "messages": [
              {
                  "messageHtml": "Step completed.",
                  "level": "information",
                  "timestamp": "2018-03-16T17:50:45.7215661Z"
              }
          ],       
          "progressPercentage": 100
      },
      {
          "estimatedTimeFrame": "~2-3 days",
          "id": "displaycertify",
          "stepName": "Certification",
          "description": "Your offer is analyzed by our certification systems for issues.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 day",
          "id": "displayprovision",
          "stepName": "Provisioning",
          "description": "Your virtual machine is being replicated in our production systems.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "displaypackage",
          "stepName": "Packaging and Lead Generation Registration",
          "description": "Your virtual machine is being packaged for customers. Additionally, lead systems are being configured and set up.",
          "status": "notStarted",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "< 1 hour",
          "id": "publisher-signoff",
          "stepName": "Publisher signoff",
          "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      },
      {
          "estimatedTimeFrame": "~2-5 days",
          "id": "live",
          "stepName": "Live",
          "description": "Offer is publicly visible and is available for purchase.",
          "status": "complete",
          "messages": [],
          "progressPercentage": 0
      }
      ],
      "previewLinks": [],
      liveLinks": [],
  }
```

### <a name="response-body-properties"></a>Egenskaper för svars text

|  **Namn**             |    **Beskrivning**                                                                             |
| --------------------  |   -------------------------------------------------------------------------------------------- |
|  status               | Status för erbjudandet. En lista över möjliga värden finns i [erbjudande status](#offer-status) nedan. |
|  meddelanden             | Matris med meddelanden som är associerade med erbjudandet                                                    |
|  steg                | Matris med de steg som erbjudandet går igenom under publiceringen av erbjudandet                      |
|  estimatedTimeFrame   | Uppskattning av hur lång tid det tar att slutföra det här steget, i eget format                       |
|  id                   | Identifierare för steget                                                                         |
|  stepName             | Namn på steget                                                                               |
|  beskrivning          | Beskrivning av steget                                                                        |
|  status               | Status för steget. En lista över möjliga värden finns i [steg status](#step-status) nedan.    |
|  meddelanden             | Matris med meddelanden som är relaterade till steget                                                          |
|  processPercentage    | Procent slutfört för steget                                                              |
|  previewLinks         | *Inte implementerad för närvarande*                                                                    |
|  liveLinks            | *Inte implementerad för närvarande*                                                                    |
|  notificationEmails   | Föråldrad för erbjudanden som migrerats till Partner Center. E-postmeddelanden för migrerade erbjudanden skickas till e-postmeddelandet som anges under säljar kontakt information i konto inställningar.<br><br>För icke-migrerade erbjudanden, kommaavgränsad lista över e-postadresser som ska meddelas om åtgärdens förlopp        |
|  |  |

### <a name="response-status-codes"></a>Svars status koder

| **Kod** |   **Beskrivning**                                                                                 |
| -------  |   ----------------------------------------------------------------------------------------------- |
|  200     |  `OK` -Begäran har bearbetats och den aktuella statusen för erbjudandet returnerades. |
|  400     | `Bad/Malformed request` – Fel svars texten kan innehålla mer information.                 |
|  404     | `Not found` -Den angivna entiteten finns inte.                                                |
|  |  |

### <a name="offer-status"></a>Erbjudandestatus

|  **Namn**                    |    **Beskrivning**                                       |
|  --------------------------  |  ------------------------------------------------------  |
|  NeverPublished              | Erbjudandet har aldrig publicerats.                          |
|  NotStarted                  | Erbjudandet är nytt och har inte startats.                            |
|  WaitingForPublisherReview   | Erbjudandet väntar på godkännande av utgivare.                 |
|  Körs                     | Överföring av erbjudande bearbetas.                     |
|  Lyckades                   | Bearbetningen av erbjudandet har slutförts.               |
|  Avbrutna                    | Överföring av erbjudande avbröts.                           |
|  Misslyckad                      | Det gick inte att skicka erbjudandet.                                 |
|  |  |

### <a name="step-status"></a>Steg status

|  **Namn**                    |    **Beskrivning**                           |
|  -------------------------   |  ------------------------------------------  |
|  NotStarted                  | Steget har inte startats.                        |
|  Pågår                  | Steget körs.                             |
|  WaitingForPublisherReview   | Steget väntar på godkännande av utgivare.      |
|  WaitingForApproval          | Steget väntar på process godkännande.        |
|  Blockerad                     | Steget är blockerat.                             |
|  Avslagen                    | Steget avvisades.                            |
|  Klart                    | Steget har slutförts.                            |
|  Avbrutna                    | Steget avbröts.                           |
|  |  |
