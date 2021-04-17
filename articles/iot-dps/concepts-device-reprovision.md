---
title: Azure IoT Hub Device Provisioning Service – Enhetsbegrepp
description: Beskriver begrepp för ometablering av enheter för Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/16/2021
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: fbc83ec62c10fae00e371cd9ad95cf2860495fad
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575776"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>IoT Hub ometableringsbegrepp för enheter

Under livscykeln för en IoT-lösning är det vanligt att flytta enheter mellan IoT-hubbar. Anledningen till flytten kan vara följande scenarier:

* **Geoplats/GeoLatency:** När en enhet flyttas mellan platser förbättras nätverksfördröjningen genom att enheten migreras till en närmare IoT-hubb.

* **Flera innehavare: En** enhet kan användas i samma IoT-lösning och tilldelas om till en ny kund eller kundwebbplats. Den nya kunden kan serdas med hjälp av en annan IoT-hubb.

* **Lösningsändring:** En enhet kan flyttas till en ny eller uppdaterad IoT-lösning. Den här omtilldeling kan kräva att enheten kommunicerar med en ny IoT-hubb som är ansluten till andra backend-komponenter.

* **Karantän:** Liknar en lösningsändring. En enhet som fungerar dåligt, komprometteras eller är inaktuell kan tilldelas till en IoT-hubb som bara kan uppdateras och uppfylla efterlevnadskrav igen. När enheten fungerar som den ska migreras den sedan tillbaka till sin huvudhubb.

Ometableringsstöd i Device Provisioning Service åtgärdar dessa behov. Enheter kan automatiskt omtilldelas till nya IoT-hubbar baserat på den återetableringsprincip som har konfigurerats för enhetens registreringspost.

## <a name="device-state-data"></a>Enhetstillståndsdata

Enhetstillståndsdata består av [enhetstvilling-](../iot-hub/iot-hub-devguide-device-twins.md) och enhetsfunktioner. Dessa data lagras i Device Provisioning Service-instansen och IoT-hubben som en enhet är tilldelad till.

![Diagram som visar hur etablering fungerar med device provisioning-tjänsten.](./media/concepts-device-reprovisioning/dps-provisioning.png)

När en enhet först etableras med en instans av enhetsetableringstjänsten utförs följande steg:

1. Enheten skickar en etableringsbegäran till en instans av enhetsetableringstjänsten. Tjänstinstansen autentiserar enhetsidentiteten baserat på en registreringspost och skapar den inledande konfigurationen av enhetens tillståndsdata. Tjänstinstansen tilldelar enheten till en IoT-hubb baserat på registreringskonfigurationen och returnerar den IoT-hubbtilldelningen till enheten.

2. Etableringstjänstinstansen ger en kopia av alla inledande enhetstillståndsdata till den tilldelade IoT-hubben. Enheten ansluter till den tilldelade IoT-hubben och börjar åtgärder.

Med tiden kan enhetens tillståndsdata på IoT-hubben uppdateras av [enhetsåtgärder](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) och [backend-åtgärder.](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations) Den initiala informationen om enhetstillstånd som lagras i Device Provisioning Service-instansen förblir orörlig. Dessa oreda enhetstillståndsdata är den inledande konfigurationen.

![Etablering med device provisioning-tjänsten](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Beroende på scenario kan det också vara nödvändigt att migrera enhetstillståndet som uppdaterades på den tidigare IoT-hubben till den nya IoT-hubben när en enhet flyttas mellan IoT-hubbar. Den här migreringen stöds genom att återetablera principer i Device Provisioning Service.

## <a name="reprovisioning-policies"></a>Återetableringsprinciper

Beroende på scenariot skickar en enhet vanligtvis en begäran till en etableringstjänstinstans vid omstart. Den stöder också en metod för att manuellt utlösa etablering på begäran. Återetableringsprincipen för en registreringspost avgör hur instansen av enhetsetableringstjänsten hanterar dessa etableringsbegäranden. Principen avgör också om enhetstillståndsdata ska migreras vid ometablering. Samma principer är tillgängliga för enskilda registreringar och registreringsgrupper:

* **Etablera om och migrera data:** Den här principen är standard för nya registreringsposter. Den här principen vidtar åtgärder när enheter som är associerade med registreringsposten skickar en ny begäran (1). Beroende på konfigurationen av registreringsposten kan enheten omtilldelades till en annan IoT-hubb. Om enheten ändrar IoT-hubbar tas enhetsregistreringen med den första IoT-hubben bort. Den uppdaterade informationen om enhetstillståndet från den första IoT-hubben migreras till den nya IoT-hubben (2). Under migreringen rapporteras enhetens status som **Tilldela**.

    ![Diagram som visar att en princip vidtar åtgärder när enheter som är associerade med registreringsposten skickar en ny begäran.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Etablera om och återställ till den första konfiguration:** Den här principen vidtar åtgärder när enheter som är associerade med registreringsposten skickar en ny etableringsbegäran (1). Beroende på konfigurationen av registreringsposten kan enheten omtilldelades till en annan IoT-hubb. Om enheten ändrar IoT-hubbar tas enhetsregistreringen med den första IoT-hubben bort. De inledande konfigurationsdata som etableringstjänstinstansen tog emot när enheten etablerades tillhandahålls till den nya IoT-hubben (2). Under migreringen rapporteras enhetens status som **Tilldela**.

    Den här principen används ofta för en fabriksåterställning utan att ändra IoT-hubbar.

    ![Diagram som visar hur en princip vidtar åtgärder när enheter som är associerade med registreringsposten skickar en ny etableringsbegäran.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Etablera aldrig om:** Enheten omtilldelades aldrig till en annan hubb. Den här principen används för att hantera bakåtkompatibilitet.

> [!NOTE]
> DPS anropar alltid webhooken för anpassad allokering oavsett princip för ometablering om det finns nya [ReturnData](how-to-send-additional-data.md) för enheten. Om återetableringsprincipen är inställd på att aldrig etablera om **anropas** webhooken, men enheten ändrar inte den tilldelade hubben.

### <a name="managing-backwards-compatibility"></a>Hantera bakåtkompatibilitet

Före september 2018 hade enhetstilldelningar till IoT-hubbar ett fäst beteende. När en enhet gick tillbaka genom etableringsprocessen skulle den bara tilldelas tillbaka till samma IoT-hubb.

För lösningar som är beroende av det här beteendet omfattar etableringstjänsten bakåtkompatibilitet. Det här beteendet upprätthålls för närvarande för enheter enligt följande kriterier:

1. Enheterna ansluter med en API-version innan det finns inbyggt återetableringsstöd i device provisioning-tjänsten. Se API-tabellen nedan.

2. Registreringsposten för enheterna har ingen återetableringsprincip inställd på dem.

Den här kompatibiliteten ser till att tidigare distribuerade enheter upplever samma beteende som under den första testningen. Spara inte en återetableringsprincip för dessa registreringar om du vill bevara det tidigare beteendet. Om en återetableringsprincip har angetts har återetableringsprincipen företräde framför beteendet. Genom att tillåta att återetableringsprincipen får företräde kan kunder uppdatera enhetsbeteendet utan att behöva avbildning av enheten.

Följande flödesdiagram hjälper till att visa när beteendet är närvarande:

![flödesschema för bakåtkompatibilitet](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

Följande tabell visar API-versionerna innan det finns inbyggt återetableringsstöd i device provisioning-tjänsten:

| REST-API | C SDK | Python SDK |  SDK för Node | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 och tidigare](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 och tidigare](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 och tidigare](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 eller tidigare](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 eller tidigare](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 eller tidigare](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Dessa värden och länkar kommer troligen att ändras. Det här är bara ett platshållarförsök för att avgöra var versionerna kan fastställas av en kund och vilka förväntade versioner som kommer att vara.

## <a name="next-steps"></a>Nästa steg

* [Så här återetablera du enheter](how-to-reprovision.md)
