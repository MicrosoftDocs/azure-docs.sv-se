---
title: Visualisera OPC UA-data i Azure Time Series Insights
description: I den här självstudien får du lära dig hur du visualiserar data med Time Series Insights.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 5bd218c0d94922b6137a964e3993f516216ca4b7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787813"
---
# <a name="tutorial-visualize-data-with-time-series-insights-tsi"></a>Självstudie: visualisera data med Time Series Insights (TSD)

OPC Publisher-modulen ansluter till OPC UA-servrar och publicerar data från dessa servrar till IoT Hub. Telemetri-processorn i den industriella IoT-plattformen bearbetar dessa händelser och vidarebefordrar sammanhangsbaserade prover till TSD och andra konsumenter.  

Den här instruktions guiden visar hur du kan visualisera och analysera OPC UA-telemetri med hjälp av den här Time Series Insights miljön.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Alla självstudier innehåller en lista som sammanfattar stegen för slut för ande
> * Var och en av dessa punkter justeras till en nyckel H2
> * Använd de här gröna kryss rutorna i en självstudie

## <a name="prerequisite"></a>Förutsättning

* Distribuera IIoT-plattformen för att få en Time Series Insights-miljö automatiskt skapad
* Data publiceras till IoT Hub

## <a name="time-series-insights-explorer"></a>Time Series Insights-utforskaren

Time Series Insights Explorer är en webbapp som du kan använda för att visualisera din telemetri. Om du vill hämta URL: en för programmet öppnar du `.env` filen som sparats som ett resultat av distributionen.  Öppna en webbläsare till URL: en i `PCS_TSI_URL` variabeln.  

Innan du använder Time Series Insights Explorer måste du bevilja åtkomst till TSD-data till de användare som har rätt att visualisera data. Observera att vid en ny distribution anges inga data åtkomst principer som standard, så att ingen kan se data. Data åtkomst principerna måste anges i Azure Portal, i Time Series Insightss miljön som distribueras i IIoT plattform distribuerad resurs grupp på följande sätt:

   ![Time Series Insights Explorer 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-1.png)

Välj principer för data åtkomst:

   ![Time Series Insights Explorer 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-2.png)

Tilldela de användare som krävs:

   ![Time Series Insights Explorer 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-data-access-3.png)


Notera de otilldelade tids serie instanserna i TSD-Utforskaren. En TSD-instans motsvarar tids-/värde serien för en viss data punkt som kommer från en publicerad nod i en OPC-Server. TSD-instansen, respektive OPC UA-datapunkt, identifieras unikt av EndpointId, SubscriptionId och NodeId. TSD-instansernas modeller identifieras automatiskt och visas i Utforskaren baserat på de telemetridata som matas in från IIoT Platform telemetriprocessor-processorns händelsehubben.

   ![Time Series Insights Explorer 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-0.png)

Telemetri-data kan visualiseras i diagrammet genom att högerklicka på instansen av TSD och välja värdet. Den tidsram som ska användas i diagrammet kan justeras från det övre högra hörnet. Värdet för flera instanser kan visualiseras på samma tids val.

Mer information finns i [snabb start: utforska Azure Time Series Insights för hands version](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)

## <a name="define-and-apply-a-new-model"></a>Definiera och tillämpa en ny modell

Eftersom telemetri-instanserna nu bara är i RAW-format måste de vara sammanhangsbaserade med lämpliga 

Detaljerad information om TSD-modeller finns i [tids serie modell i Azure Time Series Insights för hands version](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-tsm)

1. Steg 1 – på fliken modell i Utforskaren definierar du en ny hierarki för inmatade telemetridata. En hierarki är den logiska träd strukturen som är avsedd att göra det möjligt för användaren att infoga den meta-information som krävs för en mer intuitiv navigering genom TSD-instanserna. en användare kan skapa/ta bort/ändra mallar för hierarkier som senare kan bli instansierade för de olika TSD-instanserna.

   ![Steg 1](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-1.png)

2. Steg 2 – definiera en ny typ för värdena. I vårt exempel hanterar vi bara numeriska data typer

   ![Steg 2](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-2.png)

3. Steg 3 – Välj den nya TSD-instansen som måste kategoriseras i den tidigare definierade hierarkin

   ![Steg 3](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-3.png)

4. Steg 4 – Fyll i egenskaperna för instanser – namn, beskrivning, data värde, samt fälten för hierarkin för att matcha den logiska strukturen 

   ![Steg 4](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-4.png)

5. Steg 5 – Upprepa steg 5 för alla instanser av Okategoriserade-TSD

   ![Steg 5](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-5.png)

6. Steg 6 – tillbaka i TSD-Explorers huvud sida, gå igenom hierarkin för kategoriserade instanser och välj värden för de data punkter som ska analyseras

   ![Step6](media/tutorial-iiot-visualize-data-tsi/tutorial-time-series-insights-step-6.png)

## <a name="connect-time-series-insights-to-power-bi"></a>Anslut Time Series Insights till Power BI

Du kan också ansluta Time Series Insightss miljön till Power BI.  Mer information finns i [så här ansluter du TSD till Power BI](https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi) och [visualiserar data från TSD i Power BI](https://docs.microsoft.com/azure/time-series-insights/concepts-power-bi).


## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du visualiserar data i TSD kan du titta på GitHub-lagringsplatsen för industriella IoT:

> [!div class="nextstepaction"]
> [IIoT Platform GitHub-lagringsplats](https://github.com/Azure/iot-edge-opc-publisher)