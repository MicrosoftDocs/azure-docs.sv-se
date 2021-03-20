---
title: Koncept för Azure-IoT Hub Device Provisioning Service – enhet
description: Beskriver metoder för att etablera enheter för Azure-IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 9653a584382584d982c55008a6e8547de28691b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91842860"
---
# <a name="iot-hub-device-reprovisioning-concepts"></a>Metoder för att etablera IoT Hub enhet

Under livs cykeln för en IoT-lösning är det vanligt att flytta enheter mellan IoT-hubbar. Orsakerna till den här flytten kan innefatta följande scenarier:

* **Geolokalisering/lång latens**: när en enhet flyttas mellan platser förbättras nätverks fördröjningen genom att enheten migreras till en närmare IoT-hubb.

* **Flera innehavare**: en enhet kan användas inom samma IoT-lösning och omtilldelas till en ny kund eller kund webbplats. Den nya kunden kan servas med hjälp av en annan IoT-hubb.

* **Lösnings ändring**: en enhet kan flyttas till en ny eller uppdaterad IoT-lösning. Den här omtilldelningen kan kräva att enheten kommunicerar med en ny IoT-hubb som är ansluten till andra server dels komponenter.

* **Karantän**: liknar en lösnings förändring. En enhet som inte fungerar, har komprometterats eller som är inaktuell kan omtilldelas till en IoT-hubb som bara kan uppdatera och få tillbaka kompatibiliteten. När enheten fungerar korrekt migreras den tillbaka till huvud navet.

Att etablera om supporten inom enhets etablerings tjänsten uppfyller dessa krav. Enheter kan tilldelas automatiskt till nya IoT-hubbar baserat på den nyetablerings princip som har kon figurer ATS på enhetens registrerings post.

## <a name="device-state-data"></a>Enhets tillstånds data

Enhets tillstånds data består av [enhetens dubbla](../iot-hub/iot-hub-devguide-device-twins.md) och enhets funktioner. Dessa data lagras i Device Provisioning service-instansen och IoT-hubben som en enhet är tilldelad till.

![Diagram som visar hur etablering fungerar med enhets etablerings tjänsten.](./media/concepts-device-reprovisioning/dps-provisioning.png)

När en enhet etableras från början med en enhets etablerings tjänst instans utförs följande steg:

1. Enheten skickar en etablerings förfrågan till en enhets etablerings tjänst instans. Tjänst instansen autentiserar enhets identiteten baserat på en registrerings post och skapar den inledande konfigurationen av enhets tillstånds data. Tjänst instansen tilldelar enheten till en IoT-hubb baserat på registrerings konfigurationen och returnerar IoT Hub-tilldelningen till enheten.

2. Etablerings tjänst instansen ger en kopia av alla ursprungliga enhets tillstånds data till den tilldelade IoT-hubben. Enheten ansluter till den tilldelade IoT-hubben och påbörjar åtgärder.

Med tiden kan enhets tillstånds data på IoT Hub uppdateras av [enhets åtgärder](../iot-hub/iot-hub-devguide-device-twins.md#device-operations) och [backend-åtgärder](../iot-hub/iot-hub-devguide-device-twins.md#back-end-operations). Den inledande enhets tillståndsinformation som lagras i enhets etablerings tjänstens instans förblir orörd. Detta är den inledande konfigurationen.

![Etablering med enhets etablerings tjänsten](./media/concepts-device-reprovisioning/dps-provisioning-2.png)

Beroende på scenariot, som en enhet flyttas mellan IoT-hubbar, kan det också vara nödvändigt att migrera enhets status som uppdaterats på föregående IoT-hubb till den nya IoT-hubben. Migreringen stöds genom att etablera principer i Device Provisioning-tjänsten.

## <a name="reprovisioning-policies"></a>Principer för att etableras

Beroende på scenariot skickar en enhet vanligt vis en begäran till en etablerings tjänst instans vid omstart. Den har också stöd för en metod för att manuellt utlösa etablering på begäran. Den ometablerings principen på en registrerings post avgör hur enhets etablerings tjänst instansen hanterar dessa etablerings begär Anden. Principen avgör också om enhets tillstånds data ska migreras under ometableringen. Samma principer är tillgängliga för enskilda registreringar och registrerings grupper:

* **Förnya och migrera data**: den här principen är standard för nya registrerings poster. Den här principen vidtar åtgärder när enheter som är associerade med registrerings posten skickar en ny begäran (1). Beroende på konfigurationen av registrerings posten kan enheten omtilldelas till en annan IoT-hubb. Om enheten ändrar IoT-hubbar tas enhets registreringen med den inledande IoT Hub bort. Den uppdaterade enhets tillståndsinformation från den inledande IoT-hubben kommer att migreras till den nya IoT-hubben (2). Under migreringen rapporteras enhetens status som **tilldela**.

    ![Diagram som visar att en princip vidtar åtgärder när enheter som är associerade med registrerings posten skickar en ny begäran.](./media/concepts-device-reprovisioning/dps-reprovisioning-migrate.png)

* **Ometablering och återställning till ursprunglig konfiguration**: den här principen vidtar åtgärder när enheter som är associerade med registrerings posten skickar en ny etablerings förfrågan (1). Beroende på konfigurationen av registrerings posten kan enheten omtilldelas till en annan IoT-hubb. Om enheten ändrar IoT-hubbar tas enhets registreringen med den inledande IoT Hub bort. De inledande konfigurations data som etablerings tjänst instansen tog emot när enheten etablerades tillhandahålls till den nya IoT-hubben (2). Under migreringen rapporteras enhetens status som **tilldela**.

    Den här principen används ofta för en fabriks återställning utan att ändra IoT-hubbar.

    ![Diagram som visar hur en princip vidtar åtgärder när enheter som är associerade med registrerings posten skickar en ny etablerings förfrågan.](./media/concepts-device-reprovisioning/dps-reprovisioning-reset.png)

* **Återetablera aldrig**: enheten omtilldelas aldrig till en annan hubb. Den här principen tillhandahålls för att hantera bakåtkompatibilitet.

### <a name="managing-backwards-compatibility"></a>Hantera bakåtkompatibla kompatibilitet

Före september 2018 hade enhets tilldelningar till IoT-hubbar ett trögt beteende. När en enhet gick tillbaka genom etablerings processen, tilldelas den bara tillbaka till samma IoT-hubb.

För lösningar som har tagit ett beroende av det här beteendet innehåller etablerings tjänsten bakåtkompatibilitet. Det här beteendet underhålls för närvarande för enheter enligt följande kriterier:

1. Enheterna ansluts med en API-version innan det inbyggda stödet för intern etablering i Device Provisioning-tjänsten är tillgängligt. Se API-tabellen nedan.

2. Registrerings posten för enheterna har ingen angiven princip för etablering.

Den här kompatibiliteten säkerställer att tidigare distribuerade enheter upplever samma beteende som vid den första testningen. Spara inte en nyetablerings princip för dessa registreringar för att bevara det tidigare beteendet. Om en princip för att etablera om har angetts har den här principen företräde framför beteendet. Genom att tillåta att den ometablerings principen prioriteras kan kunderna uppdatera enhetens beteende utan att behöva göra en avbildning av enheten.

Följande flödes diagram hjälper till att visa när beteendet är tillgängligt:

![diagram över bakåtkompatibilitet](./media/concepts-device-reprovisioning/reprovisioning-compatibility-flow.png)

I följande tabell visas API-versionerna innan det interna etablerings stödet i enhets etablerings tjänsten är tillgängligt:

| REST-API | C SDK | Python SDK |  SDK för Node | Java SDK | .NET SDK |
| -------- | ----- | ---------- | --------- | -------- | -------- |
| [2018-04-01 och tidigare](/rest/api/iot-dps/createorupdateindividualenrollment/createorupdateindividualenrollment#uri-parameters) | [1.2.8 och tidigare](https://github.com/Azure/azure-iot-sdk-c/blob/master/version.txt) | [1.4.2 och tidigare](https://github.com/Azure/azure-iot-sdk-python/blob/0a549f21f7f4fc24bc036c1d2d5614e9544a9667/device/iothub_client_python/src/iothub_client_python.cpp#L53) | [1.7.3 eller tidigare](https://github.com/Azure/azure-iot-sdk-node/blob/074c1ac135aebb520d401b942acfad2d58fdc07f/common/core/package.json#L3) | [1.13.0 eller tidigare](https://github.com/Azure/azure-iot-sdk-java/blob/794c128000358b8ed1c4cecfbf21734dd6824de9/device/iot-device-client/pom.xml#L7) | [1.1.0 eller tidigare](https://github.com/Azure/azure-iot-sdk-csharp/blob/9f7269f4f61cff3536708cf3dc412a7316ed6236/provisioning/device/src/Microsoft.Azure.Devices.Provisioning.Client.csproj#L20)

> [!NOTE]
> Dessa värden och länkar kommer förmodligen att ändras. Detta är endast ett plats hållare som försöker avgöra var versionerna kan bestämmas av en kund och vilka förväntade versioner som ska vara.

## <a name="next-steps"></a>Nästa steg

* [Så här etablerar du om enheter](how-to-reprovision.md)
