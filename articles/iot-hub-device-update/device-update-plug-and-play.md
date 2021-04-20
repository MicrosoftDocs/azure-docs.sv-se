---
title: Förstå hur enhetsuppdatering för IoT Hub använder IoT-Plug and Play | Microsoft Docs
description: Enhetsuppdatering för IoT Hub använder för att identifiera och hantera enheter som är trådlösa uppdaterings kompatibla.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: fbc3502952e11830ef9abb06cb709fcc60288343
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739538"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Enhetsuppdatering för IoT Hub och IoT-Plug and Play

Enhetsuppdatering för IoT Hub använder [IoT-Plug and Play](../iot-pnp/index.yml) för att identifiera och hantera enheter som är trådlösa. Enhetsuppdateringstjänsten skickar och tar emot egenskaper och meddelanden till och från enheter med hjälp av PnP-gränssnitt. Enhetsuppdatering för IoT Hub kräver att IoT-enheter implementerar följande gränssnitt och modell-ID enligt beskrivningen nedan.

## <a name="adu-core-interface"></a>ADU Core-gränssnitt

Gränssnittet "ADUCoreInterface" används för att skicka uppdateringsåtgärder och metadata till enheter och ta emot uppdateringsstatus från enheter. Gränssnittet "ADU Core" är uppdelat i två objektegenskaper.

Det förväntade komponentnamnet i din modell är **"azureDeviceUpdateAgent" när du** implementerar det här gränssnittet. [Läs mer om Azure IoT PnP-komponenter](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Agentmetadata

Agentmetadata innehåller fält som enheten eller enhetsuppdateringsagenten använder för att skicka information och status till enhetsuppdateringstjänsterna.

|Name|Schema|Riktning|Beskrivning|Exempel|
|----|------|---------|-----------|-----------|
|resultCode|heltal|enhet till moln|En kod som innehåller information om resultatet av den senaste uppdateringsåtgärden. Kan fyllas i för att antingen lyckas eller misslyckas och bör följa [specifikationen för HTTP-statuskod.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)|500|
|extendedResultCode|heltal|enhet till moln|En kod som innehåller ytterligare information om resultatet. Kan fyllas i för antingen lyckad eller misslyckad.|0x80004005|
|state|heltal|enhet till moln|Det är ett heltal som anger det aktuella tillståndet för enhetsuppdateringsagenten. Mer information finns nedan |Inaktiv|
|installedUpdateId|sträng|enhet till moln|Ett ID för den uppdatering som för närvarande är installerad (via Enhetsuppdatering). Det här värdet är null för en enhet som aldrig har tagit en uppdatering via Enhetsuppdatering.|Null|
|`deviceProperties`|Karta|enhet till moln|Den uppsättning egenskaper som innehåller tillverkare och modell.|Mer information finns nedan

#### <a name="state"></a>Tillstånd

Det är den status som rapporteras av enhetsuppdateringsagenten när den har tagit emot en åtgärd från enhetsuppdateringstjänsten. `State` rapporteras som svar på en `Action` (se `Actions` nedan) som skickas till enhetsuppdateringsagenten från enhetsuppdateringstjänsten. Se [översiktsarbetsflödet](understand-device-update.md#device-update-agent) för begäranden som flödar mellan enhetsuppdateringstjänsten och enhetsuppdateringsagenten.

|Name|Värde|Beskrivning|
|---------|-----|-----------|
|Inaktiv|0|Enheten är redo att ta emot en åtgärd från enhetsuppdateringstjänsten. Efter en lyckad uppdatering returneras tillståndet till `Idle` tillståndet .|
|DownloadSucceeded|2|En lyckad nedladdning.|
|InstallSucceeded|4|En lyckad installation.|
|Misslyckad|255|Ett fel inträffade under uppdateringen.|

#### <a name="device-properties"></a>Egenskaper för enhet

Det är den uppsättning egenskaper som innehåller tillverkare och modell.

|Name|Schema|Riktning|Description|
|----|------|---------|-----------|
|manufacturer|sträng|enhet till moln|Enhetens enhetstillverkare, rapporterad via `deviceProperties` . Den här egenskapen läses från en av två platser . Gränssnittet "AzureDeviceUpdateCore" försöker först [](device-update-configuration-file.md) läsa "aduc_manufacturer"-värdet från konfigurationsfilen.  Om värdet inte fylls i i konfigurationsfilen rapporteras som standard kompileringstidsdefinitionen för ADUC_DEVICEPROPERTIES_MANUFACTURER. Den här egenskapen rapporteras endast vid start.|
|modell|sträng|enhet till moln|Enhetsmodellen för enheten, rapporterad via `deviceProperties` . Den här egenskapen läses från en av två platser . Gränssnittet AzureDeviceUpdateCore försöker först läsa "aduc_model"-värdet från [konfigurationsfilen.](device-update-configuration-file.md)  Om värdet inte fylls i i konfigurationsfilen rapporteras som standard kompileringstidsdefinitionen för ADUC_DEVICEPROPERTIES_MODEL. Den här egenskapen rapporteras endast vid start.|
|aduVer|sträng|enhet till moln|Version av enhetsuppdateringsagenten som körs på enheten. Det här värdet läses endast från versionen om under kompileringstiden ENABLE_ADU_TELEMETRY_REPORTING värdet 1 (sant). Kunder kan välja att avanmäla sig från versionsrapportering genom att ange värdet till 0 (falskt). [Anpassa egenskaper för enhetsuppdateringsagenten.](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)|
|Dover|sträng|enhet till moln|Versionen av Leveransoptimering agenten som körs på enheten. Värdet läses endast från bygget om under kompileringstiden ENABLE_ADU_TELEMETRY_REPORTING har angetts till 1 (sant). Kunder kan välja att avanmäla sig från versionsrapporteringen genom att ange värdet till 0 (falskt). [Så här anpassar du Leveransoptimering agentegenskaper](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components).|

### <a name="service-metadata"></a>Tjänstmetadata

Tjänstmetadata innehåller fält som enhetsuppdateringstjänsterna använder för att kommunicera åtgärder och data till enhetsuppdateringsagenten.

|Name|Schema|Riktning|Description|
|----|------|---------|-----------|
|åtgärd|heltal|moln till enhet|Det är ett heltal som motsvarar en åtgärd som agenten ska utföra. Värden som anges nedan.|
|updateManifest|sträng|moln till enhet|Används för att beskriva innehållet i en uppdatering. Genererad från [importmanifestet](import-update.md#create-a-device-update-import-manifest)|
|updateManifestSignature|JSON-objekt|moln till enhet|En JSON-webbsignatur (JWS) med JSON-webbnycklar som används för källverifiering.|
|fileUrls|Karta|moln till enhet|Mappning `FileHash` av till `DownloadUri` . Talar om för agenten vilka filer som ska laddas ned och vilken hash som ska användas för att verifiera att filerna har laddats ned korrekt.|

#### <a name="action"></a>Action

`Actions` nedan representerar de åtgärder som vidtas av enhetsuppdateringsagenten enligt instruktioner från enhetsuppdateringstjänsten. Enhetsuppdateringsagenten rapporterar en `State` (se `State` avsnittet ovan) som bearbetar `Action` mottaget. Se [översiktsarbetsflödet](understand-device-update.md#device-update-agent) för begäranden som flödar mellan enhetsuppdateringstjänsten och enhetsuppdateringsagenten.

|Name|Värde|Beskrivning|
|---------|-----|-----------|
|Ladda ned|0|Ladda ned publicerat innehåll eller uppdatera och allt annat innehåll som behövs|
|Installera|1|Installera innehållet eller uppdateringen. Detta innebär vanligtvis att installationsprogrammet anropas för innehållet eller uppdateringen.|
|Använd|2|Slutför uppdateringen. Den signalerar att systemet ska startas om om vid behov.|
|Avbryt|255|Sluta bearbeta den aktuella åtgärden och gå tillbaka till `Idle` . Används också för att meddela agenten i `Failed` tillståndet att gå tillbaka till `Idle` .|

## <a name="device-information-interface"></a>Gränssnitt för enhetsinformation

Device Information Interface är ett begrepp som används i [IoT Plug and Play arkitekturen](../iot-pnp/overview-iot-plug-and-play.md). Den innehåller egenskaper för enhet till moln som ger information om enhetens maskinvara och operativsystem. Enhetsuppdatering för IoT Hub använder egenskaperna DeviceInformation.manufacturer och DeviceInformation.model för telemetri och diagnostik. Mer information om gränssnittet för enhetsinformation finns i det här [exemplet.](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)

Det förväntade komponentnamnet i din modell är **deviceInformation när du** implementerar det här gränssnittet. [Läs mer om Azure IoT PnP-komponenter](../iot-pnp/concepts-components.md)

|Namn|Typ|Schema|Riktning|Beskrivning|Exempel|
|----|----|------|---------|-----------|-----------|
|manufacturer|Egenskap|sträng|enhet till moln|Enhetstillverkarens företagsnamn. Detta kan vara samma som namnet på OEM-tillverkaren (Original Equipment Manufacturer).|Contoso|
|modell|Egenskap|sträng|enhet till moln|Enhetsmodellnamn eller ID.|IoT Edge enhet|
|swVersion|Egenskap|sträng|enhet till moln|Version av programvaran på enheten. swVersion kan vara versionen av din inbyggda programvara.|4.15.0-122|
|osName|Egenskap|sträng|enhet till moln|Namnet på enhetens operativsystem.|Ubuntu Server 18.04|
|processorArchitecture|Egenskap|sträng|enhet till moln|Processorns arkitektur på enheten.|ARM64|
|processorManufacturer|Egenskap|sträng|enhet till moln|Namnet på tillverkaren av processorn på enheten.|Microsoft|
|totalStorage|Egenskap|sträng|enhet till moln|Totalt tillgängligt lagringsutrymme på enheten i kilobyte.|2048|
|totalMemory|Egenskap|sträng|enhet till moln|Totalt tillgängligt minne på enheten i kilobyte.|256|

## <a name="model-id"></a>Modell-ID 

Modell-ID är hur smarta enheter annonserar sina funktioner till Azure IoT-program med IoT Plug and Play.To läs mer om hur du skapar smarta enheter för att annonsera sina funktioner till Azure IoT-program i utvecklarhandboken för [IoT Plug and Play-enheter.](../iot-pnp/concepts-developer-guide-device.md)

Enhetsuppdatering för IoT Hub kräver att den smarta IoT Plug and Play-enheten meddelar ett modell-ID med värdet **"dtmi:AzureDeviceUpdate;1"** som en del av enhetsanslutningen. [Lär dig hur du meddelar ett modell-ID.](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement)