---
title: Funktioner i Azure Monitor logg frågor | Microsoft Docs
description: Den här artikeln beskriver hur du använder funktioner för att anropa en fråga från en annan logg fråga i Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 07a959d4e8ba41652ba4e31ad59cf852659a5926
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199770"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Använda funktioner i Azure Monitor logg frågor

Om du vill använda en logg fråga med en annan fråga kan du spara den som en funktion. På så sätt kan du förenkla komplexa frågor genom att dela upp dem i delar och du kan återanvända gemensam kod med flera frågor.

## <a name="create-a-function"></a>Skapa en funktion

Skapa en funktion med Log Analytics i Azure Portal genom att klicka på **Spara** och sedan ange informationen i följande tabell.

| Inställning | Beskrivning |
|:---|:---|
| Name           | Visnings namn för frågan i **query Explorer**. |
| Spara som        | Funktion |
| Funktions Ali Aset | Kort namn för att använda funktionen i andra frågor. Får inte innehålla blank steg och måste vara unikt. |
| Kategori       | En kategori för att organisera sparade frågor och funktioner i **query Explorer**. |

Du kan också skapa funktioner med hjälp av [REST API](/rest/api/loganalytics/savedsearches/createorupdate) eller [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightssavedsearch).


## <a name="use-a-function"></a>Använd en funktion
Använd en funktion genom att inkludera dess alias i en annan fråga. Den kan användas som vilken annan tabell som helst.

## <a name="function-parameters"></a>Funktions parametrar 
Du kan lägga till parametrar till en funktion så att du kan ange värden för vissa variabler när du anropar den. Det enda sättet för att skapa en funktion med parametrar är att använda en Resource Manager-mall. Se exempel på [Resource Manager-mallar för logg frågor i Azure Monitor](./resource-manager-log-queries.md#parameterized-function) .

## <a name="example"></a>Exempel
Följande exempel fråga returnerar alla saknade säkerhets uppdateringar som rapporter ATS under den senaste dagen. Spara den här frågan som en funktion med aliaset _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Skapa en ny fråga och referera till funktionen _security_updates_last_day_ för att söka efter SQL-relaterade nödvändiga säkerhets uppdateringar.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att skriva Azure Monitor logg frågor:

- [Strängåtgärder](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)
- [Åtgärder för datum och tid](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#date-and-time-operations)
- [Aggregeringsfunktioner](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations)
- [Avancerade aggregeringar](/azure/data-explorer/write-queries#advanced-aggregations)
- [JSON och datastrukturer](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#json-and-data-structures)
- [Kopplingar](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins)
- [Diagram](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#charts)
