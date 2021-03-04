---
title: app ()-uttryck i Azure Monitor logg frågor | Microsoft Docs
description: App-uttrycket används i en Azure Monitor log-fråga för att hämta data från en speciell Application Insights-app i samma resurs grupp, en annan resurs grupp eller en annan prenumeration.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2019
ms.openlocfilehash: 235a6bf4a0dd9afcac8751067b2eac3ddb37c0cd
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031250"
---
# <a name="app-expression-in-azure-monitor-query"></a>app ()-uttryck i Azure Monitor fråga

`app`Uttrycket används i en Azure Monitor-fråga för att hämta data från en speciell Application Insights-app i samma resurs grupp, en annan resurs grupp eller en annan prenumeration. Detta är användbart om du vill inkludera program data i en Azure Monitor logg fråga och fråga efter data över flera program i en Application Insights fråga.

> [!IMPORTANT]
> App ()-uttrycket används inte om du använder en [arbets yta-baserad Application Insights resurs](../app/create-workspace-resource.md) sedan loggdata lagras i en Log Analytics arbets yta. Använd log ()-uttrycket för att skriva en fråga som inkluderar program i flera arbets ytor. För flera program i samma arbets yta behöver du inte en fråga om flera arbets ytor.

## <a name="syntax"></a>Syntax

`app(`*Beteckning*`)`


## <a name="arguments"></a>Argument

- *Identifierare*: identifierar appen med något av formaten i tabellen nedan.

| Identifierare | Beskrivning | Exempel
|:---|:---|:---|
| Resursnamn | Appens läsliga namn (även kallat komponent namn) | app ("fabrikamapp") |
| Kvalificerat namn | Appens fullständiga namn i formatet: "subscriptionName/resourceGroup/componentName" | app (' AI-Prototype/Fabrikam/fabrikamapp ') |
| ID | Appens GUID | app ("988ba129-363e-4415-8fe7-8cbab5447518") |
| Resurs-ID för Azure | Identifierare för Azure-resursen |app ("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp") |


## <a name="notes"></a>Kommentarer

* Du måste ha Läs behörighet till programmet.
* Att identifiera ett program med hjälp av namnet förutsätter att det är unikt för alla tillgängliga prenumerationer. Om du har flera program med det angivna namnet går det inte att köra frågan på grund av tvetydighet. I det här fallet måste du använda någon av de andra identifierarna.
* Använd [arbets ytan](../logs/workspace-expression.md) relaterad-uttryck för att fråga i Log Analytics arbets ytor.
* App ()-uttrycket stöds för närvarande inte i Sök frågan när du använder Azure Portal för att skapa en [anpassad varnings regel för loggs ökning](../alerts/alerts-log.md), om inte ett Application Insights program används som resurs för varnings regeln.

## <a name="examples"></a>Exempel

```Kusto
app("fabrikamapp").requests | count
```
```Kusto
app("AI-Prototype/Fabrikam/fabrikamapp").requests | count
```
```Kusto
app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count
```
```Kusto
app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
```
```Kusto
union 
(workspace("myworkspace").Heartbeat | where Computer contains "Con"),
(app("myapplication").requests | where cloud_RoleInstance contains "Con")
| count  
```
```Kusto
union 
(workspace("myworkspace").Heartbeat), (app("myapplication").requests)
| where TimeGenerated between(todatetime("2018-02-08 15:00:00") .. todatetime("2018-12-08 15:05:00"))
```

## <a name="next-steps"></a>Nästa steg

- Se ett [arbets ytans uttryck](../logs/workspace-expression.md) för att referera till en Log Analytics-arbetsyta.
- Läs om hur [Azure Monitor data](./log-query-overview.md) lagras.
- Få fullständig dokumentation för [Kusto-frågespråket](/azure/kusto/query/).