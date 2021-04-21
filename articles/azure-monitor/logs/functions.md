---
title: Funktioner i Azure Monitor loggfrågor
description: Den här artikeln beskriver hur du använder funktioner för att anropa en fråga från en annan loggfråga i Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/19/2021
ms.openlocfilehash: fecede1d582232ebc75878a687a24818031f0d80
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752946"
---
# <a name="functions-in-azure-monitor-log-queries"></a>Funktioner i Azure Monitor loggfrågor
En funktion är en loggfråga i Azure Monitor som kan användas i andra loggfrågor som om det vore ett kommando. Med funktioner kan utvecklare tillhandahålla lösningar till olika kunder och återanvända frågelogik i din egen miljö. Den här artikeln innehåller information om hur du använder funktioner och hur du skapar egna.

## <a name="types-of-functions"></a>Typer av funktioner
Det finns två typer av funktioner i Azure Monitor:

- **Lösningsfunktion:** Förbyggda funktioner som ingår i Azure Monitor. Dessa är tillgängliga i alla Log Analytics-arbetsytor och kan inte ändras.
- **Arbetsytefunktioner:** Funktioner som installeras på en viss Log Analytics-arbetsyta och kan ändras och styras av användaren.

## <a name="viewing-functions"></a>Visa funktioner
Du kan visa lösningsfunktioner och arbetsytefunktioner i den aktuella arbetsytan från **fliken Funktioner** i den vänstra rutan på en Log Analytics-arbetsyta. Använd **filterknappen** för att filtrera funktionerna som ingår i listan och **Gruppera efter för** att ändra deras gruppering. Skriv en sträng i **sökrutan** för att hitta en viss funktion. Hovra över en funktion för att visa information om den, inklusive en beskrivning och parametrar.

:::image type="content" source="media/functions/view-functions.png" alt-text="Visa funktion" lightbox="media/functions/view-functions.png":::

## <a name="use-a-function"></a>Använda en funktion
Använd en funktion i en fråga genom att skriva dess namn med värden för alla parametrar precis som du skulle skriva i ett kommando. Utdata från funktionen kan antingen returneras som resultat eller skickas till ett annat kommando.

Lägg till en funktion i den aktuella frågan genom att dubbelklicka på dess namn eller hovra över den och välja **Använd i redigeraren**. Funktioner i arbetsytan inkluderas också i IntelliSense när du skriver i en fråga. 

Om en fråga kräver parametrar anger du dem med hjälp av syntaxen: `function_name(param1,param2,...)` .

:::image type="content" source="media/functions/function-use.png" alt-text="Använda en funktion" lightbox="media/functions/function-use.png":::

## <a name="create-a-function"></a>Skapa en funktion
Om du vill skapa en funktion från den aktuella frågan i redigeraren väljer **du Spara** och sedan Spara **som funktion.** 

:::image type="content" source="media/functions/function-save.png" alt-text="Skapa en funktion" lightbox="media/functions/function-save.png":::

Skapa en funktion med Log Analytics i Azure Portal genom att klicka **på** Spara och sedan ange informationen i följande tabell.

| Inställning | Beskrivning |
|:---|:---|
| Funktionsnamn  | Namnet på funktionen. Detta får inte innehålla blanksteg eller specialtecken. Det kanske inte heller börjar med ett understreck (_) eftersom det här tecknet är reserverat för lösningsfunktioner. |
| Äldre kategori | Användardefinierad kategori för att filtrera och gruppera funktioner.   |
| Spara som datorgrupp | Spara frågan som en [datorgrupp](computer-groups.md).  |
| Parametrar | Lägg till en parameter för varje variabel i funktionen som kräver ett värde när den används. Mer [information finns i](#function-parameters) Funktionsparametrar. |

:::image type="content" source="media/functions/function-details.png" alt-text="Funktionsinformation" lightbox="media/functions/function-details.png":::

## <a name="function-parameters"></a>Funktionsparametrar 
Du kan lägga till parametrar i en funktion så att du kan ange värden för vissa variabler när du anropar den. På så sätt kan samma funktion användas i olika frågor, där var och en tillhandahåller olika värden för parametrarna. Parametrar definieras av följande egenskaper.

| Inställning | Beskrivning |
|:---|:---|
| Typ  | Datatyp för värdet. |
| Name  | Namnet på parametern. Det här är det namn som måste användas i frågan för att ersätta med parametervärdet.  |
| Standardvärde | Värde som ska användas för parametern om inget värde anges. |

Parametrarna ordnas när de skapas med alla parametrar som inte har något standardvärde framför de som har ett standardvärde.

## <a name="working-with-function-code"></a>Arbeta med funktionskod
Du kan visa koden för en funktion för att antingen få insyn i hur den fungerar eller ändra koden för en arbetsytefunktion. Välj **Läs in funktionskoden för** att lägga till funktionskoden i den aktuella frågan i redigeraren. Om du lägger till den i en tom fråga eller den första raden i en befintlig fråga, lägger den till funktionsnamnet på fliken. Om det är en arbetsytefunktion möjliggör detta alternativet att redigera funktionsinformationen.

:::image type="content" source="media/functions/function-code.png" alt-text="Läsa in funktionskod" lightbox="media/functions/function-code.png":::

## <a name="edit-a-function"></a>Redigera en funktion
Redigera egenskaperna eller koden för en funktion genom att skapa en ny fråga och hovra sedan över namnet på funktionen och välj **läs in funktionskod**. Gör de ändringar som du vill i koden och välj Spara **och** sedan **Redigera funktionsinformation.** Gör eventuella ändringar i funktionens egenskaper och parametrar innan du klickar på **Spara.**

:::image type="content" source="media/functions/function-edit.png" alt-text="Funktionen Edit" lightbox="media/functions/function-edit.png":::
## <a name="example"></a>Exempel
Följande exempelfunktion returnerar alla händelser i Azure-aktivitetsloggen sedan ett visst datum och som matchar en viss kategori. 

Börja med följande fråga med hårdkodade värden. Detta verifierar att frågan fungerar som förväntat.

```Kusto
AzureActivity
| where CategoryValue == "Administrative"
| where TimeGenerated > todatetime("2021/04/05 5:40:01.032 PM")
```

:::image type="content" source="media/functions/example-query.png" alt-text="Exempelfunktion – inledande fråga" lightbox="media/functions/example-query.png":::

Ersätt sedan de hårdkodade värdena med parameternamn och spara sedan funktionen genom att välja **Spara** och sedan **Spara som funktion.**

```Kusto
AzureActivity
| where CategoryValue == CategoryParam
| where TimeGenerated > DateParam
```

:::image type="content" source="media/functions/example-save-function.png" alt-text="Exempelfunktion – spara funktion" lightbox="media/functions/example-save-function.png":::

 Ange följande värden för funktionsegenskaperna.

| Egenskap | Värde |
|:---|:---|
| Funktionsnamn | AzureActivityByCategory |
| Äldre kategori | Demofunktioner |

Definiera följande parametrar innan du sparar funktionen.

| Typ | Name | Standardvärde |
|:---|:---|:---|
| sträng   | CategoryParam | "Administrativ" |
| datetime | DateParam     | |

:::image type="content" source="media/functions/example-function-properties.png" alt-text="Exempelfunktion – funktionsegenskaper" lightbox="media/functions/example-function-properties.png":::

Skapa en ny fråga och visa den nya funktionen genom att hovra över den. Observera ordningen på parametrarna eftersom det är den ordning som de måste anges när du använder funktionen.

:::image type="content" source="media/functions/example-view-details.png" alt-text="Exempelfunktion – visa information" lightbox="media/functions/example-view-details.png":::

Välj **Använd i redigeraren** för att lägga till den nya funktionen i en fråga och lägg sedan till värden för parametrarna. Observera att du inte behöver ange något värde för CategoryParam eftersom det har ett standardvärde.

:::image type="content" source="media/functions/example-use-function.png" alt-text="Exempelfunktion – använd funktion" lightbox="media/functions/example-use-function.png":::



## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att skriva Azure Monitor loggfrågor:

- [Strängåtgärder](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations)

