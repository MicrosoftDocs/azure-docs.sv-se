---
title: Återuppta API för rapport körningar
description: 'Använd det här API: et för att återuppta den schemalagda körningen av en pausad marknads plats analys rapport.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4a11783b28352cb62c5a3c0d38e45dcdc47a8d86
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583940"
---
# <a name="resume-report-executions-api"></a>Återuppta API för rapport körningar

Detta API, vid körning, återupptar den schemalagda körningen av en pausad marknads plats analys rapport.

**Syntax för begäran**

| Metod | URI för förfrågan |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/resume/{reportId}` |
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
| `ReportId` | Globalt unik identifierare (UUID) för den återupptog rapporten |
| `ReportName` | Namn som angavs för rapporten vid skapande |
| `Description` | Beskrivning som angavs när rapporten skapades |
| `QueryId` | Fråge-ID skickades vid den tidpunkt då rapporten skapades |
| `Query` | Frågetext som ska utföras för den här rapporten |
| `User` | Användar-ID som används för att skapa rapporten |
| `CreatedTime` | Tid då rapporten skapades. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Tid då rapporten senast ändrades. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `StartTime` | Tiden då rapport körningen ska börja. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Rapport körningens status. De möjliga värdena är pausade, aktiva och inaktiva. |
| `RecurrenceInterval` | Upprepnings intervall som anges när rapporten skapas |
| `RecurrenceCount` | Antal upprepningar som angavs när rapporten skapas |
| `CallbackUrl` | Återanrops-URL som angetts i begäran |
| `Format` | Rapport filens format |
|||
