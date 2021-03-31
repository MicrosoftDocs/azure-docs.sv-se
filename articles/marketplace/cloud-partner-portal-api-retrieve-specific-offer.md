---
title: Hämta ett API för en special erbjudande – Azure Marketplace
description: API för att hämta det angivna erbjudandet inom utgivarens namnrymd.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 9f3ba6b2f13b9f2bb1d538db84723e3a9baaef12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87271849"
---
# <a name="retrieve-a-specific-offer"></a>Hämta ett Special erbjudande

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. CPP-API: er bör endast användas för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Hämtar det angivna erbjudandet inom utgivarens namnrymd.  

Du kan också hämta en viss version av erbjudandet eller hämta erbjudandet i utkast-, visnings-eller produktions platser. Om ingen plats anges är standardvärdet `draft` . Om du försöker hämta ett erbjudande som inte har granskats eller publicerats leder det till ett `404 Not Found` fel.

> [!WARNING]
> De hemliga värdena för fälten för hemliga typer kommer inte att hämtas av detta API.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>URI-parametrar

| **Namn**    | **Beskrivning**                                                                          | **Datatyp** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Till exempel contoso                                                        | Sträng        |
| offerId     | GUID som unikt identifierar erbjudandet.                                                 | Sträng        |
| version     | Version av erbjudandet som hämtas. Den senaste versionen av erbjudandet hämtas som standard. | Integer       |
| slotId      | Den plats som erbjudandet ska hämtas från kan vara något av följande:      <br/>  - `Draft` (standard) hämtar erbjudande versionen för närvarande i utkastet.  <br/>  -  `Preview` hämtar den erbjudande version som för närvarande finns i för hands version.     <br/>  -  `Production` hämtar den erbjudande version som för närvarande finns i produktion.          |      räkning |
| api-version | Senaste versionen av API                                                                    | Datum          |
|  |  |  |

## <a name="header"></a>Huvud

|  **Namn**          |   **Värde**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Auktorisering     | `Bearer YOUR_TOKEN`    |
|  |  |

## <a name="body-example"></a>Body-exempel

### <a name="response"></a>Svarsåtgärder

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
                "microsoft-azure-marketplace.categoryMap": [
                {
                "categoryL1": "analytics",
                "categoryL2-analytics": [
                "visualization-and-reporting"
                ]
                },
                {
                "categoryL1": "ai-plus-machine-learning",
                "categoryL2-ai-plus-machine-learning": [
                "bot-services",
                "cognitive-services",
                "other"
                ]
                }
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```

### <a name="response-body-properties"></a>Egenskaper för svars text

|  **Namn**       |   **Beskrivning**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Identifierar typen av erbjudande                                                                                                    |
|  publisherId    | Utgivarens unika identifierare                                                                                              |
|  status         | Status för erbjudandet. En lista över möjliga värden finns i [erbjudande status](#offer-status) nedan.                                  |
|  Id             | GUID som unikt identifierar erbjudandet                                                                                         |
|  version        | Aktuell version av erbjudandet. Det går inte att ändra versions egenskapen av klienten. Den ökar efter varje publicering.    |
|  definition     | Faktisk definition av arbets belastningen                                                                                               |
|  changedTime    | UTC datetime när erbjudandet senast ändrades                                                                                   |
|  |  |

### <a name="response-status-codes"></a>Svars status koder

| **Kod**  | **Beskrivning**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` -Begäran har bearbetats och alla erbjudanden under utgivaren returnerades till klienten.               |
|  400      | `Bad/Malformed request` – Fel svars texten kan innehålla mer information.                                                 |
|  403      | `Forbidden` -Klienten har inte åtkomst till den angivna namn rymden.                                                        |
|  404      | `Not found` -Den angivna entiteten finns inte. Klienten bör kontrol lera publisherId, offerId och version (om det anges).      |
|  |  |

### <a name="offer-status"></a>Erbjudandestatus

|  **Namn**                   |   **Beskrivning**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | Erbjudandet har aldrig publicerats.               |
|  NotStarted                 | Erbjudandet är nytt men har inte startats.              |
|  WaitingForPublisherReview  | Erbjudandet väntar på godkännande av utgivare.      |
|  Körs                    | Överföring av erbjudande bearbetas.          |
|  Lyckades                  | Bearbetningen av erbjudandet har slutförts.    |
|  Avbrutna                   | Överföring av erbjudande avbröts.                |
|  Misslyckad                     | Det gick inte att skicka erbjudandet.                      |
|  |  |
