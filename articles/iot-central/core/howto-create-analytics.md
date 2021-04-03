---
title: Analysera enhets data i ditt Azure IoT Central-program | Microsoft Docs
description: Analysera enhets data i ditt Azure IoT Central-program.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 8f329b6377915a3df6e3f3e212cbd8d41f9ed80d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90017668"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Använda analys för att analysera enhets data

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*

Azure IoT Central ger omfattande analys funktioner för att analysera historiska trender och korrelera olika telemetrivärden från dina enheter. Kom igång genom att gå till **Analytics** i det vänstra fönstret.

## <a name="understanding-the-analytics-ui"></a>Förstå användar gränssnittet för analys
Analytics User Interface består av tre huvud komponenter:
- **Data konfigurations panel:** På konfigurations panelen startar du genom att välja den enhets grupp som du vill analysera data för. Välj sedan den telemetri som du vill analysera och välj agg regerings metoden för varje telemetri. **Dela med** kontroll hjälper till att gruppera data med hjälp av enhets egenskaperna som dimensioner.

- **Tids kontroll:** Tids kontroll används för att välja varaktigheten för vilken du vill analysera data. Du kan dra i endera änden av skjutreglaget för att välja tids rymden. Tids kontrollen har också ett skjutreglage för **intervall storlek** som styr Bucket eller intervall storleken som används för att samla in data. 

- **Diagram kontroll:** Diagram kontrollen visualiserar data som ett linje diagram. Du kan växla synligheten för vissa linjer genom att interagera med diagram förklaringen. 


  ![Översikt över Analytics UI](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Fråga dina data

Du måste starta genom att välja en **enhets grupp** och den telemetri som du vill analysera. När du är klar väljer du **analysera** för att börja visualisera dina data.

- **Enhets grupp:** En [enhets grupp](tutorial-use-device-groups.md) är en användardefinierad grupp av dina enheter. Till exempel alla kyl skåp i Oakland eller alla version 2,0 lindnings turbiner.

- **Telemetri:** Välj den telemetri som du vill analysera och utforska. Du kan välja flera telemetrivärden för att analysera varandra. Standard agg regerings metoden har angetts till Average för numeriskt och antal för sträng data typerna. Sammansättnings metoder som stöds för numeriska data typer är genomsnitt, högsta, lägsta, antal och sum.  Sammansättnings metoder som stöds för data typen String är Count.

- **Dela efter:** "Dela med"-kontrollen hjälper till att gruppera data med hjälp av enhets egenskaperna som dimensioner. Värdena för enhets-och moln egenskaperna är anslutna tillsammans med Telemetrin som och när den skickas av enheten. Om molnet eller enhets egenskapen har uppdaterats visas telemetri grupperat efter olika värden i diagrammet.

    > [!TIP]
    > Om du vill visa data för varje enhet separat väljer du enhets-ID i kontrollen "dela efter".

## <a name="interacting-with-your-data"></a>Interagera med dina data

När du har frågat dina data kan du börja visualisera dem i linje diagrammet. Du kan visa/dölja telemetri, ändra varaktigheten för tiden, Visa telemetri i ett data rutnät.

- **Tids redigerings panel:** Som standard hämtar vi data från den senaste dagen. Du kan dra i båda ändarna av skjutreglaget för att ändra varaktigheten för tiden. Du kan också använda Kalender kontrollen för att välja en av de fördefinierade tidsintervallen eller välja ett anpassat tidsintervall. Tids kontrollen har också ett skjutreglage för **intervall storlek** som styr Bucket eller intervall storleken som används för att samla in data.

    ![Tids redigeraren](media/howto-create-analytics/timeeditorpanel.png)

    - **Skjutreglaget för inre datum intervall**: Använd de två slut punkts kontrollerna genom att dra dem under den tidsrymd du vill ha. Det här interna datum intervallet begränsas av skjutreglaget för yttre datum intervall.
    
   
    - **Skjutreglage för yttre datum intervall**: Använd slut punkts kontrollerna för att välja det yttre datum intervallet, som är tillgängligt för den interna datum intervalls kontrollen.

    - **Öka och minska datum intervall knappar**: öka eller minska din tids period genom att välja någon av knapparna för det intervall du vill ha.

    - **Skjutreglage för intervall storlek**: Använd det för att zooma in och ut ur intervall över samma tids period. Den här åtgärden ger en mer exakt kontroll av rörelsen mellan stora sektorer av tiden. Du kan använda den för att visa detaljerade och högupplösta vyer över dina data, till och med millisekunder. Skjutreglagets standard start punkt anges som den mest optimala vyn av data från ditt val, som balanserar lösning, frågans hastighet och granularitet.
    
    - **Väljare för datum intervall**: med den här webb kontrollen kan du enkelt välja de datum-och tidsintervall som du vill använda. Du kan också använda kontrollen för att växla mellan olika tids zoner. När du har gjort ändringarna som ska gälla för din aktuella arbets yta väljer du spara.

    > [!TIP]
    > Intervall storleken bestäms dynamiskt baserat på det valda tidsintervallet. Kortare tidsintervaller gör det möjligt att aggregera data i mycket detaljerade intervall på upp till några sekunder.


- **Diagram förklaring:** Diagram förklaring visar den valda Telemetrin i diagrammet. Du kan hovra över varje objekt på förklaringen så att det blir i fokus på diagrammet. När du använder "Split by" grupperas telemetri efter respektive värde för den valda dimensionen. Du kan växla synligheten för varje speciell telemetri eller hela gruppen genom att klicka på grupp namnet.  


- **Kontroll av y-axelns format:** y-axelns läge växlar mellan tillgängliga visnings alternativ för y-axel. Den här kontrollen är bara tillgänglig när olika telemetrivärden visualiseras. Du kan ställa in y-axeln genom att välja ett av tre lägen:

    - **Staplad:** En graf för varje telemetri är staplad och varje graf har sin egen y-axel. Det här läget anges som standard.
    - **Delad:** En graf för varje telemetri ritas mot samma y-axel.
    - **Överlappa:** Använd den för att stapla flera rader på samma y-axel, med data för y-axeln som har ändrats baserat på den markerade linjen.

  ![Arrangera data över y-axeln med olika visualiserings lägen](media/howto-create-analytics/yaxiscontrol.png)

- **Zoomnings kontroll:** Med zoomning kan du öka detalj nivån i dina data. Om du hittar en tids period som du vill fokusera på i din resultat uppsättning använder du mus pekaren för att hämta området och drar det sedan till den valda slut punkten. Högerklicka på det valda fältet och klicka på Zooma.

  ![Zooma in data](media/howto-create-analytics/zoom.png)

Under ellipsen finns det flera diagram kontroller för att interagera med data:

- **Visa rutnät:** Resultaten är tillgängliga i ett tabell format så att du kan se det exakta värdet för varje data punkt.

- **Hämta som CSV:** Resultaten är tillgängliga för export som en fil med kommaavgränsade värden (CSV). CSV-filen innehåller data för varje enhet. Resultaten exporteras med angivet intervall och tidsram. 

- **Släpp en markör:** Med släpp markör-kontrollen kan du fästa vissa data punkter i diagrammet. Det är användbart när du försöker jämföra data för flera rader över olika tids perioder.

  ![Visar diagramvyn för analys](media/howto-create-analytics/additionalchartcontrols.png)
