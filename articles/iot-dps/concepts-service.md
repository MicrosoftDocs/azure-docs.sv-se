---
title: Terminologi som används med Azure IoT Hub Device Provisioning Service | Microsoft Docs
description: Beskriver en vanlig terminologi som används med enhets etablerings tjänsten (DPS) och IoT Hub
author: wesmc7777
ms.author: wesmc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotga
ms.openlocfilehash: b9fc37c6589cdd0bc6a5cdce7b7ebebe2c6e9a85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019454"
---
# <a name="iot-hub-device-provisioning-service-dps-terminology"></a>Terminologi för IoT Hub Device Provisioning Service (DPS)

IoT Hub Device Provisioning Service är en hjälp tjänst för IoT Hub som du använder för att konfigurera enhets etablering med noll touch till en angiven IoT-hubb. Med enhets etablerings tjänsten kan du [etablera](about-iot-dps.md#provisioning-process) miljon tals enheter på ett säkert och skalbart sätt.

Enhets etablering är en process i två delar. Den första delen upprättar den inledande anslutningen mellan enheten och IoT-lösningen genom att *Registrera* enheten. Den andra delen tillämpar rätt *konfiguration* på enheten baserat på de särskilda kraven för lösningen. När båda stegen har slutförts är enheten helt *etablerad*. Enhetsetableringstjänsten automatiserar båda stegen och ger sömlös etablering av enheten.

Den här artikeln ger en översikt över de etablerings koncept som är mest tillämpliga för att hantera *tjänsten*. Den här artikeln är mest relevant för personer som ingår i [Cloud setup-steget](about-iot-dps.md#cloud-setup-step) för att få en enhet redo för distribution.

## <a name="service-operations-endpoint"></a>Slut punkt för tjänst åtgärder

Slut punkten för tjänst åtgärder är slut punkten för att hantera tjänst inställningar och underhålla registrerings listan. Den här slut punkten används endast av tjänst administratören. den används inte av enheter.

## <a name="device-provisioning-endpoint"></a>Slut punkt för enhets etablering

Enhets etablerings slut punkten är den enda slut punkten som alla enheter använder för automatisk etablering. URL: en är samma för alla etablerings tjänst instanser, för att eliminera behovet av att göra om Flash-enheter med ny anslutnings information i scenarier med leverans kedjan. ID-omfånget garanterar klient isolering.

## <a name="linked-iot-hubs"></a>Länkade IoT-hubbar

Enhets etablerings tjänsten kan endast etablera enheter till IoT-hubbar som har länkats till den. Genom att länka en IoT-hubb till en instans av enhets etablerings tjänsten får du Läs-/Skriv behörighet till IoT Hub-enhetens register. med länken kan en enhets etablerings tjänst registrera ett enhets-ID och ange den inledande konfigurationen på enheten. Länkade IoT-hubbar kan finnas i alla Azure-regioner. Du kan länka hubbar i andra prenumerationer till etablerings tjänsten.


## <a name="allocation-policy"></a>Resursallokeringsprincip

Inställningen på service nivå som avgör hur enhets etablerings tjänsten tilldelar enheter till en IoT-hubb. Det finns tre allokeringsprinciper som stöds:

* **Jämt viktad distribution**: länkade IoT-hubbar är lika troligt att enheter har allokerats till dem. Standardvärdet. Om du endast etablerar enheter till en IoT-hubb kan du behålla den här inställningen.

* **Lägsta latens**: enheter allokeras till en IoT-hubb med den lägsta svars tiden för enheten. Om flera länkade IoT-hubbar skulle ge samma lägsta latens, hash-värden för etablerings tjänsten enheter över dessa nav

* **Statisk konfiguration via registrerings listan**: specifikationen av den önskade IoT-hubben i registrerings listan har företräde framför tilldelnings principen på tjänst nivå.

* **Anpassad (Använd Azure Function)**: en anpassad resursallokeringsprincip ger dig större kontroll över hur enheter tilldelas till en IoT-hubb. Detta åstadkommer du genom att använda anpassad kod i en Azure-funktion för att tilldela enheter till en IoT-hubb. Enhets etablerings tjänsten anropar din Azure Function-kod och ger all relevant information om enheten och registreringen till din kod. Funktions koden körs och returnerar IoT Hub-informationen som används för att tillhandahålla enheten.

## <a name="enrollment"></a>Registrering

En registrering är en post för enheter eller grupper av enheter som kan registreras via automatisk etablering. Registrerings posten innehåller information om enheten eller gruppen med enheter, inklusive:
- [mekanismen för attestering](#attestation-mechanism) som används av enheten
- den valfria inledande önskad konfiguration
- önskad IoT Hub
- önskat enhets-ID

Det finns två typer av registreringar som stöds av enhets etablerings tjänsten:

### <a name="enrollment-group"></a>Registrerings grupp

En registrerings grupp är en grupp enheter som delar en särskild mekanism för attestering. Registrerings grupper stöder både X. 509 och symmetrisk. Alla enheter i gruppen X. 509-registrering finns X. 509-certifikat som har signerats av samma rot-eller mellanliggande certifikat utfärdare (CA). Varje enhet i gruppen för symmetrisk nyckel registrering finns SAS-token som härletts från den symmetriska gruppen. Namnet på registrerings gruppen och certifikat namnet måste vara alfanumeriskt, gement och får innehålla bindestreck.

> [!TIP]
> Vi rekommenderar att du använder en registrerings grupp för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter som alla kommer till samma klient.

### <a name="individual-enrollment"></a>Individuell registrering

En enskild registrering är en post för en enskild enhet som kan registreras. Enskilda registreringar kan använda antingen X. 509-löv certifikat eller SAS-token (från en fysisk eller virtuell TPM) som attesterings metoder. Registrerings-ID: t i en enskild registrering är alfanumeriskt, gement och får innehålla bindestreck. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

> [!TIP]
> Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika inledande konfigurationer eller för enheter som bara kan autentiseras med SAS-token via TPM-attestering.


## <a name="attestation-mechanism"></a>Mekanism för attestering

En mekanism för attestering är den metod som används för att bekräfta en enhets identitet. Mekanismen för attestering har kon figurer ATS på en registrerings post och talar om för etablerings tjänsten vilken metod som ska användas för att verifiera identiteten för en enhet under registreringen.

> [!NOTE]
> IoT Hub använder "autentiseringsschema" för ett liknande koncept i den tjänsten.

Enhets etablerings tjänsten har stöd för följande former av attestering:
* **X. 509-certifikat** som baseras på flödet för standard-x. 509-certifikatautentisering. Mer information finns i avsnittet om [attestering av X. 509](concepts-x509-attestation.md).
* **Trusted Platform Module (TPM)** baserat på en nonce-utmaning med TPM-standarden för nycklar för att presentera en signerad SAS-token (signatur för delad åtkomst). Detta kräver inte en fysisk TPM på enheten, men tjänsten förväntar sig attestering med bekräftelse nyckeln enligt [TPM-specifikationen](https://trustedcomputinggroup.org/work-groups/trusted-platform-module/). Mer information finns i [TPM-attestering](concepts-tpm-attestation.md).
* **Symmetrisk nyckel** baserat på SAS- [säkerhetstoken](../iot-hub/iot-hub-devguide-security.md#security-tokens)(signatur för delad åtkomst), som innehåller en hashad signatur och en inbäddad förfallo tid. Mer information finns i den [symmetriska nyckeln attestering](concepts-symmetric-key-attestation.md).


## <a name="hardware-security-module"></a>Modul för maskin varu säkerhet

Modulen för maskin varu säkerhet, eller HSM, används för säker, maskinvarubaserad lagring av enhets hemligheter och är den säkraste formen av hemlig lagring. Både X. 509-certifikat och SAS-token kan lagras i HSM. HSM: er kan användas med både mekanismen för attestering som stöds av etablerings tjänsten.

> [!TIP]
> Vi rekommenderar starkt att du använder en HSM med enheter för att säkert lagra hemligheter på dina enheter.

Enhets hemligheter kan också lagras i program vara (minne), men det är en mindre säker form av lagring än en HSM.



## <a name="id-scope"></a>ID-omfång

ID-omfånget tilldelas en enhets etablerings tjänst när den skapas av användaren och används för att unikt identifiera den specifika etablerings tjänsten som enheten ska registrera sig. ID-omfånget genereras av tjänsten och är oföränderligt, vilket garanterar unikheten.

> [!NOTE]
> Unikhet är viktigt för långvariga distributions åtgärder och sammanslagnings-och förvärvs scenarier.


## <a name="registration"></a>Registrering

En registrering är en post i en enhet som har registrerats/tillhandahållits till en IoT Hub via enhets etablerings tjänsten. Registrerings poster skapas automatiskt. de kan tas bort, men de kan inte uppdateras.


## <a name="registration-id"></a>Registrerings-ID

Registrerings-ID: t används för att unikt identifiera en enhets registrering med enhets etablerings tjänsten. Enhets-ID: t måste vara unikt i etablerings tjänstens [ID-omfång](#id-scope). Varje enhet måste ha ett registrerings-ID. Registrerings-ID är alfanumeriskt, SKIFT läges okänsligt och kan innehålla specialtecken, inklusive kolon, punkt, under streck och bindestreck.

* När det gäller TPM anges registrerings-ID: t av själva TPM: en.
* Om det gäller X. 509-baserad attestering anges registrerings-ID: t som certifikatets ämnes namn.

## <a name="device-id"></a>Enhets-ID

Enhets-ID: t är det ID som det visas i IoT Hub. Det önskade enhets-ID: t kan anges i registrerings posten, men det är inte obligatoriskt att ställas in. Det går bara att ange önskat enhets-ID i enskilda registreringar. Om inget önskat enhets-ID anges i registrerings listan används registrerings-ID: t som enhets-ID när enheten registreras. Läs mer om [enhets-ID: n i IoT Hub](../iot-hub/iot-hub-devguide-identity-registry.md).



## <a name="operations"></a>Operations

Åtgärder är fakturerings enheten för enhets etablerings tjänsten. En åtgärd är slutförd slut för ande av en instruktion till tjänsten. Åtgärderna inkluderar enhets registreringar och omregistreringar; åtgärder omfattar även ändringar på tjänst sidan, till exempel att lägga till registrerings List poster och uppdatera poster i registrerings listan.
