---
title: Hämta API för rapport frågor
description: 'Använd det här API: et för att hämta alla frågor som är tillgängliga för användning i kommersiella Marketplace Analytics-rapporter.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584230"
---
# <a name="get-report-queries-api"></a>Hämta API för rapport frågor

API: t Hämta rapport frågor hämtar alla frågor som är tillgängliga för användning i rapporter. Den hämtar alla system-och användardefinierade frågor som standard.

**Syntax för begäran**

| **Metod** | **URI för förfrågan** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**Begär ande huvud**

| **Huvud** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| Auktorisering | sträng | Krävs. Den Azure Active Directory (Azure AD) åtkomsttoken i formuläret `Bearer <token>` |
| Content-Type | sträng | `Application/JSON` |
||||

**Sök vägs parameter**

Inget

**Frågeparameter**

| **Parameternamn** | **Typ** | **Obligatoriskt** | **Beskrivning** |
| --- | --- | --- | --- |
| `queryId` | sträng | No | Filtrera för att hämta information om endast frågor med det ID som angavs i argumentet |
| `queryName` | sträng | No | Filtrera för att hämta information om endast frågor med det namn som angetts i argumentet |
| `IncludeSystemQueries` | boolean | Inga | Inkludera fördefinierade system frågor i svaret |
| `IncludeOnlySystemQueries` | boolean | Inga | Inkludera endast system frågor i svaret |
|||||

**Begär nytto Last**

Inget

**Ordlista**

Inget

**Response**

Svarets nytto Last struktureras enligt följande:

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

I den här tabellen beskrivs nyckel definitionerna för elementen i svaret.

| **Parameter** | **Beskrivning** |
| --- | --- |
| `QueryId` | Unikt UUID för frågan |
| `Name` | Namnet som angavs för frågan vid tidpunkten för skapandet av frågan |
| `Description` | Beskrivning som angavs när frågan skapades |
| `Query` | Frågesträng för rapport |
| `Type` | Ange som `userDefined` användare skapade frågor och `system` för fördefinierade system frågor |
| `User` | Användar-ID som skapade frågan |
| `CreatedTime` | Tiden då frågan skapades |
| `TotalCount` | Antal data uppsättningar i värde mat ris |
| `StatusCode` | Resultat kod. De möjliga värdena är 200, 400, 401, 403, 500 |
|||
