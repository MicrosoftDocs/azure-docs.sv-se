---
title: Självstudie – Skapa ett mått diagram i Azure Monitor
description: Lär dig hur du skapar ett mått diagram med Azure Metrics Explorer.
author: bwren
ms.author: bwren
ms.subservice: metrics
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 121de67ddb251e7431873c76e379eb223ceacd0d
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250680"
---
# <a name="tutorial-create-a-metrics-chart-in-azure-monitor"></a>Självstudie: skapa ett mått diagram i Azure Monitor
Mått Utforskaren är en funktion i Azure Monitor i Azure Portal som gör att du kan skapa diagram från metriska värden, visuellt korrelera trender och undersöka toppar och DIP i metriska värden. Använd Mät Utforskaren för att undersöka hälsan och användningen av dina Azure-resurser eller för att rita diagram från anpassade mått. 

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Välj ett mått för vilket du vill rita ett diagram
> * Utföra olika agg regeringar för Mät värden
> * Ändra tidsintervallet och granularitet för diagrammet

Följande är en video som visar ett mer omfattande scenario än proceduren som beskrivs i den här artikeln. Om du inte har använt måtten igen rekommenderar vi att du läser igenom den här artikeln först och sedan visar videon för att se mer information. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du en Azure-resurs för att övervaka. Du kan använda vilken resurs som helst i din Azure-prenumeration som har stöd för mått. Ta reda på om en resurs stöder mått genom att gå till menyn i Azure Portal och kontrol lera att det finns ett **mått** alternativ i avsnittet **övervakning** på menyn.


## <a name="log-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com) .

## <a name="open-metrics-explorer-and-select-a-scope"></a>Öppna Metrics Explorer och välj ett omfång
Du kan öppna Metrics Explorer antingen från Azure Monitor-menyn eller från en resurs meny i Azure Portal. Mått från alla resurser är tillgängliga oavsett vilket alternativ du använder. 

1. Välj **mått** på **Azure Monitor** -menyn eller från **övervaknings** avsnittet på en resurs meny.

1. Välj **omfång**, som är den resurs som du vill visa mått för. Området är redan ifyllt om du öppnade Metrics Explorer från en resurs meny. Om du vill veta mer om de olika funktionerna i resurs omfattnings väljaren går du till [den här artikeln](../platform/metrics-charts.md#resource-scope-picker).

    ![Välj ett omfång](media/tutorial-metrics-explorer/scope-picker.png)

2. Välj ett **namn område** om omfattningen har fler än ett. Namn området är bara ett sätt att organisera mått så att du lätt kan hitta dem. Lagrings konton har till exempel separata namn rymder för lagring av filer, tabeller, blobbar och köers mått. Många resurs typer har bara ett namn område.

3. Välj ett mått från en lista över tillgängliga mått för det valda omfånget och namn området.

    ![Välj ett mått](media/tutorial-metrics-explorer/metric-picker.png)

4. Du kan också ändra mått **agg regeringen**. Detta definierar hur Metric-värden ska aggregeras över tids kornig het för grafen. Om tids kornigheten exempelvis är inställt på 15 minuter och agg regeringen är inställd på sum, kommer varje punkt i diagrammet att vara summan av alla insamlade värden under varje 15-minuters segment.

    ![Skärm bild som visar ett diagram med rubriken Summa ingress för contosoretailweb.](media/tutorial-metrics-explorer/chart.png)

5. Använd knappen **Lägg till mått** och upprepa de här stegen om du vill se flera mått som är ritade i samma diagram. För flera diagram i en vy väljer du knappen **nytt diagram** .

## <a name="select-a-time-range-and-granularity"></a>Välj tidsintervall och granularitet

Som standard visar diagrammet de senaste 24 timmarna med mät data. Använd tid väljaren för att ändra **tidsintervallet** för diagrammet eller **tids kornig het** som definierar tidsintervallet för varje data punkt. Diagrammet använder den angivna agg regeringen för att beräkna alla exempel värden under angiven tids kornig het.

![Ändra tids områdets panel](media/tutorial-metrics-explorer/time-picker.png)


Använd **tids penseln** för att undersöka ett intressant ytdiagram i diagrammet, till exempel en insamling eller en DIP. Placera mus pekaren i början av ytan, klicka och håll ned vänster MUSKNAPP, dra till den andra sidan av arean och släpp knappen. Diagrammet kommer att zooma in inom det tidsintervallet. 

![Tids pensel](media/tutorial-metrics-explorer/time-brush.png)

## <a name="apply-dimension-filters-and-splitting"></a>Använd dimensions filter och delning
Se följande referenser för avancerade funktioner som gör att du kan utföra ytterligare analyser av dina mått och identifiera potentiella avvikare i dina data.

- Med [filtrering](../platform/metrics-charts.md#filters) kan du välja vilka dimensions värden som ska ingå i diagrammet. Du kanske till exempel bara vill visa lyckade förfrågningar när du diagramerar ett mått för *Server svars tid* . 

- [Dela upp](../platform/metrics-charts.md#apply-splitting) styr om diagrammet ska visa separata rader för varje värde i en dimension eller mängd värden i en enda rad. Till exempel kanske du vill se en rad för en genomsnittlig svars tid över alla Server instanser eller om du vill ha separata rader för varje server. 

Se [exempel på de diagram](../platform/metric-chart-samples.md) som har filtrering och delning tillämpad.

## <a name="advanced-chart-settings"></a>Avancerade diagram inställningar

Du kan anpassa diagram stil, rubrik och ändra avancerade diagram inställningar. När du är färdig med anpassning fäster du det på en instrument panel för att spara ditt arbete. Du kan också konfigurera mått aviseringar. I [avancerade funktioner i Azure Metrics Explorer](../platform/metrics-charts.md#locking the-range-of-the-y-axis) kan du läsa mer om dessa och andra avancerade funktioner i Azure Monitor Metrics Explorer.


## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du arbetar med mått i Azure Monitor kan du lära dig hur du använder mått för att skicka proaktiva aviseringar.

> [!div class="nextstepaction"]
> [Skapa, visa och hantera måttaviseringar med Azure Monitor](../platform/metrics-charts.md#alert-rules)

