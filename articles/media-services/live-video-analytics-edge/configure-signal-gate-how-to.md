---
title: Konfigurera en signal grind för händelse-baserad videoinspelning – Azure
description: Den här artikeln innehåller anvisningar om hur du konfigurerar en signal-grind i ett medie diagram.
ms.topic: how-to
ms.date: 11/3/2020
ms.openlocfilehash: afcec7c03f1353f08b58311278f5a533e0c911bc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94410801"
---
# <a name="configure-a-signal-gate-for-event-based-video-recording"></a>Konfigurera en signal grind för händelse-baserad videoinspelning

I ett medie diagram kan du med en [signal grind processor](media-graph-concept.md#signal-gate-processor) kan du vidarebefordra media från en nod till en annan när porten utlöses av en händelse. När den utlöses öppnas porten och den tillåter medie flödet genom en angiven varaktighet. Om händelser skulle utlösa porten stängs porten och mediet slutar att flöda. Du kan använda signal porten processor för händelsebaserade videoinspelningar.

I den här artikeln får du lära dig hur du konfigurerar en signal grind processor.

## <a name="suggested-prereading"></a>Föreslagen för inläsning
-   [Mediegraf](media-graph-concept.md)
-   [Händelsebaserad videoinspelning](event-based-video-recording-concept.md)


## <a name="problem"></a>Problem
En användare kanske vill starta inspelningen vid en viss tidpunkt före eller efter att porten utlöses av en händelse. Användaren känner till acceptabel latens i systemet. Det innebär att du vill ange svars tiden för signal grind processorn. De vill också ange lägsta och högsta varaktighet för registreringen, oavsett hur många nya händelser som tas emot.
 
### <a name="use-case-scenario"></a>Användningsfallsscenario
Anta att du vill spela in video varje gång den första dörren i din byggnad öppnas. Du vill att inspelningen ska: 

- Ta med *X* sekunder innan dörren öppnas. 
- Senast minst *Y* sekunder om dörren inte har öppnats igen. 
- Senaste *Z* sekunderna om dörren har öppnats upprepade gånger. 
 
Du vet att dörr sensorn har en fördröjning på *K* sekunder. För att minska risken för att händelser ignoreras som sena ankomst, vill du tillåta minst *K* sekunder innan händelserna tas emot.


## <a name="solution"></a>Lösning

Åtgärda problemet genom att ändra processor parametrarna för signal porten.

Om du vill konfigurera en signal grind processor använder du följande fyra parametrar:
- Utvärderings period för aktivering
- Förskjutning av aktiverings signal
- Minsta aktiverings period
- Maximalt aktiverings fönster

När signal porten processor utlöses förblir den öppen för den minsta aktiverings tiden. Aktiverings händelsen börjar vid tidsstämpeln för den tidigaste händelsen, plus förskjutningen för aktiverings signalen. 

Om signal porten processor utlöses igen när den är öppen, återställs timern och porten förblir öppen under minst den minsta aktiverings tiden. Signal porten förblir inte längre öppen än den maximala aktiverings tiden. 

En händelse (händelse 1) som inträffar före en annan händelse (händelse 2), baserat på mediets tidsstämplar, kan ignoreras om systemets lags och händelse 1 anländer till signalens grind processor efter händelse 2. Om händelse 1 inte anländer mellan ankomsten av händelse 2 och fönstret för aktiverings utvärdering, ignoreras händelse 1. Den skickas inte via signal grind processorn. 

Korrelations-ID: n anges för varje händelse. Dessa ID: n anges från den inledande händelsen. De är sekventiella för varje följande händelse.

> [!IMPORTANT]
> Medie tiden baseras på mediets tidsstämpel när en händelse inträffar i mediet. Sekvensen av händelser som kommer till signal porten kanske inte återspeglar sekvensen av händelser som anländer till medie tiden.


### <a name="parameters-based-on-the-physical-time-that-events-arrive-at-the-signal-gate"></a>Parametrar, baserat på den fysiska tid som händelser anländer till signal porten

* **minimumActivationTime (kortaste varaktighet för en inspelning)**: det minsta antal sekunder som signal porten är öppen när den utlöses för att ta emot nya händelser, om den inte avbryts av maximumActivationTime.
* **maximumActivationTime (längsta möjliga varaktighet för en inspelning)**: det maximala antalet sekunder från den första händelsen som signal grind processorn är öppen efter att ha utlösts för att ta emot nya händelser, oavsett vilka händelser som tas emot.
* **activationSignalOffset**: antalet sekunder mellan aktivering av signalens grind processor och video inspelningens start. Normalt är det här värdet negativt eftersom det startar inspelningen innan händelsen utlöses.
* **activationEvaluationWindow**: med början från den inledande Utlös ande händelsen, måste antalet sekunder som en händelse som inträffat före den inledande händelsen, i medie tid, tas emot vid signalen om signal-grind innan den ignoreras och betraktas som en sent ankomst.

> [!NOTE]
> En *sent införsel* är en händelse som anländer efter att utvärderings perioden för aktivering har passerat, men som kommer före den inledande händelsen i medie tid.

### <a name="limits-of-parameters"></a>Parameter gränser

* **activationEvaluationWindow**: 0 sekunder till 10 sekunder
* **activationSignalOffset**:-1 minut till 1 minut
* **minimumActivationTime**: 1 sekund till 1 timme
* **maximumActivationTime**: 1 sekund till 1 timme


I användnings fallet ställer du in parametrarna enligt följande:

* **activationEvaluationWindow**: *K* sekunder
* **activationSignalOffset**: *-X* sekunder
* **minimumActivationWindow**: *Y* sekunder
* **maximumActivationWindow**: *Z* sekunder


Här är ett exempel på hur avsnittet **signal-processor** -noden ska se ut i en Media Graph-topologi för följande parameter värden:
* **activationEvaluationWindow**: 1 sekund
* **activationSignalOffset**:-5 sekunder
* **minimumActivationTime**: 20 sekunder
* **maximumActivationTime**: 40 sekunder

> [!IMPORTANT]
> [Varaktighets formatet i ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations
) förväntas för varje parameter värde. Till exempel PT1S = 1 sekund.


```
"processors":              
[
          {
            "@type": "#Microsoft.Media.MediaGraphSignalGateProcessor",
            "name": "signalGateProcessor",
            "inputs": [
              {
                "nodeName": "iotMessageSource"
              },
              {
                "nodeName": "rtspSource"
              }
            ],
            "activationEvaluationWindow": "PT1S",
            "activationSignalOffset": "-PT5S",
            "minimumActivationTime": "PT20S",
            "maximumActivationTime": "PT40S"
          }
]
```


Överväg nu hur den här signal porten processor konfiguration fungerar i olika registrerings scenarier.

### <a name="recording-scenarios"></a>Registrera scenarier

**En händelse från en källa (*Normal aktivering*)**

En signal grind processor som tar emot en händelse resulterar i en inspelning som påbörjar 5 sekunder (aktiverings signal = 5 sekunder) innan händelsen anländer till porten. Resten av inspelningen är 20 sekunder (minsta aktiverings tid = 20 sekunder) eftersom inga andra händelser kommer före slutet av den minsta aktiverings tiden för att utlösa porten igen.

Exempel diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/normal-activation.svg" alt-text="Diagram som visar normal aktivering av en händelse från en källa.":::

* Varaktighet för inspelning =-offset + minimumActivationTime = [E1 + offset, E1 + minimumActivationTime]


**Två händelser från en källa (*Återaktiverad aktivering*)**

En signal grind processor som tar emot två händelser resulterar i en inspelning som börjar 5 sekunder (aktiverings signal förskjutning = 5 sekunder) innan händelsen anländer till porten. Händelse 2 kommer också 5 sekunder efter händelse 1. Eftersom händelse 2 tar emot innan den minsta aktiverings tiden på händelse 1 (20 sekunder) utlöses porten. Resten av inspelningen är 20 sekunder (minsta aktiverings tid = 20 sekunder) eftersom inga andra händelser kommer före slutet av den minsta aktiverings tiden från händelse 2 för att utlösa porten igen.

Exempel diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/retriggering-activation.svg" alt-text="Diagram som visar den återaktiverade aktiveringen av två händelser från en källa.":::

* Varaktighet för inspelning =-offset + (ankomst av händelse 2 – ankomst av händelse 1) + minimumActivationTime


***N* händelser från en källa (*maximal aktivering*)**

En signal grind processor som tar emot *N* händelser resulterar i en inspelning som börjar 5 sekunder (aktivering av signal förskjutning = 5 sekunder) innan den första händelsen kommer till porten. När varje händelse tas emot före slutet av den minsta aktiverings tiden på 20 sekunder från föregående händelse utlöses porten kontinuerligt. Den förblir öppen tills den maximala aktiverings tiden på 40 sekunder har infallit efter den första händelsen. Sedan stängs porten och accepterar inte längre några nya händelser.

Exempel diagram:
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/configure-signal-gate-how-to/maximum-activation.svg" alt-text="Diagram som visar maximal aktivering av N händelser från en källa.":::
 
* Varaktighet för inspelning =-offset + maximumActivationTime

> [!IMPORTANT]
> Föregående diagram förutsätter att varje händelse kommer till samma tidpunkt för fysisk tid och media tid. Det innebär att de förutsätter att det inte finns några sena ankomster.

## <a name="next-steps"></a>Nästa steg

Prova den [event-baserade videoinspelnings guiden](event-based-video-recording-tutorial.md). Börja med att redigera [topology.jspå](https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-hubMessage-assets/topology.json). Ändra parametrarna för signalgateProcessor-noden och följ resten av självstudien. Granska videoinspelningarna och analysera parametrarna.



