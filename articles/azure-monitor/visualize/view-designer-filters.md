---
title: Filter i Azure Monitor vyer | Microsoft Docs
description: Ett filter i en Azure Monitor-vy låter användare filtrera data i vyn med värdet för en viss egenskap utan att ändra vyn.  I den här artikeln beskrivs hur du använder ett filter och lägger till ett i en anpassad vy.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/22/2018
ms.openlocfilehash: 988bb1467e7bf2cca31151d85c57677b235cab30
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043311"
---
# <a name="filters-in-azure-monitor-views"></a>Filter i Azure Monitor vyer
Ett **filter** i en [Azure Monitor-vy](view-designer.md) låter användare filtrera data i vyn med värdet för en viss egenskap utan att ändra vyn.  Du kan till exempel tillåta att användare av vyn filtrerar vyn för data enbart från en viss dator eller uppsättning datorer.  Du kan skapa flera filter i en enskild vy så att användarna kan filtrera efter flera egenskaper.  I den här artikeln beskrivs hur du använder ett filter och lägger till ett i en anpassad vy.

## <a name="using-a-filter"></a>Använda ett filter
Klicka på datum tidsintervallet överst i vyn för att öppna List rutan där du kan ändra datum tidsintervallet för vyn.

![Skärm bild av list rutan tidsintervall för en vy i Azure Monitor, med alternativ knappen för de senaste 7 dagarna markerade.](media/view-designer-filters/filters-example-time.png)

Klicka på **+** för att lägga till ett filter med anpassade filter som har definierats för vyn. Välj antingen ett värde för filtret i list rutan eller ange ett värde. Fortsätt att lägga till filter genom att klicka på **+** . 


![Skärm bild av dialog rutan för att lägga till ett anpassat filter i Azure Monitor. Egenskapen datorer väljs i list rutan Välj egenskaper.](media/view-designer-filters/filters-example-custom.png)

Om du tar bort alla värden för ett filter kommer filtret inte längre att tillämpas.


## <a name="creating-a-filter"></a>Skapa ett filter

Skapa ett filter från fliken **filter** när du [redigerar en vy](view-designer.md).  Filtret är globalt för vyn och gäller för alla delar i vyn.  

![Filterinställningar](media/view-designer-filters/filters-settings.png)

I följande tabell beskrivs inställningarna för ett filter.

| Inställning | Beskrivning |
|:---|:---|
| Fältnamn | Namnet på det fält som används för filtrering.  Det här fältet måste matcha det sammanfattande fältet i **fråga efter värden**. |
| Fråga efter värden | Fråga att köra för att fylla i list rutan filter för användaren.  Frågan måste använda antingen [sammanfatta](/azure/kusto/query/summarizeoperator) eller [distinkt](/azure/kusto/query/distinctoperator) för att tillhandahålla unika värden för ett visst fält och måste matcha **fält namnet**.  Du kan använda [sort](/azure/kusto/query/sortoperator) för att sortera värdena som visas för användaren. |
| Tagg | Namnet på det fält som används i frågor som stöder filtret och visas också för användaren. |

### <a name="examples"></a>Exempel

Följande tabell innehåller några exempel på vanliga filter.  

| Fältnamn | Fråga efter värden | Tagg |
|:--|:--|:--|
| Dator   | Pulsslag &#124; distinkt dator &#124; sortera efter dator ASC | Datorer |
| EventLevelName | Event &#124; DISTINCT EventLevelName | Allvarlighetsgrad |
| SeverityLevel | Syslog &#124; DISTINCT SeverityLevel | Allvarlighetsgrad |
| SvcChangeType | ConfigurationChange &#124; distinkt svcChangeType | Ändrings typs |


## <a name="modify-view-queries"></a>Ändra visnings frågor

För att ett filter ska ha någon påverkan måste du ändra alla frågor i vyn för att filtrera efter de valda värdena.  Om du inte ändrar några frågor i vyn, kommer alla värden som användaren väljer att ha ingen påverkan.

Syntaxen för att använda ett filter värde i en fråga är: 

`where ${filter name}`  

Om din vy till exempel har en fråga som returnerar händelser och använder ett filter som kallas _datorer_, kan du använda följande fråga.

```kusto
Event | where ${Computers} | summarize count() by EventLevelName
```

Om du har lagt till ett annat filter som heter allvarlighets grad kan du använda följande fråga för att använda båda filtren.

```kusto
Event | where ${Computers} | where ${Severity} | summarize count() by EventLevelName
```

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om de [visualiserings delar](view-designer-parts.md) som du kan lägga till i din anpassade vy.
