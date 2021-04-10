---
title: Pausa API för rapport körningar
description: 'Använd det här API: et för att pausa den schemalagda körningen av en extern Marketplace Analytics-rapport.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102584206"
---
# <a name="pause-report-executions-api"></a>Pausa API för rapport körningar

Detta API, vid körning, pausar schemalagd körning av rapporter.

**Syntax för begäran**

| Metod | URI för förfrågan |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**Begär ande huvud**

| Huvud | Typ | Beskrivning |
| ------------ | ------------- | ------------- |
| Auktorisering | sträng | Krävs. Den Azure Active Directory (Azure AD) åtkomsttoken i formuläret `Bearer <token>` |
| Content-Type | sträng | `Application/JSON` |
||||

**Sök vägs parameter**

Inget

**Frågeparameter**

| Parameternamn | Krävs | Typ | Beskrivning |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Ja | sträng | ID för rapporten som ändras |
|||||

**Ordlista**

Inget

**Response**

Svarets nytto Last struktureras enligt följande i JSON-format:

Svars kod: 200, 400, 401, 403, 404, nytto last för 500 svar:

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
      "RecurrenceCount": 0,
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

| Parameter | Beskrivning |
| ------------ | ------------- |
| `ReportId` | Globalt unik identifierare (UUID) för den borttagna rapporten |
| `ReportName` | Namn som angavs för rapporten vid skapande |
| `Description` | Beskrivning som angavs när rapporten skapades |
| `QueryId` | Fråge-ID skickades vid den tidpunkt då rapporten skapades |
| `Query` | Frågetext som ska utföras för den här rapporten |
| `User` | Användar-ID som används för att skapa rapporten |
| `CreatedTime` | Tid då rapporten skapades. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Tid då rapporten senast ändrades. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `StartTime` | Tid då rapport körningen ska börja. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Rapport körningens status. De möjliga värdena är pausade, aktiva och inaktiva. |
| `RecurrenceInterval` | Upprepnings intervall som anges när rapporten skapas |
| `RecurrenceCount` | Antal upprepningar som angavs när rapporten skapas |
| `CallbackUrl` | Återanrops-URL som angetts i begäran |
| `Format` | Rapport filens format |
|||
