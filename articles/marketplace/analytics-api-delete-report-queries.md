---
title: Ta bort API för rapport frågor
description: 'Använd det här API: et för att ta bort användardefinierade frågor för kommersiell Marketplace-analys.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584258"
---
# <a name="delete-report-queries-api"></a>Ta bort API för rapport frågor

Detta API tar bort användardefinierade frågor.

**Syntax för begäran**

| **Metod** | **URI för förfrågan** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**Begär ande huvud**

| **Huvud** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| Auktorisering | sträng | Krävs. Den Azure Active Directory (Azure AD) åtkomsttoken i formuläret `Bearer <token>` |
| Content-Type | sträng | `Application/JSON` |

**Sök vägs parameter**

| **Parameternamn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| `queryId` | sträng | Filtrera för att hämta information om endast frågor med det ID som angetts i det här argumentet |

**Frågeparameter**

Inget

**Begär nytto Last**

Inget

**Ordlista**

Inget

**Response**

Svarets nytto Last struktureras enligt följande i JSON-format.

Svars kod: 200, 400, 401, 403, 404, 500

Nytto last för svar:

```json
{
  "Value": [
    {
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Ordlista**

I den här tabellen visas nyckel definitionerna för elementen i svaret.

| **Parameter** | **Beskrivning** |
| --- | --- |
| `QueryId` | Unikt UUID för den fråga som har tagits bort. |
| `Name` | Namnet på den fråga som har tagits bort |
| `Description` | Beskrivning av den borttagna frågan |
| `Query` | Rapport frågesträng för den borttagna frågan |
| `Type` | userDefined |
| `User` | Användar-ID som skapade frågan |
| `CreatedTime` | Tiden då frågan skapades |
| `ModifiedTime` | Null |
| `TotalCount` | Antal data uppsättningar i värde mat ris |
| `StatusCode` | Resultat kod. De möjliga värdena är 200, 400, 401, 403, 500 |
