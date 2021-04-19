---
title: Visualisera simulerad telemetri med Time Series Insights – Azure | Microsoft Docs
description: Lär dig hur du konfigurerar Time Series Insights miljö för att utforska och analysera telemetri som genereras av lösningsacceleratorn för enhetssimulering.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 90a4b808daccc76e8cc9125973c69b13e8086fbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713974"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Använd Time Series Insights för att visualisera telemetri som skickas från lösningsacceleratorn för enhetssimulering

Med lösningsacceleratorn för enhetssimulering kan du generera telemetri från simulerade enheter för att testa dina IoT-lösningar. Den här guiden visar hur du visualiserar och analyserar den simulerade telemetrin med hjälp av en Time Series Insights miljö.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här instruktionerna behöver du en aktiv Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Stegen i den här instruktionerna förutsätter att du har distribuerat lösningsacceleratorn för enhetssimulering till din Azure-prenumeration. Om du inte har distribuerat enhetssimuleringen ännu kan du se [Distribution av enhetssimulering](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) på GitHub.

Den här artikeln förutsätter att namnet på lösningsacceleratorn är **contoso-simulation**. Ersätt **contoso-simulation** med namnet på lösningsacceleratorn när du utför följande steg.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Skapa en konsumentgrupp

Du måste skapa en dedikerad konsumentgrupp i din IoT-hubb för att strömma telemetri till Time Series Insights. En händelsekälla i Time Series Insights bör ha exklusiv användning av en IoT Hub konsumentgrupp.

Följande steg använder Azure CLI i Azure Cloud Shell för att skapa konsumentgruppen:

1. IoT-hubben är en av flera resurser som skapades när du distribuerade lösningsacceleratorn för enhetssimulering. Kör följande kommando för att hitta namnet på din IoT-hubb – kom ihåg att använda namnet på lösningsacceleratorn:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT-hubben är resursen av typen **Microsoft.Devices/IotHubs.**

1. Lägg till en konsumentgrupp **med namnet devicesimulationtsi** till hubben. I följande kommando använder du namnet på hubben och lösningsacceleratorn:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Nu kan du stänga Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Skapa en ny Time Series Insights miljö

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) är en fullständigt hanterad tjänst för analys, lagring och visualisering för att hantera tidsseriedata i IoT-skala i molnet. Så här skapar du en Time Series Insights miljö:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **Skapa en**  >  **Sakernas Internet**  >  **Time Series Insights**:

    ![Ny Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Om du vill Time Series Insights miljö i samma resursgrupp som lösningsacceleratorn använder du värdena i följande tabell:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn på miljö | I följande skärmbild används namnet **Contoso-TSI**. Välj ett eget unikt namn när du slutför det här steget. |
    | Prenumeration | I listrutan väljer du din Azure-prenumeration. |
    | Resursgrupp | **contoso-simulation**. Använd namnet på lösningsacceleratorn. |
    | Location | I det här exemplet **används USA, östra.** Skapa din miljö i samma region som enhetssimuleringsacceleratorn. |
    | Sku |**S1** |
    | Kapacitet | **1** |

    ![Skapa Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Om du Time Series Insights miljön i samma resursgrupp som lösningsacceleratorn innebär det att den tas bort när du tar bort lösningsacceleratorn.

1. Klicka på **Skapa**. Det kan ta några minuter för miljön att skapas.

## <a name="create-event-source"></a>Skapa händelsekälla

Skapa en ny händelsekälla för att ansluta till din IoT-hubb. Använd konsumentgruppen som du skapade i föregående steg. En Time Series Insights-händelsekälla kräver en dedikerad konsumentgrupp som inte används av en annan tjänst.

1. I Azure Portal du till din nya Time Series Environment.

1. Till vänster klickar du på **Händelsekällor:**

    ![Visa händelsekällor](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klicka **på Lägg till:**

    ![Lägg till händelsekälla](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Om du vill konfigurera din IoT Hub som en ny händelsekälla använder du värdena i följande tabell:

    | Inställning | Värde |
    | ------- | ----- |
    | Namn på händelsekälla | Följande skärmbild använder namnet **contoso-iot-hub**. Använd ditt eget unika namn när du slutför det här steget. |
    | Källa | **IoT Hub** |
    | Importalternativ | **Använda IoT Hub från tillgängliga prenumerationer** |
    | Prenumerations-ID:t | I listrutan väljer du din Azure-prenumeration. |
    | Namn på IoT-hubb | **contoso-simulation7d894**. Använd namnet på din IoT Hub från lösningsacceleratorn för enhetssimulering. |
    | Principnamn för IoT Hub | **iothubowner** |
    | Principnyckel för IoT-hubb | Det här fältet fylls i automatiskt. |
    | Konsumentgrupp för IoT-hubb | **devicesimulationtsi** |
    | Händelseserialiseringsformat | **JSON** |
    | Egenskapsnamn för tidsstämpel | Lämna tomt |

    ![Skapa händelsekälla](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klicka på **Skapa**.

> [!NOTE]
> Du kan [ge ytterligare användare åtkomst till](../time-series-insights/concepts-access-policies.md#grant-data-access) Time Series Insights Explorer.

## <a name="start-a-simulation"></a>Starta en simulering

Innan du använder Time Series Insights explorer konfigurerar du lösningsacceleratorn för enhetssimulering för att generera telemetri. Följande skärmbild visar en simulering som körs med 10 kylaggregat:

![Köra enhetssimulering](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights-utforskaren

Utforskaren Time Series Insights en webbapp som du kan använda för att visualisera din telemetri.

1. I Azure Portal väljer du fliken Time Series Insights **Översikt.**

1. Öppna webbappen Time Series Insights genom att klicka på **Gå till miljö:**

    ![Time Series Insights-utforskaren](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. I panelen för tidsval väljer du **Senaste 30 minuterna** på snabbtidsmenyn och klickar på **Sök:**

    ![Time Series Insights Explorer-sökning](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. I termpanelen till vänster väljer du **temperatur** som **Mått** och **iothub-connection-device-id** som **värde för Dela** efter:

    ![Skärmbild som visar Time Series Insights för "villkor", med värdena "Mått" och "Dela med" markerade.](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Högerklicka på diagrammet och välj **Utforska händelser:**

    ![Time Series Insights explorer-händelser](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Händelsedata visas i ett rutnät:

    ![Time Series Insights Explorer-tabell](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klicka på knappen perspektivvy:

    ![Time Series Insights utforskarens perspektiv](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klicka **+** för att lägga till en ny fråga i perspektivet:

    ![Time Series Insights Explorer Lägg till fråga](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Välj **Senaste 30 minuterna** som **tidsintervall,** Fuktighet som Mått **och** **iothub-connection-device-id** som **värde för Dela efter:**

    ![Time Series Insights Explorer-fråga](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Klicka på knappen perspektivvy för att visa enhetens telemetriinstrumentpanel:

    ![Time Series Insights Explorer-instrumentpanelen](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska ytterligare låter du lösningsacceleratorn vara distribuerad.

Om du inte längre behöver lösningsacceleratorn tar du bort den från sidan Etablerade lösningar genom att välja den och sedan klicka på **Ta bort lösning.** [](https://www.azureiotsolutions.com/Accelerators#dashboard)

Om du har Time Series Insights i lösningsacceleratorns resursgrupp tas den bort automatiskt när du tar bort lösningsacceleratorn. Annars måste du ta bort Time Series Insights miljön från Azure Portal.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du utforskar och frågar efter data i Time Series Insights explorer finns i [Azure Time Series Insights Explorer](../time-series-insights/time-series-insights-explorer.md).