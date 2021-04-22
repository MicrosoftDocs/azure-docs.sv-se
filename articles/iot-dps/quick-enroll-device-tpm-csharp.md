---
title: 'Snabbstart – Registrera TPM-enhet på Azure Device Provisioning Service med C #'
description: Snabbstart – Registrera TPM-enhet på Azure IoT Hub Device Provisioning Service (DPS) med C#-tjänst-SDK. Den här snabbstarten använder enskilda registreringar.
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 73a9b27c7b9119ee49fc451ca0a1c03d8db3db0e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868635"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Snabbstart: Registrera TPM-enhet på en IoT Hub Device Provisioning Service C#-tjänst-SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Den här artikeln visar hur du programmässigt skapar en enskild registrering för en TPM-enhet i Azure IoT Hub Device Provisioning Service med hjälp av [C#-tjänst-SDK](https://github.com/Azure/azure-iot-sdk-csharp) och ett C# .NET Core-exempelprogram. Om du vill kan du registrera en simulerad TPM-enhet i etableringstjänsten med hjälp av den här enskilda registreringsposten. Även om de här stegen fungerar på både Windows- och Linux-datorer använder den här artikeln en Windows-utvecklingsdator.

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

1. Kontrollera att du [Visual Studio 2019](https://www.visualstudio.com/vs/) installerat på datorn.

1. Kontrollera att .NET Core SDK [är](https://dotnet.microsoft.com/download) installerat på datorn.

1. Slutför stegen i [Konfigurera IoT Hub Device Provisioning Service med Azure Portal](./quick-setup-auto-provision.md) innan du fortsätter.

1. (Valfritt) Om du vill registrera en simulerad enhet i slutet av den här snabbstarten följer du proceduren i Skapa och etablera en [simulerad TPM-enhet med C#-enhets-SDK](quick-create-simulated-device-tpm-csharp.md) fram till det steg där du får en bekräftelsenyckel för enheten. Spara bekräftelsenyckeln, registrerings-ID:t och eventuellt enhets-ID:t, eftersom du måste använda dem senare i den här snabbstarten.

   > [!NOTE]
   > Följ inte stegen för att skapa en enskild registrering med hjälp av Azure Portal.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Hämta anslutningssträngen för etableringstjänsten

För exemplet i den här snabbstarten behöver du anslutningssträngen för etableringstjänsten.

1. Logga in på Azure Portal, välj **Alla resurser** och sedan device provisioning-tjänsten.

1. Välj **Principer för delad** åtkomst och välj sedan den åtkomstprincip som du vill använda för att öppna dess egenskaper. I **Åtkomstprincip** kopierar och sparar du anslutningssträngen för primärnyckeln.

    ![Hämta etableringsanslutningssträng från portalen](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Skapa ett exempel på enskild registrering

Det här avsnittet visar hur du skapar en .NET Core-konsolapp som lägger till en enskild registrering för en TPM-enhet i etableringstjänsten. Med vissa ändringar kan du även följa de här stegen för att skapa en [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-konsolapp för att lägga till den enskilda registreringen. Mer information om hur du utvecklar med IoT Core finns i [utvecklardokumentationen för Windows IoT Core.](/windows/iot-core/)

1. Öppna Visual Studio och välj **Skapa ett nytt projekt**. I **Skapa ett nytt projekt** väljer du **projektmallen Konsolapp (.NET Core)** för C# och väljer **Nästa.**

1. Ge projektet namnet *CreateTpmEnrollment* och tryck på **Skapa.**

    ![Konfigurera Visual C# Windows Classic Desktop-projekt](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. När lösningen öppnas i Visual Studio i **Solution Explorer** högerklickar du på **projektet CreateTpmEnrollment.** Välj **Hantera NuGet-paket.**

1. I **NuGet Package Manager** väljer du **Bläddra,** söker efter och väljer **Microsoft.Azure.Devices.Provisioning.Service** och trycker sedan på **Installera.**

   ![Fönstret för NuGet-pakethanteraren](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Det här steget hämtar, installerar och lägger till en referens till [Azure IoT Provisioning Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet-paketet och dess beroenden.

1. Lägg till följande `using` -instruktioner efter de `using` andra -satserna överst i `Program.cs` :
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Lägg till följande fält i `Program` klassen, vilket gör de ändringar som anges nedan.

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * Ersätt `ProvisioningServiceConnectionString` platshållarvärdet med anslutningssträngen för etableringstjänsten som du vill skapa registreringen för.

   * Om du vill kan du ändra registrerings-ID:t, bekräftelsenyckeln, enhets-ID:t och etableringsstatusen.

   * Om du använder den här snabbstarten tillsammans med snabbstarten Skapa och etablera en [simulerad TPM-enhet med C#-enhets-SDK](quick-create-simulated-device-tpm-csharp.md) för att etablera en simulerad enhet ersätter du bekräftelsenyckeln och registrerings-ID:t med de värden som du antecknade i snabbstarten. Du kan ersätta enhets-ID:t med det värde som föreslås i den snabbstarten, använda ditt eget värde eller använda standardvärdet i det här exemplet.

1. Lägg till följande metod i `Program` klassen .  Den här koden skapar en enskild registreringspost och anropar `CreateOrUpdateIndividualEnrollmentAsync` sedan metoden för för att lägga till den enskilda registreringen i `ProvisioningServiceClient` etableringstjänsten.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```

1. Ersätt slutligen metoden `Main` med följande rader:

   ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
   ```

1. Skapa lösningen.

## <a name="run-the-individual-enrollment-sample"></a>Köra exemplet på enskild registrering
  
Kör exemplet i Visual Studio för att skapa den enskilda registreringen för din TPM-enhet.

Ett kommandotolksfönster visas och börjar visa bekräftelsemeddelanden. När kommandotolken har skapats visas egenskaperna för den nya enskilda registreringen.

Du kan kontrollera att den enskilda registreringen har skapats. Gå till sammanfattningen av enhetsetableringstjänsten och **välj Hantera registreringar** och välj **sedan Enskilda registreringar.** Du bör se en ny registreringspost som motsvarar det registrerings-ID som du använde i exemplet.

![Egenskaper för registrering i portalen](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Välj posten för att verifiera bekräftelsenyckeln och andra egenskaper för posten.

Om du har följt stegen i snabbstarten Skapa och etablera en [simulerad TPM-enhet med C#-enhets-SDK](quick-create-simulated-device-tpm-csharp.md) kan du fortsätta med de återstående stegen i snabbstarten för att registrera din simulerade enhet. Hoppa över stegen för att skapa en enskild registrering med Azure-portalen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska C#-tjänstexempel ska du inte rensa resurserna som skapades i den här snabbstarten. Annars använder du följande steg för att ta bort alla resurser som skapats i den här snabbstarten.

1. Stäng C#-exempelutdatafönstret på datorn.

1. Gå till device provisioning-tjänsten i Azure Portal, välj Hantera **registreringar** och välj sedan **fliken Enskilda registreringar.** Markera kryssrutan bredvid Registrerings-ID för registreringsposten som du skapade med  hjälp av den här snabbstarten och tryck på knappen Ta bort längst upp i fönstret. 

1. Om du har följt stegen i Skapa och etablera en [simulerad TPM-enhet med C#-enhets-SDK](quick-create-simulated-device-tpm-csharp.md) för att skapa en simulerad TPM-enhet gör du följande:

    1. Stäng TPM-simulatorfönstret och exempelutdatafönstret för den simulerade enheten.

    1. I Azure-portalen går du till den IoT Hub där din enhet etablerades. På menyn under **Utforskaren** väljer du **IoT-enheter,** markerar kryssrutan bredvid *ENHETS-ID* för den enhet  som du registrerade i den här snabbstarten och trycker sedan på knappen Ta bort längst upp i fönstret.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du programmatiskt skapat en enskild registreringspost för en TPM-enhet. Du kan också skapa en TPM-simulerad enhet på datorn och etablera den på din IoT-hubb med hjälp Azure IoT Hub Device Provisioning Service. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen.

> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)