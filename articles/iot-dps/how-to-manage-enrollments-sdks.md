---
title: 'Hantera enhets registreringar med hjälp av Azure DPS SDK: er'
description: 'Hantera enhets registreringar i IoT Hub Device Provisioning Service (DPS) med hjälp av tjänst-SDK: er'
author: robinsh
ms.author: robinsh
ms.date: 04/04/2018
ms.topic: conceptual
ms.service: iot-dps
ms.custom: fasttrack-edit, iot
services: iot-dps
ms.openlocfilehash: 4a5e8b6f430f6af49ab79ca0f8cb2253bd0f2049
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520664"
---
# <a name="how-to-manage-device-enrollments-with-azure-device-provisioning-service-sdks"></a>Hantera enhets registreringar med SDK: er för Azure Device Provisioning-tjänsten
En *enhets registrering* skapar en post för en enskild enhet eller en grupp av enheter som kan komma att registreras med enhets etablerings tjänsten. Registrerings posten innehåller den initiala önskade konfigurationen för enheten eller enheterna som en del av registreringen, inklusive önskad IoT-hubb. Den här artikeln visar hur du hanterar enhets registreringar för din etablerings tjänst program mässigt med hjälp av SDK: er för Azure IoT Provisioning-tjänsten.  SDK: er är tillgängliga på GitHub i samma databas som Azure IoT SDK: er.

## <a name="prerequisites"></a>Förutsättningar
* Hämta anslutnings strängen från din enhets etablerings tjänst instans.
* Hämta enhetens säkerhets artefakter för den [mekanism för attestering](concepts-security.md#attestation-mechanism) som används:
    * [**Trusted Platform Module (TPM)**](/azure/iot-dps/concepts-security#trusted-platform-module):
        * Individuell registrering: registrerings-ID och TPM-bekräftelse nyckel från en fysisk enhet eller från TPM-simulatorn.
        * Registrerings gruppen gäller inte för TPM-attestering.
    * [**X. 509**](/azure/iot-dps/concepts-security):
        * Individuell registrering: [löv certifikatet](/azure/iot-dps/concepts-security) från fysisk enhet eller från SDK [tärning](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) -emulatorn.
        * Registrerings grupp: [certifikat utfärdare/rot certifikat](/azure/iot-dps/concepts-security#root-certificate) eller [mellanliggande certifikat](/azure/iot-dps/concepts-security#intermediate-certificate)som används för att skapa enhets certifikat på en fysisk enhet.  Det kan också genereras från SDK tärning-emulatorn.
* Exakta API-anrop kan vara olika på grund av skillnader i språket. Mer information hittar du i exemplen som tillhandahålls på GitHub:
   * [Klient exempel för Java-etablerings tjänst](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples)
   * [ Klient exempel förNode.js etablerings tjänst](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)
   * [Klient exempel för .NET-etablerings tjänst](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)

## <a name="create-a-device-enrollment"></a>Skapa en enhets registrering
Det finns två sätt att registrera enheter med etablerings tjänsten:

* En **registrerings grupp** är en post för en grupp av enheter som delar en gemensam mekanism för attestering av X. 509-certifikat, signerade av [rot certifikatet](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) eller [mellanliggande certifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate). Vi rekommenderar att du använder en registrerings grupp för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter som alla kommer till samma klient. Observera att du bara kan registrera enheter som använder mekanismen för 509-attestering för X. som *registrerings grupper*. 

    Du kan skapa en registrerings grupp med SDK: er med följande arbets flöde:

    1. För registrerings gruppen använder mekanismen X. 509 rot certifikat.  Anropa service SDK API ```X509Attestation.createFromRootCertificate``` med rot certifikat för att skapa attestering för registrering.  X. 509-rotcertifikat anges antingen i en PEM-fil eller som en sträng.
    1. Skapa en ny ```EnrollmentGroup``` variabel med hjälp av den ```attestation``` skapade och en unik ```enrollmentGroupId``` .  Du kan också ange parametrar som ```IoTHubHostName``` , ```ProvisioningStatus``` .
    2. Anropa service SDK API ```createOrUpdateEnrollmentGroup``` i Server dels programmet med ```EnrollmentGroup``` för att skapa en registrerings grupp.

* En **enskild registrering** är en post för en enskild enhet som kan registreras. Enskilda registreringar kan använda antingen X. 509-certifikat eller SAS-token (från en fysisk eller virtuell TPM) som attesterings metoder. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika inledande konfigurationer eller för enheter som bara kan använda SAS-token via TPM eller virtuell TPM som mekanism för attestering. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

    Du kan skapa en enskild registrering med SDK: er med följande arbets flöde:
    
    1. Välj din ```attestation``` mekanism, som kan vara TPM eller X. 509.
        1. **TPM**: använda bekräftelse nyckeln från en fysisk enhet eller från TPM-simulatorn som indatamängden kan du anropa SDK-API: et ```TpmAttestation``` för att skapa attestering för registrering. 
        2. **X. 509**: om du använder klient certifikatet som indatamängd kan du anropa SDK-API: et ```X509Attestation.createFromClientCertificate``` för att skapa attestering för registrering.
    2. Skapa en ny ```IndividualEnrollment``` variabel med hjälp av den ```attestation``` skapade och en unik ```registrationId``` som inmatad, som finns på enheten eller som genereras från TPM-simulatorn.  Du kan också ange parametrar som ```Device ID``` , ```IoTHubHostName``` , ```ProvisioningStatus``` .
    3. Anropa service SDK API ```createOrUpdateIndividualEnrollment``` i Server dels programmet med ```IndividualEnrollment``` för att skapa en enskild registrering.

När du har skapat en registrering returnerar enhets etablerings tjänsten ett registrerings resultat. Det här arbets flödet visas [i exemplen ovan.](#prerequisites)

## <a name="update-an-enrollment-entry"></a>Uppdatera en registrerings post

När du har skapat en registrerings post kanske du vill uppdatera registreringen.  Möjliga scenarier är att uppdatera önskad egenskap, uppdatera attesterings metoden eller återkalla åtkomst till enheten.  Det finns olika API: er för enskilda registreringar och grupp registrering, men ingen åtskillnad för mekanismen för attestering.

Du kan uppdatera en registrerings post genom att följa det här arbets flödet:
* **Enskild registrering**:
    1. Hämta den senaste registreringen från etablerings tjänsten först med service SDK API ```getIndividualEnrollment``` .
    2. Ändra parametern för den senaste registreringen vid behov. 
    3. Använd den senaste registreringen och anropa SDK-API: et ```createOrUpdateIndividualEnrollment``` för att uppdatera registreringen.
* **Grupp registrering**:
    1. Hämta den senaste registreringen från etablerings tjänsten först med service SDK API ```getEnrollmentGroup``` .
    2. Ändra parametern för den senaste registreringen vid behov.
    3. Använd den senaste registreringen och anropa SDK-API: et ```createOrUpdateEnrollmentGroup``` för att uppdatera registreringen.

Det här arbets flödet visas [i exemplen ovan.](#prerequisites)

## <a name="remove-an-enrollment-entry"></a>Ta bort en registrerings post

* **Enskilda registreringar** kan tas bort genom att anropa service SDK-API: et ```deleteIndividualEnrollment``` med ```registrationId``` .
* **Grupp registrering** kan tas bort genom att anropa service SDK-API: et ```deleteEnrollmentGroup``` med ```enrollmentGroupId``` .

Det här arbets flödet visas [i exemplen ovan.](#prerequisites)

## <a name="bulk-operation-on-individual-enrollments"></a>Mass åtgärd för enskilda registreringar

Du kan utföra Mass åtgärder för att skapa, uppdatera eller ta bort flera enskilda registreringar efter det här arbets flödet:

1. Skapa en variabel som innehåller flera ```IndividualEnrollment``` .  Implementeringen av den här variabeln skiljer sig för alla språk.  Läs igenom Mass åtgärds exemplet på GitHub för mer information.
2. Anropa service SDK-API ```runBulkOperation``` med en ```BulkOperationMode``` för önskad åtgärd och din variabel för enskilda registreringar. Fyra lägen stöds: skapa, uppdatera, updateIfMatchEtag och ta bort.

När du har utfört en åtgärd returnerar enhets etablerings tjänsten resultatet av en Mass åtgärd.

Det här arbets flödet visas [i exemplen ovan.](#prerequisites)
