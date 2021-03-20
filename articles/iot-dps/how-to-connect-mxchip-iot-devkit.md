---
title: Använda Azure IoT Hub Device Provisioning Service automatisk etablering för att registrera IoT-DevKit för MXChip med IoT Hub | Microsoft Docs
description: Så här använder du automatisk etablering i Azure IoT Hub Device Provisioning Service (DPS) för att registrera MXChip IoT-DevKit med IoT Hub.
author: wesmc7777
ms.author: wesmc
ms.date: 06/25/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: eliotgra
ms.openlocfilehash: d6b6649d03da319171b24baa24983972bf270679
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954553"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Använd Azure IoT Hub Device Provisioning Service automatisk etablering för att registrera MXChip IoT-DevKit med IoT Hub

Den här artikeln beskriver hur du använder Azure-IoT Hub Device Provisioning Service för att [tillhandahålla](about-iot-dps.md#provisioning-process) IoT-DevKit för MXChip till en Azure-IoT Hub. I den här guiden får du lära dig att:

* Konfigurera den globala slut punkten för enhets etablerings tjänsten på en enhet.
* Använd en unik enhets hemlighet (UDS) för att generera ett X. 509-certifikat.
* Registrera en enskild enhet.
* Kontrol lera att enheten är registrerad.

[MXChip IoT-DevKit](https://aka.ms/iot-devkit) är en allt-i-ett Arduino-kompatibelt kort med omfattande kring utrustning och sensorer. Du kan utveckla för IT-enheter med hjälp av [Azure IoT Device Workbench](https://aka.ms/iot-workbench) eller [Azure IoT tools](https://aka.ms/azure-iot-tools) Extension Pack i Visual Studio Code. DevKit levereras med en växande [projekt katalog](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) som vägleder dina prototyp Sakernas Internet-lösningar (IoT) som utnyttjar Azure-tjänster.

## <a name="before-you-begin"></a>Innan du börjar

Utför stegen i den här självstudien genom att först utföra följande uppgifter:

* Konfigurera din DevKits Wi-Fi och Förbered utvecklings miljön genom att följa anvisningarna i avsnittet "förbereda utvecklings miljön" i [ansluta IoT DEVKIT AZ3166 till Azure IoT Hub i molnet](../iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started.md#prepare-the-development-environment).
* Uppgradera till den senaste inbyggda program varan (1.3.0 eller senare) med själv studie kursen [Uppdatera DevKit-programvaran](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) .
* Skapa och länka en IoT Hub med en enhets etablerings tjänst instans genom att följa stegen i [konfigurera IoT Hub Device Provisioning service med Azure Portal](./quick-setup-auto-provision.md).

## <a name="open-sample-project"></a>Öppna exempel projekt

1. Se till att din IoT-DevKit **inte är ansluten** till din dator. Starta VS Code först och Anslut sedan DevKit till datorn.

1. Klicka `F1` för att öppna paletten kommando, skriv och välj **Azure IoT Device Workbench: öppna exempel.**.. Välj sedan **IoT DevKit** som tavla.

1. På sidan IoT Workbench-exempel hittar du **enhets registrering med DPS** och klickar på **Öppna exempel**. Sedan väljer du standard Sök vägen för att ladda ned exempel koden.
    ![Öppna exempel](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>Spara en unik enhets hemlighet på enhetens säkerhets lagring

Automatisk etablering kan konfigureras på en enhet baserat på enhetens [mekanism för attestering](concepts-service.md#attestation-mechanism). MXChip IoT-DevKit använder [enhetens identitets sammansättnings motor](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf) från [Trusted Computing Group](https://trustedcomputinggroup.org). En **unik enhets hemlighet** (UDS) som sparas i en STSAFE Security chip ([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/)) på DevKit används för att generera enhetens unika [X. 509-certifikat](concepts-x509-attestation.md). Certifikatet används senare för registrerings processen i Device Provisioning-tjänsten och under registreringen vid körning.

En typisk UDS är en sträng på 64 tecken, som visas i följande exempel:

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

Så här sparar du en UDS på DevKit:

1. I VS Code, klickar du på statusfältet för att välja COM-porten för DevKit.
  ![Välj COM-port](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. På DevKit trycker du på **knappen a**, push och släpper knappen **Återställ** och släpper sedan **knappen A**. Din DevKit går in i konfigurations läge.

1. Klicka `F1` för att öppna kommando-paletten, skriv och välj **Azure IoT enhet Workbench: Konfigurera enhets inställningar... > konfiguration av unik enhets sträng (UDS)**.
  ![Konfigurera UDS](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. Anteckna den genererade UDS-strängen. Du behöver den för att generera X. 509-certifikatet. Tryck sedan på `Enter` .
  ![Kopiera UDS](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. Bekräfta från meddelandet att UDS har kon figurer ATS på STSAFE.
  ![Konfiguration av UDS lyckades](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> Du kan också konfigurera UDS via seriell port genom att använda verktyg som till exempel SparaTillFil. Följ [konfigurations läget](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/) för att göra det.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>Uppdatera den globala enhetens slut punkt och ID-omfång

I enhets kod måste du ange [enhets etablerings slut punkt](./concepts-service.md#device-provisioning-endpoint) och ID-omfång för att säkerställa klient isoleringen.

1. I Azure Portal väljer du fönstret **Översikt** i enhets etablerings tjänsten och noterar värdena för **Global enhets slut punkt** och **ID-omfång** .
  ![Global slut punkt och ID-omfång för enhets etablerings tjänsten](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. Öppna **DevKitDPS. INO**. Sök efter och Ersätt `[Global Device Endpoint]` och `[ID Scope]` med de värden som du nyss skrev ned.
  ![Slut punkt för enhets etablerings tjänst](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. Fyll `registrationId` variabeln i koden. Endast alfanumeriska tecken, gemener och bindestreck med högst 128 tecken tillåts. Notera också värdet.
  ![Registrerings-ID](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. Klicka `F1` , skriv och välj **Azure IoT enhet Workbench: Ladda upp enhets kod**. Den börjar kompilera och laddar upp koden till DevKit.
  ![Enhetsuppladdning](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>Generera X. 509-certifikat

[Mekanismen för attestering](./concepts-service.md#attestation-mechanism) som används i det här exemplet är X. 509-certifikat. Du måste använda ett verktyg för att generera det.

1. I VS Code, klickar du på `F1` , skriver och väljer **Öppna ny terminal** för att öppna terminalfönstret.

1. Kör `dps_cert_gen.exe` i `tool` mappen.

1. Ange platsen för den kompilerade binära filen som `..\.build\DevKitDPS` . Klistra sedan in **UDS** -och **registrationId** som du precis har nämnt. 
  ![Generera X. 509](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. Ett `.pem` X. 509-certifikat genereras i samma mapp.
  ![X. 509-fil](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>Skapa en post för enhetsregistrering

1. I Azure Portal öppnar du enhets etablerings tjänsten, navigerar till avsnittet hantera registreringar och klickar på **Lägg till enskild registrering**.
  ![Lägg till enskild registrering](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. Klicka på fil ikonen bredvid **primärt Certificate. pem eller. cer-fil** för att överföra den `.pem` genererade filen.
  ![Ladda upp. pem](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>Kontrol lera att DevKit har registrerats med Azure IoT Hub

Tryck på **återställnings** knappen på din DevKit. Du bör se **DPS Connected!** på DevKit-skärmen. När enheten har startats om utförs följande åtgärder:

1. Enheten skickar en registreringsbegäran till enhetsetableringstjänsten.
1. Enhets etablerings tjänsten skickar tillbaka en registrerings utmaning som enheten svarar på.
1. Vid lyckad registrering skickar enhets etablerings tjänsten IoT Hub-URI, enhets-ID och den krypterade nyckeln tillbaka till enheten.
1. IoT Hub klient programmet på enheten ansluter till din hubb.
1. Vid lyckad anslutning till hubben visas enheten i IoT Hub Device Explorer.
  ![Registrerad enhet](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem kan du läsa [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)om IoT-DevKit, eller kontakta följande kanaler för support:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att registrera en enhet säkert på enhets etablerings tjänsten med hjälp av motorn för enhets identitets sammansättning, så att enheten kan registreras automatiskt med Azure IoT Hub. 

Sammanfattnings vis har du lärt dig att:

> [!div class="checklist"]
> * Konfigurera den globala slut punkten för enhets etablerings tjänsten på en enhet.
> * Använd en unik enhets hemlighet för att generera ett X. 509-certifikat.
> * Registrera en enskild enhet.
> * Kontrol lera att enheten är registrerad.

Lär dig hur du [skapar och etablerar en simulerad enhet](./quick-create-simulated-device.md).