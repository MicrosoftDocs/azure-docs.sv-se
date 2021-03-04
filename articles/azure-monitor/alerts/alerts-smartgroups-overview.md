---
title: Smarta grupper
description: Smarta grupper är agg regeringar för aviseringar som hjälper dig att minska aviserings bruset
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: 8a164fe6e5fd5be58da969d9266329755705ea82
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037973"
---
# <a name="smart-groups"></a>Smarta grupper

En vanlig utmaning vid hantering av aviseringar är att gå igenom bruset och ta reda på vad som egentligen är viktigt – smarta grupper är avsedda att vara lösningen på det problemet.  

Smarta grupper skapas automatiskt med hjälp av Machine Learning-algoritmer för att kombinera relaterade aviseringar som representerar ett enda problem.  När en avisering skapas lägger algoritmen till den i en ny smart grupp eller en befintlig smart grupp baserat på information som historiska mönster, liknande egenskaper och liknande struktur. Om till exempel% CPU på flera virtuella datorer i en prenumeration samtidigt stöter till många enskilda aviseringar, och om sådana aviseringar har inträffat när som helst tidigare, kommer de här aviseringarna troligen att grupperas i en enda smart grupp, vilket ger en potentiell allmän rotorsak. Det innebär att för någon fel sökning av aviseringar kan smarta grupper inte bara tillåta att de minskar bruset genom att hantera relaterade aviseringar som en enda sammanställd enhet, men de guidar också dem mot möjliga vanliga Rotors Aker för sina aviseringar.

För närvarande tar algoritmen bara hänsyn till aviseringar från samma övervakare i en prenumeration. Smarta grupper kan minska upp till 99% av varnings bruset genom denna konsolidering. Du kan visa orsaken till att aviseringar inkluderades i en grupp på sidan information om smart grupp.

Du kan visa information om smarta grupper och ange tillstånd på samma sätt som du kan med aviseringar. Varje avisering är medlem i en och endast en smart grupp. 

## <a name="smart-group-state"></a>Smart Group-tillstånd

Smart Group State är ett liknande koncept som aviserings tillstånd, vilket gör att du kan hantera lösnings processen på nivån i en smart grupp. På liknande sätt som aviserings tillstånd, när en smart grupp skapas, har den det **nya** läget, som kan ändras till antingen **godkänd** eller **stängd**.

Följande Smart Group-tillstånd stöds.

| Stat | Beskrivning |
|:---|:---|
| Ny | Problemet har precis identifierats och har ännu inte granskats. |
| Bekräftad | En administratör har granskat den smarta gruppen och börjat arbeta med den. |
| Stängd | Problemet har åtgärd ATS. När en smart grupp har stängts kan du öppna den igen genom att ändra den till ett annat tillstånd. |

[Lär dig hur du ändrar status för din smart grupp.](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)

> [!NOTE]
>  Att ändra status för en smart grupp ändrar inte statusen för de enskilda medlems aviseringarna.

## <a name="smart-group-details-page"></a>Informations sida för smart grupp

Sidan smart grupp information visas när du väljer en smart grupp. Den innehåller information om den smarta gruppen, inklusive de orsaker som användes för att skapa gruppen och gör att du kan ändra dess tillstånd.
 
![Information om smart grupp](media/alerts-smartgroups-overview/smart-group-detail.png)


Informations sidan för smart grupp innehåller följande avsnitt.

| Avsnitt | Beskrivning |
|:---|:---|
| Aviseringar | Visar en lista över enskilda aviseringar som ingår i den smarta gruppen. Välj en avisering för att öppna sidan aviserings information. |
| Historik | Visar en lista över varje åtgärd som utförs av den smarta gruppen och eventuella ändringar som görs i den. Detta är för närvarande begränsat för att ge status ändringar och ändringar i varnings medlemskap. |

## <a name="smart-group-taxonomy"></a>Smart Group-taxonomi

Namnet på en smart grupp är namnet på den första aviseringen. Du kan inte skapa eller byta namn på en smart grupp.

## <a name="next-steps"></a>Nästa steg

- [Hantera smarta grupper](./alerts-managing-smart-groups.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
- [Ändra din status för avisering och smart grupp](./alerts-managing-alert-states.md?toc=%2fazure%2fazure-monitor%2ftoc.json)