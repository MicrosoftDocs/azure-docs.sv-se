---
title: 'Självstudie: Ta emot enhetsdata via Azure IoT Hub'
description: I den här självstudien lär du dig att aktivera routning av enhetsdata från IoT Hub till Azure API for FHIR via Azure IoT Connector för FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 77d54ef0c9bef40af47c2fc48b9d4b5d6315b119
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780403"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Självstudie: Ta emot enhetsdata via Azure IoT Hub

Med Azure IoT Connector for FHIR (elektronisk standard för utbyte av vårdinformation) (FHIR&#174;)* kan du mata in data från IoMT-enheter (Internet of Medical Things) till Azure API for FHIR. [Snabbstarten Distribuera Azure IoT Connector for FHIR (förhandsversion)](iot-fhir-portal-quickstart.md) med Azure Portal visade ett exempel på en enhet som hanteras av Azure IoT Central skickar [telemetri](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) till Azure IoT Connector för FHIR. Azure IoT Connector for FHIR kan också fungera med enheter som etablerats och hanteras via Azure IoT Hub. Den här självstudien innehåller en procedur för att ansluta och dirigera enhetsdata från Azure IoT Hub till Azure IoT Connector för FHIR.

## <a name="prerequisites"></a>Förutsättningar

- En aktiv Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API for FHIR resurs med minst en Azure IoT Connector for FHIR – [Distribuera Azure IoT Connector för FHIR (förhandsversion) med hjälp av Azure Portal](iot-fhir-portal-quickstart.md)
- Azure IoT Hub-resurs ansluten till verkliga eller simulerade enheter – Skapa [en IoT-hubb med hjälp av Azure Portal](../../iot-hub/quickstart-send-telemetry-dotnet.md)

> [!TIP]
> Om du använder ett Azure IoT Hub simulerat enhetsprogram kan du välja program bland olika språk och system som stöds.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Hämta anslutningssträng för Azure IoT Connector för FHIR (förhandsversion)

Azure IoT Hub kräver en anslutningssträng för att på ett säkert sätt ansluta till din Azure IoT Connector för FHIR. Skapa en ny anslutningssträng för din Azure IoT Connector för FHIR enligt beskrivningen i [Generera en anslutningssträng](iot-fhir-portal-quickstart.md#generate-a-connection-string). Bevara den här anslutningssträngen som ska användas i nästa steg.

Azure IoT Connector for FHIR använder en Azure Event Hub-instans under huven för att ta emot enhetsmeddelanden. Anslutningssträngen som skapades ovan är i princip anslutningssträngen till den underliggande händelsehubben.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Ansluta Azure IoT Hub med Azure IoT Connector för FHIR (förhandsversion)

Azure IoT Hub stöder en funktion [](../../iot-hub/iot-hub-devguide-messages-d2c.md) som kallas meddelanderoutning som ger möjlighet att skicka enhetsdata till olika Azure-tjänster som Händelsehubb, Lagringskonto och Service Bus. Azure IoT Connector for FHIR använder den här funktionen för att ansluta och skicka enhetsdata från Azure IoT Hub till händelsehubbens slutpunkt.

> [!NOTE] 
> Just nu kan du bara använda PowerShell- eller CLI-kommando för att skapa meddelanderoutning eftersom Azure IoT Connector för FHIR:s händelsehubb inte finns i kundprenumerationen, och därför visas den inte för dig via Azure Portal. [](../../iot-hub/tutorial-routing.md) När meddelandevägsobjekten har lagts till med PowerShell eller CLI är de dock synliga på Azure Portal och kan hanteras därifrån.

Att konfigurera en meddelanderoutning består av två steg.

### <a name="add-an-endpoint"></a>Lägga till en slutpunkt
Det här steget definierar en slutpunkt som IoT Hub som data ska dirigeras till. Skapa den här slutpunkten med [antingen PowerShell-kommandot Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) eller [cli-kommandot az iot hub routing-endpoint create,](/cli/azure/iot/hub/routing-endpoint#az_iot_hub_routing_endpoint_create) baserat på dina önskemål.

Här är listan över parametrar som ska användas med kommandot för att skapa en slutpunkt:

|PowerShell-parameter|CLI-parameter|Description|
|---|---|---|
|ResourceGroupName|resource-group|Resursgruppens namn på din IoT Hub resurs.|
|Name|hub-name|Namnet på din IoT Hub resurs.|
|EndpointName|slutpunktsnamn|Använd ett namn som du vill tilldela till slutpunkten som skapas.|
|EndpointType|slutpunktstyp|Typ av slutpunkt som IoT Hub måste ansluta till. Använd literalvärdet "EventHub" för PowerShell och "eventhub" för CLI.|
|EndpointResourceGroup|endpoint-resource-group|Resursgruppsnamn för din Azure IoT Connector för FHIR:s Azure API for FHIR resurs. Du kan hämta det här värdet från översiktssidan för Azure API for FHIR.|
|EndpointSubscriptionId|endpoint-subscription-id|Prenumerations-ID för din Azure IoT Connector för FHIR:s Azure API for FHIR resurs. Du kan hämta det här värdet från översiktssidan för Azure API for FHIR.|
|Connectionstring|connection-string|Anslutningssträng till din Azure IoT Connector för FHIR. Använd det värde som du fick i föregående steg.|

### <a name="add-a-message-route"></a>Lägga till en meddelandeväg
Det här steget definierar en meddelandeväg med hjälp av slutpunkten som skapades ovan. Skapa en väg med [antingen PowerShell-kommandot Add-AzIotHubRoute eller cli-kommandot](/powershell/module/az.iothub/Add-AzIoTHubRoute) [az iot hub route create,](/cli/azure/iot/hub/route#az_iot_hub_route_create) baserat på dina önskemål.

Här är en lista över parametrar som ska användas med kommandot för att lägga till en meddelandeväg:

|PowerShell-parameter|CLI-parameter|Description|
|---|---|---|
|ResourceGroupName|g|Resursgruppens namn på din IoT Hub resurs.|
|Name|hub-name|Namnet på din IoT Hub resurs.|
|EndpointName|slutpunktsnamn|Namnet på den slutpunkt som du skapade ovan.|
|RouteName|route-name|Ett namn som du vill tilldela till meddelandevägen som skapas.|
|Källa|källtyp|Typ av data som ska skickas till slutpunkten. Använd literalvärdet "DeviceMessages" för PowerShell och "devicemessages" för CLI.|

## <a name="send-device-message-to-iot-hub"></a>Skicka enhetsmeddelande till IoT Hub

Använd din enhet (verklig eller simulerad) för att skicka exemplets pulsfrekvensmeddelande som visas nedan till Azure IoT Hub. Det här meddelandet dirigeras till Azure IoT Connector for FHIR, där meddelandet omvandlas till en FHIR-observationsresurs och lagras i Azure API for FHIR.

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```
> [!IMPORTANT]
> Se till att skicka enhetsmeddelandet [](iot-mapping-templates.md) som överensstämmer med mappningsmallarna som konfigurerats med din Azure IoT Connector för FHIR.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Visa enhetsdata i Azure API for FHIR

Du kan visa FHIR-observationsresursen som skapats av Azure IoT Connector for FHIR på Azure API for FHIR postman. Konfigurera [Postman för åtkomst Azure API for FHIR](access-fhir-postman-tutorial.md) skicka en begäran om att visa Observation FHIR-resurser med pulsvärde som `GET` `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` skickades i exempelmeddelandet ovan.

> [!TIP]
> Kontrollera att användaren har rätt åtkomst till Azure API for FHIR dataplanet. Använd [rollbaserad åtkomstkontroll i Azure (Azure RBAC) för](configure-azure-rbac.md) att tilldela nödvändiga dataplansroller.


## <a name="next-steps"></a>Nästa steg

I den här snabbstartsguiden ska du konfigurera Azure IoT Hub att dirigera enhetsdata till Azure IoT Connector för FHIR. Välj nedanstående steg om du vill veta mer om Azure IoT Connector för FHIR:

Förstå olika faser i dataflödet i Azure IoT Connector för FHIR.

>[!div class="nextstepaction"]
>[Azure IoT Connector för FHIR-dataflöde](iot-data-flow.md)

Lär dig hur du konfigurerar IoT Connector med hjälp av enhets- och FHIR-mappningsmallar.

>[!div class="nextstepaction"]
>[Azure IoT Connector för FHIR-mappningsmallar](iot-mapping-templates.md)

*I Azure Portal kallas Azure IoT Connector för FHIR för IoT Connector (förhandsversion). FHIR är ett registrerat varumärke som tillhör HL7 och används med tillstånd av HL7.