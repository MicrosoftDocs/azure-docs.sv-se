---
title: Själv studie kurs – etablera enhet med Azure IoT Hub Device Provisioning Service
description: Den här självstudien visar hur du kan etablera din enhet till en enda IoT-hubb med Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 876fd8260b64fba4d3d34a766b4259323c660b76
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968085"
---
# <a name="tutorial-provision-the-device-to-an-iot-hub-using-the-azure-iot-hub-device-provisioning-service"></a>Självstudie: etablera enheten till en IoT-hubb med Azure-IoT Hub Device Provisioning Service

I den förra självstudien lärde du dig att konfigurera en enhet för att ansluta till din enhetsetableringstjänst. I den här självstudien lär du dig att använda tjänsten för att etablera enheten till en enda IoT-hubb, med hjälp av automatisk etablering och **_registreringslistor_**. I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter måste du konfigurera din enhet enligt anvisningarna i självstudien [Konfigurera en enhet för etablering med Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-device.md).

Om du inte är bekant med processen för automatisk etablering, granskar du [etablerings](about-iot-dps.md#provisioning-process) översikten innan du fortsätter.

<a id="enrolldevice"></a>
## <a name="enroll-the-device"></a>Registrera enheten

I det här steget ska du lägga till enhetens unika säkerhetsartefakter till enhetsetableringstjänsten. Säkerhetsartefakterna baseras på enhetens [attesteringsmekanism](concepts-service.md#attestation-mechanism) enligt följande:

- För TPM-baserade enheter behöver du:
    - *Bekräftelsenyckeln* som är unik för varje TPM-krets eller -simulering, som hämtas från tillverkaren av TPM-kretsen.  Läs [Understand TPM Endorsement Key](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)) (Förstå TPM-bekräftelsenyckeln) för mer information.
    - *Registrerings-ID:t* som används för att unikt identifiera en enhet i namnrymden/omfattningen. ID:t kanske eller kanske inte är samma som enhetens ID. ID:t är obligatoriskt för alla enheter. För TPM-baserade enheter kan registrerings-ID:t härledas från själv TPM, till exempel en SHA-256-hash för TPM-bekräftelsenyckeln.

      [![Registrerings information för TPM i portalen](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png)](./media/tutorial-provision-device-to-hub/tpm-device-enrollment.png#lightbox)  

- För X.509-baserade enheter behöver du:
    - [Certifikatet som utfärdats till X.509](/windows/win32/seccertenroll/about-x-509-public-key-certificates)-kretsen eller -simuleringen, som antingen en *.pem*- eller *.cer*-fil. För enskild registrering måste du använda det  *signerade certifikatet* per enhet för ditt X. 509-system, och för registrerings grupper måste du använda *rot certifikatet*. 

      [![Lägg till enskild registrering för X. 509-attestering i portalen](./media/tutorial-provision-device-to-hub/individual-enrollment.png)](./media/tutorial-provision-device-to-hub/individual-enrollment.png#lightbox)

Det finns två sätt att registrera enheten till enhetsetableringstjänsten:

- **Registreringsgrupper** Det här representerar en grupp med enheter som delar en specifik attesteringsmekanism. Vi rekommenderar att du använder en registreringsgrupp för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter som ska till samma klient. Mer information om identitetsattestering för registreringsgrupper finns i [Säkerhet](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

    [![Lägg till grupp registrering för X. 509-attestering i portalen](./media/tutorial-provision-device-to-hub/group-enrollment.png)](./media/tutorial-provision-device-to-hub/group-enrollment.png#lightbox)

- **Enskilda registreringar** Detta representerar en inmatning för en enskild enhet som kan registreras med enhetsetableringstjänsten. Enskilda registreringar kan använda antingen x509-certifikat eller SAS-token (i en verklig eller virtuell TPM) som attesteringsmekanismer. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer och enheter som endast kan använda SAS-token via TPM eller virtuella TPM:er som attesteringsmekanism. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

Nu registrerar du enheten med enhetsetableringstjänstens instans, med de säkerhetsartefakter som krävs baserat på enhetens attesteringsmekanism: 

1. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna Device Provisioning-tjänsten.

2. På sammanfattningsbladet för Device Provisioning-tjänsten väljer du **Manage enrollments** (Hantera registreringar). Markera fliken **Enskilda registreringar** eller **Registreringsgrupper** enligt enhetskonfigurationen. Klicka på knappen **Lägg till** längst upp. Välj **TPM** eller **X.509** som identitet för bestyrkande *mekanism* och ange lämpliga säkerhetsartefakter som diskuterades ovan. Du kan ange ett nytt **Enhets-ID för IoT Hub**. Klicka på knappen **Spara** när det är klart. 

3. När enheten har registrerats bör du se den i portalen på följande sätt:

    ![Lyckad TPM-registrering i portalen](./media/tutorial-provision-device-to-hub/tpm-enrollment-success.png)

Efter registreringen väntar etableringstjänsten tills enheten har startats och ansluter till den vid en senare tidpunkt. När enheten startar för första gången interagerar klient-SDK-biblioteket med din krets för att extrahera säkerhetsuppgifter från enheten och verifierar registreringen med enhetsetableringstjänsten. 

## <a name="start-the-iot-device"></a>Starta IoT-enheten

IoT-enheten kan vara en riktig enhet eller en simulerad enhet. Eftersom IoT-enheten nu har registrerats med en instans av enhetsetableringstjänsten kan enheten nu starta och anropa etableringstjänsten för att identifieras med hjälp av attesteringsmetoden. När etableringstjänsten har identifierat enheten tilldelas den till en IoT-hubb. 

Exempel av simulerade enheter, med både TPM- och X.509-attestering, ingår för C, Java, C#, Node.js och Python. Till exempel skulle en simulerad enhet med TPM och [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) följa den process som beskrivs i avsnittet om att [simulera den första startsekvensen för en enhet](quick-create-simulated-device.md#simulate-first-boot-sequence-for-the-device). För samma enhet som använder X.509-certifikatattestering skulle det här avsnittet om [startsekvens](quick-create-simulated-device-x509.md#simulate-first-boot-sequence-for-the-device) gälla.

Se [instruktionsguiden för MXChip Iot DevKit](how-to-connect-mxchip-iot-devkit.md) för ett exempel på en riktig enhet.

Starta enheten för att låta enhetens klientprogram starta registreringen med din enhetsetableringstjänst.  

## <a name="verify-the-device-is-registered"></a>Kontrollera att enheten är registrerad

När enheten startar ska följande åtgärder utföras:

1. Enheten skickar en registreringsbegäran till enhetsetableringstjänsten.
2. För TPM-enheten skickar enhetsetableringstjänsten tillbaka en registreringskontroll som enheten svarar på. 
3. Om registringen har lyckats skickar enhetsetableringstjänsten tillbaka IoT-hubbens URI, enhets-ID och den krypterade nyckeln till enheten. 
4. IoT Hub-klientprogrammet på enheten ansluter därefter till hubben. 
5. Om anslutningen till hubben lyckas bör enheten visas i IoT-hubbens utforskare för **IoT-enheter**. 

    ![Lyckad anslutning till hubben i portalen](./media/tutorial-provision-device-to-hub/hub-connect-success.png)

Mer information finns i exemplet med etablering av enhetsklient, [prov_dev_client_sample.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c). Exemplet demonstrerar etablering av en simulerad enhet med hjälp av TPM, X.509-certifikat och symmetriska nycklar. Stegvisa instruktioner om hur du använder exemplet finns i snabbstarterna om attestering med [TPM](./quick-create-simulated-device.md), [X.509](./quick-create-simulated-device-x509.md) och [symmetrisk nyckel](./quick-create-simulated-device-symm-key.md).

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

Gå vidare till nästa självstudiekurs för att lära dig att etablera flera enheter över belastningsutjämnade hubbar. 

> [!div class="nextstepaction"]
> [Etablera enheter till flera belastningsutjämnade IoT-hubbar](./tutorial-provision-multiple-hubs.md)