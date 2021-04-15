---
title: Felsöka enhetsanslutningar till Azure IoT Central | Microsoft Docs
description: Felsöka varför du inte ser data från dina enheter i IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: 494608f9dd8fbf986dcda6eeb782a64f6a2ca008
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378575"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Felsök varför data från dina enheter inte visas i Azure IoT Central

Det här dokumentet hjälper enhetsutvecklare att ta reda på varför de data som deras enheter skickar till IoT Central kanske inte visas i programmet.

Det finns två huvudområden att undersöka:

- Problem med enhetsanslutning
  - Autentiseringsproblem som att enheten har ogiltiga autentiseringsuppgifter
  - Problem med nätverksanslutning
  - Enheten är inte godkänd eller blockerad
- Problem med form av nyttolast för enhet

Den här felsökningsguiden fokuserar på problem med enhetsanslutningar och problem med nyttolastens form.

## <a name="device-connectivity-issues"></a>Problem med enhetsanslutning

Det här avsnittet hjälper dig att avgöra om dina data når IoT Central.

Om du inte redan har gjort det installerar du `az cli` verktyget och `azure-iot` tillägget.

Information om hur du installerar `az cli` finns i Installera Azure [CLI.](/cli/azure/install-azure-cli)

Kör [följande](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) kommando `azure-iot` för att installera tillägget:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> Du kan uppmanas att installera biblioteket `uamqp` första gången du kör ett tilläggskommando.

När du har installerat tillägget startar du enheten för att se om de meddelanden som den skickar kommer till `azure-iot` IoT Central.

Använd följande kommandon för att logga in i prenumerationen där du har ditt IoT Central program:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Om du vill övervaka telemetrin som enheten skickar använder du följande kommando:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Om enheten har anslutits till IoT Central visas utdata som liknar följande:

```output
Monitoring telemetry.
Filtering on device: device-001
{
    "event": {
        "origin": "device-001",
        "module": "",
        "interface": "",
        "component": "",
        "payload": {
            "temp": 65.57910343679293,
            "humid": 36.16224660107426
        }
    }
}
```

Om du vill övervaka egenskapsuppdateringar som din enhet utbyter med IoT Central använder du följande förhandsgranskningskommando:

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Om enheten skickar egenskapsuppdateringar visas utdata som liknar följande:

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Om du ser data som visas i terminalen, kommer data att göra det så långt som IoT Central program.

Om inga data visas efter några minuter kan du försöka trycka på tangenten eller på tangentbordet `Enter` om utdatan `return` har fastnat.

Om du fortfarande inte ser några data i terminalen är det troligt att enheten har problem med nätverksanslutningen eller inte skickar data korrekt till IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Kontrollera enhetens etableringsstatus

Om dina data inte visas på övervakaren kontrollerar du enhetens etableringsstatus genom att köra följande kommando:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

Följande utdata visar ett exempel på en enhet som har blockerats från att ansluta:

```json
{
  "@device_id": "v22upeoqx6",
  "device_registration_info": {
    "device_status": "blocked",
    "display_name": "Environmental Sensor - v22upeoqx6",
    "id": "v22upeoqx6",
    "instance_of": "urn:krhsi_k0u:modelDefinition:w53jukkazs",
    "simulated": false
  },
  "dps_state": {
    "error": "Device is blocked from connecting to IoT Central application. Unblock the device in IoT Central and retry. Learn more:
https://aka.ms/iotcentral-docs-dps-SAS",
    "status": null
  }
}
```

| Enhetsetableringsstatus | Beskrivning | Möjlig åtgärd |
| - | - | - |
| Etablerad | Inget omedelbart identifierbart problem. | Ej tillämpligt |
| Registrerad | Enheten har ännu inte anslutit till IoT Central. | Kontrollera enhetsloggarna för att se om det finns anslutningsproblem. |
| Blockerad | Enheten blockeras från att ansluta till IoT Central. | Enheten blockeras från att ansluta till IoT Central program. Avblockera enheten i IoT Central och försök igen. Mer information finns i [Blockera enheter.](concepts-get-connected.md#device-status-values) |
| Ej godkänd | Enheten är inte godkänd. | Enheten är inte godkänd för att ansluta till IoT Central program. Godkänn enheten i IoT Central och försök igen. Mer information finns i [Godkänn enheter](concepts-get-connected.md#device-registration) |
| Oassocierade | Enheten är inte associerad med en enhetsmall. | Associera enheten med en enhetsmall så att IoT Central vet hur data ska parsas. |

Läs mer om [enhetsstatuskoder.](concepts-get-connected.md#device-status-values)

### <a name="error-codes"></a>Felkoder

Om du fortfarande inte kan diagnostisera varför dina data inte visas i är nästa steg att leta efter felkoder som `monitor-events` rapporteras av enheten.

Starta en felsökningssession på enheten eller samla in loggar från enheten. Sök efter eventuella felkoder som enheten rapporterar.

I följande tabeller visas vanliga felkoder och möjliga åtgärder att åtgärda.

Om du får problem som rör ditt autentiseringsflöde:

| Felkod | Beskrivning | Möjlig åtgärd |
| - | - | - |
| 400 | Brödtexten i begäran är inte giltig. Den kan till exempel inte parsas eller så går det inte att verifiera objektet. | Se till att du skickar rätt begärandetext som en del av attestationsflödet eller använd en enhets-SDK. |
| 401 | Det går inte att verifiera auktoriseringstoken. Den har till exempel upphört att gälla eller gäller inte för begärans URI. Den här felkoden returneras också till enheter som en del av TPM-attestationsflödet. | Kontrollera att enheten har rätt autentiseringsuppgifter. |
| 404 | Device Provisioning Service-instansen eller en resurs som en registrering finns inte. | [Skapa en supportbiljett med kundsupporten](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | i `ETag` begäran matchar inte för den befintliga resursen enligt `ETag` RFC7232. | [Skapa en supportbiljett med kundsupporten](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 429 | Åtgärder begränsas av tjänsten. Specifika tjänstbegränsningar finns i [IoT Hub Device Provisioning Service gränser.](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits) | Minska meddelandefrekvensen, dela upp ansvarsområden mellan fler enheter. |
| 500 | Ett internt fel inträffade. | [Skapa ett ärende hos kundsupporten för](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) att se om de kan hjälpa dig ytterligare. |

### <a name="file-upload-error-codes"></a>Felkoder för filuppladdning

Här är en lista över vanliga felkoder som kan visas när en enhet försöker ladda upp en fil till molnet. Kom ihåg att innan enheten kan ladda upp en fil måste du konfigurera [enhetsfiluppladdningar](howto-configure-file-uploads.md) i ditt program.

| Felkod | Beskrivning | Möjlig åtgärd |
| - | - | - |
| 403006  | Du har överskridit antalet samtidiga filöverföringsåtgärder. Varje enhetsklient är begränsad till 10 samtidiga filuppladdningar. | Se till att enheten snabbt meddelar IoT Central att filuppladdningen har slutförts. Om det inte fungerar kan du försöka minska tidsgränsen för begäran. |

## <a name="payload-shape-issues"></a>Problem med nyttolastform

När du har fastställt att enheten skickar data till IoT Central är nästa steg att se till att enheten skickar data i ett giltigt format.

Det finns två huvudkategorier av vanliga problem som gör att enhetsdata inte visas i IoT Central:

- Enhetsmall till enhetsdatamatchningsfel:
  - Matchningsfel i namngivning, till exempel skrivfel eller problem med ärendematchning.
  - Omodelade egenskaper där schemat inte har definierats i enhetsmallen.
  - Schemamatchningsfel, till exempel en typ som definierats i mallen `boolean` som , men data är en sträng.
  - Samma telemetrinamn definieras i flera gränssnitt, men enheten är inte IoT-Plug and Play kompatibel.
- Dataformen är ogiltig JSON. Mer information finns i [Telemetri, egenskaps- och kommandonyttolaster.](concepts-telemetry-properties-commands.md)

Kör det lämpligaste kommandot för ditt scenario för att identifiera vilka kategorier problemet finns i:

- Verifiera telemetrin med hjälp av förhandsgranskningskommandot:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Om du vill verifiera egenskapsuppdateringar använder du förhandsgranskningskommandot

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Du kan uppmanas att installera biblioteket `uamqp` första gången du kör ett `validate` kommando.

Följande utdata visar exempel på fel- och varningsmeddelanden från kommandot validate:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Om du föredrar att använda ett grafiskt användargränssnitt IoT Central **vyn Rådata** för att se om något inte modelleras. Vyn **Rådata** identifierar inte om enheten skickar felaktig JSON.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Skärmbild av vyn Rådata":::

När du har identifierat problemet kan du behöva uppdatera enhetens inbyggda programvara eller skapa en ny enhetsmall som modellerar tidigare omoderade data.

Om du väljer att skapa en ny mall som modellerar data korrekt migrerar du enheter från din gamla mall till den nya mallen. Mer information finns i [Hantera enheter i ditt Azure IoT Central program.](howto-manage-devices.md)

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow forumen](https://azure.microsoft.com/support/community/). Du kan också skapa en [Azure-supportbiljett.](https://portal.azure.com/#create/Microsoft.Support)

Mer information finns i [Support- och hjälpalternativ för Azure IoT.](../../iot-fundamentals/iot-support-help.md)
