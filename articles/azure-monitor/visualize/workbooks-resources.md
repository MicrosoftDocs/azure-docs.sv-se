---
title: Resurs parametrar för Azure Monitor-arbetsböcker
description: Lär dig hur du använder resurs parametrar för att tillåta plockning av resurser i arbets böcker. Använd resurs parametrarna för att ange det omfång som data ska hämtas från.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: b607cdd462becdcb37f88d35d934a10efd0bfe9b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621474"
---
# <a name="workbook-resource-parameters"></a>Resurs parametrar för arbets bok

Resurs parametrar tillåter plockning av resurser i arbets böcker. Detta är användbart när du anger det omfång som data ska hämtas från. Ett exempel är att användarna kan välja en uppsättning virtuella datorer, vilka diagram som senare ska användas för att presentera data.

Värden från resurs väljare kan komma från arbets bokens kontext, statisk lista eller från Azure Resource Graph-frågor.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Skapa en resurs parameter (arbets boks resurser)
1. Börja med en tom arbets bok i redigerings läge.
2. Välj _Lägg till parametrar_ från länkarna i arbets boken.
3. Klicka på knappen blå _Lägg till parameter_ .
4. I fönstret ny parameter som öppnas anger du:
    1. Parameter namn: `Applications`
    2. Parameter typ: `Resource picker`
    3. Kunna `checked`
    4. Tillåt flera val: `checked`
5. Hämta data från: `Workbook Resources`
6. Inkludera endast resurs typer: `Application Insights`
7. Skapa parametern genom att välja Spara i verktygsfältet.

![Bild som visar hur du skapar en resurs parameter med hjälp av arbets boks resurser](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Skapa en resurs parameter (Azure Resource Graph)
1. Börja med en tom arbets bok i redigerings läge.
2. Välj _Lägg till parametrar_ från länkarna i arbets boken.
3. Klicka på knappen blå _Lägg till parameter_ .
4. I fönstret ny parameter som öppnas anger du:
    1. Parameter namn: `Applications`
    2. Parameter typ: `Resource picker`
    3. Kunna `checked`
    4. Tillåt flera val: `checked`
5. Hämta data från: `Query`
    1. Frågetyp: `Azure Resource Graph`
    2. Prenumerationer `Use default subscriptions`
    3. Lägg till det här kodfragmentet i kontrollen fråga
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Skapa parametern genom att välja Spara i verktygsfältet.

![Bild som visar hur du skapar en resurs parameter med Azure Resource Graph](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Azure Resource Graph är inte tillgängligt ännu i alla moln. Se till att det stöds i mål molnet om du väljer den här metoden.

[Azure Resource Graph-dokumentation](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>Skapa en resurs parameter (JSON-lista)
1. Börja med en tom arbets bok i redigerings läge.
2. Välj _Lägg till parametrar_ från länkarna i arbets boken.
3. Klicka på knappen blå _Lägg till parameter_ .
4. I fönstret ny parameter som öppnas anger du:
    1. Parameter namn: `Applications`
    2. Parameter typ: `Resource picker`
    3. Kunna `checked`
    4. Tillåt flera val: `checked`
5. Hämta data från: `JSON`
    1. Lägg till det här JSON-kodfragmentet i innehålls kontrollen
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Tryck på knappen blå _uppdatering_ .
6. Alternativt kan du ange `Include only resource types` till _Application Insights_
7. Skapa parametern genom att välja Spara i verktygsfältet.

## <a name="referencing-a-resource-parameter"></a>Referera till en resurs parameter
1. Lägg till en frågeplan i arbets boken och välj en Application Insights resurs.
2. Använd List rutan _Application Insights_ för att binda parametern till kontrollen. Detta anger omfånget för frågan till de resurser som returneras av parametern vid körning.
4. Lägg till det här kodfragmentet i KQL-kontrollen
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Kör fråga för att visa resultatet. 

![Bild som visar en resurs parameter som refereras i en frågegrupp](./media/workbooks-resources/resource-reference.png)

> Den här metoden kan användas för att binda resurser till andra kontroller som mått.

## <a name="resource-parameter-options"></a>Alternativ för resurs parameter
| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `{Applications}` | Det valda resurs-ID: t | _/Subscriptions/<under-ID>/resourceGroups/<resurs-grupp>/providers/<resurs typ>/acmeauthentication_ |
| `{Applications:label}` | Etiketten för den valda resursen | `acmefrontend` |
| `{Applications:value}` | Värdet för den valda resursen | _/Subscriptions/<under-ID>/resourceGroups/<resurs-grupp>/providers/<resurs typ>/acmeauthentication_ |
| `{Applications:name}` | Namnet på den valda resursen | `acmefrontend` |
| `{Applications:resourceGroup}` | Resurs gruppen för den valda resursen | `acmegroup` |
| `{Applications:resourceType}` | Den valda resursens typ | _Microsoft. Insights/komponenter_ |
| `{Applications:subscription}` | Den valda resursens prenumeration |  |
| `{Applications:grid}` | Ett rutnät som visar resurs egenskaperna. Användbart för att återge i ett textblock under fel sökning  |  |

## <a name="next-steps"></a>Nästa steg

* [Kom igång](../platform/workbooks-overview.md#visualizations) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](../platform/workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.