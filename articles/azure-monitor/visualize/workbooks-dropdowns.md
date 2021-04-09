---
title: Parametrar för List rutan Azure Monitor arbets bok
description: Förenkla komplex rapportering med färdiga och anpassade parameterstyrda arbets böcker som innehåller List parametrar
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d6fb4954ff616bccd9b237aedb3001b0a8d592dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717204"
---
# <a name="workbook-drop-down-parameters"></a>List Rute parametrar för arbets bok

List rutor gör att användaren kan samla in en eller flera indatavärden från en känd uppsättning (till exempel väljer du en av appens begär Anden). List rutor ger ett användarvänligt sätt att samla in godtyckliga indata från användare. List rutor är särskilt användbara när du aktiverar filtrering i dina interaktiva rapporter. 

Det enklaste sättet att ange en listruta är genom att tillhandahålla en statisk lista i parameter inställningen. Ett mer intressant sätt är att hämta listan dynamiskt via en KQL-fråga. Parameter inställningar låter dig också ange om det är en enda eller flera val och om det är flera val, hur resultat uppsättningen ska formateras (avgränsare, citat osv.).

## <a name="creating-a-static-drop-down-parameter"></a>Skapa en statisk List Rute parameter

1. Börja med en tom arbets bok i redigerings läge.
2. Välj _Lägg till parametrar_ från länkarna i arbets boken.
3. Klicka på knappen blå _Lägg till parameter_ .
4. I fönstret ny parameter som öppnas anger du:
    1. Parameter namn: `Environment`
    2. Parameter typ: `Drop down`
    3. Kunna `checked`
    4. Tillåt `multiple selection` : `unchecked`
    5. Hämta data från: `JSON`
5. I JSON-inmatnings textblocket infogar du följande JSON-kodfragment:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Tryck på den blå `Update` knappen.
7. Skapa parametern genom att välja Spara i verktygsfältet.
8. Miljö parametern är en listruta med de tre värdena.

    ![Bild som visar hur en statisk Drown skapas](./media/workbooks-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Skapa en statisk listruta med objekt grupper

Om frågeresultatet/JSON innehåller ett "grupp"-fält visas grupper med värden i list rutan. Följ exemplet ovan, men Använd följande JSON i stället:

```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```

![Bild som visar ett exempel på en grupperad listruta](./media/workbooks-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Skapa en dynamisk List Rute parameter
1. Börja med en tom arbets bok i redigerings läge.
2. Välj _Lägg till parametrar_ från länkarna i arbets boken.
3. Klicka på knappen blå _Lägg till parameter_ .
4. I fönstret ny parameter som öppnas anger du:
    1. Parameter namn: `RequestName`
    2. Parameter typ: `Drop down`
    3. Kunna `checked`
    4. Tillåt `multiple selection` : `unchecked`
    5. Hämta data från: `Query`
5. I JSON-inmatnings textblocket infogar du följande JSON-kodfragment:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Tryck på den blå `Run Query` knappen.
2. Skapa parametern genom att välja Spara i verktygsfältet.
3. Parametern RequestName är en listruta med namnen på alla begär anden i appen.

    ![Bild som visar hur en dynamisk listruta skapas](./media/workbooks-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Referens för List Rute parameter

### <a name="in-kql"></a>I KQL
1. Lägg till en frågeplan i arbets boken och välj en Application Insights resurs.
2. I KQL-redigeraren anger du det här kodfragmentet

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Detta utökar vid utvärderings tiden för frågan till:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Kör fråga för att visa resultatet. Du kan också återge det som ett diagram.

    ![Bild som visar en listruta som refereras i KQL](./media/workbooks-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Parameter värde, etikett, val och grupp
Frågan som används i den dynamiska nedrullningsbara List parametern ovan returnerar bara en lista med värden som återges i den nedrullningsbara List rutan. Men vad händer om du vill att ett annat visnings namn eller någon av dessa ska väljas? Med de nedrullningsbara parametrarna kan du göra detta via kolumnerna värde, etikett, markering och grupp.

Exemplet nedan visar hur du hämtar en lista över Application Insights beroenden vars visnings namn är formaterade med en emoji, har den första som är markerad och är grupperad efter åtgärds namn.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```

![Bild som visar en nedrullningsbar parameter med alternativ för värde, etikett, markering och grupp](./media/workbooks-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>List alternativ för parametrar
| Parameter | Förklaring | Exempel |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | Det valda värdet | Hämta fabrikamaccount |
| `{DependencyName:label}` | Den valda etiketten | 🌐 Hämta fabrikamaccount |
| `{DependencyName:value}` | Det valda värdet | Hämta fabrikamaccount |

## <a name="multiple-selection"></a>Flera val
I exemplen har du uttryckligen angett parametern för att bara välja ett värde i list rutan. Nedrullningsbara parametrar stöder även `multiple selection` aktivering av detta är lika enkelt som att kontrol lera `Allow multiple selection` alternativet. 

Användaren kan också ange formatet för resultat uppsättningen via `delimiter` `quote with` inställningarna och. Standardvärdet returnerar bara värdena som en samling i det här formuläret: "a", "b", "c". De har också möjlighet att begränsa antalet val.

KQL som refererar till parametern måste ändras för att fungera med resultat formatet. Det vanligaste sättet att aktivera det är via- `in` operatorn.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Här är ett exempel på en listruta för flera val i arbetet:

![Bild som visar en List Rute parameter med flera val](./media/workbooks-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Nästa steg

* [Kom igång](./workbooks-overview.md#visualizations) lär dig mer om arbets böcker många avancerade visualiserings alternativ.
* [Kontrol lera](./workbooks-access-control.md) och dela åtkomst till dina arbets boks resurser.