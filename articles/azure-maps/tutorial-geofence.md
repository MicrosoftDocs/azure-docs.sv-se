---
title: 'Självstudie: skapa en inhägnad och spåra enheter på en Microsoft Azure karta'
description: Själv studie kurs om hur du konfigurerar ett geografiskt avgränsnings tecken. Se hur du spårar enheter i förhållande till den här gränsen genom att använda tjänsten Azure Maps spatial
author: anastasia-ms
ms.author: v-stharr
ms.date: 8/20/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 759adea3cf34b79c76b6facec3bd4626ca54107e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98625040"
---
# <a name="tutorial-set-up-a-geofence-by-using-azure-maps"></a>Självstudie: Konfigurera ett geofence med hjälp av Azure Maps

I den här självstudien får du lära dig grunderna i hur du skapar och använder Azure Maps-tjänster för inhägnad. Du gör detta i samband med följande scenario:

*En konstruktions plats hanterare måste spåra utrustning när den går in och lämnar perimeter för ett byggnads område. När en del av utrustningen avslutar eller anger dessa perimeter skickas ett e-postmeddelande till Operations Manager.*

Azure Maps tillhandahåller ett antal tjänster för att stödja spårning av utrustning som går in och lämnar arbets ytan. I den här kursen får du:

> [!div class="checklist"]
> * Överför [polystaket av POLYjson-data](geofence-geojson.md) som definierar de konstruktions plats områden som du vill övervaka. Du använder [API: et för data överföring](/rest/api/maps/data/uploadpreview) för att överföra avgränsningar som polygon-koordinater till ditt Azure Maps-konto.
> * Konfigurera två [Logic Apps](../event-grid/handler-webhooks.md#logic-apps) som, när de utlöses, skickar e-postaviseringar till arbets platsens Operations Manager när utrustningen går in och avslutar området för avgränsning.
> * Använd [Azure Event Grid](../event-grid/overview.md) för att prenumerera på att ange och avsluta händelser för ditt Azure Maps-avgränsning. Du ställer in två händelse prenumerationer för webhook som anropar de HTTP-slutpunkter som definierats i dina två Logic Apps. Logi Kap par skickar sedan lämpliga e-postaviseringar om utrustning som flyttas utanför eller genom att ange ett geografiskt avgränsnings tecken.
> * Använd [Sök funktionen för att hämta API: er](/rest/api/maps/spatial/getgeofence) för att ta emot meddelanden när en del av utrustningen avslutas och anger de områden som ligger utanför gränsen.

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps-konto](quick-demo-map-app.md#create-an-azure-maps-account).
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.

I den här självstudien används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

## <a name="upload-geofencing-geojson-data"></a>Överför polystaket, polyjson-data

I den här självstudien laddar du upp polyinhägnaden av polyjson-data som innehåller en `FeatureCollection` . `FeatureCollection`Innehåller två gränser som definierar polygonens avgränsnings områden på bygg platsen. Den första gränsen har ingen förfallo tid eller begränsningar. Den andra kan bara frågas mot under kontors tid (9:00 AM-5:00 PM i Stilla havs området) och kommer inte längre att gälla efter den 1 januari 2022. Mer information om formatet för det här formatet finns i [polyjson-data (polystaket](geofence-geojson.md)).

>[!TIP]
>Du kan när som helst uppdatera dina avgränsnings data. Mer information finns i [API för data överföring](/rest/api/maps/data/uploadpreview).

1. Öppna Postman-appen. Välj **nytt** längst upp. I fönstret **Skapa nytt** väljer du **samling**. Namnge samlingen och välj **skapa**.

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående steg och välj sedan **Spara**.

3. Välj metoden **post** http på fliken Builder och ange följande URL för att ladda upp de polyavgränsnings data som ska Azure Maps. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```HTTP
    https://atlas.microsoft.com/mapData/upload?subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&dataFormat=geojson
    ```

    `geojson`Parametern i URL-sökvägen representerar data formatet för de data som överförs.

4. Välj fliken **brödtext** . Välj **RAW** och sedan **JSON** som indataformat. Kopiera och klistra in följande interjson-data i **bröd** text området:

   ```JSON
   {
      "type": "FeatureCollection",
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13393688201903,
                  47.63829579223815
                ],
                [
                  -122.13389128446579,
                  47.63782047131512
                ],
                [
                  -122.13240802288054,
                  47.63783312249837
                ],
                [
                  -122.13238388299942,
                  47.63829037035086
                ],
                [
                  -122.13393688201903,
                  47.63829579223815
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "1"
          }
        },
        {
          "type": "Feature",
          "geometry": {
            "type": "Polygon",
            "coordinates": [
              [
                [
                  -122.13374376296996,
                  47.63784758098976
                ],
                [
                  -122.13277012109755,
                  47.63784577367854
                ],
                [
                  -122.13314831256866,
                  47.6382813338708
                ],
                [
                  -122.1334782242775,
                  47.63827591198201
                ],
                [
                  -122.13374376296996,
                  47.63784758098976
                ]
              ]
            ]
          },
          "properties": {
            "geometryId": "2",
            "validityTime": {
            "expiredTime": "2022-01-01T00:00:00",
            "validityPeriod": [
                {
                  "startTime": "2020-07-15T16:00:00",
                  "endTime": "2020-07-15T24:00:00",
                  "recurrenceType": "Daily",
                  "recurrenceFrequency": 1,
                  "businessDayOnly": true
                }
              ]
            }
          }
        }
      ]
   }
   ```

5. Välj **Skicka** och vänta på att begäran ska bearbetas. När begäran har slutförts går du till fliken **sidhuvud** i svaret. Kopiera värdet för **plats** nyckeln, som är `status URL` .

    ```http
    https://atlas.microsoft.com/mapData/operations/<operationId>?api-version=1.0
    ```

6. Om du vill kontrol lera status för API-anropet skapar du en **Get** http-begäran på `status URL` . Du måste lägga till din primära prenumerations nyckel till URL: en för autentisering. **Get** -begäran bör likna följande URL:

   ```HTTP
   https://atlas.microsoft.com/mapData/<operationId>/status?api-version=1.0&subscription-key={Subscription-key}
   ```

7. När **Get** http-begäran har slutförts returneras en `resourceLocation` . `resourceLocation`Innehåller det unika `udid` för det överförda innehållet. Spara detta `udid` om du vill fråga The get unstängsel API i det sista avsnittet av den här självstudien. Du kan också använda `resourceLocation` URL: en för att hämta metadata från den här resursen i nästa steg.

      ```json
      {
          "status": "Succeeded",
          "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0"
      }
      ```

8. Om du vill hämta metadata för innehåll skapar **du en http** -begäran på den `resourceLocation` URL som hämtades i steg 7. Se till att lägga till din primära prenumerations nyckel till URL: en för autentisering. **Get** -begäran bör likna följande URL:

    ```http
   https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}
    ```

9. När **Get** http-begäran har slutförts innehåller svars texten som `udid` anges i i `resourceLocation` steg 7. Den kommer också att innehålla platsen för att komma åt och hämta innehållet i framtiden och andra metadata om innehållet. Ett exempel på det övergripande svaret är:

    ```json
    {
        "udid": "{udid}",
        "location": "https://atlas.microsoft.com/mapData/{udid}?api-version=1.0",
        "created": "7/15/2020 6:11:43 PM +00:00",
        "updated": "7/15/2020 6:11:45 PM +00:00",
        "sizeInBytes": 1962,
        "uploadStatus": "Completed"
    }
    ```

## <a name="create-workflows-in-azure-logic-apps"></a>Skapa arbets flöden i Azure Logic Apps

Därefter skapar du två [Logic app](../event-grid/handler-webhooks.md#logic-apps) -slutpunkter som utlöser ett e-postmeddelande. Så här skapar du en första:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. I det övre vänstra hörnet av Azure Portal väljer du **skapa en resurs**.

3. Skriv **Logic app** i rutan **Sök på Marketplace** .

4. Välj **Logic app**  >  **create** i resultatet.

5. På sidan **Logic app** anger du följande värden:
    * Den **prenumeration** som du vill använda för den här Logic-appen.
    * **Resurs grupps** namnet för den här Logic-appen. Du kan välja att **skapa en ny** eller **använda en befintlig** resurs grupp.
    * **Logic App-namnet** för din Logi Kap par. I det här fallet använder `Equipment-Enter` du som namn.

    I den här självstudien behåller du alla andra värden i standardinställningarna.

    :::image type="content" source="./media/tutorial-geofence/logic-app-create.png" alt-text="Skärm bild av skapa en logisk app.":::

6. Välj **Granska + skapa**. Granska inställningarna och välj **skapa** för att skicka distributionen. När distributionen har slutförts väljer **du gå till resurs**. Du kommer till **Logic App Designer**.

7. Välj en utlösnings typ. Rulla ned till avsnittet **börja med ett gemensamt utlösare** . Välj **när en HTTP-begäran tas emot**.

     :::image type="content" source="./media/tutorial-geofence/logic-app-trigger.png" alt-text="Skärm bild av skapa en HTTP-utlösare för Logic app.":::

8. I det övre högra hörnet i Logic App Designer väljer du **Spara**. **Http post-URL: en** genereras automatiskt. Spara URL: en. Du behöver den i nästa avsnitt för att skapa en händelse slut punkt.

    :::image type="content" source="./media/tutorial-geofence/logic-app-httprequest.png" alt-text="Skärm bild av HTTP-begärans URL och JSON för Logic app.":::

9. Välj **+ nytt steg**. Nu ska du välja en åtgärd. Skriv `outlook.com email` i sökrutan. Rulla nedåt i listan **åtgärder** och välj **Skicka ett e-postmeddelande (v2)**.
  
    :::image type="content" source="./media/tutorial-geofence/logic-app-designer.png" alt-text="Skärm bild av skapa en Logic Apps designer.":::

10. Logga in på ditt Outlook-konto. Se till att välja **Ja** för att tillåta att Logic-appen får åtkomst till kontot. Fyll i fälten för att skicka ett e-postmeddelande.

    :::image type="content" source="./media/tutorial-geofence/logic-app-email.png" alt-text="Skärm bild av skapa ett e-poststeg för att skicka ett Logic-program.":::

    >[!TIP]
    > Du kan hämta data för multijson-svar, till exempel `geometryId` eller `deviceId` , i dina e-postaviseringar. Du kan konfigurera Logic Apps att läsa data som skickas av Event Grid. Information om hur du konfigurerar Logic Apps att använda och skicka händelse data till e-postaviseringar finns i [Självstudier: skicka e-postaviseringar om Azure IoT Hub händelser med event Grid och Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

11. I det övre vänstra hörnet i Logic App Designer väljer du **Spara**.

Om du vill skapa en andra Logic app för att meddela chefen när utrustningen avslutar Bygg platsen upprepar du steg 3-11. Namnge Logic-appen `Equipment-Exit` .

## <a name="create-azure-maps-events-subscriptions"></a>Skapa prenumerationer för Azure Maps händelser

Azure Maps stöder [tre händelse typer](../event-grid/event-schema-azure-maps.md). Här måste du skapa två olika händelse prenumerationer: en för ett geografiskt avgränsnings tecken, och en för händelser med en mark gräns.

Följande steg visar hur du skapar en händelse prenumeration för det avgränsnings sätt som anger händelser. Du kan prenumerera på händelser med en inhägnad genom att upprepa stegen på liknande sätt.

1. Gå till ditt Azure Maps-konto. I instrument panelen väljer du **prenumerationer**. Välj ditt prenumerations namn och välj **händelser** på menyn Inställningar.

    :::image type="content" source="./media/tutorial-geofence/events-tab.png" alt-text="Skärm bild av gå till Azure Maps konto händelser.":::

2. Om du vill skapa en händelse prenumeration väljer du **+ händelse prenumeration** på sidan händelser.

    :::image type="content" source="./media/tutorial-geofence/create-event-subscription.png" alt-text="Skärm bild av skapa en prenumeration på Azure Maps händelser.":::

3. På sidan **Skapa händelse prenumeration** anger du följande värden:
    * **Namnet** på händelse prenumerationen.
    * **Händelse schemat** ska vara *Event Grid schema*.
    * **Systemets ämnes namn** för den här händelse prenumerationen, som i det här fallet är `Contoso-Construction` .
    * För **filter till händelse typer** väljer du `Geofence Entered` som händelse typ.
    * För **slut punkts typ** väljer du `Web Hook` .
    * För **slut punkt** kopierar du HTTP post-URL: en för Logic-appen ange den slut punkt som du skapade i föregående avsnitt. Om du har glömt att spara det kan du bara gå tillbaka till Logic App Designer och kopiera den från steget HTTP-utlösare.

    :::image type="content" source="./media/tutorial-geofence/events-subscription.png" alt-text="Skärm bild av prenumerations information för Azure Maps händelser.":::

4. Välj **Skapa**.

Upprepa steg 1-4 för den logiska appens slut punkt som du skapade i föregående avsnitt. I steg 3, se till att välja `Geofence Exited` som händelse typ.

## <a name="use-spatial-geofence-get-api"></a>Använd API för att hämta geospatiala gränser

Använd [spatiala polystängsel Hämta API](/rest/api/maps/spatial/getgeofence) för att skicka e-postmeddelanden till Operations Manager när en del av utrustningen går in eller avslutar-avgränsningarna.

Varje utrustnings enhet har en `deviceId` . I den här självstudien spårar du en enskild utrustning med ett unikt ID för `device_1` .

Följande diagram visar de fem platserna för utrustningen över tid, med början från *Start* platsen, som ligger någonstans utanför de yttre avgränsningarna. I den här självstudien är *Start* platsen odefinierad, eftersom du inte kommer att fråga enheten på den platsen.

När du frågar efter den [spatiala netstängsel get-API: et](/rest/api/maps/spatial/getgeofence) med en utrustnings plats som anger inledande gräns eller avsluta, anropar Event Grid lämplig Logic app-slutpunkt för att skicka ett e-postmeddelande till Operations Manager.

Vart och ett av följande avsnitt gör API-begäranden med hjälp av de fem olika plats koordinaterna för utrustningen.

![Diagram över avgränsnings karta i Azure Maps](./media/tutorial-geofence/geofence.png)

### <a name="equipment-location-1-47638237-122132483"></a>Utrustnings plats 1 (47.638237,-122,132483)

1. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Gör det till *plats 1*. Välj den samling som du skapade i [avsnittet Ladda upp polystaket-data](#upload-geofencing-geojson-data)och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. Ersätt `{Azure-Maps-Primary-Subscription-key}` med den primära prenumerations nyckeln och `{udid}` med det `udid` som du sparade i [avsnittet Ladda upp polystaket-indata-JSON](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.638237&lon=-122.1324831&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Välj **Skicka**. Följande interjson visas i svars fönstret.

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638291,
          "nearestLon": -122.132483
        },
        {
          "deviceId": "device_1",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.638053,
          "nearestLon": -122.13295
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ```

I föregående geografiskt JSON-svar innebär det negativa avståndet från huvud platsens inre gräns att utrustningen ligger innanför den inre gränsen. Det positiva avståndet från under platsens land avgränsning innebär att utrustningen ligger utanför under platsens yttre gräns. Eftersom det här är första gången den här enheten finns inuti huvud platsens inre gräns, `isEventPublished` anges parametern till `true` . Operations Manager tar emot ett e-postmeddelande om att utrustningen har fyllt i avgränsningen.

### <a name="location-2-4763800-122132531"></a>Plats 2 (47.63800,-122,132531)

1. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Gör det till *plats 2*. Välj den samling som du skapade i [avsnittet Ladda upp polystaket-data](#upload-geofencing-geojson-data)och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. Ersätt `{Azure-Maps-Primary-Subscription-key}` med den primära prenumerations nyckeln och `{udid}` med det `udid` som du sparade i [avsnittet Ladda upp polystaket-indata-JSON](#upload-geofencing-geojson-data).

   ```HTTP
   https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udId={udId}&lat=47.63800&lon=-122.132531&searchBuffer=5&isAsync=True&mode=EnterAndExit
   ```

3. Välj **Skicka**. Följande Poly-JSON visas i svars fönstret:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637997,
          "nearestLon": -122.132399
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": 999.0,
          "nearestLat": 47.63789,
          "nearestLon": -122.132809
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

I föregående geografiskt JSON-svar har utrustningen hållits på huvud platsens gräns och har inte angett ett geografiskt område. Därför `isEventPublished` anges parametern till `false` och Operations Manager får inte några e-postaviseringar.

### <a name="location-3-4763810783315048-12213336020708084"></a>Plats 3 (47.63810783315048,-122.13336020708084)

1. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Gör den till *plats 3*. Välj den samling som du skapade i [avsnittet Ladda upp polystaket-data](#upload-geofencing-geojson-data)och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. Ersätt `{Azure-Maps-Primary-Subscription-key}` med den primära prenumerations nyckeln och `{udid}` med det `udid` som du sparade i [avsnittet Ladda upp polystaket-indata-JSON](#upload-geofencing-geojson-data).

    ```HTTP
      https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63810783315048&lon=-122.13336020708084&searchBuffer=5&isAsync=True&mode=EnterAndExit
      ```

3. Välj **Skicka**. Följande Poly-JSON visas i svars fönstret:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.638294,
          "nearestLon": -122.133359
        },
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "2",
          "distance": -999.0,
          "nearestLat": 47.638161,
          "nearestLon": -122.133549
        }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

I föregående geografiskt JSON-svar har utrustningen hållits i den huvudsakliga platsens huvud plats, men har angett under platsens gräns. Därför `isEventPublished` anges parametern till `true` . Operations Manager får ett e-postmeddelande som anger att utrustningen har angett ett avgränsnings tecken.

>[!NOTE]
>Om utrustningen har flyttats till under platsen efter kontors tid skulle ingen händelse publiceras och Operations Manager skulle inte ta emot några meddelanden.  

### <a name="location-4-47637988-1221338344"></a>Plats 4 (47.637988,-122,1338344)

1. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Gör IT- *plats 4*. Välj den samling som du skapade i [avsnittet Ladda upp polystaket-data](#upload-geofencing-geojson-data)och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. Ersätt `{Azure-Maps-Primary-Subscription-key}` med den primära prenumerations nyckeln och `{udid}` med det `udid` som du sparade i [avsnittet Ladda upp polystaket-indata-JSON](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.637988&userTime=2023-01-16&lon=-122.1338344&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Välj **Skicka**. Följande Poly-JSON visas i svars fönstret:

    ```json
    {
      "geometries": [
        {
          "deviceId": "device_01",
          "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
          "geometryId": "1",
          "distance": -999.0,
          "nearestLat": 47.637985,
          "nearestLon": -122.133907
        }
      ],
      "expiredGeofenceGeometryId": [
        "2"
      ],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": false
    }
    ````

I föregående geografiskt JSON-svar har utrustningen hållits på den huvudsakliga platsens gräns, men har slut på under platsens gräns. Observera dock att `userTime` värdet är efter det `expiredTime` som definieras i de här gräns värdena. Därför `isEventPublished` anges parametern till `false` och Operations Manager får inte något e-postmeddelande.

### <a name="location-5-4763799--122134505"></a>Plats 5 (47,63799,-122,134505)

1. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Gör det till *plats 5*. Välj den samling som du skapade i [avsnittet Ladda upp polystaket-data](#upload-geofencing-geojson-data)och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. Ersätt `{Azure-Maps-Primary-Subscription-key}` med den primära prenumerations nyckeln och `{udid}` med det `udid` som du sparade i [avsnittet Ladda upp polystaket-indata-JSON](#upload-geofencing-geojson-data).

    ```HTTP
    https://atlas.microsoft.com/spatial/geofence/json?subscription-key={subscription-key}&api-version=1.0&deviceId=device_01&udid={udid}&lat=47.63799&lon=-122.134505&searchBuffer=5&isAsync=True&mode=EnterAndExit
    ```

3. Välj **Skicka**. Följande Poly-JSON visas i svars fönstret:

    ```json
    {
      "geometries": [
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "1",
        "distance": -999.0,
        "nearestLat": 47.637985,
        "nearestLon": -122.133907
      },
      {
        "deviceId": "device_01",
        "udId": "64f71aa5-bbee-942d-e351-651a6679a7da",
        "geometryId": "2",
        "distance": 999.0,
        "nearestLat": 47.637945,
        "nearestLon": -122.133683
      }
      ],
      "expiredGeofenceGeometryId": [],
      "invalidPeriodGeofenceGeometryId": [],
      "isEventPublished": true
    }
    ````

I föregående geografiskt JSON-svar har utrustningen avslutat huvud platsens gräns. Därför `isEventPublished` anges parametern till `true` och Operations Manager får ett e-postmeddelande som anger att utrustningen har avslutat ett avgränsnings tecken.


Du kan också [skicka e-postmeddelanden med hjälp av event Grid och Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) och kontrol lera de [händelser som stöds i Event Grid](../event-grid/event-handlers.md) med Azure Maps.

## <a name="clean-up-resources"></a>Rensa resurser

Det finns inga resurser som kräver rensning.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera innehålls typer i Azure Logic Apps](../logic-apps/logic-apps-content-type.md)