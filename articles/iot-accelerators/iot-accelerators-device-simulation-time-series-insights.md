---
title: Visualisera simulerad telemetri med Time Series Insights – Azure | Microsoft Docs
description: Lär dig hur du konfigurerar din Time Series Insightss miljö för att utforska och analysera telemetri som genereras av enhets simulerings lösnings Accelerator.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: e1409b43f0ce1fc0d8c622dda79e857ac6abdd33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854571"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Använd Time Series Insights för att visualisera telemetri som skickas från Device simulering Solution Accelerator

Med Device simulering Solution Accelerator kan du generera telemetri från simulerade enheter för att testa dina IoT-lösningar. Den här instruktions guiden visar hur du kan visualisera och analysera simulerad telemetri med hjälp av en Time Series Insightss miljö.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här instruktions guiden behöver du en aktiv Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Stegen i den här instruktions guiden förutsätter att du har distribuerat lösnings Accelerator för enhets simulering till din Azure-prenumeration. Om du inte redan har distribuerat enhets simuleringen kan du läsa [distribuera enhets simulering](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) på GitHub.

Den här artikeln förutsätter att namnet på din lösnings Accelerator är **contoso-simulering**. Ersätt **contoso-simulering** med namnet på din Solution Accelerator när du utför följande steg.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Skapa en konsument grupp

Du måste skapa en dedikerad konsument grupp i din IoT-hubb för att strömma telemetri till Time Series Insights. En händelse källa i Time Series Insights bör ha exklusiv användning av en IoT Hub konsument grupp.

I följande steg används Azure CLI i Azure Cloud Shell för att skapa konsument gruppen:

1. IoT Hub är en av flera resurser som skapas när du distribuerar lösnings acceleratorn för enhets simulering. Kör följande kommando för att hitta namnet på din IoT-hubb – kom ihåg att använda namnet på din Solution Accelerator:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT Hub är en resurs av typen **Microsoft. Devices/IotHubs**.

1. Lägg till en konsument grupp med namnet **devicesimulationtsi** i hubben. I följande kommando använder du namnet på NAV-och Solution Accelerator:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Nu kan du stänga Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Skapa en ny Time Series Insightss miljö

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) är en helt hanterad analys-, lagrings-och visualiserings tjänst för hantering av data i IoT-Scale Time-serien i molnet. Så här skapar du en ny Time Series Insightss miljö:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **skapa en resurs**  >  **Sakernas Internet**  >  **Time Series Insights**:

    ![Ny Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Om du vill skapa din Time Series Insights-miljö i samma resurs grupp som Solution Accelerator använder du värdena i följande tabell:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn på miljö | Följande skärm bild använder namnet **contoso-TSD**. Välj ditt eget unika namn när du är klar med det här steget. |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | **contoso-simulering**. Använd namnet på din lösnings Accelerator. |
    | Location | I det här exemplet används **östra USA**. Skapa din miljö i samma region som din enhets simulerings Accelerator. |
    | Sku |**S1** |
    | Kapacitet | **1** |

    ![Skapa Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Om du lägger till Time Series Insightss miljön i samma resurs grupp som lösnings acceleratorn innebär det att den tas bort när du tar bort lösnings acceleratorn.

1. Klicka på **Skapa**. Det kan ta några minuter för miljön att skapas.

## <a name="create-event-source"></a>Skapa händelsekälla

Skapa en ny händelse källa för att ansluta till din IoT Hub. Använd den konsument grupp som du skapade i föregående steg. En Time Series Insights händelse källa kräver en dedikerad konsument grupp som inte används av någon annan tjänst.

1. I Azure Portal navigerar du till din nya tids serie miljö.

1. Klicka på **händelse källor** till vänster:

    ![Visa händelse källor](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klicka på **Lägg till**:

    ![Lägg till händelse källa](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Om du vill konfigurera IoT-hubben som en ny händelse källa använder du värdena i följande tabell:

    | Inställning | Värde |
    | ------- | ----- |
    | Händelse källans namn | Följande skärm bild använder namnet **contoso-IoT-Hub**. Använd ett eget unikt namn när du har slutfört det här steget. |
    | Källa | **IoT Hub** |
    | Importalternativ | **Använd IoT Hub från tillgängliga prenumerationer** |
    | Prenumerations-ID:t | I listrutan väljer du din Azure-prenumeration. |
    | Namn på IoT Hub | **contoso-simulation7d894**. Använd namnet på din IoT-hubb från Device simulering Solution Accelerator. |
    | Principnamn för IoT Hub | **iothubowner** |
    | Princip nyckel för IoT Hub | Det här fältet fylls i automatiskt. |
    | Konsument grupp för IoT Hub | **devicesimulationtsi** |
    | Händelseserialiseringsformat | **JSON** |
    | Egenskapsnamn för tidsstämpel | Lämna tomt |

    ![Skapa händelse källa](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klicka på **Skapa**.

> [!NOTE]
> Du kan [bevilja ytterligare användare åtkomst](../time-series-insights/concepts-access-policies.md#grant-data-access) till Time Series Insights Explorer.

## <a name="start-a-simulation"></a>Starta en simulering

Innan du använder Time Series Insights Explorer konfigurerar du lösnings acceleratorn för enhets simulering för att generera telemetri. Följande skärm bild visar en simulering som körs med 10 kyl enheter:

![Köra enhets simulering](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights-utforskaren

Time Series Insights Explorer är en webbapp som du kan använda för att visualisera din telemetri.

1. I Azure Portal väljer du fliken Time Series Insights **Översikt** .

1. Öppna Time Series Insights Explorer-webbappen genom att klicka på **gå till miljö**:

    ![Time Series Insights-utforskaren](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. I panelen val av tid väljer du de **senaste 30 minuterna** från snabb tider-menyn och klickar på **Sök**:

    ![Time Series Insights Explorer-sökning](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. I panelen villkor till vänster väljer du **temperatur** som **mått** och **iothub-Device-ID** som **delning efter** värde:

    ![Skärm bild som visar panelen med Time Series Insights "mått" och "dela efter"-värden markerade.](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Högerklicka på diagrammet och välj **utforska händelser**:

    ![Händelser i Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Händelse data visas i ett rutnät:

    ![Time Series Insights Explorer-tabell](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klicka på knappen perspektiv vy:

    ![Time Series Insights Explorer-perspektiv](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klicka **+** om du vill lägga till en ny fråga i perspektivet:

    ![Lägg till fråga i Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Välj de **senaste 30 minuterna** som tidsintervall, **fuktighet** som **mått** och Iothub- **Device-ID** som **delning efter** värde:

    ![Fråga om Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Klicka på knappen perspektiv vy för att visa din instrument panel för telemetri:

    ![Instrument panel för Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska ytterligare lämnar du Solution Accelerator distribuerad.

Om du inte längre behöver Solution Accelerator tar du bort den från sidan [etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) genom att markera den och sedan klicka på **ta bort lösning**.

Om du har lagt till Time Series Insightss miljön i Solution Accelerators resurs grupp tas den bort automatiskt när du tar bort lösnings acceleratorn. Annars måste du ta bort Time Series Insightss miljön manuellt från Azure Portal.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utforskar och frågar data i Time Series Insights Explorer finns i [Azure Time Series Insights Explorer](../time-series-insights/time-series-insights-explorer.md).