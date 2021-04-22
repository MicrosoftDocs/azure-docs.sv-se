---
title: 'Snabbstart – Registrera X.509-enhet på Azure Device Provisioning Service med C #'
description: Den här snabbstarten använder gruppregistreringar. I den här snabbstarten registrerar du X.509-enheter till Azure IoT Hub Device Provisioning Service (DPS) med C#.
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: b6b9331007ec633fe8b8368bd493ce18b4ddde24
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862101"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Snabbstart: Registrera X.509-enheter till Device Provisioning Service med hjälp av C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Den här snabbstarten beskriver hur du använder C# för att programmässigt skapa en [registreringsgrupp](concepts-service.md#enrollment-group) som använder mellanliggande CA X.509-certifikat eller CA X.509-rotcertifikat. Registreringsgruppen skapas med hjälp av Microsoft Azure [IoT SDK för .NET](https://github.com/Azure/azure-iot-sdk-csharp) och ett C# .NET Core-exempelprogram. En registreringsgrupp kontrollerar åtkomst till etableringstjänsten för enheter som delar ett gemensamt signeringscertifikat i certifikatkedjan. Läs mer i informationen om att [kontrollera enhetsåtkomst till etableringstjänsten med X.509-certifikat](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). Mer information om att använda X.509-certifikatbaserad Public Key Infrastructure (PKI) med Azure IoT Hub och enhetsetableringstjänst finns i [Översikt över certifikatsäkerhet med X.509 CA](../iot-hub/iot-hub-x509ca-overview.md). 

Den här snabbstarten förväntar sig att du redan har skapat en IoT Hub- och Device Provisioning Service-instans. Om du inte redan har skapat dessa resurser slutför du snabbstarten [Konfigurera IoT Hub Device Provisioning Service](./quick-setup-auto-provision.md) med Azure Portal innan du fortsätter med den här artikeln.

Även om stegen i den här artikeln fungerar på både Windows- och Linux-datorer använder den här artikeln en Windows-utvecklingsdator.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Installera [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Installera [.NET Core SDK](https://dotnet.microsoft.com/download).
* Installera [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Förbereda testcertifikat

I den här snabbstarten måste du ha en .pem- eller en .cer-fil som innehåller den offentliga delen av ett mellanliggande X.509-certifikat eller ett X.509-rotcertifikat. Det här certifikatet måste laddas upp till din etableringstjänst och verifieras av tjänsten.

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) innehåller testverktyg som kan hjälpa dig att skapa en X.509-certifikatkedja, ladda upp ett rotcertifikat eller mellanliggande certifikat från kedjan och göra innehavarbevis med tjänsten för att verifiera certifikatet.

> [!CAUTION]
> Använd certifikat som skapats med SDK-verktyg endast för utvecklingstestning.
> Använd inte dessa certifikat i produktion.
> De innehåller hårdkodade lösenord, till exempel *1234*, som upphör att gälla efter 30 dagar.
> Om du vill lära dig mer om att hämta certifikat som är lämpliga för produktion kan du läsa [How to get an X.509 CA certificate](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) (Hämta ett X.509 CA-certifikat) i dokumentationen för Azure IoT Hub.
>

Om du vill använda det här testverktyget för att generera certifikat gör du följande:

1. Leta reda på taggnamnet för [den senaste versionen](https://github.com/Azure/azure-iot-sdk-c/releases/latest) av Azure IoT C SDK.

2. Öppna en kommandotolk eller Git Bash-gränssnittet och växla till en arbetsmapp på datorn. Kör följande kommandon för att klona den senaste versionen av [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen. Använd taggen som du hittade i föregående steg som värde för `-b` parametern :

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

   Testverktyget finns i den *azure-iot-sdk-c/tools/CACertificates* för den lagringsplats som du klonade.

3. Följ stegen på sidan om att [hantera CA-testcertifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

Förutom verktygen i C SDK visar [](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) exemplet på verifiering av gruppcertifikat i *Microsoft Azure IoT SDK för .NET* hur du gör innehavarbevis i C# med ett befintligt mellanliggande X.509-certifikat eller rotcertifikatutfärdarcertifikat.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>Hämta anslutningssträngen för etableringstjänsten

För exemplet i den här snabbstarten behöver du anslutningssträngen för etableringstjänsten.

1. Logga in på Azure Portal, välj **Alla resurser** och sedan device provisioning-tjänsten.

1. Välj **Principer för delad** åtkomst och välj sedan den åtkomstprincip som du vill använda för att öppna dess egenskaper. I **Åtkomstprincip** kopierar och sparar du anslutningssträngen för primärnyckeln.

    ![Hämta etableringsanslutningssträng från portalen](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Skapa exemplet för registrering av grupp 

Det här avsnittet visar hur du skapar en .NET Core-konsolapp som lägger till en registreringsgrupp i etableringstjänsten. Med vissa ändringar kan du även följa de här stegen för att skapa en [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-konsolapp för att lägga till registreringsgruppen. Mer information om hur du utvecklar med IoT Core i finns i [Windows IoT Core-dokumentationen för utvecklare](/windows/iot-core/).

1. Öppna Visual Studio och välj **Skapa ett nytt projekt.** I **Skapa ett nytt projekt** väljer du **projektmallen Konsolapp (.NET Core)** för C# och sedan **Nästa.**

1. Ge projektet namnet *CreateEnrollmentGroup* och tryck sedan på **Skapa.**

    ![Konfigurera Visual C# Windows Classic Desktop-projekt](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. När lösningen öppnas i Visual Studio i **Solution Explorer** högerklickar du på **projektet CreateEnrollmentGroup** och väljer sedan **Hantera NuGet-paket.**

1. I **NuGet Package Manager** väljer du **Bläddra,** söker efter och väljer **Microsoft.Azure.Devices.Provisioning.Service** och trycker sedan på **Installera.**

    ![Fönstret för NuGet-pakethanteraren](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Det här steget hämtar, installerar och lägger till en referens till NuGet-paketet [för Klient-SDK för Azure IoT-etableringstjänst](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) och dess beroenden.

1. Lägg till följande `using` -satser efter `using` de andra -satserna överst i `Program.cs` :

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Lägg till följande fält i `Program` klassen och gör ändringarna i listan.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Ersätt `ProvisioningServiceConnectionString` platshållarvärdet med anslutningssträngen för etableringstjänsten som du vill skapa registreringen för.

   * Ersätt `X509RootCertPath` platshållarvärdet med sökvägen till en .pem- eller .cer-fil. Den här filen representerar den offentliga delen av ett mellanliggande X.509-certifikat eller ett X.509-rotcertifikatutfärdarcertifikat som tidigare har laddats upp och verifierats med din etableringstjänst.

   * Du kan också ändra `EnrollmentGroupId` värdet. Strängen får bara innehålla gemener och bindestreck.

   > [!IMPORTANT]
   > I produktionskoden ska du vara medveten om följande säkerhetsöverväganden:
   >
   > * Hårdkodning av anslutningssträngen för etableringstjänstadministratören går emot bästa säkerhetsmetoder. Istället ska anslutningssträngen lagras på ett säkert sätt, som i en säker konfigurationsfil eller i registret.
   > * Glöm inte att överföra den offentliga delen av signeringscertifikatet. Ladda aldrig upp .pfx- (PKCS12) eller .pem-filer som innehåller privata nycklar till etableringstjänsten.

1. Lägg till följande metod i `Program` klassen . Den här koden skapar en post i registreringsgruppen och anropar `CreateOrUpdateEnrollmentGroupAsync` sedan metoden på för att lägga till `ProvisioningServiceClient` registreringsgruppen i etableringstjänsten.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
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

## <a name="run-the-enrollment-group-sample"></a>Köra exemplet för registrering av grupp
  
Kör exemplet i Visual Studio för att skapa registreringsgruppen. Ett kommandotolksfönster visas och börjar visa bekräftelsemeddelanden. När kommandotolken har skapats visas egenskaperna för den nya registreringsgruppen.

Du kan kontrollera att registreringsgruppen har skapats. Gå till Sammanfattning av enhetsetableringstjänsten och **välj Hantera registreringar** och välj **sedan Registreringsgrupper.** Du bör se en ny registreringspost som motsvarar det registrerings-ID som du använde i exemplet.

![Egenskaper för registrering i portalen](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Välj posten för att verifiera certifikatets tumavtryck och andra egenskaper för posten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska C#-tjänstexempel ska du inte rensa resurserna som skapades i den här snabbstarten. Annars kan du använda följande steg för att ta bort alla resurser som skapats av den här snabbstarten.

1. Stäng C#-exempelutdatafönstret på datorn.

1. Gå till device provisioning-tjänsten i Azure Portal, välj **Hantera registreringar** och välj sedan **Registreringsgrupper.** Välj *registrerings-ID för* registreringsposten som du skapade med den här snabbstarten och tryck på **Ta bort.**

1. Från Device Provisioning-tjänsten i Azure Portal väljer du **Certifikat,** väljer det certifikat som  du laddade upp för den här snabbstarten och trycker på Ta bort överst **i Certifikatinformation.**  

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en registreringsgrupp för ett mellanliggande X.509-certifikat eller rotcertifikatutfärdarcertifikat med hjälp Azure IoT Hub Device Provisioning-tjänsten. Om du vill ha mer djupgående information om enhetsetablering kan du fortsätta till självstudien om konfiguration av Device Provisioning-tjänsten i Azure-portalen.

> [!div class="nextstepaction"]
> [Självstudier om Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-cloud.md)