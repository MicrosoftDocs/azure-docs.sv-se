---
title: 'Självstudie: skapa en miljö – Azure Time Series Insights | Microsoft Docs'
description: Lär dig hur du skapar en Azure Time Series Insights miljö som är ifylld med data från simulerade enheter.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: ea57a623546df99408d488da4522411f55ab0dd9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094353"
---
# <a name="tutorial-create-an-azure-time-series-insights-gen1-environment"></a>Självstudie: skapa en Azure Time Series Insights gen1-miljö

> [!CAUTION]
> Det här är en gen1-artikel.

Den här självstudien vägleder dig genom processen att skapa en Azure Time Series Insights-miljö som är ifylld med data från simulerade enheter. I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Skapa en Azure Time Series Insightss miljö.
> * Skapa en lösning för enhets simulering som innehåller en IoT-hubb.
> * Anslut Azure Time Series Insightss miljön till IoT Hub.
> * Kör en enhets simulering för att strömma data till Azure Time Series Insightss miljön.
> * Verifiera simulerade telemetridata.

> [!IMPORTANT]
> Registrera dig för en [kostnads fri Azure-prenumeration](https://azure.microsoft.com/free/) om du inte redan har en.

## <a name="prerequisites"></a>Förutsättningar

* Ditt Azure-inloggningsnamn måste också vara medlem i prenumerationens **ägar** roll. Mer information finns [i tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="review-video"></a>Granska video

Lär dig hur du använder en Azure IoT Solution Accelerator för att generera data och komma igång med Azure Time Series Insights.

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Översikt

Azure Time Series Insightss miljön är den plats där enhets data samlas in och lagras. När du har lagrat kan du använda [API: et](/rest/api/time-series-insights/gen1-query-api) för [Azure Time Series Insights Explorer](time-series-quickstart.md) och Azure Time Series Insights frågor för att fråga och analysera data.

Azure IoT Hub är händelse källan som används av alla enheter (simulerade eller fysiska) i självstudien för att på ett säkert sätt ansluta och skicka data till Azure-molnet.

Den här självstudien använder också en [IoT Solution Accelerator](https://www.azureiotsolutions.com) för att generera och strömma exempel på telemetridata till IoT Hub.

>[!TIP]
> [IoT Solution Accelerators](https://www.azureiotsolutions.com) tillhandahåller förkonfigurerade lösningar i företags klass som du kan använda för att påskynda utvecklingen av anpassade IoT-lösningar.

## <a name="create-a-device-simulation"></a>Skapa en enhetssimulering

Först skapar du lösningen för enhets simulering, som genererar test data för att fylla i din Azure Time Series Insightss miljö.

1. I ett separat fönster eller flik går du till [azureiotsolutions.com](https://www.azureiotsolutions.com). Logga in med samma Azure-prenumerations konto och välj **enhets simulerings** Accelerator.

   [![Kör acceleratorn för enhetssimulering](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. Välj **Testa nu**. Ange sedan de nödvändiga parametrarna på sidan **skapa lösning för enhets simulering** .

   Parameter|Beskrivning
   ---|---
   **Distributions namn** | Det unika värdet används för att skapa en ny resurs grupp. Angivna Azure-resurser skapas och tilldelas till resursgruppen.
   **Azure-prenumeration** | Ange samma prenumeration som användes för att skapa din Azure Time Series Insights-miljö i föregående avsnitt.
   **Distributionsalternativ** | Välj **etablera nya IoT Hub** för att skapa en ny IoT-hubb som är speciell för den här självstudien.
   **Azure-plats** | Ange samma region som användes för att skapa din Azure Time Series Insights-miljö i föregående avsnitt.

   När du är klar väljer du **skapa** för att etablera lösningens Azure-resurser. Det kan ta upp till 20 minuter att slutföra den här processen.

   [![Etablera enhetssimuleringslösningen](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. När etableringen har slutförts visas två uppdateringar som meddelar dig att distributions statusen har flyttats från **etablering** till **klar**.

   >[!IMPORTANT]
   > Ange inte Solution Accelerator ännu! Behåll den här webb sidan öppen eftersom du kommer tillbaka till den senare.

   [![Etableringen för enhetssimuleringslösningen klar](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Granska nu de nyligen skapade resurserna i Azure Portal. På sidan **resurs grupper** ser du att en ny resurs grupp har skapats med hjälp av **lösnings namnet** som angavs i det sista steget. Anteckna de resurser som har skapats för enhets simuleringen.

   [![Resurser för enhets simulering](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Skapa en miljö

Skapa sedan en Azure Time Series Insights-miljö i din Azure-prenumeration.

1. Logga in på [Azure Portal](https://portal.azure.com) med ditt Azure-prenumerations konto.
1. Välj **+ skapa en resurs** i det övre vänstra hörnet.
1. Välj kategorin **Sakernas Internet** och välj sedan **Time Series Insights**.

   [![Välj Azure Time Series Insights miljö resurs](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. Fyll i de obligatoriska parametrarna på sidan **Time Series Insights miljö** .

   Parameter|Beskrivning
   ---|---
   **Miljönamn** | Välj ett unikt namn för Azure Time Series Insightss miljön. Namnen används av Azure Time Series Insights Explorer och API: er för [frågor](/rest/api/time-series-insights/gen1-query).
   **Prenumeration** | Prenumerationer är containrar för Azure-resurser. Välj en prenumeration för att skapa Azure Time Series Insightss miljön.
   **Resursgrupp** | En resursgrupp är en container för Azure-resurser. Välj en befintlig resurs grupp eller skapa en ny för resursen Azure Time Series Insightss miljö.
   **Plats** | Välj en data Center region för din Azure Time Series Insightss miljö. Om du vill undvika ytterligare svars tid skapar du Azure Time Series Insightss miljön i samma region som andra IoT-resurser.
   **Nivå** | Välj det genomflöde som behövs. Välj **S1**.
   **Kapacitet** | Kapaciteten är multiplikatorn som tillämpas på den ingångs takt och lagrings kapacitet som är kopplad till den valda SKU: n. Du kan ändra kapaciteten när du har skapat den. Välj en kapacitet på **1**.

   När du är färdig väljer du **Nästa: händelse källa** för att gå vidare till nästa steg.

   [![Skapa en Azure Time Series Insights miljö resurs](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Nu ansluter du Azure Time Series Insightss miljön till IoT Hub som skapats av Solution Accelerator. Ange **Välj en hubb** till `Select existing` . Välj sedan den IoT-hubb som skapats av Solution Accelerator när du anger **IoT Hub namn**.

   [![Anslut Azure Time Series Insightss miljön till den skapade IoT-hubben](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Slutligen väljer du **Granska + skapa**.

1. Gå till panelen **meddelanden** för att övervaka slut för ande av distribution.

   [![Distributionen av Azure Time Series Insights miljön lyckades](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Kör enhets simulering

Nu när distributionen och den inledande konfigurationen är slutförd, fyller du i Azure Time Series Insights-miljön med exempel data från [simulerade enheter som skapats av acceleratorn](#create-a-device-simulation).

Tillsammans med IoT Hub genererades ett Azure App Service-webbprogram för att skapa och överföra simulerad enhets telemetri.

1. Gå tillbaka till din [Instrumentpanel för lösningsacceleratorer](https://www.azureiotsolutions.com/Accelerators#dashboard). Logga in igen, om det behövs, med samma Azure-konto som du har använt i den här självstudien. Välj din "enhets lösning" och **gå sedan till Solution Accelerator** för att starta din distribuerade lösning.

   [![Instrumentpanel för lösningsacceleratorer](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Webb programmet för enhets simulering börjar genom att du tillfrågas om du vill ge webb programmet det **loggade du in och läsa din profil** behörighet. Med den här behörigheten kan programmet hämta den användar profil information som krävs för att stödja programmets funktion.

   [![Medgivande för enhetssimuleringens webbprogram](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. Välj **+ ny simulering**. Ange de parametrar som krävs efter att installations sidan för **simulering** har lästs in.

   Parameter|Beskrivning
   ---|---
   **Mål-IoT Hub** | Välj **Använd företablerade IoT Hub**.
   **Enhetsmodell** | Välj **Kylaggregat**.
   **Antal enheter**  | Ange `10` under **belopp**.
   **Telemetrifrekvens** | Ange `10` sekunder.
   **Simulerings varaktighet** | Välj **sluta i:** och ange `5` minuter.

   När du är klar väljer du **Starta simulering**. Simuleringen körs i totalt 5 minuter. Den genererar data från 1 000 simulerade enheter var 10: e sekund.

   [![Inställning av enhetssimulering](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. När simuleringen körs ser du att fälten **Totalt antal meddelanden** och **meddelanden per sekund** uppdateras, ungefär var 10: e sekund. Simuleringen slutar efter cirka 5 minuter och återgår till **simulerings installationen**.

   [![Enhetssimuleringen körs](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Kontrollera telemetridata

I det här sista avsnittet verifierar du att telemetridata har genererats och lagrats i Azure Time Series Insightss miljön. För att verifiera data använder du Azure Time Series Insights Explorer, som används för att fråga och analysera telemetridata.

1. Gå tillbaka till **översikts** sidan för Azure Time Series Insightss miljöns resurs grupp. Välj Azure Time Series Insightss miljö.

   [![Resurs grupp och miljö för Azure Time Series Insights miljö](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. På sidan **Översikt över** Azure Time Series Insightss miljö väljer du **URL: en för Time Series Insights Explorer** för att öppna Azure Time Series Insights Explorer.

   [![Azure Time Series Insights-utforskaren](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Azure Time Series Insights Explorer läser in och autentiserar med hjälp av ditt Azure Portal-konto. I början visas det diagram fält som Azure Time Series Insightss miljön fylldes med tillsammans med dess simulerade telemetridata. Om du vill filtrera en snävare tid väljer du List rutan i det övre vänstra hörnet. Ange ett tidsintervall som är tillräckligt stort för att omfatta varaktigheten för enhets simuleringen. Välj sedan förstorings glaset för sökning.

   [![Tids intervalls filter för Azure Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Genom att begränsa tidsintervallet kan diagrammet zooma in i de distinkta burst-data överföringarna till IoT Hub och Azure Time Series Insights miljön. Observera också att den **strömmande texten är klar** i det övre högra hörnet, vilket visar det totala antalet händelser som har hittats. Du kan också dra skjutreglaget för **intervall storlek** för att kontrol lera diagrammets rityta.

   [![Filtrerad vy för tidsintervallet i Azure Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Slutligen kan du också vänsterklicka på en region för att filtrera ett intervall. Högerklicka sedan och Använd **utforska händelser** för att visa händelse information i vyn tabell **händelser** .

   [![Filtrerade vyer och händelser i Azure Time Series Insights Explorer](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien skapar flera Azure-tjänster som körs för att stödja Azure Time Series Insights miljö och enhets simulerings lösning. Om du vill ta bort dem går du tillbaka till Azure Portal.

Från menyn till vänster i Azure Portal:

1. Välj **resurs grupps** ikonen. Välj sedan den resurs grupp som du skapade för Azure Time Series Insightss miljön. Välj **ta bort resurs grupp** längst upp på sidan, ange namnet på resurs gruppen och välj **ta bort**.

1. Välj **resurs grupps** ikonen. Välj sedan den resurs grupp som skapades av enhets simulerings lösnings Accelerator. Välj **ta bort resurs grupp** längst upp på sidan, ange namnet på resurs gruppen och välj **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Skapa en Azure Time Series Insightss miljö.
> * Skapa en lösning för enhets simulering som innehåller en IoT-hubb.
> * Anslut Azure Time Series Insightss miljön till IoT Hub.
> * Kör en enhets simulering för att strömma data till Azure Time Series Insightss miljön.
> * Verifiera simulerade telemetridata.

Nu när du vet hur du skapar en egen Azure Time Series Insights miljö, lär du dig hur du skapar ett webb program som använder data från en Azure Time Series Insights-miljö:

> [!div class="nextstepaction"]
> [Läs exempel på den värdbaserade klient-SDK-visualiseringen](https://tsiclientsample.azurewebsites.net/)