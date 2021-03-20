---
title: API-överväganden – Azure Marketplace
description: 'Versions hantering, fel hantering och auktoriseringsfel vid användning av Marketplace-API: er.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0920bec160874f27c8b1b6f2132951b57719b31c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88035288"
---
# <a name="api-considerations"></a>Att tänka på när det gäller API:er

<a name="api-versioning"></a>API-versioner
--------------

> [!NOTE]
> Cloud Partner Portal API: er är integrerade i och fortsätter att fungera i Partner Center. Över gången introducerar små ändringar. Granska ändringarna som anges i [Cloud Partner Portal API-referensen](./cloud-partner-portal-api-overview.md) för att se till att koden fortsätter att fungera efter över gången till Partner Center. CPP-API: er bör endast användas för befintliga produkter som redan har integrerats före över gången till Partner Center. nya produkter bör använda API: er för överföring av Partner Center.

Det kan finnas flera versioner av API: et som är tillgängliga på samma gång. Klienterna måste ange vilken version de vill anropa användningen genom att ange `api-version` parametern som en del av frågesträngen.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Svaret på en begäran med en okänd eller ogiltig API-version är en HTTP-kod 400. Det här felet returnerar samlingen kända API-versioner i svars texten.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Fel
------

API: et svarar på fel med motsvarande HTTP-statuskod och eventuellt ytterligare information i svaret som är serialiserad som JSON.
När du får ett fel, särskilt ett 400-klass fel, ska du inte försöka utföra begäran igen innan du korrigerar den bakomliggande orsaken. I exempel svaret ovan bör du till exempel reparera API-version-parametern innan du skickar begäran igen.

<a name="authorization-header"></a>Authorization-huvud
--------------------

För alla API: er i denna referens måste du skicka Authorization-huvudet tillsammans med Bearer-token som hämtats från Azure Active Directory (Azure AD). Den här rubriken krävs för att få ett giltigt svar. om detta inte finns `401 Unauthorized` returneras ett fel. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
