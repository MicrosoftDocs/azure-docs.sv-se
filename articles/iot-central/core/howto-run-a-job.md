---
title: Skapa och köra jobb i ditt Azure IoT Central-program | Microsoft Docs
description: Azure IoT Central-jobb tillåter hantering av enhets hanterings funktioner, till exempel uppdaterings egenskaper eller körning av ett kommando.
ms.service: iot-central
services: iot-central
author: philmea
ms.author: philmea
ms.date: 11/19/2020
ms.topic: how-to
ms.openlocfilehash: 19d8738790b5634b9de989fa94edac6a542f85f4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94917349"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Skapa och köra ett jobb i ditt Azure IoT Central-program

Du kan använda Azure-IoT Central för att hantera dina anslutna enheter i stor skala genom att använda jobb. Med jobb kan du göra Mass uppdateringar av enhets-och moln egenskaper och köra kommandon. Den här artikeln visar hur du kommer igång med att använda jobb i ditt eget program.

## <a name="create-and-run-a-job"></a>Skapa och köra ett jobb

I följande exempel visas hur du skapar och kör ett jobb för att ställa in ett ljus tröskelvärde för en grupp logistik gateway-enheter. Du kan använda jobb guiden för att skapa och köra jobb. Du kan spara ett jobb för att köra senare.

1. I det vänstra fönstret väljer du **jobb**.

1. Välj **+ nytt jobb**.

1. På sidan **Konfigurera ditt jobb** anger du ett namn och en beskrivning för att identifiera det jobb som du skapar.

1. Välj den mål enhets grupp som du vill att jobbet ska gälla för. Du kan se hur många enheter din jobb konfiguration gäller under valet av **enhets grupp** .

1. Välj **moln egenskap**, **egenskap** eller **kommando** som **jobb typ**:

    Om du vill konfigurera ett **egenskaps** jobb väljer du en egenskap och anger dess nya värde. Om du vill konfigurera ett **kommando** jobb väljer du kommandot som ska köras. Ett egenskaps jobb kan ange flera egenskaper.

    :::image type="content" source="media/howto-run-a-job/configure-job.png" alt-text="Skärm bild som visar val för att skapa ett egenskaps jobb med namnet set Light Threshold":::

    Välj **Spara och avsluta** för att lägga till jobbet i listan över sparade jobb på sidan **jobb** . Du kan senare återgå till ett jobb från listan över sparade jobb.

1. Välj **Nästa** för att gå till sidan **leverans alternativ** . På sidan **leverans alternativ** kan du ange leverans alternativ för det här jobbet: **batchar** och **avbrotts tröskel**.

    Med batchar kan du sprida jobb för ett stort antal enheter. Jobbet är uppdelat i flera batchar och varje batch innehåller en delmängd av enheterna. Batcharna placeras i kö och körs i följd.

    Med tröskelvärdet för annullering kan du automatiskt avbryta ett jobb om antalet fel överstiger din angivna gräns. Tröskelvärdet kan gälla för alla enheter i jobbet, eller för enskilda batchar.

    :::image type="content" source="media/howto-run-a-job/job-wizard-delivery-options.png" alt-text="Skärm bild av sidan leverans alternativ för jobb guiden":::

1. Välj **Nästa** för att gå till sidan **schema** . På sidan **schema** kan du aktivera ett schema för att köra jobbet i framtiden:

    Välj ett upprepnings alternativ för schemat. Du kan konfigurera ett jobb som ska köras:

    * Engångshändelse
    * Varje dag
    * Varje vecka

    Ange start datum och-tid för ett schemalagt jobb. Datum och tid är bara för din tidszon och inte till enhetens lokala tid.

    Om du vill avsluta ett återkommande schema väljer du:

    * Ange ett slutdatum för schemat **på den här dagen** .
    * **När** du har angett hur många gånger jobbet ska köras.

    Schemalagda jobb körs alltid på enheterna i en enhets grupp, även om enhets gruppens medlemskap ändras över tid.

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule.png" alt-text="Skärm bild av sidan schema alternativ i jobb guiden":::

1. Välj **Nästa** för att gå till **gransknings** sidan. På sidan **Granska** visas information om jobb konfigurationen. Välj **schema** för att schemalägga jobbet:

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule-review.png" alt-text="Skärm bild av guiden för schemalagda jobb gransknings Sidan":::

1. Sidan jobb information visar information om schemalagda jobb. När det schemalagda jobbet körs visas en lista över jobb instanserna. Den schemalagda jobb körningen är också en del av den **senaste 30-dagars** jobb listan.

    På den här sidan kan du **Schemalägga** jobbet eller **Redigera** det schemalagda jobbet. Du kan återgå till ett schemalagt jobb från listan över schemalagda jobb.

    :::image type="content" source="media/howto-run-a-job/job-schedule-details.png" alt-text="Skärm bild av sidan information om schemalagt jobb":::

1. I jobb guiden kan du välja att inte schemalägga ett jobb och köra det direkt. Följande skärm bild visar ett jobb utan ett schema som är redo att köras omedelbart. Välj **Kör** för att köra jobbet:

    :::image type="content" source="media/howto-run-a-job/job-wizard-schedule-immediate.png" alt-text="Skärm bild av sidan granskning av jobb guiden":::

1. Ett jobb går genom *väntande*, *pågående* och *slutförda* faser. Informationen om jobb körningen innehåller resultat statistik, varaktighets information och ett rutnät i enhets listan.

    När jobbet har slutförts kan du välja **resultat logg** för att ladda ned en CSV-fil med jobb information, inklusive enheterna och deras status värden. Den här informationen kan vara användbar vid fel sökning.

    :::image type="content" source="media/howto-run-a-job/download-details.png" alt-text="Skärm bild som visar enhets status":::

1. Jobbet visas nu i listan **senaste 30 dagar** på sidan **jobb** . Den här sidan visar jobb som körs och historik för tidigare körningar eller sparade jobb.

    > [!NOTE]
    > Du kan visa 30 dagars historik för dina jobb som körs tidigare.

## <a name="manage-jobs"></a>Hantera jobb

Om du vill stoppa ett pågående jobb öppnar du det och väljer **stoppa**. Jobbets status ändras för att visa att jobbet har stoppats. I **sammanfattnings** avsnittet visas vilka enheter som har slutförts, som har misslyckats eller som fortfarande väntar.

:::image type="content" source="media/howto-run-a-job/manage-job.png" alt-text="Skärm bild som visar ett jobb som körs och knappen för att stoppa ett jobb":::

När ett jobb är i stoppat läge kan du välja **Fortsätt** för att återuppta körningen av jobbet. Jobbets status ändras för att visa att jobbet nu körs igen. Avsnittet **Sammanfattning** fortsätter att uppdateras med den senaste förloppet.

:::image type="content" source="media/howto-run-a-job/stopped-job.png" alt-text="Skärm bild som visar ett stoppat jobb och en knapp för att fortsätta ett jobb":::

## <a name="copy-a-job"></a>Kopiera ett jobb

Om du vill kopiera ett befintligt jobb väljer du ett jobb som körs. Välj **Kopiera** på sidan jobb resultat sidan eller jobb informations sidan:

:::image type="content" source="media/howto-run-a-job/job-details-copy.png" alt-text="Skärm bild som visar knappen Kopiera":::

En kopia av jobb konfigurationen öppnas och du kan redigera den och **Kopiera** den till jobb namnet.

## <a name="view-job-status"></a>Visa jobbstatus

När ett jobb har skapats uppdateras **status** kolumnen med det senaste jobb status meddelandet. I följande tabell visas de möjliga *jobb status* värdena:

| Statusmeddelande       | Status betydelse                                          |
| -------------------- | ------------------------------------------------------- |
| Slutförd            | Det här jobbet kördes på alla enheter.              |
| Misslyckad               | Jobbet kunde inte köras och kunde inte köras helt på enheterna.  |
| Väntar              | Det här jobbet har ännu inte börjat köras på enheter.         |
| Körs              | Det här jobbet körs för närvarande på enheter.             |
| Stoppad              | En användare har stoppat det här jobbet manuellt.           |
| Avbrutna             | Jobbet avbröts eftersom tröskelvärdet som har angetts på sidan **leverans alternativ** överskreds. |

Status meddelandet följs av en översikt över enheterna i jobbet. I följande tabell visas möjliga *enhets status* värden:

| Statusmeddelande       | Status betydelse                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Lyckades            | Antalet enheter som jobbet har körts på.       |
| Misslyckad               | Antalet enheter som jobbet inte kunde köras på.       |

Om du vill visa status för jobbet och alla berörda enheter öppnar du jobbet. Bredvid varje enhets namn ser du något av följande status meddelanden:

| Statusmeddelande       | Status betydelse                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Slutförd            | Jobbet kördes på den här enheten.                                     |
| Misslyckad               | Det gick inte att köra jobbet på den här enheten. I fel meddelandet visas mer information.  |
| Väntar              | Jobbet har ännu inte körts på den här enheten.                                   |

Om du vill ladda ned en CSV-fil som innehåller jobb information och listan över enheter och deras status värden väljer du **resultat logg**.

## <a name="filter-the-device-list"></a>Filtrera enhetslistan

Du kan filtrera enhets listan på **jobb informations** sidan genom att välja filter ikonen. Du kan filtrera efter **enhets-ID** eller **status** fältet:

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Skärm bild som visar val för att filtrera en enhets lista.":::

## <a name="customize-columns-in-the-device-list"></a>Anpassa kolumner i enhets listan

Du kan lägga till kolumner i enhets listan genom att välja ikonen för kolumn alternativ:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Skärm bild som visar ikonen för kolumn alternativ.":::

Använd dialog rutan **kolumn alternativ** om du vill välja kolumnerna i enhets listan. Markera de kolumner som du vill visa, Välj högerpilen och välj sedan **OK**. Välj **Markera** alla om du vill markera alla tillgängliga kolumner. De markerade kolumnerna visas i enhets listan.

De markerade kolumnerna behålls i en användarsession eller mellan användarsessioner som har åtkomst till programmet.

## <a name="rerun-jobs"></a>Kör om jobb

Du kan köra om ett jobb som har fel enheter. Select **Kör igen misslyckades**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Skärm bild som visar knappen för att köra om ett jobb på misslyckade enheter.":::

Ange ett jobb namn och en beskrivning och välj sedan **Kör om jobb**. Ett nytt jobb skickas för att försöka utföra åtgärden på misslyckade enheter igen.

> [!NOTE]
> Du kan inte köra fler än fem jobb samtidigt från ett Azure IoT Central-program.
>
> När ett jobb är klart och du tar bort en enhet som finns i jobbets enhets lista, visas enhets posten som borttagen i enhetens namn. Informations länken är inte tillgänglig för den borttagna enheten.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar jobb i ditt Azure IoT Central-program kan du använda följande steg:

- [Hantera dina enheter](howto-manage-devices.md)
- [Version din enhets mall](howto-version-device-template.md)
