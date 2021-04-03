---
title: 'Snabb start – etablera en simulerad TPM-enhet till Azure IoT Hub med C #'
description: Snabb start – skapa och etablera en simulerad TPM-enhet med C#-enhets-SDK för Azure IoT Hub Device Provisioning Service (DPS). Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b2648382fb19fafcfc342379aa9da974f6f8d1ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90528428"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Snabb start: skapa och etablera en simulerad TPM-enhet med C#-enhets-SDK för IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

De här stegen visar hur du använder [Azure IoT-exempel för C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp) för att simulera en TPM-enhet på en utvecklingsdator som kör Windows operativsystem. Exemplet ansluter även den simulerade enhet till en IoT Hub med hjälp av enhetsetableringstjänsten. 

Exempelkoden använder Windows TPM-simulatorn som enhetens [maskinvarusäkerhetsmodul (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). 

Om du inte är bekant med processen för autoetablering, granskar du [etablerings](about-iot-dps.md#provisioning-process) översikten. Se också till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal) innan du fortsätter. 

Azure IoT Device Provisioning Service stöder två typer av registreringar:

- [Registreringsgrupper](concepts-service.md#enrollment-group): används för att registrera flera relaterade enheter.
- [Enskilda registreringar](concepts-service.md#individual-enrollment): används för att registrera en enskild enhet.

Den här artikeln visar enskilda registreringar.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön 

1. Kontrol lera att du har [.net Core 2,1 SDK eller senare](https://www.microsoft.com/net/download/windows) installerad på datorn. 

1. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

1. Öppna en kommandotolk eller Git Bash. Klona Azure IoT-exempel för C# GitHub-lagringsplatsen:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Etablera den simulerade enheten

1. Logga in på Azure-portalen. Välj knappen **alla resurser** i den vänstra menyn och öppna Device Provisioning-tjänsten. På bladet **Översikt** noterar du värdet för **_ID-omfång_** .

    ![Kopiera omfångs-ID för etableringstjänsten från portalbladet](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. I en kommandotolk ändrar du kataloger till projektkatalogen för TPM-enhetsetableringsexemplet.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Skriv följande kommando för att skapa och köra TPM-enhetsetableringsexemplet. Ersätt `<IDScope>`-värdet med ID-omfånget för etableringstjänsten. 

    ```cmd
    dotnet run <IDScope>
    ```

    Det här kommandot startar simulatorn för TPM-chip i en separat kommandotolk. I Windows kan du stöta på en säkerhets avisering i Windows som frågar om du vill tillåta Simulator.exe att kommunicera i offentliga nätverk. I det här exemplet kan du avbryta begäran.

1. Det ursprungliga kommando fönstret visar **_bekräftelse nyckeln_**, **_registrerings-ID_** och ett föreslaget **_enhets-ID_** som krävs för enhets registrering. Anteckna värdena. Du använder dessa värden för att skapa en enskild registrering i din instans av enhetsetableringstjänsten. 
   > [!NOTE]
   > Blanda inte ihop fönstret som innehåller utdata från kommandot med fönstret som innehåller utdata från TPM-simulatorn. Du kan behöva välja det ursprungliga kommando fönstret för att ta det i förgrunden.

    ![Utdata från fönstret Kommando](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. I Azure Portal väljer du **Hantera registreringar** från menyn Device Provisioning-tjänst. Välj fliken **enskilda registreringar** och välj knappen **Lägg till individuell registrering** överst. 

1. Ange följande information på panelen **Lägg till registrering** :
   - Välj **TPM** som identitet för bestyrkande *mekanism*.
   - Ange *registrerings-ID* och *bekräftelse nyckel* för din TPM-enhet från de värden som du antecknade tidigare.
   - Välj en IoT hub som är länkad till din etableringstjänst.
   - Du kan även ange följande information:
       - Ange ett unikt *enhets-ID* (du kan använda den föreslagna en eller ange en egen). Se till att undvika känsliga data när du namnger din enhet. Om du väljer att inte ange en, används registrerings-ID: t för att identifiera enheten i stället.
       - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
   - När du är klar trycker du på knappen **Spara** . 

     ![Ange information för enhetsregistrering på portalbladet](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Vid lyckad registrering visas *Registrerings-ID* för enheten i listan under fliken *Individual Enrollments* (Enskilda registreringar). 

1. Tryck på *RETUR* i kommando fönstret (det som visade **_bekräftelse nyckeln_**, **_registrerings-ID_** och föreslaget **_enhets-ID_**) för att registrera den simulerade enheten. Lägg märke till de meddelanden som simulerar enhetsstart och anslutning till Device Provisioning-tjänsten för att hämta IoT-hubinformationen. 

1. Kontrollera att enheten har etablerats. Vid lyckad etablering av den simulerade enheten till IoT-hubben som är länkad till din etablerings tjänst visas enhets-ID på hubbens bladet **IoT-enheter** . 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med och utforska enhets klient exemplet ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Stäng fönstret för TPM-simulatorn på datorn.
1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan enhets etablerings tjänsten. Tryck på **ta bort** överst i fönstret på **översikts** bladet.  
1. Välj **alla resurser** på den vänstra menyn i Azure Portal och välj sedan din IoT Hub. Tryck på **ta bort** överst i fönstret på **översikts** bladet.  

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat en TPM-simulerad enhet på datorn och allokerat den till IoT-hubben med hjälp av IoT Hub Device Provisioning Service. Om du vill lära dig hur du registrerar en TPM-enhet program mässigt kan du fortsätta till snabb starten för program registrering av en TPM-enhet. 

> [!div class="nextstepaction"]
> [Azure snabb start – registrera TPM-enhet på Azure IoT Hub Device Provisioning Service](quick-enroll-device-tpm-csharp.md)
