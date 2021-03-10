---
title: Ta bort rapport-API
description: Använd detta API för att ta bort alla rapport-och rapport körnings poster för kommersiella Marketplace Analytics-rapporter.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7c39f8bc0db44f1d8aa885969ca09d90b0dcd332
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102584238"
---
# <a name="delete-report-api"></a>Ta bort rapport-API

Vid körning tar detta API bort alla rapport-och rapport körnings poster.

**Syntax för begäran**

| Metod | URI för förfrågan |
| ------------ | ------------- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**Begär ande huvud**

| Huvud | Typ | Beskrivning |
| ------------ | ------------- | ------------- |
| Auktorisering | sträng | Krävs. Azure AD-åtkomsttoken i formuläret `Bearer <token>` |
| Innehållstyp | sträng | `Application/JSON` |
||||

**Sök vägs parameter**

Inget

**Frågeparameter**

| Parameternamn | Obligatorisk | sträng | Beskrivning |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Ja | sträng | ID för rapporten som ändras |
|||||

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
| `ReportId` | Unikt UUID för den borttagna rapporten |
| `ReportName` | Namn som angavs för rapporten vid skapande |
| `Description` | Beskrivning som angavs när rapporten skapades |
| `QueryId` | Fråge-ID skickades vid den tidpunkt då rapporten skapades |
| `Query` | Frågetext som ska utföras för den här rapporten |
| `User` | Användar-ID som används för att skapa rapporten |
| `CreatedTime` | Tid då rapporten skapades. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `ModifiedTime` | Tid då rapporten senast ändrades. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `StartTime` | Tid då rapport körningen ska börja. Tids formatet är åååå-MM-ddTHH: mm: ssZ |
| `ReportStatus` | Status för rapport körning. De möjliga värdena är pausade, aktiva och inaktiva. |
| `RecurrenceInterval` | Upprepnings intervall som anges när rapporten skapas |
| `RecurrenceCount` | Antal upprepningar som angavs när rapporten skapas |
| `CallbackUrl` | Återanrops-URL som angetts i begäran |
| `Format` | Rapport filens format |
|||
