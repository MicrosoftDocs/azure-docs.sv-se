---
title: 'Självstudie: ta emot enhets data via Azure IoT Hub'
description: I den här självstudien får du lära dig hur du aktiverar routning av enhets data från IoT Hub till Azure API för FHIR via Azure IoT Connector för FHIR.
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: punagpal
ms.openlocfilehash: 36d6d8b4d4435b1db99f2721c817c9c06ca60945
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020351"
---
# <a name="tutorial-receive-device-data-through-azure-iot-hub"></a>Självstudie: ta emot enhets data via Azure IoT Hub

Azure IoT Connector för arbets resurser för snabb hälso samverkan (FHIR&#174;) * ger dig möjlighet att mata in data från IoMT-enheter (Internet of Medicine saker) till Azure API för FHIR. [Distribuera Azure IoT Connector för FHIR (för hands version) med Azure Portal](iot-fhir-portal-quickstart.md) snabb start visade ett exempel på en enhet som hanteras av Azure IoT Central [Skicka telemetri](iot-fhir-portal-quickstart.md#connect-your-devices-to-iot) till Azure IoT Connector för FHIR. Azure IoT-anslutning för FHIR kan också användas med enheter som tillhandahålls och hanteras via Azure IoT Hub. Den här självstudien visar hur du ansluter och dirigerar enhets data från Azure IoT Hub till Azure IoT Connector för FHIR.

## <a name="prerequisites"></a>Förutsättningar

- En aktiv Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure API för FHIR-resurs med minst en Azure IoT-anslutning för FHIR – [Distribuera Azure IoT Connector för FHIR (för hands version) med Azure Portal](iot-fhir-portal-quickstart.md)
- Azure IoT Hub resurs som är ansluten till verkliga eller simulerade enheter – [skapa en IoT Hub med hjälp av Azure Portal](../../iot-hub/quickstart-send-telemetry-dotnet.md)

> [!TIP]
> Om du använder ett Azure IoT Hub simulerat enhets program kan du välja mellan olika språk och system som stöds.

## <a name="get-connection-string-for-azure-iot-connector-for-fhir-preview"></a>Hämta anslutnings sträng för Azure IoT Connector för FHIR (för hands version)

Azure IoT Hub kräver en anslutnings sträng för att säkert ansluta till din Azure IoT-anslutning för FHIR. Skapa en ny anslutnings sträng för din Azure IoT-anslutning för FHIR enligt beskrivningen i [skapa en anslutnings sträng](iot-fhir-portal-quickstart.md#generate-a-connection-string). Behåll den här anslutnings strängen som ska användas i nästa steg.

Azure IoT Connector för FHIR använder en Azure Event Hub-instans under huven för att ta emot enhets meddelanden. Den anslutnings sträng som skapades ovan är i princip anslutnings strängen till den här underliggande Händelsehubben.

## <a name="connect-azure-iot-hub-with-the-azure-iot-connector-for-fhir-preview"></a>Ansluta Azure IoT Hub med Azure IoT Connector för FHIR (för hands version)

Azure IoT Hub stöder en funktion som kallas [meddelanderoutning](../../iot-hub/iot-hub-devguide-messages-d2c.md) som ger möjlighet att skicka enhets data till olika Azure-tjänster som Händelsehubben, lagrings konto och Service Bus. Azure IoT Connector för FHIR använder den här funktionen för att ansluta och skicka enhets data från Azure IoT Hub till händelse navets slut punkt.

> [!NOTE] 
> Vid detta tillfälle kan du bara använda PowerShell-eller CLI-kommandot för att [skapa meddelanderoutning](../../iot-hub/tutorial-routing.md) eftersom Azure IoT Connector för FHIR-händelsehubben inte finns på kund prenumerationen, och därför är den inte synlig för dig via Azure Portal. Trots att meddelande väg objekt har lagts till med hjälp av PowerShell eller CLI, är de synliga i Azure Portal och kan hanteras därifrån.

Att konfigurera en meddelanderoutning består av två steg.

### <a name="add-an-endpoint"></a>Lägga till en slutpunkt
I det här steget definieras en slut punkt som IoT Hub dirigerar data till. Skapa den här slut punkten med hjälp av antingen [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint) PowerShell-kommandot eller [AZ IoT Hub routing-Endpoint Create CLI-](/cli/azure/iot/hub/routing-endpoint#az-iot-hub-routing-endpoint-create) kommando baserat på din preferens.

Här är listan över parametrar som ska användas med kommandot för att skapa en slut punkt:

|PowerShell-parameter|CLI-parameter|Beskrivning|
|---|---|---|
|ResourceGroupName|resource-group|Resurs grupps namnet för din IoT Hub-resurs.|
|Name|hubb-namn|Namnet på din IoT Hub-resurs.|
|EndpointName|slut punkt-namn|Använd ett namn som du vill tilldela till slut punkten som skapas.|
|EndpointType|slut punkt-typ|Typ av slut punkt som IoT Hub måste ansluta till. Använd literal-värdet "EventHub" för PowerShell och "eventhub" för CLI.|
|EndpointResourceGroup|slut punkt-resurs grupp|Resurs grupp namn för din Azure IoT-anslutning för FHIRs Azure API för FHIR-resurs. Du kan hämta det här värdet från översikts sidan för Azure API för FHIR.|
|EndpointSubscriptionId|slut punkt-prenumerations-ID|Prenumerations-ID för din Azure IoT-anslutning för FHIRs Azure API för FHIR-resurs. Du kan hämta det här värdet från översikts sidan för Azure API för FHIR.|
|Begär|connection-string|Anslutnings sträng till din Azure IoT-anslutning för FHIR. Använd det värde som du fick i föregående steg.|

### <a name="add-a-message-route"></a>Lägg till en meddelande väg
I det här steget definieras en meddelande väg som använder den slut punkt som skapades ovan. Skapa en väg med antingen [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute) PowerShell-kommandot eller [AZ IoT Hub Route skapa](/cli/azure/iot/hub/route#az-iot-hub-route-create) CLI-kommando baserat på din preferens.

Här är listan över parametrar som ska användas med kommandot för att lägga till en meddelande väg:

|PowerShell-parameter|CLI-parameter|Beskrivning|
|---|---|---|
|ResourceGroupName|g|Resurs grupps namnet för din IoT Hub-resurs.|
|Name|hubb-namn|Namnet på din IoT Hub-resurs.|
|EndpointName|slut punkt-namn|Namnet på den slut punkt som du har skapat ovan.|
|RouteName|flödes namn|Ett namn som du vill tilldela till den meddelande väg som skapas.|
|Källa|typ av källa|Typ av data som ska skickas till slut punkten. Använd litteralt värde för "DeviceMessages" för PowerShell och "DeviceMessages" för CLI.|

## <a name="send-device-message-to-iot-hub"></a>Skicka enhets meddelande till IoT Hub

Använd din enhet (verklig eller simulerad) om du vill skicka ett exempel på ett meddelande om hjärtat-pris som visas nedan till Azure IoT Hub. Det här meddelandet skickas till Azure IoT Connector för FHIR, där meddelandet kommer att omvandlas till en FHIR observations resurs och lagras i Azure-API: et för FHIR.

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
> Se till att skicka enhets meddelandet som följer de [mappar](iot-mapping-templates.md) som kon figurer ATS med din Azure IoT-anslutning för FHIR.

## <a name="view-device-data-in-azure-api-for-fhir"></a>Visa enhets data i Azure API för FHIR

Du kan visa de FHIR observations resurser som skapats av Azure IoT Connector för FHIR på Azure API för FHIR med Postman. Konfigurera din [Postman för att få åtkomst till Azure API för FHIR](access-fhir-postman-tutorial.md) och gör en `GET` begäran till `https://your-fhir-server-url/Observation?code=http://loinc.org|8867-4` att Visa observations FHIR resurser med ett hjärta pris värde som skickats i ovanstående exempel meddelande.

> [!TIP]
> Se till att användaren har rätt åtkomst till Azure API för FHIR-dataplan. Använd [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](configure-azure-rbac.md) för att tilldela nödvändiga data Plans roller.


## <a name="next-steps"></a>Nästa steg

I den här snabb starts guiden ställer du in Azure-IoT Hub för att dirigera enhets data till Azure IoT Connector för FHIR. Välj mellan nästa steg för att lära dig mer om Azure IoT Connector för FHIR:

Förstå olika stadier i data flödet i Azure IoT Connector för FHIR.

>[!div class="nextstepaction"]
>[Azure IoT-anslutning för FHIR data flöde](iot-data-flow.md)

Lär dig hur du konfigurerar IoT Connector med hjälp av mallar för enhets-och FHIR-mappning.

>[!div class="nextstepaction"]
>[FHIR för Azure IoT Connector för mappning av](iot-mapping-templates.md)

* I Azure Portal kallas Azure IoT Connector för FHIR IoT Connector (för hands version). FHIR är ett registrerat varumärke som tillhör HL7 och används med behörigheten för HL7.