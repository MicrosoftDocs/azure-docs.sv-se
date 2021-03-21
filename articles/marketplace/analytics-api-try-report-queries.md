---
title: Prova API för rapport frågor
description: 'Använd det här API: et för att köra en rapport fråga för affärs platsers analys rapporter.'
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583930"
---
# <a name="try-report-queries-api"></a>Prova API för rapport frågor

Detta API kör en rapports frågeuttryck. API: t returnerar bara 10 poster som du som partner kan använda för att kontrol lera om data är som du förväntade dig.

> [!IMPORTANT]
> Det här API: et har en tids gräns på 100 sekunder för körning av fråga. Om du märker att API: et tar mer än 100 sekunder är det mycket troligt att frågan är syntaktiskt korrekt eller annars skulle du ha fått en annan felkod än 200. Den faktiska rapporten kommer att skickas om frågesyntaxen är korrekt.

**Syntax för begäran**

| **Metod** | **URI för förfrågan** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**Begär ande huvud**

| **Huvud** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| Auktorisering | sträng | Krävs. Den Azure Active Directory (Azure AD) åtkomsttoken i formuläret `Bearer <token>` |
| Content-Type | sträng | `Application/JSON` |
|||

**QueryParameter**

| **Parameter namn** | **Typ** | **Beskrivning** |
| --- | --- | --- |
| `exportQuery` | sträng | Rapport fråga som måste köras |
| `queryId` | sträng | Ett giltigt befintligt fråge-ID |
|||

**Sök vägs**  **parameter**

Inget

**Begär nytto Last**

Inget

**Ordlista**

Inget

**Response**

Svarets nytto Last struktureras enligt följande:

Svars kod: 200, 400, 401, 403, 404, 500

Svars nytto last: de 10 översta raderna i frågekörningen
