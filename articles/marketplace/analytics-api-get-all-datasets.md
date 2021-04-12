---
title: Hämta alla data uppsättnings-API
description: 'Använd det här API: et för att hämta alla tillgängliga data uppsättningar för affärs marknads plats analys.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583941"
---
# <a name="get-all-datasets-api"></a>Hämta alla data uppsättnings-API

API: t hämta alla data uppsättningar hämtar alla tillgängliga data uppsättningar. Data uppsättningar visar tabeller, kolumner, mått och tidsintervall.

**Syntax för begäran**

| **Metod** | **URI för förfrågan** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**Begär ande huvud**

| **Huvud** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| Auktorisering | sträng | Krävs. Den Azure Active Directory (Azure AD) åtkomsttoken i formuläret `Bearer <token>` |
| Content-Type | sträng | `Application/JSON` |

**Sök vägs parameter**

Inget

**Frågeparameter**

| **Parameter namn** | **Typ** | **Obligatoriskt** | **Beskrivning** |
| --- | --- | --- | --- |
| `datasetName` | sträng | No | Filtrera för att hämta information om endast en data uppsättning |

**Begär nytto Last**

Inget

**Ordlista**

Inget

**Response**

Svarets nytto Last struktureras enligt följande:

Svars kod: 200, 400, 401, 403, 404, 500

Exempel på svars nytto last:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**Ordlista**

I den här tabellen definieras viktiga element i svaret:

| **Parameter** | **Beskrivning** |
| --- | --- |
| `DatasetName` | Namnet på data uppsättningen som detta mat ris objekt definierar |
| `SelectableColumns` | Råa kolumner som kan anges i de valda kolumnerna |
| `AvailableMetrics` | Agg regerings-/mått kolumn namn som kan anges i de valda kolumnerna |
| `AvailableDateRanges` | Datum intervall som kan användas i rapport frågor för data uppsättningen |
| `NextLink` | Länka till nästa sida om data är sid brytning |
| `TotalCount` | Antal data uppsättningar i värde mat ris |
| `StatusCode` | Resultat kod. De möjliga värdena är 200, 400, 401, 403, 500 |
