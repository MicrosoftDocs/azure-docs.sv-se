---
title: Prova en lösning för att hantera mina industriella IoT-enheter – Azure | Microsoft Docs
description: I den här snabbstarten distribuerar du Azure IoT-lösningsacceleratorn Ansluten fabrik och loggar in för att använda lösningsinstrumentpanelen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 6338403818259c7263fe66af7d8c8840d98efbcd
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646895"
---
# <a name="quickstart-try-a-cloud-based-solution-to-manage-my-industrial-iot-devices"></a>Snabbstart: Testa en molnbaserad lösning för att hantera mina industriella IoT-enheter

Den här snabbstarten visar hur du distribuerar Azure IoT-lösningsacceleratorn Ansluten fabrik för att köra en molnbaserad övervaknings- och hanteringssimulering för industriella IoT-enheter. När du distribuerar lösningsacceleratorn Ansluten fabrik är den förfylld med simulerade resurser som hjälper dig genom ett vanligt IoT-industriscenario. Flera simulerade fabriker är anslutna till det här scenariot. De rapporterar de datavärden som behövs för att beräkna övergripande utrustningseffektivitet (Overall Equipment Efficiency, OEE) och KPI:er (Key Performance Indicators). Den här snabbstarten visar hur du använder lösningsinstrumentpanelen för att:

* Övervaka fabrik, produktionslinjer, stations-OEE och KPI-värden.
* Analysera telemetridata som genereras av dessa enheter.
* Svara på larm.

Du behöver en aktiv Azure-prenumeration för att kunna utföra den här snabbstarten.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="deploy-the-solution"></a>Distribuera lösningen

När du distribuerar lösningsacceleratorn till Azure-prenumerationen måste du ange några konfigurationsalternativ.

Gå till [Microsoft Azure IoT-lösningsacceleratorer](https://www.azureiotsolutions.com) och logga in med autentiseringsuppgifterna för ditt Azure-konto.

Klicka på panelen **Ansluten fabrik**. På sidan **Ansluten fabrik** klickar du på **Testa nu**:

![Testa nu](./media/quickstart-connected-factory-deploy/connectedfactory.png)

På sidan **Skapa lösningen Ansluten fabrik** anger du ett unikt **lösningsnamn** för lösningsacceleratorn Ansluten fabrik. Det här namnet är namnet på den Azure-resursgrupp som innehåller alla acceleratorresurser för lösningen. Den här snabbstarten använder namnet **MyDemoConnectedFactory**.

Välj den **prenumeration** och **region** du vill använda för att distribuera lösningsacceleratorn. Normalt väljer du regionen närmast dig. I den här snabbstarten använder vi **Visual Studio Enterprise** och **USA, östra**. Du måste vara [global administratör eller användare](iot-accelerators-permissions.md) i prenumerationen.

Klicka på **Skapa** för att inleda distributionen. Processen tar minst fem minuter att köra:

![Information om lösningen Ansluten fabrik](./media/quickstart-connected-factory-deploy/createform.png)

## <a name="sign-in-to-the-solution"></a>Logga in till lösningen

När distributionen till Azure-prenumerationen är klar visas en grön bockmarkering och **Redo** på lösningspanelen. Du kan nu logga in på instrumentpanelen för lösningsacceleratorn Ansluten fabrik.

På sidan **Etablerade lösningar** klickar du på den nya lösningsacceleratorn Ansluten fabrik:

![Välj en ny lösning](./media/quickstart-connected-factory-deploy/choosenew.png)

Du kan visa information om lösningsacceleratorn Ansluten fabrik på sidan som visas. Välj **Go to your Solution accelerator** (Gå till din lösningsaccelerator) för att visa lösningsacceleratorn Ansluten fabrik:

![Lösningens panel](./media/quickstart-connected-factory-deploy/solutionpanel.png)

Klicka på **Acceptera** för att acceptera begäran om behörigheter som instrumentpanelen för lösningen Ansluten fabrik visar i webbläsaren. Den visar en uppsättning simulerade fabriker, produktionslinjer och stationer.

## <a name="view-the-dashboard"></a>Visa instrumentpanelen

Standardvyn är *instrumentpanelen*. Du kan navigera till andra delar av portalen via menyn till vänster på sidan:

[![Instrumentpanel för lösningen](./media/quickstart-connected-factory-deploy/dashboard-inline.png)](./media/quickstart-connected-factory-deploy/dashboard-expanded.png#lightbox)

Du kan använda instrumentpanelen för att hantera dina industriella IoT-enheter. Ansluten fabrik använder en hierarki för att visa en global fabrikskonfiguration. Den översta nivån i hierarkin är det företag som innehåller en eller flera fabriker. Varje fabriken innehåller produktionslinjer, och varje produktionslinje består av stationer. På varje nivå kan du se OEE och KPI:er, publicera nya noder för telemetri och svara på larm.

På instrumentpanelen kan du se:

## <a name="overall-equipment-efficiency"></a>Övergripande utrustningseffektivitet

Panelen **Övergripande utrustningseffektivitet** visar OEE-värdena för hela företaget eller den fabrik, produktionslinje eller station du tittar på. Det här värdet sammanställs från stationsvyn till företagsnivån. OEE-bilden och dess beståndsdelar kan analyseras ytterligare.

[![Allmän utrustnings effektivitet](./media/quickstart-connected-factory-deploy/oee-inline.png)](./media/quickstart-connected-factory-deploy/oee-expanded.png#lightbox)

OEE mäter produktionseffektiviteten med hjälp av produktionsrelaterade driftsparametrar. OEE är ett branschstandardmått som beräknas genom att multiplicera tillgänglighet, anläggningsutnyttjande och kvalitetsutbyte: OEE = tillgänglighet x anläggningsutnyttjande x kvalitetsutbyte.

Du kan ytterligare analysera OEE för vilken nivå som helst i hierarkidata. Klicka på reglaget för OEE, tillgänglighet, prestanda eller kvalitetsprocent. En kontextpanel visas med visualiseringar som visar data över olika tidsskalor:

[![Allmän information om utrustnings effektivitet](./media/quickstart-connected-factory-deploy/oeedetail-inline.png)](./media/quickstart-connected-factory-deploy/oeedetail-expanded.png#lightbox)

Du kan klicka på ett diagram för att göra vidare analyser av data.

### <a name="key-performance-indicators"></a>KPI:er

Panelen för **KPI:er** visar antalet enheter som produceras per timme och energi (kWh) som förbrukas av hela företaget eller av den fabrik, produktionslinje eller station som du visar. Dessa värden sammanställs från stationsvyn till företagsnivån.

[![Nyckeltal för viktiga prestanda](./media/quickstart-connected-factory-deploy/kpis-inline.png)](./media/quickstart-connected-factory-deploy/kpis-expanded.png#lightbox)

Du kan ytterligare analysera KPI:er för vilken nivå som helst i hierarkidata. Klicka på reglaget för OEE, tillgänglighet, prestanda eller kvalitetsprocent. En kontextpanel visas med visualiseringar som visar data över olika tidsskalor:

[![KPI-information](./media/quickstart-connected-factory-deploy/kpidetail-inline.png)](./media/quickstart-connected-factory-deploy/kpidetail-expanded.png#lightbox)

Du kan klicka på ett diagram för att göra vidare analyser av data.

### <a name="factory-locations"></a>Fabriksplatser

En panel med **fabriksplatser** som visar status, plats och aktuell produktionskonfiguration i lösningen. Första gången du kör lösningsacceleratorn visar instrumentpanelen en simulerad uppsättning fabriker. Varje produktionslinjesimulering består av tre verkliga OPC UA-servrar som kör simulerade uppgifter och delar data. Mer information om OPC UA finns i [Vanliga frågor och svar om Ansluten fabrik](iot-accelerators-faq-cf.md):

[![Fabriks platser](./media/quickstart-connected-factory-deploy/factorylocations-inline.png)](./media/quickstart-connected-factory-deploy/factorylocations-expanded.png#lightbox)

Du kan navigera genom lösningshierarkin och visa OEE-värden och KPI:er på varje nivå:

1. I **Fabriksplatser** klickar du på **Mumbai**. Du ser produktionslinjerna på den här platsen.

1. Klicka på **Produktionslinje 1**. Du ser stationerna på den här produktionslinjen.

1. Klicka på **Packaging** (Paketering). Du ser OPC UA-noder som publicerats av denna station.

1. Klicka på **EnergyConsumption** (Energiförbrukning). Du ser vissa diagram som ritar det här värdet över olika tidsskalor. Du kan klicka på ett diagram för att göra vidare analyser av data.

[![Visa energi förbrukning](./media/quickstart-connected-factory-deploy/explorelocations-inline.png)](./media/quickstart-connected-factory-deploy/explorelocations-expanded.png#lightbox)

### <a name="map"></a>Karta

Om din prenumeration ger åtkomst till [Bing Maps-API:t](iot-accelerators-faq-cf.md) visar *fabrikskartan* geografisk plats och status för alla fabriker i lösningen. Klicka på platserna som visas på kartan om du vill visa mer detaljerad information om platsen.

[![Karta](./media/quickstart-connected-factory-deploy/map-inline.png)](./media/quickstart-connected-factory-deploy/map-expanded.png#lightbox)

### <a name="alarms"></a>Larm

**Larmpanelen** visar de larm som genereras när ett rapporterat värde eller ett beräknat OEE/KPI-värde överstiger det ett tröskelvärde. Den här panelen visar larm på varje nivå i hierarkin, från stationsnivå till företagsnivå. Varje larm innehåller en beskrivning, datum, tid, plats och antal förekomster:

[![Larm](./media/quickstart-connected-factory-deploy/alarms-inline.png)](./media/quickstart-connected-factory-deploy/alarms-expanded.png#lightbox)

Du kan analysera de data som orsakade larmet från instrumentpanelen. Om du är administratör kan du vidta standardåtgärder på larmen, till exempel:

* Stäng larmet.
* Bekräfta larmet.

Klicka på något av larmen. I listrutan **Välj åtgärd** väljer du **Bekräfta avisering** och klickar sedan på **Tillämpa**:

[![Bekräfta larm](./media/quickstart-connected-factory-deploy/acknowledge-inline.png)](./media/quickstart-connected-factory-deploy/acknowledge-expanded.png#lightbox)

Om du vill analysera larmdata mer i detalj klickar du på diagrammet på larmpanelen.

Larmen genereras av regler som anges i en konfigurationsfil i lösningsacceleratorn. Dessa regler kan generera larm när OEE-eller KPI-värden eller OPC UA Node-värden går över ett tröskelvärde. Du kan ange detta tröskelvärde.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill utforska ytterligare låter du lösningsacceleratorn Ansluten fabrik förbli distribuerad.

Om du inte längre behöver lösningsacceleratorn tar du bort den från sidan [Etablerade lösningar](https://www.azureiotsolutions.com/Accelerators#dashboard) genom att välja den och sedan klicka på **Ta bort lösningen**:

![Ta bort lösningen](media/quickstart-connected-factory-deploy/deletesolution.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du lösningsacceleratorn Ansluten fabrik och lärde dig hur du navigerar i dina fabriker, produktionslinjer och stationer. Du såg även hur du visar OEE och KPI-värden på alla nivåer i hierarkin och hur du svarar på larm.

Information om hur du använder andra funktioner i instrumentpanelen för att hantera dina industriella IoT-enheter finns i följande instruktionsguide:

> [!div class="nextstepaction"]
> [Använda instrumentpanelen Ansluten fabrik](iot-accelerators-connected-factory-dashboard.md)