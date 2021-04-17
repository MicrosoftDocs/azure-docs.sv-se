---
title: Hantera enheter offline – Azure IoT Edge | Microsoft Docs
description: Förstå hur IoT Edge enheter och moduler kan fungera utan Internetanslutning under längre tidsperioder och hur IoT Edge kan göra det möjligt för vanliga IoT-enheter att fungera offline också.
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c9412e2adeb9b43b4c61437fb41e68bc96b86afd
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481845"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Förstå utökade offlinefunktioner för IoT Edge enheter, moduler och underordnade enheter

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge har stöd för utökade offlineåtgärder på IoT Edge enheter och möjliggör offlineåtgärder på icke-IoT Edge underordnade enheter. Så länge en IoT Edge enhet har haft en möjlighet att ansluta till IoT Hub kan enheten och eventuella underordnade enheter fortsätta att fungera med tillfällig eller ingen Internetanslutning.

## <a name="how-it-works"></a>Så här fungerar det

När en IoT Edge-enhet förs i offlineläge IoT Edge hubben tre roller. Först lagrar den alla meddelanden som går uppströms och sparar dem tills enheten återansluter. Den autentiserar moduler och underordnade enheter åt IoT Hub så att de kan fortsätta att fungera. Slutligen möjliggör den även kommunikation mellan underordnade enheter som normalt skulle gå via IoT Hub.

I följande exempel visas hur ett IoT Edge-scenario fungerar i offlineläge:

1. **Konfigurera enheter**

   IoT Edge enheter automatiskt har offlinefunktioner aktiverade. Om du vill utöka den funktionen till andra IoT-enheter måste du deklarera en överordnad-underordnad-relation mellan enheterna i IoT Hub. Sedan konfigurerar du de underordnade enheterna så att de litar på sin tilldelade överordnade enhet och dirigerar kommunikationen från enhet till moln via den överordnade enheten som en gateway.

2. **Synkronisera med IoT Hub**

   Minst en gång efter installationen av IoT Edge måste enheten IoT Edge vara online för att synkronisera med IoT Hub. I den här synkroniseringen IoT Edge enheten information om underordnade enheter som tilldelats till den. Enheten IoT Edge också på ett säkert sätt sin lokala cache för att aktivera offlineåtgärder och hämtar inställningar för lokal lagring av telemetrimeddelanden.

3. **Gå offline**

   När enheten IoT Hub från en IoT Edge enhet, dess distribuerade moduler och eventuella underordnade IoT-enheter kan den fungera på obestämd tid. Moduler och underordnade enheter kan starta och starta om genom att autentisera med IoT Edge-hubben när den är offline. Telemetri som är bunden uppströms IoT Hub lagras lokalt. Kommunikation mellan moduler eller mellan underordnade IoT-enheter underhålls via direkta metoder eller meddelanden.

4. **Återanslut och synkronisera om med IoT Hub**

   När anslutningen till IoT Hub har återställts synkroniseras IoT Edge enheten igen. Lokalt lagrade meddelanden levereras till IoT Hub direkt, men är beroende av anslutningens hastighet, svarstiden IoT Hub och relaterade faktorer. De levereras i samma ordning som de lagrades.

   Eventuella skillnader mellan önskade och rapporterade egenskaper för modulerna och enheterna stäms av. Enheten IoT Edge alla ändringar i sin uppsättning tilldelade underordnade IoT-enheter.

## <a name="restrictions-and-limits"></a>Begränsningar och begränsningar

De utökade offline-funktionerna som beskrivs i den här artikeln finns [i version 1.0.7 IoT Edge version 1.0.7 eller senare.](https://github.com/Azure/azure-iotedge/releases) Tidigare versioner har en delmängd av offline-funktioner. Befintliga IoT Edge enheter som inte har utökade offlinefunktioner kan inte uppgraderas genom att ändra körningsversionen, men måste konfigureras om med en ny IoT Edge-enhetsidentitet för att få dessa funktioner.

Endast icke-IoT Edge enheter kan läggas till som underordnade enheter.

IoT Edge enheter och deras tilldelade underordnade enheter kan fungera utan tidsbestämd offline efter den första synkroniseringen en gång. Lagringen av meddelanden beror dock på TTL-inställningen (Time to Live) och det tillgängliga diskutrymmet för att lagra meddelandena.

## <a name="set-up-parent-and-child-devices"></a>Konfigurera överordnade och underordnade enheter

För att IoT Edge en enhet ska kunna utöka sina utökade offlinefunktioner till underordnade IoT-enheter måste du utföra två steg. Deklarera först de överordnade-underordnade relationerna i Azure Portal. Skapa sedan en förtroenderelation mellan den överordnade enheten och eventuella underordnade enheter och konfigurera sedan kommunikation från enhet till moln så att den går via den överordnade enheten som en gateway.

### <a name="assign-child-devices"></a>Tilldela underordnade enheter

Underordnade enheter kan vara valfri icke-IoT Edge som är registrerad på samma IoT Hub. Överordnade enheter kan ha flera underordnade enheter, men en underordnad enhet har bara en överordnad enhet. Det finns tre alternativ för att ställa in underordnade enheter på en gränsenhet: via Azure Portal, med Hjälp av Azure CLI eller med hjälp IoT Hub SDK för tjänsten.

Följande avsnitt innehåller exempel på hur du kan deklarera den överordnade/underordnade relationen i IoT Hub befintliga IoT-enheter. Om du skapar nya enhetsidentiteter för dina underordnade enheter kan du läsa mer i Autentisera en nedströmsenhet [för att Azure IoT Hub](how-to-authenticate-downstream-device.md) mer information.

#### <a name="option-1-iot-hub-portal"></a>Alternativ 1: IoT Hub portalen

Du kan deklarera den överordnade-underordnade relationen när du skapar en ny enhet. Eller för befintliga enheter kan du deklarera relationen från enhetsinformationssidan för antingen den överordnade IoT Edge eller den underordnade IoT-enheten.

   ![Hantera underordnade enheter från IoT Edge enhetsinformationssidan](./media/offline-capabilities/manage-child-devices.png)

#### <a name="option-2-use-the-az-command-line-tool"></a>Alternativ 2: `az` Använd kommandoradsverktyget

Med [hjälp av Azure-kommandoradsgränssnittet](/cli/azure/) med [IoT-tillägget](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 eller senare) kan du hantera överordnade underordnade relationer med [enhetsidentitetens](/cli/azure/iot/hub/device-identity/) underkommandon. I exemplet nedan används en fråga för att tilldela alla icke-IoT Edge enheter i hubben till underordnade enheter till en IoT Edge enhet.

```azurecli
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name
```

Du kan ändra frågan [för](../iot-hub/iot-hub-devguide-query-language.md) att välja en annan delmängd av enheterna. Kommandot kan ta flera sekunder om du anger en stor uppsättning enheter.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Alternativ 3: Använd IoT Hub Service SDK

Slutligen kan du hantera överordnade underordnade relationer programmatiskt med hjälp av antingen C#, Java eller Node.js IoT Hub Service SDK. Här är ett [exempel på hur du tilldelar en underordnad enhet](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/e2e/test/iothub/service/RegistryManagerE2ETests.cs) med hjälp av C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Konfigurera den överordnade enheten som en gateway

Du kan tänka på en överordnad/underordnad relation som en transparent gateway, där den underordnade enheten har sin egen identitet i IoT Hub men kommunicerar via molnet via sin överordnade enhet. För säker kommunikation måste den underordnade enheten kunna verifiera att den överordnade enheten kommer från en betrodd källa. I annat fall kan tredje part konfigurera skadliga enheter för att personifiera föräldrar och fånga upp kommunikation.

Ett sätt att skapa den här förtroenderelationen beskrivs i detalj i följande artiklar:

* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Ansluta en underordnad enhet (underordnad) till en Azure IoT Edge gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Ange DNS-servrar

För att förbättra robustheten rekommenderar vi starkt att du anger de DNS-serveradresser som används i din miljö. Information om hur du ställer in DNS-servern för IoT Edge finns i lösningen för Edge Agent-modulen som kontinuerligt rapporterar ["tom konfigurationsfil"](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device) och inga moduler startar på enheten i felsökningsartikeln.

## <a name="optional-offline-settings"></a>Valfria offlineinställningar

Om dina enheter går offline IoT Edge överordnade enheten alla meddelanden från enheten till molnet tills anslutningen återupprättas. Modulen IoT Edge hanterar lagring och vidarebefordran av offlinemeddelanden. För enheter som kan vara offline under längre tidsperioder kan du optimera prestanda genom att konfigurera IoT Edge av hubbinställningar.

Öka först time to live-inställningen så att IoT Edge-hubben behåller meddelandena tillräckligt länge för att enheten ska kunna återansluta. Lägg sedan till ytterligare diskutrymme för meddelandelagring.

### <a name="time-to-live"></a>Time to live

Inställningen Time to Live är den tid (i sekunder) som ett meddelande kan vänta på att levereras innan det upphör att gälla. Standardvärdet är 7 200 sekunder (två timmar). Det maximala värdet begränsas endast av det maximala värdet för en heltalsvariabel, som är cirka 2 miljarder.

Den här inställningen är en önskad egenskap för IoT Edge hub, som lagras i modultvillingen. Du kan konfigurera den i Azure Portal eller direkt i distributionsmanifestet.

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Värdlagring för systemmoduler

Meddelanden och information om modultillstånd lagras som IoT Edge i hubbens lokala containerfilsystem. För bättre tillförlitlighet, särskilt när du arbetar offline, kan du även dedikera lagring på värden IoT Edge enhet. Mer information finns i [Ge moduler åtkomst till en enhets lokala lagring](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du ställer in en transparent gateway för dina överordnade/underordnade enhetsanslutningar:

* [Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway](how-to-create-transparent-gateway.md)
* [Autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
