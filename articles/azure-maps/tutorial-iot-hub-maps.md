---
title: 'Självstudie: Implementera | Microsoft Azure Maps'
description: Självstudie om hur du integrerar IoT Hub med Microsoft Azure API:er för Maps-tjänsten
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9ebc6e266c93e55bc250e8450356f8b695dd9080
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715000"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Självstudie: Implementera rumslig IoT-analys med hjälp av Azure Maps

I ett IoT-scenario är det vanligt att samla in och spåra relevanta händelser som inträffar i tid och rum. Några exempel är vagnparkshantering, tillgångsspårning, mobilitet och program för smarta städer. Den här självstudien vägleder dig genom en lösning som spårar använda biluthyrningsflyttningar med hjälp Azure Maps API:er.

I den här självstudien kommer du att:

> [!div class="checklist"]
> * Skapa ett Azure Storage-konto för att logga spårningsdata för bilar.
> * Ladda upp en geofence till Azure Maps tjänsten Data (förhandsversion) med hjälp av API:et för datauppladdning.
> * Skapa en hubb Azure IoT Hub och registrera en enhet.
> * Skapa en funktion i Azure Functions implementera affärslogik baserat på Azure Maps rumslig analys.
> * Prenumerera på telemetrihändelser för IoT-enheter från Azure-funktionen via Azure Event Grid.
> * Filtrera telemetrihändelserna med hjälp IoT Hub dirigering av meddelanden.

## <a name="prerequisites"></a>Förutsättningar

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Skaffa en primär prenumerationsnyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), som även kallas primärnyckel eller prenumerationsnyckel. Mer information finns i [Hantera autentisering i Azure Maps](how-to-manage-authentication.md).

4. [Skapa en resursgrupp](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). I den här självstudien ger vi resursgruppen namnet *ContosoRental,* men du kan välja vilket namn du vill.

5. Ladda ned [C#-projektet rentalCarSimulation.](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)

I den här [självstudien används Postman-programmet,](https://www.postman.com/) men du kan välja en annan API-utvecklingsmiljö.

## <a name="use-case-rental-car-tracking"></a>Användningsfall: spårning av uthyrningsbil

Anta att ett biluthyrningsföretag vill logga platsinformation, avstånd som färdats och körningstillstånd för sina uthyrningsbilar. Företaget vill också lagra den här informationen när en bil lämnar rätt geografisk region.

Uthyrningsbilarna är utrustade med IoT-enheter som regelbundet skickar telemetridata till IoT Hub. Telemetrin innehåller den aktuella platsen och anger om bilens motor körs. Schemat för enhetens plats följer IoT-Plug and Play [för geospatiala data](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Telemetrischemat för uthyrningsbilens enhet ser ut som följande JSON-kod:

```JSON
{
    "data": {
        "properties": {
            "Engine": "ON"
        },
        "systemProperties": {
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "ContosoRentalDevice",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "636959817064335548",
            "iothub-enqueuedtime": "2019-06-18T00:17:20.608Z",
            "iothub-message-source": "Telemetry"
        },
        "body": {
            "location": {
                "type": "Point",
                "coordinates": [ -77.025988698005662, 38.9015330523316 ]
            }
        }
    }
}
```

I den här självstudien spårar du bara ett fordon. När du har ställt in Azure-tjänsterna måste du ladda ned [rentalCarSimulation C#-projektet för](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) att köra fordonssimulatorn. Hela processen, från händelse till funktionskörning, sammanfattas i följande steg:

1. Enheten i fordonet skickar telemetridata till IoT Hub.

2. Om bilmotorn körs publicerar hubben telemetridata till Event Grid.

3. En Azure-funktion utlöses på grund av händelseprenumerationen på telemetrihändelser på enheten.

4. Funktionen loggar fordonsenhetens platskoordinater, händelsetid och enhets-ID. Den använder sedan [Api:et Spatial Geofence Get](/rest/api/maps/spatial/getgeofence) för att avgöra om bilen har kört utanför geofence-et. Om den har rört sig utanför geofence-gränserna lagrar funktionen platsdata som tas emot från händelsen i en blobcontainer. Funktionen frågar även omvänd [sökadress för](/rest/api/maps/search/getsearchaddressreverse) att översätta koordinatplatsen till en gatuadress och lagrar den med resten av enhetens platsdata.

Följande diagram visar en översikt över systemet på hög nivå.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Diagram över systemöversikt.":::

Följande bild visar geofence-området i blått. Uthyrningsbilens väg anges med en grön linje.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Bild som visar geofence-vägen.":::

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Om du vill lagra spårningsdata för bilöverträdelser [skapar du ett v2-lagringskonto](../storage/common/storage-account-overview.md) för generell användning i resursgruppen. Om du inte har skapat en resursgrupp följer du anvisningarna i [skapa en resursgrupp.](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) I den här självstudien ger du resursgruppen namnet *ContosoRental*.

Om du vill skapa ett lagringskonto följer du anvisningarna i [Skapa ett lagringskonto](../storage/common/storage-account-create.md?tabs=azure-portal). I den här självstudien ger du *lagringskontot namnet contosorentalstorage,* men i allmänhet kan du ge det vad du vill.

När du har skapat ditt lagringskonto måste du skapa en container för att lagra loggningsdata.

1. Gå till ditt nyligen skapade lagringskonto. I avsnittet **Essentials** väljer du **länken Containrar.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Skärmbild av containrar för bloblagring.":::

2. I det övre vänstra hörnet väljer du **+ Container**. En panel visas till höger i webbläsaren. Ge containern *namnet contoso-rental-logs* och välj **Skapa**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Skärmbild av att skapa en blobcontainer.":::

3. Gå till **fönstret Åtkomstnycklar** i ditt lagringskonto och kopiera **lagringskontots** namn **och** nyckelvärdet i **avsnittet key1.** Du behöver båda dessa värden i avsnittet "Skapa en Azure-funktion och lägg till en Event Grid prenumeration".

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Skärmbild av kopiera lagringskontots namn och nyckel.":::

## <a name="upload-a-geofence"></a>Ladda upp en geofence

Använd sedan [Postman-appen för](https://www.getpostman.com) att [ladda upp geofence](./geofence-geojson.md) till Azure Maps. Geofence definierar det auktoriserade geografiska området för vårt uthyrningsfordon. Du använder geofence i Din Azure-funktion för att avgöra om en bil har flyttats utanför geofence-området.

Följ de här stegen för att ladda upp geofence med hjälp Azure Maps API för datauppladdning: 

1. Öppna Postman-appen och välj **Ny.** I fönstret **Skapa ny** väljer du **Samling.** Ge samlingen ett namn och välj **Skapa**.

2. Välj Nytt igen för att **skapa begäran.** I fönstret **Skapa ny** väljer du **Begär** och anger ett namn på begäran. Välj den samling som du skapade i föregående steg och välj sedan **Spara**.

3. Välj **POST HTTP-metoden** på fliken Builder (Byggare) och ange följande URL för att ladda upp geofence-filen till API:et för datauppladdning. Ersätt med din `{subscription-key}` primära prenumerationsnyckel.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    I URL-sökvägen `geojson` representerar värdet mot `dataFormat` parametern formatet för de data som laddas upp.

4. Välj **Body**  >  **raw** (Brödtext) som indataformat och välj **JSON** i listrutan. [Öppna JSON-datafilen](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)och kopiera JSON till brödtextavsnittet. Välj **Skicka**.

5. Välj **Skicka** och vänta tills begäran har bearbetas. När begäran har slutförts går du till **fliken Rubriker** i svaret. Kopiera värdet för **platsnyckeln,** som är `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Om du vill kontrollera statusen för API-anropet skapar du en **GET** HTTP-begäran på `status URL` . Du måste lägga till din primära prenumerationsnyckel i URL:en för autentisering. **GET-begäran** bör se ut som följande URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. När **GET** HTTP-begäran har slutförts returneras en `resourceLocation` . `resourceLocation`innehåller det unika för det `udid` uppladdade innehållet. Kopiera detta för `udid` senare användning i den här självstudien.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

IoT Hub möjliggör säker och tillförlitlig dubbelriktad kommunikation mellan ett IoT-program och de enheter som det hanterar. I den här självstudien vill du hämta information från din fordonsenhet för att fastställa platsen för uthyrningsbilen. I det här avsnittet skapar du en IoT-hubb i *resursgruppen ContosoRental.* Den här hubben ansvarar för att publicera telemetrihändelser för enheten.

> [!NOTE]
> Möjligheten att publicera enhetstelemetrihändelser på en Event Grid är för närvarande i förhandsversion. Den här funktionen är tillgänglig i alla regioner utom följande: USA, östra, USA, västra, Europa, västra, Azure Government, Azure China 21Vianet och Azure Tyskland.

Om du vill skapa en IoT-hubb *i resursgruppen ContosoRental* följer du stegen i [skapa en IoT-hubb.](../iot-hub/quickstart-send-telemetry-dotnet.md#create-an-iot-hub)

## <a name="register-a-device-in-your-iot-hub"></a>Registrera en enhet i din IoT-hubb

Enheter kan inte ansluta till IoT-hubben om de inte är registrerade i IoT Hub-identitetsregistret. Här skapar du en enskild enhet med namnet *InVehicleDevice*. Om du vill skapa och registrera enheten i din IoT-hubb följer du stegen i registrera en ny [enhet i IoT Hub](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub). Se till att kopiera enhetens primära anslutningssträng. Du behöver det senare.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Skapa en funktion och lägga till en Event Grid prenumeration

Azure Functions är en serverlös beräkningstjänst som gör att du kan köra små delar av kod ("funktioner"), utan att behöva uttryckligen etablera eller hantera beräkningsinfrastrukturen. Mer information finns i [Azure Functions](../azure-functions/functions-overview.md).

En funktion utlöses av en viss händelse. Här skapar du en funktion som utlöses av en Event Grid utlösare. Skapa relationen mellan utlösare och funktion genom att skapa en händelseprenumeration för IoT Hub-telemetrihändelser. När en enhettelemetrihändelse inträffar anropas din funktion som en slutpunkt och tar emot relevanta data för den enhet som du tidigare registrerade i IoT Hub.

Här är [C#-skriptkoden som din funktion kommer att innehålla](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Konfigurera nu din Azure-funktion.

1. På Azure Portal väljer du **Skapa en resurs.** Skriv **Funktionsapp** i sökrutan. Välj **Funktionsapp**  >  **Skapa.**

1. Ge **funktionsappen** ett namn på sidan för att skapa funktionsappen. Under **Resursgrupp** väljer **du ContosoRental** i listrutan. Välj **.NET Core** som **Körningsstack.** Längst ned på sidan väljer du **Nästa: Värd >**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Skärmbild av att skapa en funktionsapp.":::

1. För **Lagringskonto** väljer du det lagringskonto som du skapade [i Skapa ett Azure-lagringskonto.](#create-an-azure-storage-account) Välj **Granska + skapa**.

1. Granska informationen om funktionsappen och välj **Skapa**.

1. När appen har skapats lägger du till en funktion i den. Gå till funktionsappen. Välj **fönstret** Funktioner. Längst upp på sidan väljer du **+ Lägg till**. Panelen för funktionsmallen visas. Rulla nedåt i panelen och välj Azure Event Grid **utlösaren**.

     >[!IMPORTANT]
    > Azure **Event Hub-utlösaren** och **mallarna Azure Event Grid utlösare** har liknande namn. Se till att du väljer **mallen Azure Event Grid utlösare.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Skärmbild av att skapa en funktion.":::

1. Ge funktionen ett namn. I den här självstudien använder du namnet *GetGeoFunction,* men i allmänhet kan du använda vilket namn du vill. Välj **Skapa funktion**.

1. I den vänstra menyn väljer du **fönstret Kod +** test. Kopiera och klistra in [C#-skriptet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) i kodfönstret.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kopiera/skärmbild av att klistra in kod i funktionsfönstret.":::

1. Ersätt följande parametrar i C#-koden:
    * Ersätt **SUBSCRIPTION_KEY med** din Azure Maps primära prenumerationsnyckel för kontot.
    * Ersätt **UDID** med för `udid` geofence som du laddade upp i [Ladda upp en geofence](#upload-a-geofence).
    * Funktionen `CreateBlobAsync` i skriptet skapar en blob per händelse i datalagringskontot. Ersätt **ACCESS_KEY**, **ACCOUNT_NAME** och **STORAGE_CONTAINER_NAME** med lagringskontots åtkomstnyckel, kontonamn och datalagringscontainer. Dessa värden genererades när du skapade ditt lagringskonto i [Skapa ett Azure Storage-konto.](#create-an-azure-storage-account)

1. I den vänstra menyn väljer du **fönstret** Integration. Välj **Event Grid utlösare** i diagrammet. Ange ett namn för *utlösaren, eventGridEvent,* och välj **Skapa Event Grid prenumeration .**

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Skärmbild av lägg till händelseprenumeration.":::

1. Fyll i prenumerationsinformationen. Namnge händelseprenumerationen. För **Händelseschema** väljer **du Event Grid Schema**. För **Ämnestyper** väljer **du Azure IoT Hub Konton**. För **Resursgrupp** väljer du den resursgrupp som du skapade i början av den här självstudien. För **Resurs** väljer du den IoT-hubb som du skapade i "Skapa en Azure IoT-hubb". För **Filter to Event Types (Filtrera till** händelsetyper) väljer du Device **Telemetry (Enhettelemetri).**

   När du har valt de här alternativen ser du att **Ämnestyp ändras** till **IoT Hub**. För **System topic Name (Systemämnesnamn)** kan du använda samma namn som din resurs. I avsnittet **Slutpunktsinformation väljer** du slutligen **Välj en slutpunkt.** Acceptera alla inställningar och välj **Bekräfta markering.**

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Skärmbild av skapa händelseprenumeration.":::

1. Granska dina inställningar. Kontrollera att slutpunkten anger den funktion som du skapade i början av det här avsnittet. Välj **Skapa**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Skärmbild av bekräftelse för att skapa händelseprenumeration.":::

1. Nu är du tillbaka på panelen **Redigera utlösare.** Välj **Spara**.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Filtrera händelser med hjälp IoT Hub dirigering av meddelanden

När du lägger till Event Grid prenumeration i Azure-funktionen skapas automatiskt en meddelandeväg i den angivna IoT-hubben. Med meddelanderoutning kan du dirigera olika datatyper till olika slutpunkter. Du kan till exempel dirigera enhettelemetrimeddelanden, enhetslivscykelhändelser och enhetstvillingändringshändelser. Mer information finns i Använda [IoT Hub routning av meddelanden.](../iot-hub/iot-hub-devguide-messages-d2c.md)

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Skärmbild av meddelanderoutning i IoT Hub.":::

I ditt exempelscenario vill du bara ta emot meddelanden när uthyrningsbilen flyttas. Skapa en routningsfråga för att filtrera händelserna där `Engine` egenskapen är lika med **"PÅ".** Om du vill skapa en routningsfråga väljer **du vägen RouteToEventGrid** och **ersätter** routningsfrågan med **"Engine='ON".** Välj sedan **Spara**. Nu publicerar IoT-hubben endast enhetste telemetri där motorn är på.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Skärmbild av filterroutningsmeddelanden.":::

>[!TIP]
>Det finns olika sätt att köra frågor mot IoT-meddelanden från enhet till moln. Mer information om syntax för meddelanderoutning finns i [IoT Hub routning av meddelanden.](../iot-hub/iot-hub-devguide-routing-query-syntax.md)

## <a name="send-telemetry-data-to-iot-hub"></a>Skicka telemetridata till IoT Hub

När Din Azure-funktion körs kan du nu skicka telemetridata till IoT-hubben, som dirigerar dem till Event Grid. Använd ett C#-program för att simulera platsdata för en fordonsenhet för en uthyrningsbil. Om du vill köra programmet behöver du .NET Core SDK 2.1.0 eller senare på utvecklingsdatorn. Följ dessa steg för att skicka simulerade telemetridata till IoT-hubben:

1. Om du inte redan har gjort det laddar du ned [C#-projektet rentalCarSimulation.](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation)

2. Öppna filen i en valfri textredigerare och ersätt värdet för med det `simulatedCar.cs` som du sparade när du registrerade `connectionString` enheten. Spara ändringarna i filen.

3. Kontrollera att du har .NET Core installerat på datorn. I det lokala terminalfönstret går du till C#-projektets rotmapp och kör följande kommando för att installera de nödvändiga paketen för det simulerade enhetsprogrammet:

    ```cmd/sh
    dotnet restore
    ```

4. Kör följande kommando i samma terminal för att skapa och köra simuleringsprogrammet för uthyrningsbilen:

    ```cmd/sh
    dotnet run
    ```


  Din lokala terminal bör se ut som den nedan.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Skärmbild av terminalutdata.":::

Om du öppnar bloblagringscontainern nu kan du se fyra blobar för platser där fordonet låg utanför geofence-platsen.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Skärmbild av visa blobar i containern.":::

Följande karta visar fyra fordonsplatser utanför geofence- Varje plats loggades med jämna mellanrum.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Skärmbild av överträdelsekarta.":::

## <a name="explore-azure-maps-and-iot"></a>Utforska Azure Maps och IoT

Om du vill utforska de Azure Maps-API:er som används i den här självstudien kan du se:

* [Hämta omvänd sökadress](/rest/api/maps/search/getsearchaddressreverse)
* [Hämta Geofence](/rest/api/maps/spatial/getgeofence)

En fullständig lista över Azure Maps REST-API:er finns i:

* [Azure Maps REST API:er](/rest/api/maps/spatial/getgeofence)

* [IoT Plug and Play](../iot-pnp/index.yml)

Om du vill hämta en lista över enheter som är Azure-certifierade för IoT går du till:

* [Azure-certifierade enheter](https://devicecatalog.azure.com/)

## <a name="clean-up-resources"></a>Rensa resurser

Det finns inga resurser som kräver rensning.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skickar enhet-till-moln-telemetri och tvärtom finns i:


> [!div class="nextstepaction"]
> [Skicka telemetri från en enhet](../iot-hub/quickstart-send-telemetry-dotnet.md)