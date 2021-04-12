---
title: 'Snabb start: Distribuera Azure IoT Connector för FHIR (för hands version) med Azure Portal'
description: I den här snabb starten får du lära dig hur du distribuerar, konfigurerar och använder funktionen Azure IoT Connector för FHIR i Azure API för FHIR med hjälp av Azure Portal.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: quickstart
ms.date: 04/05/2021
ms.author: punagpal
ms.openlocfilehash: 0a382ad948e7fd2efc6ab59eb94da3d6cbb210d7
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443787"
---
# <a name="quickstart-deploy-azure-iot-connector-for-fhir-preview-using-azure-portal"></a>Snabb start: Distribuera Azure IoT Connector för FHIR (för hands version) med Azure Portal

Azure IoT Connector för snabba hälso samverkande resurser (FHIR&#174;) * är en valfri funktion i Azure API för FHIR som gör det möjligt att mata in data från-enheter (Internet of medicin ting). I förhands gransknings fasen är Azure IoT Connector för FHIR-funktionen tillgänglig kostnads fritt. I den här snabbstarten lär du dig att:
- Distribuera och konfigurera Azure IoT Connector för FHIR med hjälp av Azure Portal
- Använda en simulerad enhet för att skicka data till Azure IoT Connector för FHIR
- Visa resurser som har skapats av Azure IoT Connector för FHIR i Azure API för FHIR

## <a name="prerequisites"></a>Förutsättningar

- En aktiv Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API för FHIR-resurs – [Distribuera Azure API för FHIR med hjälp av Azure Portal](fhir-paas-portal-quickstart.md)

## <a name="go-to-azure-api-for-fhir-resource"></a>Gå till Azure API för FHIR-resurs

Öppna [Azure Portal](https://portal.azure.com) och gå till Azure- **API: et för den FHIR** -resurs som du vill skapa Azure IoT-kopplingen för FHIR-funktionen för.

[![Azure API för FHIR-resurs](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg)](media/quickstart-iot-fhir-portal/portal-azure-api-fhir.jpg#lightbox)

På den vänstra navigerings menyn klickar du på **IoT Connector (för hands version)** under avsnittet **tillägg** för att öppna sidan IoT- **anslutningar** .

[![Funktionen IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors.jpg#lightbox)

## <a name="create-new-azure-iot-connector-for-fhir-preview"></a>Skapa en ny Azure IoT-anslutning för FHIR (för hands version)

Klicka på knappen **Lägg till** för att öppna sidan **skapa IoT Connector** .

[![Lägg till IoT-koppling](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connectors-add.jpg#lightbox)

Ange inställningar för den nya Azure IoT-anslutningen för FHIR. Klicka på knappen **skapa** och vänta på Azure IoT Connector för FHIR-distribution.

> [!NOTE]
> Du måste välja **skapa** som värde för List rutan för **stängnings typ** för den här installationen. 

[![Skapa IoT-koppling](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-create.jpg#lightbox)

|Inställning|Värde|Beskrivning |
|---|---|---|
|Anslutnings namn|Ett unikt namn|Ange ett namn för att identifiera din Azure IoT-anslutning för FHIR. det här namnet måste vara unikt inom ett Azure-API för FHIR-resursen. Namnet får endast innehålla gemener, siffror och bindestreck (-). Det måste börja och sluta med en bokstav eller en siffra, och måste vara mellan 3-24 tecken långt.|
|Lösnings typ|Sök eller skapa|Välj **Lookup** om du har en out-of-band-process för att skapa [enhets](https://www.hl7.org/fhir/device.html) -och [patient](https://www.hl7.org/fhir/patient.html) FHIR-resurser i ditt Azure API för FHIR. Azure IoT Connector för FHIR använder referenser till dessa resurser när de skapar en [observations](https://www.hl7.org/fhir/observation.html) FHIR resurs som representerar enhets data. Välj **skapa** när du vill att Azure IoT Connector för FHIR ska skapa enheter för Bare-ben och patient resurser i ditt Azure API för FHIR med hjälp av respektive Identifier-värden som finns i enhets data.|

När installationen är klar visas den nya Azure IoT-anslutningen för FHIR på sidan **IoT-anslutningar** .

[![IoT-koppling har skapats](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-created.jpg#lightbox)

## <a name="configure-azure-iot-connector-for-fhir-preview"></a>Konfigurera Azure IoT Connector för FHIR (för hands version)

Azure IoT Connector för FHIR behöver två mappnings-mallar för att transformera enhets meddelanden till FHIR-baserade observations resurser: **enhets mappning** och **FHIR mappning**. Din Azure IoT-anslutning för FHIR är inte fullt fungerande förrän dessa mappningar har laddats upp.

[![IoT Connector saknar mappningar](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-missing-mappings.jpg#lightbox)

Om du vill överföra mappnings mallar klickar du på den nyligen distribuerade Azure IoT-anslutaren för FHIR för att gå till **IoT Connector** -sidan.

[![IoT-koppling Klicka](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click.jpg#lightbox)

#### <a name="device-mapping"></a>Enhets mappning

Enhets mappnings mal len omvandlar enhets data till ett normaliserat schema. På **IoT Connector** -sidan klickar du på knappen **Konfigurera enhets mappning** för att gå till **enhets mappnings** sidan. 

[![IoT-koppling Klicka på Konfigurera enhets mappning](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-device-mapping.jpg#lightbox)

På sidan **enhets mappning** lägger du till följande skript i JSON-redigeraren och klickar på **Spara**.

```json
{
  "templateType": "CollectionContent",
  "template": [
    {
      "templateType": "IotJsonPathContent",
      "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.telemetry.HeartRate)]",
        "patientIdExpression": "$.Properties.iotcentral-device-id",
        "values": [
          {
            "required": "true",
            "valueExpression": "$.Body.telemetry.HeartRate",
            "valueName": "hr"
          }
        ]
      }
    }
  ]
}
```

[![Enhets mappning för IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-device-mapping.jpg#lightbox)

#### <a name="fhir-mapping"></a>FHIR-mappning

FHIR-mappnings mal len transformerar ett normaliserat meddelande till en FHIR-baserad observations resurs. På **IoT Connector** -sidan klickar du på knappen **Konfigurera FHIR-mappning** för att gå till **mappnings** sidan för FHIR.  

[![IoT-koppling Klicka på Konfigurera FHIR-mappning](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-fhir-mapping.jpg#lightbox)

På sidan **Mappning av FHIR** lägger du till följande skript i JSON-redigeraren och klickar på **Spara**.

```json
{
  "templateType": "CollectionFhir",
  "template": [
    {
      "templateType": "CodeValueFhir",
      "template": {
        "codes": [
          {
            "code": "8867-4",
            "system": "http://loinc.org",
            "display": "Heart rate"
          }
        ],
        "periodInterval": 0,
        "typeName": "heartrate",
        "value": {
          "unit": "count/min",
          "valueName": "hr",
          "valueType": "Quantity"
        }
      }
    }
  ]
}
```

[![FHIR-mappning för IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg)](media/quickstart-iot-fhir-portal/portal-iot-fhir-mapping.jpg#lightbox)

## <a name="generate-a-connection-string"></a>Generera en anslutningssträng

IoMT-enheten behöver en anslutnings sträng för att ansluta och skicka meddelanden till Azure IoT Connector för FHIR. På sidan **IoT Connector** för den nyligen distribuerade Azure IoT-ANSLUTNINGSPROGRAMMET för FHIR väljer du **Hantera klient anslutningar** . 

[![IoT-koppling Klicka på Hantera klient anslutningar](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-click-client-connections.jpg#lightbox)

På sidan **anslutningar** klickar du på knappen **Lägg till** för att skapa en ny anslutning. 

[![IoT Connector-anslutningar](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connections.jpg#lightbox)

Ange ett eget namn för den här anslutningen i överlägg-fönstret och välj knappen **skapa** .

[![IoT Connector ny anslutning](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg)](media/quickstart-iot-fhir-portal/portal-iot-new-connection.jpg#lightbox)

Välj den nya anslutningen från sidan **anslutningar** och kopiera värdet för fältet **primär anslutnings sträng** från överlägg till höger.

[![Anslutnings sträng för IoT Connector](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connection-string.jpg#lightbox)

Behåll den här anslutnings strängen som ska användas i ett senare steg. 

## <a name="connect-your-devices-to-iot"></a>Anslut dina enheter till IoT

Azure erbjuder en omfattande uppsättning IoT-produkter för att ansluta och hantera dina IoT-enheter. Du kan skapa en egen lösning baserat på PaaS med hjälp av Azure IoT Hub eller börja med en hantera IoT Apps-plattform med Azure IoT Central. I den här självstudien får vi använda Azure IoT Central, som har branschledande Solution mallar som hjälper dig att komma igång.

Distribuera [program mal len för kontinuerlig övervakning av patienter](../../iot-central/healthcare/tutorial-continuous-patient-monitoring.md#create-an-application-template). Den här mallen innehåller två simulerade enheter som genererar real tids data för att hjälpa dig att komma igång: snabb **korrigering av smarta viktiga** **Knee och smart-klammer**.

> [!NOTE]
> När dina riktiga enheter är klara kan du använda samma IoT Central-program för att [publicera dina enheter](../../iot-central/core/howto-set-up-template.md) och ersätta enhets simulatorer. Enhets data kommer automatiskt att börja flöda till FHIR. 

## <a name="connect-your-iot-data-with-the-azure-iot-connector-for-fhir-preview"></a>Anslut dina IoT-data med Azure IoT Connector för FHIR (för hands version)

När du har distribuerat ditt IoT Central-program börjar du skapa telemetri genom att använda dina två färdiga simulerade enheter. I den här självstudien tar vi in Telemetrin från *smarta viktigare korrigerings* Simulator i FHIR via Azure IoT-anslutningen för FHIR. Om du vill exportera dina IoT-data till Azure IoT-anslutningsprogrammet för FHIR ska vi [ställa in en kontinuerlig data export i IoT Central](../../iot-central/core/howto-export-data.md). Vi måste först skapa en anslutning till målet och sedan ska vi skapa ett data export jobb för kontinuerlig körning: 

> [!NOTE]
> Du vill välja **data export** vs. **data export (bakåtkompatibelt)** i IoT Central app-inställningarna för det här avsnittet.

[![IoT Central inställningar för data export](media/quickstart-iot-fhir-portal/iot-central-data-export-dashboard.png)](media/quickstart-iot-fhir-portal/iot-central-data-export-dashboard.png#lightbox)

Skapa ett nytt mål:
- Gå till fliken **destinationer** och skapa ett nytt mål.
- Börja med att ge ditt mål ett unikt namn.
- Välj *Azure-Event Hubs* som typ av mål.
- Tillhandahåll Azure IoT Connector för FHIR anslutnings sträng som hämtades i föregående steg för fältet **anslutnings sträng** .

Skapa en ny data export:
- När du har skapat målet går du till fliken **Exportera** och skapar en ny data export. 
- Börja med att ge den data exporten ett unikt namn.
- Under **data** väljer du *telemetri* som den *typ av data som ska exporteras*.
- Under **mål** väljer du det mål namn som du skapade i föregående namn.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Visa enhets data i Azure API för FHIR

Du kan visa de FHIR-baserade observations resurserna som skapats av Azure IoT Connector för FHIR på Azure API för FHIR med Postman. Konfigurera din [Postman för att få åtkomst till Azure API för FHIR](access-fhir-postman-tutorial.md) och gör en `GET` begäran till `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` att Visa observations FHIR resurser med ett hjärta pris värde. 

> [!TIP]
> Se till att användaren har rätt åtkomst till Azure API för FHIR-dataplan. Använd [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](configure-azure-rbac.md) för att tilldela nödvändiga data Plans roller.

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du ta bort en instans av Azure IoT Connector för FHIR genom att ta bort den tillhör ande resurs gruppen eller tillhör ande Azure API för FHIR-tjänsten eller Azure IoT-anslutaren för FHIR-instansen. 

Om du vill ta bort en Azure IoT Connector för FHIR-instans direkt väljer du sidan instans från **IoT-kopplingar** för att gå till **IoT Connector** -sidan och klickar på knappen **ta bort** . Välj **Ja** när du uppmanas att bekräfta. 

[![Ta bort IoT Connector-instans](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg)](media/quickstart-iot-fhir-portal/portal-iot-connector-delete.jpg#lightbox)

## <a name="next-steps"></a>Nästa steg

I den här snabb starts guiden har du distribuerat Azure IoT Connector för FHIR-funktionen i Azure API för FHIR-resursen. Välj mellan nästa steg för att lära dig mer om Azure IoT Connector för FHIR:

Förstå olika stadier i data flödet i Azure IoT Connector för FHIR.

>[!div class="nextstepaction"]
>[Azure IoT-anslutning för FHIR data flöde](iot-data-flow.md)

Lär dig hur du konfigurerar IoT Connector med hjälp av mallar för enhets-och FHIR-mappning.

>[!div class="nextstepaction"]
>[FHIR för Azure IoT Connector för mappning av](iot-mapping-templates.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version). FHIR är ett registrerat varumärke som tillhör HL7 och används med behörigheten för HL7.
