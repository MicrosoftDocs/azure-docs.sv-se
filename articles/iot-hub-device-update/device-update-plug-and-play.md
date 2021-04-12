---
title: Förstå hur enhets uppdatering för IoT Hub använder IoT Plug and Play | Microsoft Docs
description: Enhets uppdatering för IoT Hub använder för att identifiera och hantera enheter som har stöd för över-Air-uppdateringar.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0283a84650abaadd454b4f5bca83d1473e443fb8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561822"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Enhets uppdatering för IoT Hub-och IoT-Plug and Play

Enhets uppdatering för IoT Hub använder [IoT plug and Play](../iot-pnp/index.yml) för att identifiera och hantera enheter som är kompatibla med den-Air-uppdateringen. Enhets uppdaterings tjänsten skickar och tar emot egenskaper och meddelanden till och från enheter som använder PnP-gränssnitt. Enhets uppdatering för IoT Hub kräver IoT-enheter för att implementera följande gränssnitt och modell-ID enligt beskrivningen nedan.

## <a name="adu-core-interface"></a>ADU Core-gränssnitt

Gränssnittet "ADUCoreInterface" används för att skicka uppdaterings åtgärder och metadata till enheter och ta emot uppdaterings status från enheter. Gränssnittet "ADU Core" är uppdelat i två objekt egenskaper.

Det förväntade komponent namnet i din modell är **"azureDeviceUpdateAgent"** när du implementerar det här gränssnittet. [Läs mer om Azure IoT PnP-komponenter](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Gent-metadata

Agentens metadata innehåller fält som enhets-eller enhets uppdaterings agenten använder för att skicka information och status till enhets uppdaterings tjänsterna.

|Name|Schema|Riktning|Beskrivning|Exempel|
|----|------|---------|-----------|-----------|
|resultCode|heltal|enhet till molnet|En kod som innehåller information om resultatet av den senaste uppdaterings åtgärden. Kan fyllas i antingen lyckades eller Miss lyckas och bör följa [specifikationen för HTTP-statuskod](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).|500|
|extendedResultCode|heltal|enhet till molnet|En kod som innehåller ytterligare information om resultatet. Kan fyllas i för lyckade eller misslyckade.|0x80004005 visas|
|state|heltal|enhet till molnet|Det är ett heltal som visar enhets uppdaterings agentens aktuella status. Se nedan för information |Inaktiv|
|installedUpdateId|sträng|enhet till molnet|Ett ID för uppdateringen som för närvarande är installerad (via enhets uppdatering). Det här värdet är null för en enhet som aldrig har tagit en uppdatering genom enhets uppdateringen.|Null|
|`deviceProperties`|Karta|enhet till molnet|Uppsättningen egenskaper som innehåller tillverkare och modell.|Se nedan för information

#### <a name="state"></a>Tillstånd

Det är den status som rapporteras av enhets uppdaterings agenten efter att ha tagit emot en åtgärd från enhets uppdaterings tjänsten. `State` rapporteras som svar på en `Action` (se `Actions` nedan) som skickas till enhets uppdaterings agenten från enhets uppdaterings tjänsten. Se [arbets flödet för översikt](understand-device-update.md#device-update-agent) för begär Anden som flödar mellan enhets uppdaterings tjänsten och enhets uppdaterings agenten.

|Name|Värde|Beskrivning|
|---------|-----|-----------|
|Inaktiv|0|Enheten är redo att ta emot en åtgärd från enhets uppdaterings tjänsten. Efter en lyckad uppdatering returneras tillstånd till `Idle` tillstånd.|
|DownloadSucceeded|2|En lyckad nedladdning.|
|InstallSucceeded|4|En lyckad installation.|
|Misslyckad|255|Ett fel uppstod under uppdateringen.|

#### <a name="device-properties"></a>Egenskaper för enhet

Det är en uppsättning egenskaper som innehåller tillverkare och modell.

|Name|Schema|Riktning|Beskrivning|
|----|------|---------|-----------|
|manufacturer|sträng|enhet till molnet|Enhetens tillverkare, som rapporteras genom `deviceProperties` . Den här egenskapen läses från en av två platser – "AzureDeviceUpdateCore"-gränssnittet försöker först läsa värdet "aduc_manufacturer" från [konfigurations fil](device-update-configuration-file.md) filen.  Om värdet inte är ifyllt i konfigurations filen, är det standard att rapportera kompileringen av kompileringen för ADUC_DEVICEPROPERTIES_MANUFACTURER. Den här egenskapen rapporteras bara vid start.|
|modell|sträng|enhet till molnet|Enhets modellen för enheten, som rapporteras genom `deviceProperties` . Den här egenskapen läses från en av två platser – AzureDeviceUpdateCore-gränssnittet kommer först att försöka läsa värdet "aduc_model" från [konfigurations fil](device-update-configuration-file.md) filen.  Om värdet inte är ifyllt i konfigurations filen, är det standard att rapportera kompileringen av kompileringen för ADUC_DEVICEPROPERTIES_MODEL. Den här egenskapen rapporteras bara vid start.|
|aduVer|sträng|enhet till molnet|Versionen av enhets uppdaterings agenten som körs på enheten. Det här värdet läses från versionen endast om tiden för kompilering ENABLE_ADU_TELEMETRY_REPORTING har värdet 1 (sant). Kunder kan välja att inaktivera versions rapportering genom att ange värdet till 0 (falskt). [Anpassa egenskaperna för enhets uppdaterings agenten](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).|
|doVer|sträng|enhet till molnet|Versionen av leverans optimerings agenten som körs på enheten. Värdet läses från versionen endast om tiden för kompilering ENABLE_ADU_TELEMETRY_REPORTING har värdet 1 (sant). Kunder kan välja att inaktivera versions rapportering genom att ange värdet till 0 (falskt). [Anpassa egenskaper för leverans optimerings agent](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components).|

### <a name="service-metadata"></a>Tjänstens metadata

Metadata för tjänsten innehåller fält som enhets uppdaterings tjänsterna använder för att kommunicera åtgärder och data till enhets uppdaterings agenten.

|Name|Schema|Riktning|Beskrivning|
|----|------|---------|-----------|
|åtgärd|heltal|moln till enhet|Det är ett heltal som motsvarar en åtgärd som agenten ska utföra. Värden som anges nedan.|
|updateManifest|sträng|moln till enhet|Används för att beskriva innehållet i en uppdatering. Genererat från [import manifestet](import-update.md#create-device-update-import-manifest)|
|updateManifestSignature|JSON-objekt|moln till enhet|En JSON-webbsignatur (JWS) med JSON-webbnycklar som används för käll verifiering.|
|fileUrls|Karta|moln till enhet|Mappa `FileHash` till `DownloadUri` . Instruerar agenten, vilka filer som ska laddas ned och vilken hash som ska användas för att kontrol lera att filerna laddades ned korrekt.|

#### <a name="action"></a>Action

`Actions` nedan återges de åtgärder som vidtas av enhets uppdaterings agenten enligt anvisningarna i enhets uppdaterings tjänsten. Enhets uppdaterings agenten kommer att rapportera ett `State` (se `State` avsnittet ovan) som bearbetar den `Action` mottagna. Se [arbets flödet för översikt](understand-device-update.md#device-update-agent) för begär Anden som flödar mellan enhets uppdaterings tjänsten och enhets uppdaterings agenten.

|Name|Värde|Beskrivning|
|---------|-----|-----------|
|Ladda ned|0|Ladda ned publicerat innehåll eller uppdatering och annat innehåll som behövs|
|Installera|1|Installera innehållet eller uppdateringen. Det innebär vanligt vis att du anropar installations programmet för innehållet eller uppdateringen.|
|Använd|2|Slutför uppdateringen. Den signalerar systemet för omstart om det behövs.|
|Avbryt|255|Stoppa bearbetningen av den aktuella åtgärden och gå tillbaka till `Idle` . Används också för att meddela agenten i `Failed` tillstånd att gå tillbaka till `Idle` .|

## <a name="device-information-interface"></a>Enhets informations gränssnitt

Enhets informations gränssnittet är ett koncept som används i [IoT plug and Play-arkitekturen](../iot-pnp/overview-iot-plug-and-play.md). Den innehåller en enhet för moln egenskaper som ger information om enhetens maskin vara och operativ system. Enhets uppdatering för IoT Hub använder egenskaperna DeviceInformation. Manufacturer och DeviceInformation. Model för telemetri och diagnostik. Läs mer om enhets informations gränssnittet i det här [exemplet](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json).

Det förväntade komponent namnet i din modell är **deviceInformation** när du implementerar det här gränssnittet. [Lär dig mer om Azure IoT PnP-komponenter](../iot-pnp/concepts-components.md)

|Namn|Typ|Schema|Riktning|Beskrivning|Exempel|
|----|----|------|---------|-----------|-----------|
|manufacturer|Egenskap|sträng|enhet till molnet|Enhets tillverkarens företags namn. Detta kan vara samma som namnet på OEM-tillverkaren (Original Equipment Manufacturer).|Contoso|
|modell|Egenskap|sträng|enhet till molnet|Namn eller ID för enhets modell.|IoT Edge enhet|
|swVersion|Egenskap|sträng|enhet till molnet|Version av program varan på enheten. swVersion kan vara versionen av den inbyggda program varan.|4.15.0 – 122|
|osName|Egenskap|sträng|enhet till molnet|Namnet på enhetens operativ system.|Ubuntu Server 18.04|
|processorArchitecture|Egenskap|sträng|enhet till molnet|Processorns arkitektur på enheten.|ARM64|
|processorManufacturer|Egenskap|sträng|enhet till molnet|Namnet på tillverkaren av processorn på enheten.|Microsoft|
|totalStorage|Egenskap|sträng|enhet till molnet|Totalt tillgängligt lagrings utrymme på enheten i kilobyte.|2048|
|totalMemory|Egenskap|sträng|enhet till molnet|Totalt tillgängligt minne på enheten i kilobyte.|256|

## <a name="model-id"></a>Modell-ID 

Modell-ID är hur smarta enheter annonserar sina funktioner till Azure IoT-program med IoT-plugin och Play.To lär dig mer om hur du skapar smarta enheter för att annonsera sina funktioner till Azure IoT-program besök [IoT plug and Play Device Developer Guide](../iot-pnp/concepts-developer-guide-device.md).

Enhets uppdatering för IoT Hub kräver att IoT Plug and Play smart enhet meddelar ett modell-ID med värdet **"dtmi: AzureDeviceUpdate; 1"** som en del av enhets anslutningen. [Lär dig hur du meddelar ett modell-ID](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement).