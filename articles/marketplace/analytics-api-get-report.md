---
title: Hämta rapport-API
description: Använd detta API för att få analys rapporter som har schemalagts i Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102584211"
---
# <a name="get-report-api"></a>Hämta rapport-API

Detta API hämtar alla rapporter som har schemalagts.

**Syntax för begäran**

| **Metod** | **URI för förfrågan** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**Begär ande huvud**

| **Huvud** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| Auktorisering | sträng | Krävs. Den Azure Active Directory (Azure AD) åtkomsttoken i formuläret `Bearer <token>` |
| Content-Type | sträng | `Application/JSON` |

**Sök vägs parameter**

Inget

**Frågeparameter**

| **Parameter namn** | **Obligatoriskt** | **Typ** | **Beskrivning** |
| --- | --- | --- | --- |
| `reportId` | Inga | sträng | Filtrera för att få information om enbart rapporter med `reportId` angivet i det här argumentet |
| `reportName` | Inga | sträng | Filtrera för att få information om enbart rapporter med `reportName` angivet i det här argumentet |
| `queryId` | Inga | boolean | Inkludera fördefinierade system frågor i svaret |

**Ordlista**

Inget

**Response**

Svarets nytto Last är strukturerad i JSON-format enligt följande:

Svars kod: 200, 400, 401, 403, 404, 500

Nytto last för svar:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
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
| `ReportId` | Unikt UUID för rapporten som skapades |
| `ReportName` | Namn angivet för rapporten i nytto lasten för begäran |
| `Description` | Beskrivning som angavs när rapporten skapades |
| `QueryId` | Fråge-ID skickades vid den tidpunkt då rapporten skapades |
| `Query` | Frågetext som ska utföras för den här rapporten |
| `User` | Användar-ID som används för att skapa rapporten |
| `CreatedTime` | Tid då rapporten skapades. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Tid då rapporten senast ändrades. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `StartTime` | Tids körningen börjar. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Rapport körningens status. De möjliga värdena är pausade, aktiva och inaktiva. |
| `RecurrenceInterval` | Upprepnings intervall som anges när rapporten skapas |
| `RecurrenceCount` | Antal upprepningar som angavs när rapporten skapas |
| `CallbackUrl` | Återanrops-URL som angetts i begäran |
| `Format` | Rapport filens format |
