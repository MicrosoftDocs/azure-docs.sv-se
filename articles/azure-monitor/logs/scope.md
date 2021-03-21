---
title: Logg frågans omfång i Azure Monitor Log Analytics
description: Beskriver omfånget och tidsintervallet för en logg fråga i Azure Monitor Log Analytics.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/09/2020
ms.openlocfilehash: 43e4e861905352c2818dfb08b8cb442bd70481c1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047187"
---
# <a name="log-query-scope-and-time-range-in-azure-monitor-log-analytics"></a>Logg frågans omfång och tidsintervall i Azure Monitor Log Analytics
När du kör en [logg fråga](../logs/log-query-overview.md) i [Log Analytics i Azure Portal](../logs/log-analytics-tutorial.md), beror den uppsättning data som utvärderas av frågan på omfattningen och tidsintervallet som du väljer. I den här artikeln beskrivs omfattning och tidsintervall och hur du kan ställa in dem beroende på dina behov. Det beskriver också beteendet för olika typer av omfång.


## <a name="query-scope"></a>Frågeomfång
Fråge omfånget definierar de poster som utvärderas av frågan. Detta omfattar vanligt vis alla poster i en enskild Log Analytics arbets yta eller Application Insights program. Med Log Analytics kan du också ange ett omfång för en viss övervakad Azure-resurs. På så sätt kan en resurs ägare bara fokusera på sina data, även om den resursen skriver till flera arbets ytor.

Omfattningen visas alltid längst upp till vänster i Log Analyticss fönstret. En ikon visar om omfånget är en Log Analytics arbets yta eller ett Application Insights program. Ingen ikon indikerar en annan Azure-resurs.

![Omfattning som visas i portalen](media/scope/scope.png)

Omfattningen bestäms av den metod som du använder för att starta Log Analytics, och i vissa fall kan du ändra omfattningen genom att klicka på den. I följande tabell visas olika typer av omfång som används och olika Detaljer för var och en.

> [!IMPORTANT]
> Om du använder ett arbets grupps program i Application Insights lagras data i en Log Analytics arbets yta med alla andra loggdata. För bakåtkompatibilitet får du den klassiska Application Insights upplevelsen när du väljer programmet som definitions område. Ange omfånget till arbets ytan om du vill se dessa data i arbets ytan Log Analytics.

| Frågeomfång | Poster i omfånget | Så här väljer du | Ändra omfång |
|:---|:---|:---|:---|
| Log Analytics-arbetsyta | Alla poster i Log Analytics-arbetsytan. | Välj **loggar** på **Azure Monitor** -menyn eller **Log Analytics arbets ytans** meny.  | Kan ändra omfång till någon annan resurs typ. |
| Application Insights program | Alla poster i Application Insights programmet. | Välj **loggar** på menyn **Application Insights** för programmet. | Det går bara att ändra omfattningen till ett annat Application Insights-program. |
| Resursgrupp | Poster som skapats av alla resurser i resurs gruppen. Kan innehålla data från flera Log Analytics arbets ytor. | Välj **loggar** i menyn resurs grupp. | Det går inte att ändra omfång.|
| Prenumeration | Poster som skapats av alla resurser i prenumerationen. Kan innehålla data från flera Log Analytics arbets ytor. | Välj **loggar** på menyn prenumeration.   | Det går inte att ändra omfång. |
| Andra Azure-resurser | Poster som skapats av resursen. Kan innehålla data från flera Log Analytics arbets ytor.  | Välj **loggar** på resurs-menyn.<br>ELLER<br>Välj **loggar** på **Azure Monitor** -menyn och välj sedan ett nytt omfång. | Det går bara att ändra omfattningen till samma resurs typ. |

### <a name="limitations-when-scoped-to-a-resource"></a>Begränsningar vid omfattning av en resurs

När fråge omfånget är en Log Analytics arbets yta eller ett Application Insights program är alla alternativ i portalen och alla frågor tillgängliga. När den är begränsad till en resurs kan följande alternativ i portalen inte vara tillgängliga eftersom de är kopplade till en enda arbets yta eller ett program:

- Spara
- Frågeutforskaren
- Ny varnings regel

Du kan inte använda följande kommandon i en fråga som är begränsad till en resurs eftersom fråge omfånget redan innehåller arbets ytor med data för resursen eller en uppsättning resurser:

- [mobilappar](../logs/app-expression.md)
- [platsen](../logs/workspace-expression.md)
 

## <a name="query-scope-limits"></a>Omfattnings gränser för fråga
Att ange ett omfång till en resurs eller en uppsättning resurser är en särskilt kraftfull funktion i Log Analytics eftersom det gör det möjligt att automatiskt konsolidera distribuerade data i en enda fråga. Det kan påverka prestanda avsevärt om data behöver hämtas från arbets ytor i flera Azure-regioner.

Log Analytics skyddar mot onödig omkostnader från frågor som omfattar arbets ytor i flera regioner genom att skicka en varning eller ett fel när ett visst antal regioner används. Frågan får en varning om omfånget omfattar arbets ytor i 5 eller flera regioner. den kommer fortfarande att köras, men det kan ta lång tid att slutföra den.

![Fråga-varning](media/scope/query-warning.png)

Frågan kommer att blockeras från att köras om omfattningen omfattar arbets ytor i 20 eller flera regioner. I det här fallet uppmanas du att minska antalet regioner för arbets ytor och försöka köra frågan igen. I list rutan visas alla regioner i frågans omfång och du bör minska antalet regioner innan du försöker köra frågan igen.

![Frågan misslyckades](media/scope/query-failed.png)


## <a name="time-range"></a>Tidsintervall
Tidsintervallet anger den uppsättning poster som utvärderas för frågan baserat på när posten skapades. Detta definieras av kolumnen **TimeGenerated** på varje post i arbets ytan eller programmet som anges i följande tabell. För ett klassiskt Application Insights-program används **timestamp** -kolumnen för tidsintervallet.


Ange tidsintervallet genom att välja det från tids väljaren överst i Log Analyticss fönstret.  Du kan välja en fördefinierad period eller välja **anpassad** om du vill ange ett tidsintervall.

![Tids väljare](media/scope/time-picker.png)

Om du anger ett filter i frågan som använder kolumnen standard tid som visas i tabellen ovan, ändras tids väljaren till **set i Query** och tids väljare är inaktive rad. I det här fallet är det mest effektivt att ställa in filtret överst i frågan så att all efterföljande bearbetning bara behöver arbeta med de filtrerade posterna.

![Filtrerad fråga](media/scope/query-filtered.png)

Om du använder [arbets ytan](../logs/workspace-expression.md) eller [appens](../logs/app-expression.md) kommando för att hämta data från en annan arbets yta eller ett klassiskt program kan tids väljaren bete sig annorlunda. Om omfånget är en Log Analytics arbets yta och du använder **appen**, eller om omfånget är ett klassiskt Application Insights-program och du använder **arbets ytan**, kan Log Analytics inte förstå att den kolumn som används i filtret bestämmer tids filtret.

I följande exempel är omfånget inställt på en Log Analytics-arbetsyta.  Frågan använder **arbets ytan** för att hämta data från en annan Log Analytics-arbetsyta. Tids väljaren ändras till **set i frågan** eftersom den ser ett filter som använder den förväntade **TimeGenerated** -kolumnen.

![Fråga med arbets yta](media/scope/query-workspace.png)

Om frågan använder **appen** för att hämta data från ett klassiskt Application Insights program, kan Log Analytics inte identifiera kolumnen **tidsstämpelkolumn** i filtret, och tids väljaren förblir oförändrad. I det här fallet tillämpas båda filtren. I exemplet ingår endast poster som skapats under de senaste 24 timmarna i frågan även om det anger 7 dagar i **WHERE** -satsen.

![Fråga med app](media/scope/query-app.png)

## <a name="next-steps"></a>Nästa steg

- Gå igenom en [själv studie kurs om hur du använder Log Analytics i Azure Portal](../logs/log-analytics-tutorial.md).
- Gå igenom en [själv studie kurs om att skriva frågor](../logs/get-started-queries.md).