---
title: Hämta IoT Edge loggar – Azure IoT Edge
description: IoT Edge modul logg hämtning och uppladdning till Azure Blob Storage.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/12/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 65d95533e4cff02866111881f036225f9f544852
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719023"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>Hämta loggar från IoT Edge-distributioner

Hämta loggar från dina IoT Edge-distributioner utan att behöva fysisk eller SSH-åtkomst till enheten med hjälp av de direkta metoderna som ingår i modulen för IoT Edge agent. Direkta metoder implementeras på enheten och kan sedan anropas från molnet. IoT Edge agenten innehåller direkta metoder som hjälper dig att övervaka och hantera dina IoT Edge-enheter via fjärr anslutning. De direkta metoder som beskrivs i den här artikeln är allmänt tillgängliga i 1.0.10-versionen.

Mer information om direkta metoder, hur du använder dem och hur du implementerar dem i dina egna moduler finns i [förstå och anropa direkt metoder från IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Namnen på dessa direkta metoder hanteras Skift läges känsliga.

## <a name="recommended-logging-format"></a>Rekommenderat loggnings format

Det krävs inte, för bästa kompatibilitet med den här funktionen, det rekommenderade loggnings formatet:

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Timestamp}` ska formateras som `yyyy-MM-dd hh:mm:ss.fff zzz` och `{Log Level}` ska följa tabellen nedan, vilket härleder dess allvarlighets grader från [allvarlighets grad koden i syslog-standarden](https://wikipedia.org/wiki/Syslog#Severity_level).

| Värde | Allvarlighetsgrad |
|-|-|
| 0 | Akut |
| 1 | Varning |
| 2 | Kritiskt |
| 3 | Fel |
| 4 | Varning |
| 5 | anslagstavlan, |
| 6 | Information |
| 7 | Felsökning |

[Klassen logga in IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs) fungerar som en kanonisk implementering.

## <a name="retrieve-module-logs"></a>Hämta Modulnamn

Använd metoden **GetModuleLogs** Direct för att hämta loggarna för en IoT Edge-modul.

Den här metoden accepterar en JSON-nyttolast med följande schema:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Namn | Typ | Beskrivning |
|-|-|-|
| Schema | sträng | Ange till `1.0` |
| objekt | JSON-matris | En matris med `id` och `filter` tupler. |
| ID | sträng | Ett reguljärt uttryck som tillhandahåller modulnamnet. Den kan matcha flera moduler på en Edge-enhet. [Reguljära uttrycks](/dotnet/standard/base-types/regular-expressions) format för .NET förväntas. |
| filter | JSON-avsnitt | Logg filter som ska tillämpas på de moduler som matchar det `id` reguljära uttrycket i tuppeln. |
| slutet | heltal | Antal logg rader tidigare att hämta från den senaste. Valfritt. |
| Starta | sträng | Returnera bara loggar sedan den här tiden, som varaktighet (1 d, 90 m, 2 dagar 3 timmar 2 minuter), rfc3339 tidsstämpel eller UNIX-tidsstämpel.  Om både `tail` och `since` anges, hämtas loggarna med `since` värdet först. Sedan `tail` tillämpas värdet på resultatet och det slutliga resultatet returneras. Valfritt. |
| tills | sträng | Returnera endast loggar före den angivna tiden, som rfc3339-tidsstämpel, UNIX-tidsstämpel eller varaktighet (1 d, 90 m, 2 dagar 3 timmar 2 minuter). Valfritt. |
| loggnings nivå | heltal | Filtrera logg rader som är mindre än eller lika med den angivna logg nivån. Logg rader ska följa det rekommenderade loggnings formatet och använda [syslog-allvarlighets nivå](https://en.wikipedia.org/wiki/Syslog#Severity_level) standard. Valfritt. |
| verifiering | sträng | Filtrera logg rader som har innehåll som matchar det angivna reguljära uttrycket med .net-format för [reguljära uttryck](/dotnet/standard/base-types/regular-expressions) . Valfritt. |
| inställning | sträng | Antingen `gzip` eller `none`. Standardvärdet är `none`. |
| Innehålls | sträng | Antingen `json` eller `text`. Standardvärdet är `text`. |

> [!NOTE]
> Om innehållet i loggar överskrider gränsen för svars storlek för direkta metoder, som för närvarande är 128 KB, returnerar svaret ett fel.

En lyckad hämtning av loggar returnerar **"status": 200** följt av en nytto last som innehåller de loggar som hämtats från modulen, filtrerat efter de inställningar som du anger i din begäran.

Exempel:

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

Anropa metoden med metod namnet `GetModuleLogs` och följande JSON-nyttolast i Azure Portal:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Anropa Direct-metoden ' GetModuleLogs ' i Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

Du kan också skicka CLI-utdata till Linux-verktyg som [gzip](https://en.wikipedia.org/wiki/Gzip), för att bearbeta ett komprimerat svar. Exempel:

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>Ladda upp modul loggar

Använd metoden **UploadModuleLogs** Direct för att skicka de begärda loggarna till en angiven Azure Blob Storage-behållare.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Om du vill överföra loggar från en enhet bakom en gateway-enhet måste du ha konfigurerat [API-proxyn och Blob Storage-modulerna](how-to-configure-api-proxy-module.md) på enheten på den översta nivån. Dessa moduler dirigerar loggarna från den lägre lager enheten via din gateway-enhet till din lagring i molnet.

::: moniker-end

Den här metoden godkänner en JSON-nyttolast som liknar **GetModuleLogs**, med tillägg av nyckeln "sasUrl":

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": "int",
                "since": "string",
                "until": "string",
                "loglevel": "int",
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Namn | Typ | Beskrivning |
|-|-|-|
| sasURL | sträng (URI) | [URL för signatur för delad åtkomst med skriv åtkomst till Azure Blob Storage-behållare](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer). |

En lyckad begäran om att ladda upp loggar returnerar **"status": 200** följt av en nytto last med följande schema:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Namn | Typ | Beskrivning |
|-|-|-|
| status | sträng | En av `NotStarted` ,,, `Running` `Completed` `Failed` eller `Unknown` . |
| meddelande | sträng | Meddelande om fel, tom sträng annars. |
| correlationId | sträng   | ID för att fråga efter status för uppladdnings förfrågan. |

Exempel:

Följande anrop överför de senaste 100-logg raderna från alla moduler i komprimerat JSON-format:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

Följande anrop överför de senaste 100-logg raderna från edgeAgent och edgeHub med de senaste 1000-logg raderna från tempSensor-modulen i okomprimerat text format:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

I Azure Portal startar du metoden med metod namnet `UploadModuleLogs` och följande JSON-nyttolast när du har fyllt i sasURL med din information:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Anropa Direct-metoden ' UploadModuleLogs ' i Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>Ladda upp support pakets diagnostik

Använd metoden **UploadSupportBundle** Direct för att paketera och överföra en zip-fil med IoT Edge modul loggar till en tillgänglig Azure Blob Storage-behållare. Den här direkta metoden kör [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) kommandot på din IoT Edge enhet för att hämta loggarna.

<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"

> [!NOTE]
> Om du vill överföra loggar från en enhet bakom en gateway-enhet måste du ha konfigurerat [API-proxyn och Blob Storage-modulerna](how-to-configure-api-proxy-module.md) på enheten på den översta nivån. Dessa moduler dirigerar loggarna från den lägre lager enheten via din gateway-enhet till din lagring i molnet.

::: moniker-end

Den här metoden accepterar en JSON-nyttolast med följande schema:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| Namn | Typ | Beskrivning |
|-|-|-|
| Schema | sträng | Ange till `1.0` |
| sasURL | sträng (URI) | [URL för signatur för delad åtkomst med skriv åtkomst till Azure Blob Storage container](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| Starta | sträng | Returnera bara loggar sedan den här tiden, som varaktighet (1 d, 90 m, 2 dagar 3 timmar 2 minuter), rfc3339 tidsstämpel eller UNIX-tidsstämpel. Valfritt. |
| tills | sträng | Returnera endast loggar före den angivna tiden, som rfc3339-tidsstämpel, UNIX-tidsstämpel eller varaktighet (1 d, 90 m, 2 dagar 3 timmar 2 minuter). Valfritt. |
| edgeRuntimeOnly | boolean | Om värdet är true returneras bara loggar från Edge agent, Edge Hub och Edge Security daemon. Standard: falskt.  Valfritt. |

> [!IMPORTANT]
> IoT Edge support paket kan innehålla personligt identifierbar information.

En lyckad begäran om att ladda upp loggar returnerar **"status": 200** följt av en nytto last med samma schema som **UploadModuleLogs** -svaret:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Namn | Typ | Beskrivning |
|-|-|-|
| status | sträng | En av `NotStarted` ,,, `Running` `Completed` `Failed` eller `Unknown` . |
| meddelande | sträng | Meddelande om fel, tom sträng annars. |
| correlationId | sträng   | ID för att fråga efter status för uppladdnings förfrågan. |

Exempel:

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

I Azure Portal startar du metoden med metod namnet `UploadSupportBundle` och följande JSON-nyttolast när du har fyllt i sasURL med din information:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Anropa Direct-metoden ' UploadSupportBundle ' i Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>Hämta status för överförings förfrågan

Använd metoden **GetTaskStatus** Direct för att fråga efter status för en begäran om uppladdning av loggar. Nytto lasten för begäran om **GetTaskStatus** använder `correlationId` i begäran om att ladda upp loggar för att hämta aktivitetens status. `correlationId`Returneras som svar på anropet till **UploadModuleLogs** Direct-metoden.

Den här metoden accepterar en JSON-nyttolast med följande schema:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

En lyckad begäran om att ladda upp loggar returnerar **"status": 200** följt av en nytto last med samma schema som **UploadModuleLogs** -svaret:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Namn | Typ | Beskrivning |
|-|-|-|
| status | sträng | En av `NotStarted` ,,, `Running` `Completed` `Failed` eller `Unknown` . |
| meddelande | sträng | Meddelande om fel, tom sträng annars. |
| correlationId | sträng   | ID för att fråga efter status för uppladdnings förfrågan. |

Exempel:

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

I Azure Portal anropar du metoden med metod namnet `GetTaskStatus` och följande JSON-nyttolast när du har fyllt i GUID med din information:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Anropa Direct-metoden ' GetTaskStatus ' i Azure Portal](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>Nästa steg

[Egenskaperna för IoT Edge agenten och IoT Edge Hub-modulen är dubbla](module-edgeagent-edgehub.md)
