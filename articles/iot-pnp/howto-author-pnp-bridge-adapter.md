---
title: Skapa ett kort för IoT Plug and Play-bryggan | Microsoft Docs
description: Identifiera komponenter för IoT Plug and Play Bridge-kort. Lär dig hur du utökar bryggan genom att skriva ditt eget kort.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746835"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>Utöka IoT Plug and Play-bryggan
[Iot plug and Play Bridge](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) låter dig ansluta de befintliga enheterna som är anslutna till en gateway till IoT Hub. Du kan använda Bridge för att mappa IoT Plug and Play-gränssnitt till anslutna enheter. Ett IoT Plug and Play-gränssnitt definierar den telemetri som en enhet skickar, de egenskaper som synkroniseras mellan enheten och molnet och de kommandon som enheten svarar på. Du kan installera och konfigurera brygga applikationen med öppen källkod på Windows-eller Linux-gatewayer. Dessutom kan bryggan köras som en Azure IoT Edge runtime-modul.

Den här artikeln beskriver i detalj hur du:

- Utöka IoT Plug and Play-bryggan med ett kort.
- Implementera vanliga återanrop för ett bro kort.

Ett enkelt exempel som visar hur du använder Bridge finns i [så här ansluter du IoT plug and Play Bridge-exemplet som körs på Linux eller Windows till IoT Hub](howto-use-iot-pnp-bridge.md).

Vägledningen och exemplen i den här artikeln förutsätter grundläggande välbekanthet med [Azure Digital](../digital-twins/overview.md) -och [IoT-plug and Play](overview-iot-plug-and-play.md). Den här artikeln förutsätter att du [skapar och distribuerar IoT plug and Play Bridge](howto-build-deploy-extend-pnp-bridge.md).

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>Design Guide för att utöka IoT Plug and Play-bryggan med ett kort

Om du vill utöka brons funktioner kan du redigera dina egna brygga kort.

Bryggan använder kort för att:

- Upprätta en anslutning mellan en enhet och molnet.
- Aktivera data flödet mellan en enhet och molnet.
- Aktivera enhets hantering från molnet.

Varje bro kort måste:

- Skapa ett digitalt garn gränssnitt.
- Använd gränssnittet för att binda enhets sidans funktioner till molnbaserade funktioner som telemetri, egenskaper och kommandon.
- Upprätta kontroll-och data kommunikation med enhets maskin varan eller den inbyggda program varan.

Varje bro kort samverkar med en speciell typ av enhet baserat på hur adaptern ansluter till och interagerar med enheten. Även om kommunikationen med en enhet använder ett hand skaknings protokoll kan ett bro kort ha flera sätt att tolka data från enheten. I det här scenariot använder Bridge-kortet information om kortet i konfigurations filen för att avgöra vilken *gränssnitts konfiguration* som kortet ska använda för att parsa data.

För att interagera med enheten använder ett bro kort ett kommunikations protokoll som stöds av den enhet och de API: er som tillhandahålls av det underliggande operativ systemet, eller enhets leverantören.

För att interagera med molnet används API: er som tillhandahålls av Azure IoT Device C SDK för att skicka telemetri, skapa digitala dubbla gränssnitt, skicka egenskaps uppdateringar och skapa callback-funktioner för egenskaps uppdateringar och kommandon.

### <a name="create-a-bridge-adapter"></a>Skapa ett bro kort

Bryggan förväntar sig ett bro kort som implementerar de API: er som definierats i [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) gränssnittet:

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

I det här gränssnittet:

- `PNPBRIDGE_ADAPTER_CREATE` skapar kortet och konfigurerar gränssnitts hanterings resurserna. Ett kort kan också vara beroende av parametrar för globala kort för skapande av kort. Den här funktionen anropas en gång för ett enda nätverkskort.
- `PNPBRIDGE_COMPONENT_CREATE` skapar de digitala dubbla klient gränssnitten och binder återanrops funktionerna. Kortet initierar kommunikations kanalen till enheten. Kortet kan konfigurera resurserna så att telemetri-flödet aktive ras, men det går inte att starta rapportering av telemetri förrän `PNPBRIDGE_COMPONENT_START` anropas. Den här funktionen anropas en gång för varje gränssnitts komponent i konfigurations filen.
- `PNPBRIDGE_COMPONENT_START` kallas att låta bryggan börja vidarebefordra telemetri från enheten till den digitala dubbla klienten. Den här funktionen anropas en gång för varje gränssnitts komponent i konfigurations filen.
- `PNPBRIDGE_COMPONENT_STOP` stoppar telemetri-flödet.
- `PNPBRIDGE_COMPONENT_DESTROY` förstör den digitala dubbla klienten och de associerade gränssnitts resurserna. Den här funktionen anropas en gång för varje gränssnitts komponent i konfigurations filen när bryggan är felaktig eller när ett allvarligt fel uppstår.
- `PNPBRIDGE_ADAPTER_DESTROY` rensar resurserna för bro nätverkskort.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Brygga Core-interaktion med Bridge-kort

I följande lista beskrivs vad som händer när Bridge startar:

1. När bryggan startar söker Bridge hanteraren igenom varje gränssnitts komponent som definierats i konfigurations filen och anropar `PNPBRIDGE_ADAPTER_CREATE` på lämpligt kort. Kortet kan använda globala kort konfigurations parametrar för att konfigurera resurser för att stödja de olika *gränssnitts konfigurationerna*.
1. För varje enhet i konfigurations filen initierar Bridge Manager att gränssnittet skapas genom att anropa `PNPBRIDGE_COMPONENT_CREATE` i lämpligt bro kort.
1. Kortet tar emot valfria kort konfigurations inställningar för gränssnitts komponenten och använder den här informationen för att konfigurera anslutningar till enheten.
1. Kortet skapar de digitala dubbla klient gränssnitten och binder återanrops funktionerna för egenskaps uppdateringar och-kommandon. Om du skapar enhets anslutningar blockeras inte återanropen när digitala dubbla gränssnitt har skapats. Den aktiva enhets anslutningen är oberoende av den aktiva gränssnitts klient som bryggan skapar. Om anslutningen Miss lyckas förutsätter kortet att enheten är inaktiv. Bridge-kortet kan välja att göra om anslutningen.
1. När bryggan för korts hanteraren skapar alla gränssnitts komponenter som anges i konfigurations filen, registreras alla gränssnitt med Azure IoT Hub. Registreringen är ett block som är ett asynkront anrop. När anropet har slutförts utlöser den ett återanrop i Bridge-kortet som sedan kan börja hantera egenskaper och kommando återanrop från molnet.
1. Bridge adapter Manager anropar sedan `PNPBRIDGE_INTERFACE_START` varje komponent och Bridge-kortet börjar rapportera telemetri till den digitala dubbla klienten.

### <a name="design-guidelines"></a>Designriktlinjer

Följ dessa rikt linjer när du utvecklar ett nytt bro kort:

- Bestäm vilka enhets funktioner som stöds och vilka gränssnitts definitioner för komponenterna som använder det här kortet.
- Ta reda på vilka gränssnitt och globala parametrar ditt nätverkskort behöver ha definierat i konfigurations filen.
- Identifiera den lågnivå enhets kommunikation som krävs för att stödja komponent egenskaper och-kommandon.
- Ta reda på hur kortet ska parsa rå data från enheten och konvertera dem till de typer av telemetri som IoT Plug and Play-gränssnittets definition anger.
- Implementera det brygga nätverkskorts gränssnitt som beskrivs ovan.
- Lägg till det nya kortet till kort manifestet och skapa bryggan.

### <a name="enable-a-new-bridge-adapter"></a>Aktivera ett nytt bro kort

Du aktiverar kort i bryggan genom att lägga till en referens i [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> Återanrop i Bridge-kort anropas sekventiellt. Ett nätverkskort ska inte blockera ett återanrop eftersom det förhindrar att bryggan skapas.

### <a name="sample-camera-adapter"></a>Exempel på kamera kort

I [README för kamera kortet](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) beskrivs ett exempel på ett kamera kort som du kan aktivera.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>Kod exempel för vanliga kort scenarier/motringningar

I följande avsnitt får du information om hur ett kort för bryggan implementerar återanrop för ett antal vanliga scenarier och användnings områden i det här avsnittet beskrivs följande återanrop:
- [Ta emot uppdatering av egenskap (moln till enhet)](#receive-property-update-cloud-to-device)
- [Rapportera en egenskaps uppdatering (enhet till molnet)](#report-a-property-update-device-to-cloud)
- [Skicka telemetri (enhet till molnet)](#send-telemetry-device-to-cloud)
- [Ta emot kommando uppdatera motringning från molnet och bearbeta det på enhets sidan (moln till enhet)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [Svara på kommando uppdateringen på enhets sidan (enhet till molnet)](#respond-to-command-update-on-the-device-side-device-to-cloud)

Exemplen nedan baseras på [exempel kortet för miljö sensorn](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor).

### <a name="receive-property-update-cloud-to-device"></a>Ta emot uppdatering av egenskap (moln till enhet)
Det första steget är att registrera en callback-funktion:

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
Nästa steg är att implementera motringningsfunktionen för att läsa egenskaps uppdateringen på enheten:

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>Rapportera en egenskaps uppdatering (enhet till molnet)
När som helst efter att komponenten har skapats kan enheten rapportera egenskaper till molnet med status: 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>Skicka telemetri (enhet till molnet)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>Ta emot kommando uppdatera motringning från molnet och bearbeta det på enhets sidan (moln till enhet)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>Svara på kommando uppdateringen på enhets sidan (enhet till molnet)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om IoT Plug and Play-bryggan går du till [iot plug and Play Bridge](https://github.com/Azure/iot-plug-and-play-bridge) GitHub-lagringsplatsen.
