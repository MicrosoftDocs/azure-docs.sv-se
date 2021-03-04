---
title: Skapa vyer för att analysera loggdata i Azure Monitor | Microsoft Docs
description: Med hjälp av View Designer i Azure Monitor kan du skapa anpassade vyer som visas i Azure Portal och som innehåller en mängd olika visualiseringar på data i arbets ytan Log Analytics. Den här artikeln innehåller en översikt över hur du skapar och redigerar anpassade vyer i designern.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/04/2020
ms.openlocfilehash: cb0274260022c55ae657b5b28b2c9ad1903d0296
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043277"
---
# <a name="create-custom-views-by-using-view-designer-in-azure-monitor"></a>Skapa anpassade vyer med hjälp av View Designer i Azure Monitor
Med hjälp av View Designer i Azure Monitor kan du skapa en mängd olika anpassade vyer i Azure Portal som kan hjälpa dig att visualisera data i din Log Analytics-arbetsyta. Den här artikeln innehåller en översikt över Visa designer och procedurer för att skapa och redigera anpassade vyer.

> [!IMPORTANT]
> Vyer i Azure Monitor har övergått till [arbets böcker](workbooks-overview.md) som tillhandahåller ytterligare funktioner. Mer information om hur du konverterar befintliga vyer till arbets böcker finns i [Azure Monitor Visa designer till arbets böcker över gångs guide](view-designer-conversion-overview.md) .
 


Mer information om View Designer finns i:

* [Panel referens](view-designer-tiles.md): innehåller en referens guide för inställningarna för var och en av de tillgängliga panelerna i dina anpassade vyer.
* [Referens för visualiserings del](view-designer-parts.md): innehåller en referens guide för inställningarna för de visualiserings delar som är tillgängliga i dina anpassade vyer.


## <a name="concepts"></a>Begrepp
Vyer visas på sidan Azure Monitor **Översikt** i Azure Portal. Öppna den här sidan från **Azure Monitor** -menyn genom att klicka på **mer** under avsnittet om **insikter** . Panelerna i varje anpassad vy visas i alfabetisk ordning och panelerna för övervaknings lösningarna installeras på samma arbets yta.

![Översiktssidan](media/view-designer/overview-page.png)

Vyerna som du skapar med View Designer innehåller de element som beskrivs i följande tabell:

| Del | Beskrivning |
|:--- |:--- |
| Ikoner | Visas på din Azure Monitor **översikts** sida. Varje panel visar en visuell Sammanfattning av den anpassade vyn som den representerar. Varje panel typ innehåller en annan visualisering av dina poster. Du väljer en panel för att visa en anpassad vy. |
| Anpassad vy | Visas när du väljer en panel. Varje vy innehåller en eller flera visualiserings delar. |
| Visualiserings delar | Presentera en visualisering av data i Log Analytics arbets ytan baserat på en eller flera [logg frågor](../logs/log-query-overview.md). De flesta delar innehåller ett sidhuvud, som innehåller en visualisering på hög nivå, och en lista som visar de främsta resultaten. Varje Deltyp innehåller en annan visualisering av posterna i arbets ytan Log Analytics. Du väljer element i delen för att utföra en logg fråga som innehåller detaljerade poster. |

## <a name="required-permissions"></a>Behörigheter som krävs
Du måste ha behörighet för minst [deltagar nivå](../logs/manage-access.md#manage-access-using-azure-permissions) på arbets ytan Log Analytics för att kunna skapa eller ändra vyer. Om du inte har den här behörigheten visas inte alternativet Visa designer i menyn.


## <a name="work-with-an-existing-view"></a>Arbeta med en befintlig vy
Vyer som har skapats med View Designer visar följande alternativ:

![Översikts meny](media/view-designer/overview-menu.png)

Alternativen beskrivs i följande tabell:

| Alternativ | Beskrivning |
|:--|:--|
| Uppdatera   | Uppdaterar vyn med den senaste informationen. | 
| Loggar      | Öppnar [Log Analytics](../logs/log-query-overview.md) för att analysera data med logg frågor. |
| Redigera       | Öppnar vyn i View Designer för att redigera innehållet och konfigurationen.  |
| Klona      | Skapar en ny vy och öppnar den i Visa designer. Namnet på den nya vyn är detsamma som det ursprungliga namnet, men med *kopia* bifogat. |
| Datumintervall | Ange datum-och tidsintervall filter för de data som ingår i vyn. Det här datum intervallet används före eventuella datum intervall som anges i frågor i vyn.  |
| +          | Definiera ett anpassat filter som definierats för vyn. |


## <a name="create-a-new-view"></a>Skapa en ny vy
Du kan skapa en ny vy i View Designer genom att välja **Visa designer** på menyn i Log Analytics arbets ytan.

![Visa design panel](media/view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Arbeta med View Designer
Du kan använda Visa designer för att skapa nya vyer eller redigera befintliga. 

View Designer har tre fönster: 
* **Design**: innehåller den anpassade vy som du skapar eller redigerar. 
* **Kontroller**: innehåller de paneler och delar som du lägger till i **design** fönstret. 
* **Egenskaper**: visar egenskaperna för panelerna eller de valda delarna.

![Vydesigner](media/view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurera panelen Visa
En anpassad vy kan bara ha en panel. Om du vill visa den aktuella panelen eller välja en annan, väljer du fliken **panel** i **kontroll** fönstret. I fönstret **Egenskaper** visas egenskaperna för den aktuella panelen. 

Du kan konfigurera panel egenskaperna enligt informationen i [panel referensen](view-designer-tiles.md) och sedan klicka på **Använd** för att spara ändringarna.

### <a name="configure-the-visualization-parts"></a>Konfigurera visualiserings delarna
En vy kan innehålla valfritt antal visualiserings delar. Om du vill lägga till delar i en vy väljer du fliken **Visa** och väljer sedan en visualiserings del. I fönstret **Egenskaper** visas egenskaperna för den markerade delen. 

Du kan konfigurera visnings egenskaperna enligt informationen i [visualiserings dels referensen](view-designer-parts.md) och sedan klicka på **Använd** för att spara ändringarna.

Vyer har bara en rad med visualiserings delar. Du kan arrangera om de befintliga delarna genom att dra dem till en ny plats.

Du kan ta bort en visualiserings del från vyn genom att välja **X** längst upp till höger i delen.


### <a name="menu-options"></a>Meny alternativ
Alternativen för att arbeta med vyer i redigerings läge beskrivs i följande tabell.

![Redigera-menyn](media/view-designer/edit-menu.png)

| Alternativ | Beskrivning |
|:--|:--|
| Spara        | Sparar ändringarna och stänger vyn. |
| Avbryt      | Ignorerar ändringarna och stänger vyn. |
| Ta bort vy | Tar bort vyn. |
| Exportera      | Exporterar vyn till en [Azure Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) som du kan importera till en annan arbets yta. Namnet på filen är namnet på vyn och har ett *omsview* -tillägg. |
| Importera      | Importerar *omsview* -filen som du exporterade från en annan arbets yta. Den här åtgärden skriver över konfigurationen för den befintliga vyn. |
| Klona       | Skapar en ny vy och öppnar den i Visa designer. Namnet på den nya vyn är detsamma som det ursprungliga namnet, men med *kopia* bifogat. |

## <a name="next-steps"></a>Nästa steg
* Lägg till [paneler](view-designer-tiles.md) i den anpassade vyn.
* Lägg till [visualiserings delar](view-designer-parts.md) i din anpassade vy.