---
title: Integrera med Azure Maps
titleSuffix: Azure Digital Twins
description: Se hur du använder Azure Functions för att skapa en funktion som kan använda den dubbla grafen och digitala Azure-meddelanden för att uppdatera en Azure Maps inomhus-karta.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 990a0ee73bd91ccb748c948b5fcf0e6124d84a03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102201438"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Använd Azure Digitals flätas för att uppdatera en Azure Maps inomhus karta

Den här artikeln vägleder dig genom de steg som krävs för att använda Azure Digitals dubbla data för att uppdatera information som visas på en *inomhus karta* med [Azure Maps](../azure-maps/about-azure-maps.md). Azure Digitals innehåller ett diagram över dina IoT-entitetsrelationer och dirigerar telemetri till olika slut punkter, vilket gör den till den perfekta tjänsten för att uppdatera informations överlägg i Maps.

Den här instruktionen kommer att avse:

1. Konfigurera din Azure Digital-instansen för att skicka dubbla uppdaterings händelser till en funktion i [Azure Functions](../azure-functions/functions-overview.md).
2. Skapa en funktion för att uppdatera en Azure Maps inomhus Maps-funktion stateset.
3. Så här lagrar du ditt Maps-ID och funktions stateset-ID i Azure Digitals grafen.

### <a name="prerequisites"></a>Förutsättningar

* Följ självstudien om Azure Digitals dubbla [*steg: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md).
    * Du kommer att utöka den här dubbla med en ytterligare slut punkt och väg. Du kommer även att lägga till en annan funktion i din Function-app från den självstudien. 
* Följ Azure Maps [*själv studie kursen: använd Azure Maps Creator för att skapa inlednings kartor*](../azure-maps/tutorial-creator-indoor-maps.md) för att skapa en Azure Maps inomhus karta med en *funktions stateset*.
    * [Funktionen statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) är samlingar med dynamiska egenskaper (tillstånd) som har tilldelats till data uppsättnings funktioner, till exempel rum eller utrustning. I Azure Maps självstudien ovan lagrar funktionen stateset rums status som visas på en karta.
    * Du behöver ditt funktions *stateset-ID* och Azure Maps *prenumerations nyckel*.

### <a name="topology"></a>Topologi

Bilden nedan visar var integrerings elementen i den här självstudien i den här självstudien passar in i ett större Azure Digital-scenario från slut punkt till slut punkt.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="En vy över Azure-tjänster i ett scenario från slut punkt till slut punkt som markerar integrerings delen för inomhus Maps" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Skapa en funktion för att uppdatera en karta när den är dubbelt uppdaterad

Först ska du skapa en väg i Azure Digitals flätas för att vidarebefordra alla dubbla uppdaterings händelser till ett event Grid-ämne. Sedan använder du en funktion för att läsa dessa uppdaterings meddelanden och uppdatera en funktion som stateset i Azure Maps. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Skapa en väg och filtrera till tvillingmeddelanden om uppdateringar

Azure Digitals dubbla instanser kan generera dubbla uppdaterings händelser när en dubbel status uppdateras. Självstudien om Azure Digitals dubblare [*: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md) som är länkad ovan går igenom ett scenario där en termometer används för att uppdatera ett testattribut som är kopplat till rummets dubbla. Du kommer att utöka lösningen genom att prenumerera på uppdaterings meddelanden för dubbla och använda informationen för att uppdatera dina kartor.

Det här mönstret läser från rummet direkt, i stället för IoT-enheten, vilket ger dig flexibiliteten att ändra den underliggande data källan för temperatur utan att behöva uppdatera mappnings logiken. Du kan till exempel lägga till flera termometers eller ange det här rummet för att dela en termometer med ett annat rum, utan att du behöver uppdatera din kart logik.

1. Skapa ett event Grid-ämne, som tar emot händelser från din Azure Digital-instansen.
    ```azurecli-interactive
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Skapa en slut punkt för att länka ditt event Grid-ämne till Azure Digitals.
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Skapa en väg i Azure Digital Twins för att skicka dubbla uppdateringshändelser till din slutpunkt.

    >[!NOTE]
    >Det finns ett **känt problem** i Cloud Shell som påverkar dessa kommandogrupper: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Du kan lösa problemet genom att köra `az login` i Cloud Shell innan du kör kommandot eller använda det [lokala kommandoradsgränssnittet](/cli/azure/install-azure-cli) i stället för Cloud Shell. Mer information finns i [*fel sökning: kända problem i Azure Digitals*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>Skapa en funktion för att uppdatera Maps

Du ska skapa en **Event Grid utlöst funktion** i din Function-app från slut punkt till slut punkt ([*Självstudier: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md)). Den här funktionen kommer att packa upp dessa meddelanden och skicka uppdateringar till en Azure Maps funktion stateset för att uppdatera temperaturen för ett rum.

Se följande dokument som referens information: [*Azure Event Grid utlösare för Azure Functions*](../azure-functions/functions-bindings-event-grid-trigger.md).

Ersätt funktions koden med följande kod. Den filtrerar bara uppdateringar till utrymmes visare, läser den uppdaterade temperaturen och skickar informationen till Azure Maps.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

Du måste ange två miljövariabler i din Function-app. En är din [Azure Maps primära prenumerations nyckel](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)och en är ditt [Azure Maps stateset-ID](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --name <your-App-Service-(function-app)-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-App-Service-(function-app)-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>Visa live-uppdateringar på kartan

Följ stegen nedan om du vill se färsk uppdaterings temperatur:

1. Börja skicka simulerade IoT-data genom att köra **DeviceSimulator** -projektet från Azure Digitals [*själv studie kurs: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md). Anvisningarna för detta finns i avsnittet [*Konfigurera och köra simuleringen*](././tutorial-end-to-end.md#configure-and-run-the-simulation) .
2. Använd [modulen **Azure Maps inomhus**](../azure-maps/how-to-use-indoor-module.md) för att rendera dina inliggande kartor som skapats i Azure Maps Creator.
    1. Kopiera HTML-koden från [*exemplet: Använd modulen inomhus Maps-modul*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) i [*självstudierna i inomhus Maps: Använd modulen Azure Maps inomhus Maps*](../azure-maps/how-to-use-indoor-module.md) till en lokal fil.
    1. Ersätt *prenumerations nyckeln*, *tilesetId* och *statesetID*  i den lokala HTML-filen med dina värden.
    1. Öppna filen i webbläsaren.

Båda exemplen skickar temperatur i ett kompatibelt intervall, så du bör se färgen på Room 121-uppdateringen på kartan var 30: e sekund.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="En Office-karta som visar rum 121 färgad orange":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Lagra din Maps-information i Azure Digitals, dubbla

Nu när du har en hårdkodad-lösning för att uppdatera dina Maps-information kan du använda Azure Digitals grafer för att lagra all information som behövs för att uppdatera din inomhus karta. Detta omfattar stateset-ID, Maps-prenumerations-ID och funktions-ID för varje karta respektive plats. 

En lösning för det här exemplet innebär att uppdatera varje utrymme på den översta nivån så att det har ett stateset-ID och mappar prenumerations-ID-attribut och uppdaterar varje rum för att ha ett funktions-ID. Du måste ange dessa värden en gång när du har initierat den dubbla grafen och sedan skicka frågor till dessa värden för varje dubbel uppdaterings händelse.

Beroende på konfigurationen av din topologi kommer du att kunna lagra dessa tre attribut på olika nivåer som motsvarar din kartas granularitet.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du hanterar, uppgraderar och hämtar information från diagrammet med dubbla diagram i följande referenser:

* [*Anvisningar: hantera digitala dubbla*](./how-to-manage-twin.md)
* [*Anvisningar: fråga det dubbla diagrammet*](./how-to-query-graph.md)
