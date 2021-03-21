---
title: Felsöka enhets anslutningar till Azure IoT Central | Microsoft Docs
description: Felsök varför du inte ser data från dina enheter i IoT Central
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2020
ms.topic: troubleshooting
ms.service: iot-central
ms.custom: device-developer, devx-track-azurecli
ms.openlocfilehash: d1a7c94152b611ea0dbea249156add617178d7ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98673242"
---
# <a name="troubleshoot-why-data-from-your-devices-isnt-showing-up-in-azure-iot-central"></a>Felsök varför data från dina enheter inte visas i Azure IoT Central

Det här dokumentet hjälper enhets utvecklare att ta reda på varför data enheterna skickas till IoT Central kanske inte visas i programmet.

Det finns två huvud områden att undersöka:

- Problem med enhets anslutning
  - Autentiseringsproblem som enheten har ogiltiga autentiseringsuppgifter
  - Problem med nätverks anslutningen
  - Enheten är inte godkänd eller blockerad
- Problem med enhetens nytto Last form

Den här fel söknings guiden fokuserar på problem med enhets anslutning och form av enhets nytto Last.

## <a name="device-connectivity-issues"></a>Problem med enhets anslutning

Det här avsnittet hjälper dig att avgöra om dina data når IoT Central.

Installera verktyget och tillägget om du inte redan gjort det `az cli` `azure-iot` .

Information om hur du installerar `az cli` finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

[Installera](/cli/azure/azure-cli-reference-for-IoT#extension-reference-installation) `azure-iot` tillägget genom att köra följande kommando:

```azurecli
az extension add --name azure-iot
```

> [!NOTE]
> Du kan uppmanas att installera `uamqp` biblioteket första gången du kör ett tilläggs kommando.

När du har installerat `azure-iot` tillägget kan du starta enheten för att se om de meddelanden som skickas är på väg att IoT Central.

Använd följande kommandon för att logga in på prenumerationen där du har ditt IoT Central-program:

```azurecli
az login
az set account --subscription <your-subscription-id>
```

Använd följande kommando för att övervaka den telemetri som enheten skickar:

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --device-id <device-name>
```

Om enheten har anslutit till IoT Central visas utdata som liknar följande:

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

Om du vill övervaka egenskapen uppdateringar som enheten utbyter med IoT Central använder du följande för hands versions kommando:

```azurecli
az iot central diagnostics monitor-properties --app-id <app-id> --device-id <device-name>
```

Om enheten har skickat egenskaps uppdateringar ser du utdata som liknar följande:

```output
Changes in reported properties:
version : 32
{'state': 'true', 'name': {'value': {'value': 'Contoso'}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac
': 200}, 'brightness': {'value': {'value': 2}, 'status': 'completed', 'desiredVersion': 7, 'ad': 'completed', 'av': 7, 'ac': 200}, 'p
rocessorArchitecture': 'ARM', 'swVersion': '1.0.0'}
```

Om du ser data som visas i terminalen så gör data det så långt som IoT Central programmet.

Om du inte ser några data efter några minuter kan du prova att trycka på `Enter` `return` tangenten eller på tangent bordet, om resultatet är fastnat.

Om du fortfarande inte ser några data i terminalen är det troligt att enheten har problem med nätverks anslutningen eller att inte skickar data korrekt till IoT Central.

### <a name="check-the-provisioning-status-of-your-device"></a>Kontrol lera enhetens etablerings status

Om dina data inte visas på övervakaren kontrollerar du etablerings statusen för enheten genom att köra följande kommando:

```azurecli
az iot central device registration-info --app-id <app-id> --device-id <device-name>
```

Följande utdata visar ett exempel på en enhet som blockeras från att ansluta:

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

| Enhets etablerings status | Beskrivning | Möjlig minskning |
| - | - | - |
| Etablerad | Ingen direkt identifierbar fråga. | Ej tillämpligt |
| Registrerad | Enheten har ännu inte anslutits till IoT Central. | Kontrol lera dina enhets loggar för anslutnings problem. |
| Blockerad | Enheten har blockerats från att ansluta till IoT Central. | Enheten blockeras från att ansluta till IoT Central-programmet. Avblockera enheten i IoT Central och försök igen. Mer information finns i [blockera enheter](concepts-get-connected.md#device-status-values). |
| Ej godkända | Enheten är inte godkänd. | Enheten är inte godkänd för att ansluta till IoT Central-programmet. Godkänn enheten i IoT Central och försök igen. Läs mer i [Godkänn enheter](concepts-get-connected.md#device-registration) |
| Inte kopplad | Enheten är inte kopplad till någon enhets mall. | Associera enheten med en enhets mall så att IoT Central vet hur data ska parsas. |

Läs mer om [enhets status koder](concepts-get-connected.md#device-status-values).

### <a name="error-codes"></a>Felkoder

Om du fortfarande inte kan diagnostisera varför dina data inte visas i `monitor-events` , är nästa steg att leta efter felkoder som rapporteras av enheten.

Starta en felsökningssession på enheten eller samla in loggar från enheten. Sök efter felkoder som rapporteras av enheten.

Följande tabeller visar de vanliga felkoderna och möjliga åtgärder för att åtgärda problemet.

Om du ser problem som rör ditt autentiseringspaket:

| Felkod | Beskrivning | Möjlig minskning |
| - | - | - |
| 400 | Bröd texten i begäran är inte giltig. Det går till exempel inte att parsa den, eller så går det inte att verifiera objektet. | Se till att du skickar rätt begär ande text som en del av attesterings flödet eller Använd en enhets-SDK. |
| 401 | Det går inte att verifiera autentiseringstoken. Till exempel har den upphört att gälla eller inte gäller för frågans URI. Den här felkoden returneras även till enheter som en del av flödet för TPM-attestering. | Kontrol lera att enheten har rätt autentiseringsuppgifter. |
| 404 | Enhets etablerings tjänstens instans eller en resurs, till exempel en registrering, finns inte. | [Filen a Ticket med kund support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 412 | `ETag`I begäran matchar inte den `ETag` befintliga resursen som per RFC7232. | [Filen a Ticket med kund support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). |
| 429 | Åtgärder begränsas av tjänsten. Information om begränsningar för vissa tjänster finns i [IoT Hub Device Provisioning service gränser](../../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-device-provisioning-service-limits). | Minska meddelande frekvensen, dela ansvar mellan fler enheter. |
| 500 | Ett internt fel har inträffat. | [Filen a Ticket med kund support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) för att se om de kan hjälpa dig. |

## <a name="payload-shape-issues"></a>Problem med nytto Last form

När du har upprättat att enheten skickar data till IoT Central, är nästa steg att se till att enheten skickar data i ett giltigt format.

Det finns två huvud kategorier av vanliga problem som gör att enhets data inte visas i IoT Central:

- Enhets mal len för enhets data matchar inte:
  - Matchnings fel i namn, till exempel skrivfel eller Skift läges matchnings problem.
  - Nedmodellerade egenskaper där schemat inte har definierats i enhets mal len.
  - Schema matchnings fel, till exempel en typ som definierats i mallen som `boolean` , men data är en sträng.
  - Samma telemetri-namn definieras i flera gränssnitt, men enheten är inte IoT Plug and Play-kompatibel.
- Data formen är ogiltig JSON. Läs mer i [telemetri, egenskaper och kommando nytto laster](concepts-telemetry-properties-commands.md).

För att identifiera vilka kategorier ditt problem är i kör du det mest lämpliga kommandot för ditt scenario:

- Använd för hands versions kommandot för att validera telemetri:

    ```azurecli
    az iot central diagnostics validate-messages --app-id <app-id> --device-id <device-name>
    ```

- Om du vill validera egenskaps uppdateringar använder du kommandot för hands version

    ```azurecli
    az iot central diagnostics validate-properties --app-id <app-id> --device-id <device-name>
    ```

Du kan uppmanas att installera `uamqp` biblioteket första gången du kör ett `validate` kommando.

Följande utdata visar exempel fel och varnings meddelanden från kommandot validate:

```output
Validating telemetry.
Filtering on device: v22upeoqx6.
Exiting after 300 second(s), or 10 message(s) have been parsed (whichever happens first).
[WARNING] [DeviceId: v22upeoqx6] No encoding found. Expected encoding 'utf-8' to be present in message header.

[WARNING] [DeviceId: v22upeoqx6] Content type '' is not supported. Expected Content type is 'application/json'.

[ERROR] [DeviceId: v22upeoqx6] [TemplateId: urn:krhsi_k0u:modelDefinition:w53jukkazs] Datatype of field 'humid' does not match the da
tatype 'double'. Data '56'. All dates/times/datetimes/durations must be ISO 8601 compliant.
```

Om du föredrar att använda ett grafiskt användar gränssnitt använder du vyn IoT Central **rå data** för att se om något inte har modeller ATS. Vyn **rå data** identifierar inte om enheten skickar felaktig JSON.

:::image type="content" source="media/troubleshoot-connection/raw-data-view.png" alt-text="Skärm bild av vyn rå data":::

När du har identifierat problemet kan du behöva uppdatera enhetens inbyggda program vara eller skapa en ny enhets mall som modeller tidigare modellbaserade data.

Om du väljer att skapa en ny mall som modellerar data korrekt, migrerar du enheter från din gamla mall till den nya mallen. Mer information finns i [Hantera enheter i ditt Azure IoT Central-program](howto-manage-devices.md).

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/community/). Du kan också skriva ett [support ärende för Azure](https://portal.azure.com/#create/Microsoft.Support).

Mer information finns i [Azure IoT-support och hjälp alternativ](../../iot-fundamentals/iot-support-help.md).
