---
title: 'Snabbstart – Etablera en simulerad TPM-enhet för att Azure IoT Hub med C #'
description: Snabbstart – Skapa och etablera en simulerad TPM-enhet med C#-enhets-SDK för Azure IoT Hub Device Provisioning Service (DPS). Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 43ad18173c96c506c0df87a01fc7452065442bc9
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868671"
---
# <a name="quickstart-create-and-provision-a-simulated-tpm-device-using-c-device-sdk-for-iot-hub-device-provisioning-service"></a>Snabbstart: Skapa och etablera en simulerad TPM-enhet med C#-enhets-SDK för IoT Hub Device Provisioning Service

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

De här stegen visar hur du använder [Azure IoT-exempel för C# ](https://github.com/Azure-Samples/azure-iot-samples-csharp) för att simulera en TPM-enhet på en utvecklingsdator som kör Windows operativsystem. Exemplet ansluter även den simulerade enhet till en IoT Hub med hjälp av enhetsetableringstjänsten. 

Exempelkoden använder Windows TPM-simulatorn som enhetens [maskinvarusäkerhetsmodul (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). 

Om du inte är bekant med processen för automatisk etablering kan du läsa [etableringsöversikten.](about-iot-dps.md#provisioning-process) Se också till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub Device Provisioning-tjänsten med Azure Portal) innan du fortsätter. 

Azure IoT Device Provisioning Service stöder två typer av registreringar:

- [Registreringsgrupper](concepts-service.md#enrollment-group): används för att registrera flera relaterade enheter.
- [Enskilda registreringar](concepts-service.md#individual-enrollment): används för att registrera en enskild enhet.

Den här artikeln visar enskilda registreringar.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

<a id="setupdevbox"></a>
## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön 

1. Kontrollera att du har [.NET Core 2.1 SDK eller senare](https://dotnet.microsoft.com/download) installerat på datorn. 

1. Kontrollera att `git` är installerat på datorn och har lagts till i de miljövariabler som är tillgängliga för kommandofönstret. Se [Git-klientverktyg för Software Freedom Conservancy](https://git-scm.com/download/) för att få den senaste versionen av `git`-verktyg att installera, vilket omfattar **Git Bash**, kommandoradsappen som du kan använda för att interagera med det lokala Git-lagret. 

1. Öppna en kommandotolk eller Git Bash. Klona Azure IoT-exempel för C# GitHub-lagringsplatsen:

    ```cmd
    git clone https://github.com/Azure-Samples/azure-iot-samples-csharp.git
    ```

## <a name="provision-the-simulated-device"></a>Etablera den simulerade enheten

1. Logga in på Azure-portalen. Välj knappen **Alla resurser** på menyn till vänster och öppna Device Provisioning-tjänsten. På **bladet Översikt** noterar du värdet **_FÖR ID-omfång._**

    ![Kopiera omfångs-ID för etableringstjänsten från portalbladet](./media/quick-create-simulated-device-tpm-csharp/copy-scope.png) 

1. I en kommandotolk ändrar du kataloger till projektkatalogen för TPM-enhetsetableringsexemplet.

    ```cmd
    cd .\azure-iot-samples-csharp\provisioning\Samples\device\TpmSample
    ```

1. Skriv följande kommando för att skapa och köra TPM-enhetsetableringsexemplet. Ersätt `<IDScope>`-värdet med ID-omfånget för etableringstjänsten. 

    ```cmd
    dotnet run <IDScope>
    ```

    Det här kommandot startar simulatorn för TPM-chip i en separat kommandotolk. I Windows kan du stöta på en Windows-säkerhet-avisering som frågar om du vill tillåta Simulator.exe kommunicera i offentliga nätverk. I det här exemplet kan du avbryta begäran.

1. Det ursprungliga kommandofönstret visar **_bekräftelsenyckeln, registrerings-ID_** **_och_** ett föreslaget **_enhets-ID som_** krävs för enhetsregistrering. Anteckna värdena. Du använder dessa värden för att skapa en enskild registrering i din instans av enhetsetableringstjänsten. 
   > [!NOTE]
   > Blanda inte ihop fönstret som innehåller utdata från kommandot med fönstret som innehåller utdata från TPM-simulatorn. Du kan behöva välja det ursprungliga kommandofönstret för att få det till förgrunden.

    ![Utdata från fönstret Kommando](./media/quick-create-simulated-device-tpm-csharp/output1.png) 

1. I Azure Portal väljer du Hantera registreringar på menyn Device Provisioning **Service.** Välj fliken **Enskilda registreringar** och välj knappen **Lägg till enskild** registrering längst upp. 

1. I panelen **Lägg till** registrering anger du följande information:
   - Välj **TPM** som identitet för bestyrkande *mekanism*.
   - Ange *registrerings-ID och* *bekräftelsenyckel för* din TPM-enhet från de värden som du antecknade tidigare.
   - Välj en IoT hub som är länkad till din etableringstjänst.
   - Du kan även ange följande information:
       - Ange ett unikt *enhets-ID* (du kan använda det föreslagna eller ange ett eget). Se till att undvika känsliga data när du namnger din enhet. Om du väljer att inte ange ett kommer registrerings-ID:t att användas för att identifiera enheten i stället.
       - Uppdatera **inledande enhetstvillingstatus** med önskad inledande konfiguration för enheten.
   - När du är klar trycker du **på knappen** Spara. 

     ![Ange information för enhetsregistrering på portalbladet](./media/quick-create-simulated-device-tpm-csharp/enterdevice-enrollment.png)  

   Vid lyckad registrering visas *Registrerings-ID* för enheten i listan under fliken *Individual Enrollments* (Enskilda registreringar). 

1. Tryck *på* Retur i kommandofönstret (den som visade bekräftelsenyckeln, **** registrerings-ID och föreslaget **** **_enhets-ID)_** för att registrera den simulerade enheten. Lägg märke till de meddelanden som simulerar enhetsstart och anslutning till Device Provisioning-tjänsten för att hämta IoT-hubinformationen. 

1. Kontrollera att enheten har etablerats. Vid lyckad etablering av den simulerade enheten till den IoT-hubb som är länkad till etableringstjänsten visas enhets-ID:t på hubbens **blad för IoT-enheter.** 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device-tpm-csharp/hub_registration.png) 

    Om du ändrade din *inledande enhetstvillingstatus* från standardvärdet i registreringsposten för din enhet kan den hämta önskad tvillingstatus från hubben och agera utifrån det. Mer information finns i [Understand and use device twins in IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md) (Förstå och använda enhetstvillingar i IoT Hub).

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med och utforska enhetsklientexempel ska du inte rensa resurserna som skapades i den här snabbstarten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
1. Stäng fönstret för TPM-simulatorn på datorn.
1. På den vänstra menyn i Azure Portal väljer du **Alla resurser** och sedan din enhetsetableringstjänst. Längst upp på **bladet Översikt** trycker du **på** Ta bort överst i fönstret.  
1. På den vänstra menyn i den Azure Portal väljer du **Alla resurser** och sedan din IoT-hubb. Längst upp på **bladet Översikt** trycker du **på** Ta bort överst i fönstret.  

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en TPM-simulerad enhet på datorn och etablerat den på din IoT-hubb med hjälp av IoT Hub Device Provisioning Service. Om du vill lära dig hur du registrerar TPM-enheten programmatiskt fortsätter du till snabbstarten för programmatisk registrering av en TPM-enhet. 

> [!div class="nextstepaction"]
> [Azure-snabbstart – Registrera TPM-enhet på Azure IoT Hub Device Provisioning-tjänsten](quick-enroll-device-tpm-csharp.md)
