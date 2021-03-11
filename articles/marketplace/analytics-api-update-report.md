---
title: Uppdatera rapport-API
description: 'Använd det här API: et för att rapportera parameter för affärs platser i marknadsförings rapporter.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583929"
---
# <a name="update-report-api"></a>Uppdatera rapport-API

Detta API hjälper dig att ändra en rapport parameter.

**Syntax för begäran**

| Metod | URI för förfrågan |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
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

**Begär nytto Last**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Ordlista**

Den här tabellen innehåller nyckel definitionerna för element i nytto lasten för begäran.

| Parameter | Krävs | Beskrivning | Tillåtna värden |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Ja | Namn som ska tilldelas till rapporten | sträng |
| `Description` | No | Beskrivning av den skapade rapporten | sträng |
| `StartTime` | Ja | Tidsstämpel efter vilken rapportgenerering kommer att börja | sträng |
| `RecurrenceInterval` | No | Den frekvens som rapporten ska genereras i timmar. Minimalt värde är 4 | heltal |
| `RecurrenceCount` | Inga | Antal rapporter som ska genereras. Standardvärdet är oändligt | heltal |
| `Format` | Ja | Den exporterade filens fil format. Standardvärdet är CSV. | CSV/TSV |
| `CallbackUrl` | Ja | https-callback-URL som ska anropas vid rapportgenerering | sträng |
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
| `ReportId` | Globalt unik identifierare (UUID) för rapporten som uppdateras |
| `ReportName` | Namn angivet för rapporten i nytto lasten för begäran |
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
