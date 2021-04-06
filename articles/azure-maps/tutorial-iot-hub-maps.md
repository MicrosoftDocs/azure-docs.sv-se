---
title: 'Självstudie: implementera IoT spatial Analytics | Microsoft Azure Maps'
description: 'Självstudie om hur du integrerar IoT Hub med API: er för Microsoft Azure Maps-tjänsten'
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/01/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: b5c65035f8b51b53f617d4562fe1982f53f0deec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98678279"
---
# <a name="tutorial-implement-iot-spatial-analytics-by-using-azure-maps"></a>Självstudie: implementera IoT spatial Analytics med hjälp av Azure Maps

I ett IoT-scenario är det vanligt att fånga och spåra relevanta händelser som inträffar i utrymme och tid. Exempel på detta är hantering av flottan, till gångs spårning, mobilitet och smarta stads program. Den här självstudien vägleder dig genom en lösning som spårar användning av Hyr Hyr bilar med hjälp av Azure Maps API: er.

I den här självstudien kommer du att:

> [!div class="checklist"]
> * Skapa ett Azure Storage-konto för att logga Car-spårnings data.
> * Överför en avgränsning till Azure Maps data tjänsten (för hands version) med hjälp av API: et för data överföring.
> * Skapa en hubb i Azure IoT Hub och registrera en enhet.
> * Skapa en funktion i Azure Functions, implementera affärs logik baserat på Azure Maps spatial analys.
> * Prenumerera på IoT Device telemetri-händelser från Azure-funktionen via Azure Event Grid.
> * Filtrera telemetri-händelserna genom att använda IoT Hub meddelanderoutning.

## <a name="prerequisites"></a>Förutsättningar

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. [Skapa ett Azure Maps-konto](quick-demo-map-app.md#create-an-azure-maps-account).

3. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel. Mer information finns i [hantera autentisering i Azure Maps](how-to-manage-authentication.md).

4. [Skapa en resurs grupp](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). I den här självstudien namnger vi vår resurs grupp *ContosoRental*, men du kan välja det namn du vill.

5. Ladda ned [C#-projektet rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation).

I den här självstudien används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

## <a name="use-case-rental-car-tracking"></a>Användnings fall: Hyr bils spårning

Anta att ett bil uthyrnings företag vill logga plats information, sträcka sig över sitt tillstånd och köra tillstånd för sina hyr bilar. Företaget vill också lagra den här informationen när en bil lämnar rätt auktoriserad geografisk region.

Hyr bilar är utrustade med IoT-enheter som regelbundet skickar telemetridata till IoT Hub. Telemetrin innehåller den aktuella platsen och anger om bilens motor körs. Schemat för enhets platsen följer IoT Plug and Play- [schemat för geospatiala data](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v1-preview/schemas/geospatial.md). Schemat för hyr bils enhets telemetri ser ut som följande JSON-kod:

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

I den här självstudien spårar du bara ett fordon. När du har konfigurerat Azure-tjänsterna måste du ladda ned [RentalCarSimulation C#-projektet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) för att köra fordon simulatorn. Hela processen, från händelse till Function-körning, sammanfattas i följande steg:

1. Enheten i fordonet skickar telemetridata till IoT Hub.

2. Om Car-motorn körs publicerar hubben telemetridata till Event Grid.

3. En Azure-funktion utlöses på grund av händelse prenumerationen på händelser för telemetri.

4. Funktionen loggar enhets platsens koordinater, händelse tid och enhets-ID. Den använder sedan den [spatiala gränsen för att hämta API](/rest/api/maps/spatial/getgeofence) för att avgöra om bilen har drivas utanför avgränsningen. Om den har passerat utanför gränserna, lagrar funktionen plats data som tas emot från händelsen i en BLOB-behållare. Funktionen frågar också om [Sök adressen omvänts](/rest/api/maps/search/getsearchaddressreverse) för att översätta koordinatens plats till en gatuadress och lagrar den med resten av enhetens plats data.

Följande diagram visar en översikt över systemet på en hög nivå.

   :::image type="content" source="./media/tutorial-iot-hub-maps/system-diagram.png" border="false" alt-text="Diagram över system översikt.":::

Följande bild visar det yt avgränsnings ytan i blått. Hyr bils väg visas med en grön linje.

   :::image type="content" source="./media/tutorial-iot-hub-maps/geofence-route.png" border="false" alt-text="Bild som visar en avgränsnings väg.":::

## <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

Om du vill lagra spårnings data för Car-överträdelse skapar du ett [Allmänt-syfte v2-lagrings konto](../storage/common/storage-account-overview.md#general-purpose-v2-accounts) i resurs gruppen. Om du inte har skapat någon resurs grupp följer du anvisningarna i [skapa en resurs grupp](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups). I den här självstudien får du namnge resurs gruppen *ContosoRental*.

Följ instruktionerna i [skapa ett lagrings konto](../storage/common/storage-account-create.md?tabs=azure-portal)för att skapa ett lagrings konto. I den här självstudien namnger du lagrings kontots *contosorentalstorage*, men i allmänhet kan du ge det något som du vill.

När du har skapat ditt lagrings konto måste du skapa en behållare för att lagra loggnings data.

1. Gå till ditt nyligen skapade lagrings konto. I avsnittet **Essentials** väljer du **behållare** -länken.

    :::image type="content" source="./media/tutorial-iot-hub-maps/containers.png" alt-text="Skärm bild av behållare för Blob Storage.":::

2. I det övre vänstra hörnet väljer du **+ container**. En panel visas till höger i webbläsaren. Namnge din container *contoso-hyr loggar* och välj **skapa**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/container-new.png" alt-text="Skärm bild av skapa en BLOB-behållare.":::

3. Gå till rutan **åtkomst nycklar** i ditt lagrings konto och kopiera **lagrings kontots namn** och **nyckel** värde i avsnittet **KEY1** . Du behöver båda dessa värden i avsnittet "skapa en Azure-funktion och lägga till en Event Grid-prenumeration".

    :::image type="content" source="./media/tutorial-iot-hub-maps/access-keys.png" alt-text="Skärm bild av namn och nyckel för kopiering av lagrings konto.":::

## <a name="upload-a-geofence"></a>Ladda upp en avgränsning

Sedan använder du [Postman-appen](https://www.getpostman.com) för att [Ladda upp den här gränsen](./geofence-geojson.md) för Azure Maps. Den här gränsen definierar det tillåtna geografiska området för vårt hyr fordon. Du kommer att använda den yttersta gränsen i din Azure-funktion för att avgöra om en bil har flyttat utanför avgränsnings området.

Följ de här stegen för att ladda upp den här avgränsningen med hjälp av API: et för Azure Maps data överföring: 

1. Öppna Postman-appen och välj **ny**. I fönstret **Skapa nytt** väljer du **samling**. Namnge samlingen och välj **skapa**.

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran** och anger ett namn för begäran. Välj den samling som du skapade i föregående steg och välj sedan **Spara**.

3. Välj metoden **post** http på fliken Builder och ange följande URL för att ladda upp avgränsningen till API: et för data uppladdning. Se till att ersätta `{subscription-key}` med den primära prenumerations nyckeln.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    I URL-sökvägen `geojson` representerar värdet mot `dataFormat` parametern formatet för de data som laddas upp.

4. Välj **brödtext**  >  **RAW** som indataformat och välj **JSON** i list rutan. [Öppna JSON-datafilen](https://raw.githubusercontent.com/Azure-Samples/iothub-to-azure-maps-geofencing/master/src/Data/geofence.json?token=AKD25BYJYKDJBJ55PT62N4C5LRNN4)och kopiera JSON till avsnittet brödtext. Välj **Skicka**.

5. Välj **Skicka** och vänta på att begäran ska bearbetas. När begäran har slutförts går du till fliken **sidhuvud** i svaret. Kopiera värdet för **plats** nyckeln, som är `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Om du vill kontrol lera status för API-anropet skapar du en **Get** http-begäran på `status URL` . Du måste lägga till din primära prenumerations nyckel till URL: en för autentisering. **Get** -begäran bör likna följande URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={subscription-key}
   ```
   
7. När **Get** http-begäran har slutförts returneras en `resourceLocation` . `resourceLocation`Innehåller det unika `udid` för det överförda innehållet. Kopiera detta `udid` för senare användning i den här självstudien.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

IoT Hub möjliggör säker och tillförlitlig dubbelriktad kommunikation mellan ett IoT-program och de enheter som hanteras av. I den här självstudien vill du hämta information från din fordons enhet för att fastställa placeringen av hyr bilen. I det här avsnittet skapar du en IoT-hubb i resurs gruppen *ContosoRental* . Den här hubben ansvarar för att publicera dina telemetri om enhets händelser.

> [!NOTE]
> Möjligheten att publicera händelser för enhets telemetri på Event Grid är för närvarande en för hands version. Den här funktionen är tillgänglig i alla regioner förutom följande: östra USA, västra USA, Västeuropa, Azure Government, Azure Kina 21Vianet och Azure Germany.

Om du vill skapa en IoT-hubb i resurs gruppen *ContosoRental* följer du stegen i [skapa en IoT-hubb](../iot-hub/quickstart-send-telemetry-dotnet.md#create-an-iot-hub).

## <a name="register-a-device-in-your-iot-hub"></a>Registrera en enhet i IoT Hub

Enheter kan inte ansluta till IoT Hub om de inte är registrerade i IoT Hub Identity-registret. Här skapar du en enskild enhet med namnet *InVehicleDevice*. Om du vill skapa och registrera enheten i din IoT-hubb följer du stegen i [Registrera en ny enhet i IoT Hub](../iot-hub/iot-hub-create-through-portal.md#register-a-new-device-in-the-iot-hub). Se till att kopiera den primära anslutnings strängen för enheten. Du behöver det senare.

## <a name="create-a-function-and-add-an-event-grid-subscription"></a>Skapa en funktion och Lägg till en Event Grid-prenumeration

Azure Functions är en server lös beräknings tjänst som gör att du kan köra små delar av kod ("Functions"), utan att uttryckligen behöva etablera eller hantera beräknings infrastruktur. Läs mer i [Azure Functions](../azure-functions/functions-overview.md).

En funktion utlöses av en viss händelse. Här skapar du en funktion som utlöses av en Event Grid-utlösare. Skapa relationen mellan utlösare och funktion genom att skapa en händelse prenumeration för IoT Hub telemetri för enhets händelser. När en händelse för telemetri om enhet inträffar, anropas funktionen som en slut punkt och tar emot relevanta data för den enhet som du tidigare registrerade i IoT Hub.

Här är den [C#-skript kod som din funktion kommer att innehålla](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx).

Konfigurera nu Azure-funktionen.

1. I instrument panelen Azure Portal väljer du **skapa en resurs**. Skriv **Funktionsapp** i text rutan Sök. Välj **Funktionsapp**  >  **skapa**.

1. På sidan Skapa **Funktionsapp** namnger du din Function-app. Under **resurs grupp** väljer du **ContosoRental** i list rutan. Välj **.net Core** som **körnings stack**. Längst ned på sidan väljer du **Nästa: värd >**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/rental-app.png" alt-text="Skärm bild av skapa en Function-app.":::

1. För **lagrings konto** väljer du det lagrings konto som du skapade i [skapa ett Azure Storage-konto](#create-an-azure-storage-account). Välj **Granska + skapa**.

1. Granska programmets information och välj **skapa**.

1. När appen har skapats lägger du till en funktion i den. Gå till Function-appen. Välj rutan **funktioner** . Längst upp på sidan väljer du **+ Lägg till**. Panelen funktions mal len visas. Rulla nedåt i panelen och välj **Azure Event Grid utlösare**.

     >[!IMPORTANT]
    > **Azure Event Hub-utlösaren** och **Azure Event Grid utlösare** har liknande namn. Se till att du väljer mallen **Azure Event Grid utlösare** .

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create.png" alt-text="Skärm bild av skapa en funktion.":::

1. Ge funktionen ett namn. I den här självstudien använder du namnet, *GetGeoFunction*, men i allmänhet kan du använda alla namn som du vill. Välj **skapa funktion**.

1. På den vänstra menyn väljer du kod-och **test** fönstret. Kopiera och klistra in [C#-skriptet](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/blob/master/src/Azure%20Function/run.csx) i fönstret kod.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-code.png" alt-text="Kopiera/skärm bild av klistra in kod i funktions fönstret.":::

1. Ersätt följande parametrar i C#-koden:
    * Ersätt **SUBSCRIPTION_KEY** med din primära prenumerations nyckel för Azure Maps-kontot.
    * Ersätt **UDID** med det geografiskt `udid` avgränsnings tecken som du laddade upp i [överför ett avgränsnings](#upload-a-geofence)tecken.
    * `CreateBlobAsync`Funktionen i skriptet skapar en BLOB per händelse i data lagrings kontot. Ersätt **ACCESS_KEY**, **ACCOUNT_NAME** och **STORAGE_CONTAINER_NAME** med lagrings kontots åtkomst nyckel, konto namn och data lagrings behållare. Dessa värden genererades när du skapade ditt lagrings konto i [skapa ett Azure Storage-konto](#create-an-azure-storage-account).

1. På den vänstra menyn väljer du **integrations** fönstret. Välj **Event Grid utlösare** i diagrammet. Skriv ett namn för utlösaren, *eventGridEvent* och välj **Skapa event Grid prenumeration**.

     :::image type="content" source="./media/tutorial-iot-hub-maps/function-integration.png" alt-text="Skärm bild av Lägg till händelse prenumeration.":::

1. Fyll i prenumerations informationen. Namnge händelse prenumerationen. För **händelse schema** väljer du **Event Grid schema**. För **ämnes typer** väljer du **Azure IoT Hub-konton**. För **resurs grupp** väljer du den resurs grupp som du skapade i början av den här självstudien. För **resurs** väljer du den IoT-hubb som du skapade i "skapa en Azure IoT Hub". För **filter till händelse typer** väljer du **telemetri för enhet**.

   När du har valt de här alternativen visas **ämnes typen** ändra till **IoT Hub**. I **namn på system ämne** kan du använda samma namn som din resurs. I avsnittet **slut punkts information** väljer du slutligen **Välj en slut punkt**. Godkänn alla inställningar och välj **Bekräfta markering**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription.png" alt-text="Skärm bild av prenumerationen skapa händelse.":::

1. Granska inställningarna. Kontrol lera att slut punkten anger den funktion som du skapade i början av det här avsnittet. Välj **Skapa**.

    :::image type="content" source="./media/tutorial-iot-hub-maps/function-create-event-subscription-confirm.png" alt-text="Skärm bild av bekräftelse av åtgärden Skapa händelse prenumeration.":::

1. Nu är du tillbaka på panelen **Redigera utlösare** . Välj **Spara**.

## <a name="filter-events-by-using-iot-hub-message-routing"></a>Filtrera händelser med hjälp av IoT Hub meddelanderoutning

När du lägger till en Event Grid-prenumeration i Azure-funktionen skapas en meddelande väg automatiskt i den angivna IoT-hubben. Med meddelanderoutning kan du dirigera olika data typer till olika slut punkter. Du kan till exempel dirigera meddelanden om enhets telemetri, livs cykel händelser för enhet och enhets dubbla ändrings händelser. Mer information finns i [använda IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md)meddelanderoutning.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-route.png" alt-text="Skärm bild av meddelanderoutning i IoT Hub.":::

I ditt exempel scenario vill du bara ta emot meddelanden när hyr bil flyttas. Skapa en cirkulations fråga för att filtrera händelserna där `Engine` egenskapen är lika med **"på"**. Om du vill skapa en cirkulations fråga väljer du **RouteToEventGrid** -vägen och ersätter **Oper. åga** med **"motor =" på ""**. Välj sedan **Spara**. Nu publicerar IoT Hub bara enhets telemetri där motorn är på.

:::image type="content" source="./media/tutorial-iot-hub-maps/hub-filter.png" alt-text="Skärm bild av filtrera Dirigerings meddelanden.":::

>[!TIP]
>Det finns olika sätt att fråga IoT-meddelanden från enhet till moln. Mer information om syntax för meddelanderoutning finns i [IoT Hub](../iot-hub/iot-hub-devguide-routing-query-syntax.md)meddelanderoutning.

## <a name="send-telemetry-data-to-iot-hub"></a>Skicka telemetridata till IoT Hub

När din Azure-funktion körs kan du nu skicka telemetridata till IoT Hub, som dirigerar den till Event Grid. Använd ett C#-program för att simulera plats data för en fordons enhet i en hyr bil. Om du vill köra programmet behöver du .NET Core SDK 2.1.0 eller senare på din utvecklings dator. Följ de här stegen för att skicka simulerade telemetridata till IoT Hub:

1. Om du inte redan har gjort det kan du ladda ned [rentalCarSimulation](https://github.com/Azure-Samples/iothub-to-azure-maps-geofencing/tree/master/src/rentalCarSimulation) C#-projektet.

2. Öppna `simulatedCar.cs` filen i valfri text redigerare och Ersätt värdet för `connectionString` med den som du sparade när du registrerade enheten. Spara ändringarna i filen.

3. Kontrol lera att du har .NET Core installerat på datorn. I det lokala terminalfönstret går du till rotmappen i C#-projektet och kör följande kommando för att installera de nödvändiga paketen för det simulerade enhets programmet:

    ```cmd/sh
    dotnet restore
    ```

4. I samma Terminal kör du följande kommando för att skapa och köra ett simulerings program för hyr bil:

    ```cmd/sh
    dotnet run
    ```


  Den lokala terminalen bör se ut som den som anges nedan.

:::image type="content" source="./media/tutorial-iot-hub-maps/terminal.png" alt-text="Skärm bild av utdata från terminalen.":::

Om du öppnar Blob storage-behållaren nu kan du se fyra blobbar för platser där fordonet befinner sig utanför gränsen.

:::image type="content" source="./media/tutorial-iot-hub-maps/blob.png" alt-text="Skärm bild av Visa blobbar i behållare.":::

I följande karta visas fyra fordons platser utanför den yttre gränsen. Varje plats loggades med regelbundna tidsintervall.

:::image type="content" source="./media/tutorial-iot-hub-maps/violation-map.png" alt-text="Skärm bild av överträdelse-karta.":::

## <a name="explore-azure-maps-and-iot"></a>Utforska Azure Maps och IoT

Information om Azure Maps-API: er som används i den här självstudien finns i:

* [Hämta omvänd söknings adress](/rest/api/maps/search/getsearchaddressreverse)
* [Hämta avgränsning](/rest/api/maps/spatial/getgeofence)

En fullständig lista över Azure Maps REST API: er finns i:

* [Azure Maps REST-API: er](/rest/api/maps/spatial/getgeofence)

* [IoT Plug and Play](../iot-pnp/index.yml)

Om du vill hämta en lista över enheter som är Azure-certifierade för IoT går du till:

* [Azure Certified-enheter](https://catalog.azureiotsolutions.com/)

## <a name="clean-up-resources"></a>Rensa resurser

Det finns inga resurser som kräver rensning.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skickar telemetri från enhet till moln, och det andra sättet, finns i:


> [!div class="nextstepaction"]
> [Skicka telemetri från en enhet](../iot-hub/quickstart-send-telemetry-dotnet.md)