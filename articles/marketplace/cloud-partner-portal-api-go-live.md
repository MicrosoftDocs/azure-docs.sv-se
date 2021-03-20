---
title: Go Live API – Azure Marketplace
description: 'Go Live API: t initierar bearbetnings processen för Live-erbjudandet.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: d612b796f85c9eaab1600c55cde7e79acb49f352
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87292938"
---
# <a name="go-live"></a>Gå live

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. CPP-API: er bör endast användas för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Detta API startar processen för att skicka en app till produktion. Den här åtgärden körs vanligt vis längre. Det här anropet använder e-postlistan för meddelanden från [publicerings](./cloud-partner-portal-api-publish-offer.md) -API-åtgärden.

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

## <a name="uri-parameters"></a>URI-parametrar
--------------

|  **Namn**      |   **Beskrivning**                                                           | **Datatyp** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Utgivarens identifierare för erbjudandet att hämta, till exempel `contoso`       |  Sträng       |
| offerId        | Erbjudande-ID för erbjudandet som ska hämtas                                   |  Sträng       |
| api-version    | Senaste versionen av API: et                                                   |  Datum         |
|  |  |  |

## <a name="header"></a>Huvud
------

|  **Namn**       |     **Värde**       |
|  ---------      |     ----------      |
| Content-Type    | `application/json`  |
| Auktorisering   | `Bearer YOUR_TOKEN` |
|  |  |

## <a name="body-example"></a>Body-exempel

### <a name="response"></a>Svarsåtgärder

#### <a name="migrated-offers"></a>Migrerade erbjudanden

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ej migrerade erbjudanden

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Svarshuvud

|  **Namn**             |      **Värde**                                                            |
|  --------             |      ----------                                                           |
| Location    |  Den relativa sökvägen för att hämta den här åtgärdens status            |
|  |  |

### <a name="response-status-codes"></a>Svars status koder

| **Kod** |  **Beskrivning**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` -Begäran har godkänts. Svaret innehåller en plats för att spåra åtgärdens status. |
|  400     | `Bad/Malformed request` -Ytterligare fel information finns i svars texten. |
|  404     |  `Not found` -Den angivna entiteten finns inte.                                       |
|  |  |
