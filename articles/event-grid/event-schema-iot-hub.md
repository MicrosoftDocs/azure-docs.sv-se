---
title: Azure IoT Hub som Event Grid källa
description: Den här artikeln innehåller egenskaper och schema för Azure IoT Hub-händelser. Den innehåller en lista över tillgängliga händelse typer, exempel händelser och händelse egenskaper.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 7e1c480bd2a662a2ee3418b35dc9c3b50d412a60
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185843"
---
# <a name="azure-iot-hub-as-an-event-grid-source"></a>Azure IoT Hub som en Event Grid källa
Den här artikeln innehåller egenskaper och schema för Azure IoT Hub-händelser. En introduktion till händelse scheman finns i [Azure Event Grid händelse schema](event-schema.md). 

## <a name="event-grid-event-schema"></a>Event Grid-händelseschema

### <a name="available-event-types"></a>Tillgängliga händelse typer

Azure IoT Hub avger följande händelse typer:

| Händelsetyp | Beskrivning |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicerad när en enhet registreras i en IoT-hubb. |
| Microsoft.Devices.DeviceDeleted | Publicerad när en enhet tas bort från en IoT-hubb. | 
| Microsoft.Devices.DeviceConnected | Publicerad när en enhet är ansluten till en IoT-hubb. |
| Microsoft.Devices.DeviceDisconnected | Publicerad när en enhet kopplas från en IoT-hubb. | 
| Microsoft.Devices.DeviceTelemetry | Publicerad när ett telemetri skickas till en IoT-hubb. |

### <a name="example-event"></a>Exempel händelse

Schemat för DeviceConnected-och DeviceDisconnected-händelser har samma struktur. Den här exempel händelsen visar schemat för en händelse som aktive ras när en enhet är ansluten till en IoT-hubb:

```json
[{
  "id": "f6bbf8f4-d365-520d-a878-17bf7238abd8", 
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceConnected", 
  "eventTime": "2018-06-02T19:17:44.4383997Z", 
  "data": {
    "deviceConnectionStateEventInfo": {
      "sequenceNumber":
        "000000000000000001D4132452F67CE200000002000000000000000000000001"
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "moduleId" : "DeviceModuleID"
  }, 
  "dataVersion": "1", 
  "metadataVersion": "1" 
}]
```

DeviceTelemetry-händelsen utlöses när en telemetri-händelse skickas till en IoT Hub. Ett exempel schema för den här händelsen visas nedan.

```json
[{
  "id": "9af86784-8d40-fe2g-8b2a-bab65e106785",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>", 
  "subject": "devices/LogicAppTestDevice", 
  "eventType": "Microsoft.Devices.DeviceTelemetry",
  "eventTime": "2019-01-07T20:58:30.48Z",
  "data": {        
      "body": {            
          "Weather": {                
              "Temperature": 900            
          },
          "Location": "USA"        
      },
        "properties": {            
          "Status": "Active"        
        },
        "systemProperties": {            
            "iothub-content-type": "application/json",
            "iothub-content-encoding": "utf-8",
            "iothub-connection-device-id": "d1",
            "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
            "iothub-connection-auth-generation-id": "123455432199234570",
            "iothub-enqueuedtime": "2019-01-07T20:58:30.48Z",
            "iothub-message-source": "Telemetry"        
        }    
    },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Schemat för DeviceCreated-och DeviceDeleted-händelser har samma struktur. Den här exempel händelsen visar schemat för en händelse som aktive ras när en enhet registreras till en IoT-hubb:

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "deviceEtag": "null",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Händelse egenskaper

Alla händelser innehåller samma data på översta nivån: 

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| id | sträng | Unikt ID för händelsen. |
| ämne | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| data | objekt | IoT Hub händelse data.  |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Data-objektet innehåller följande egenskaper för alla IoT Hub-händelser:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| hubName | sträng | Namnet på den IoT Hub där enheten skapades eller togs bort. |
| deviceId | sträng | Enhetens unika identifierare. Den här Skift läges känsliga strängen kan vara upp till 128 tecken lång och har stöd för ASCII 7-bitars alfanumeriska tecken plus följande specialtecken: `- : . + % _ # * ? ! ( ) , = @ ; $ '` . |

Innehållet i data-objektet skiljer sig åt för varje händelse utgivare. 

Om **enheten är ansluten** och **enheten frånkopplade** IoT Hub händelser, innehåller dataobjektet följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| moduleId | sträng | Den unika identifieraren för modulen. Det här fältet är endast utdata för modul enheter. Den här Skift läges känsliga strängen kan vara upp till 128 tecken lång och har stöd för ASCII 7-bitars alfanumeriska tecken plus följande specialtecken: `- : . + % _ # * ? ! ( ) , = @ ; $ '` . |
| deviceConnectionStateEventInfo | objekt | Händelse information för status för enhets anslutning
| sequenceNumber | sträng | Ett tal som visar ordningen på enheter som är anslutna eller enheter som är frånkopplade. Den senaste händelsen kommer att ha ett sekvensnummer som är högre än föregående händelse. Det här antalet kan ändras med mer än 1, men det är en strikt ökning. Se [hur du använder Sequence Number](../iot-hub/iot-hub-how-to-order-connection-state-events.md). |

För **telemetri** IoT Hub händelse innehåller dataobjektet enhets-till-moln-meddelandet i [IoT Hub-meddelande formatet](../iot-hub/iot-hub-devguide-messages-construct.md) och har följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| body | sträng | Innehållet i meddelandet från enheten. |
| properties | sträng | Program egenskaperna är användardefinierade strängar som kan läggas till i meddelandet. Dessa fält är valfria. |
| system egenskaper | sträng | [System egenskaper](../iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties) hjälper till att identifiera innehåll och källa för meddelandena. Telemetriprocessor för enheten måste vara i ett giltigt JSON-format med contentType inställt på JSON och contentEncoding inställd på UTF-8 i meddelande systemets egenskaper. Om detta inte anges kommer IoT Hub att skriva meddelanden i bas 64-kodat format.  |

För att enheten ska kunna **skapas** och **enheten tas bort** IoT Hub händelser, innehåller dataobjektet följande egenskaper:

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| Noble | objekt | Information om enheten, som är moln representation av metadata för program enheten. | 
| deviceID | sträng | Enhetens unika identifierare. | 
| etag | sträng | En verifierare för att säkerställa konsekvensen av uppdateringar på en enhet. Varje etag garanteras vara unik per enhet. |  
| deviceEtag| sträng | En verifierare för att säkerställa konsekvensen av uppdateringar av ett enhets register. Varje deviceEtag garanteras vara unik per enhets register. |
| status | sträng | Om enheten är delad eller inaktive rad. | 
| statusUpdateTime | sträng | ISO8601 tidsstämpel för den senaste enhetens dubbla status uppdatering. |
| connectionState | sträng | Om enheten är ansluten eller frånkopplad. | 
| lastActivityTime | sträng | ISO8601 tidsstämpel för den senaste aktiviteten. | 
| cloudToDeviceMessageCount | heltal | Antal moln till enhets meddelanden som skickats till den här enheten. | 
| authenticationType | sträng | Autentiseringstyp som används för den här enheten: `SAS` , `SelfSigned` eller `CertificateAuthority` . |
| x509Thumbprint | sträng | Tumavtrycket är ett unikt värde för x509-certifikatet, som ofta används för att hitta ett visst certifikat i ett certifikat arkiv. Tumavtrycket genereras dynamiskt med SHA1-algoritmen och finns inte fysiskt i certifikatet. | 
| primaryThumbprint | sträng | Primärt tumavtryck för x509-certifikatet. |
| secondaryThumbprint | sträng | Sekundärt tumavtryck för x509-certifikatet. | 
| version | heltal | Ett heltal som ökas med ett värde varje gången enheten är i ett uppdaterat. |
| dina | objekt | En del av egenskaperna som bara kan skrivas av program Server delen och läsas av enheten. | 
| ovänta | objekt | En del av egenskaperna som bara kan skrivas av enheten och som läses av programmets backend-server. |
| lastUpdated | sträng | ISO8601 tidsstämpel för den senaste enhetens dubbla egenskaps uppdatering. | 

## <a name="tutorials-and-how-tos"></a>Självstudier och instruktioner
|Rubrik  |Beskrivning  |
|---------|---------|
| [Skicka e-postmeddelanden om Azure IoT Hub-händelser med hjälp av Logic Apps](publish-iot-hub-events-to-logic-apps.md) | En Logic App skickar ett e-postmeddelande varje gång en enhet läggs till i din IoT Hub. |
| [Reagera på IoT Hub händelser genom att använda Event Grid för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md) | Översikt över att integrera IoT Hub med Event Grid. |
| [Beställ enhet ansluten och avkopplade enhets händelser](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | Visar hur du beställer händelser i enhetens anslutnings tillstånd. |

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](overview.md)
* Information om hur IoT Hub och Event Grid arbetar tillsammans finns i [reagera på att IoT Hub händelser genom att använda Event Grid för att utlösa åtgärder](../iot-hub/iot-hub-event-grid.md).