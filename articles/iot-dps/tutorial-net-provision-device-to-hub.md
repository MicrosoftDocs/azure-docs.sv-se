---
title: Självstudie – etablera enhet med Azure IoT Hub Device Provisioning Service (.NET)
description: Den här självstudien visar hur du kan etablera din enhet till en enda IoT-hubb med hjälp av Azure-IoT Hub Device Provisioning Service (DPS) med hjälp av .NET.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: f9a14ee6ee3e10b36d64ec11fc23807efe2bfaf2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966572"
---
# <a name="tutorial-enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Självstudie: registrera enheten till en IoT-hubb med Azure IoT Hub Provisioning service-klienten (.NET)

I den förra självstudien lärde du dig att konfigurera en enhet för att ansluta till din enhetsetableringstjänst. I den här självstudien lär du dig att använda tjänsten för att etablera enheten till en enda IoT-hubb, med hjälp av **_individuell registrering_** och **_registreringslistor_**. I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

## <a name="prerequisites"></a>Förutsättningar

Innan du fortsätter måste du konfigurera din enhet och dess *säkerhetsmodul för maskinvara* enligt anvisningarna i självstudien [Konfigurera en enhet för etablering med Azure IoT Hub Device Provisioning-tjänsten](./tutorial-set-up-device.md).

* Visual Studio

> [!NOTE]
> Visual Studio krävs inte. Installationen av [.NET](https://www.microsoft.com/net) räcker och utvecklare kan använda det redigeringsprogram de föredrar på Windows eller Linux.  

Den här självstudien simulerar perioden under eller direkt efter processen för maskinvarutillverkning, när enhetsinformation läggs till i etableringstjänsten. Den här koden körs vanligtvis på en dator eller en enhet som kan köra .NET-kod och bör inte lägga till själva enheterna.


## <a name="enroll-the-device"></a>Registrera enheten

I det här steget ska du lägga till enhetens unika säkerhetsartefakter till enhetsetableringstjänsten. Dessa säkerhetsartefakter är:

- För TPM-baserade enheter:
    - *Bekräftelsenyckeln* som är unik för varje TPM-krets eller simulering. Läs [Understand TPM Endorsement Key](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)) (Förstå TPM-bekräftelsenyckeln) för mer information.
    - *Registrerings-ID:t* som används för att unikt identifiera en enhet i namnrymden/omfattningen. Det kan vara samma som enhetens ID, men det måste inte vara det. ID:t är obligatoriskt för alla enheter. För TPM-baserade enheter kan registrerings-ID:t härledas från själv TPM, till exempel en SHA-256-hash för TPM-bekräftelsenyckeln.

- För X.509-baserade enheter:
    - [X.509-certifikatet som utfärdats till enheten](/windows/win32/seccertenroll/about-x-509-public-key-certificates), antingen som en *.pem*- eller *.cer*-fil. För enskild registrering måste du använda *lövcertifikatet* för ditt X.509-system, och för registreringsgrupper måste du använda *rotcertifikatet* eller ett motsvarande *signeringscertifikat*.
    - *Registrerings-ID:t* som används för att unikt identifiera en enhet i namnrymden/omfattningen. Det kan vara samma som enhetens ID, men det måste inte vara det. ID:t är obligatoriskt för alla enheter. Registrerings-ID härleds från certifikatets eget namn (CN) för X.509-baserade enheter. Mer information om kraven finns under [Enhetskoncept](./concepts-service.md).

Det finns två sätt att registrera enheten till enhetsetableringstjänsten:

- **Enskilda registreringar** Detta representerar en inmatning för en enskild enhet som kan registreras med enhetsetableringstjänsten. Enskilda registreringar kan använda antingen X509-certifikat eller SAS-token (i en verklig eller virtuell TPM) som attesteringsmetoder. Vi rekommenderar att du använder enskilda registreringar för enheter som kräver unika första konfigurationer eller enheter som endast kan använda SAS-token via TPM som attesteringsmetod. Enskilda registreringar kan ha angivet önskat enhets-ID för IoT Hub.

- **Registreringsgrupper** Det här representerar en grupp med enheter som delar en specifik attesteringsmekanism. Vi rekommenderar att du använder en registreringsgrupp för ett stort antal enheter som delar en önskad inledande konfiguration, eller för enheter som ska till samma klient. För Registreringsgrupper gäller bara X.509 och alla delar ett signeringscertifikat i X.509-certifikatkedjan.

### <a name="enroll-the-device-using-individual-enrollments"></a>Registrera enheten med enskilda registreringar

1. I Visual Studio skapar du ett nytt Visual C# Console Application-projekt med hjälp av projektmallen **Konsolapp**. Kalla projektet **DeviceProvisioning**.
    
1. Högerklicka på projektet **DeviceProvisioning** i Solution Explorer och klicka sedan på **Hantera NuGet-paket...**.

1. I fönstret **NuGet-pakethanteraren** väljer du **Bläddra** och letar upp **microsoft.azure.devices.provisioning.service**. Välj posten och klicka på **Installera** för att installera **Microsoft.Azure.Devices.Provisioning.Service**-paketet och godkänn användningsvillkoren. Den här proceduren hämtar, installerar och lägger till en referens för NuGet-paketet för [Azure IoT Device Provisioning Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) och dess beroenden.

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Lägg till följande fält i klassen **Program**. Ersätt platshållarvärdet med anslutningssträngen för enhetsetableringstjänsten från föregående avsnitt.
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Lägg till följande för att implementera registreringen för enheten:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Lägg till sist till följande kod i metoden **Main** för att öppna anslutningen till IoT-hubben och starta registreringen:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.ReadLine();
    ```
        
1. Högerklicka på din lösning i Visual Studio-Solution Explorer och klicka sedan på **Ange start projekt...**. Välj **enstaka start projekt** och välj sedan **DeviceProvisioning** -projektet i list menyn.  

1. Kör .NET-enhetsappen **DeviceProvisiong**. Den bör konfigurera etablering av enheten: 

    ![Körning av individuell registrering](./media/tutorial-net-provision-device-to-hub/individual.png)

När enheten har registrerats bör du se den i portalen på följande sätt:

   ![Lyckad registrering i portalen](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Registrera enheten med registreringsgrupper

> [!NOTE]
> Exemplet för registreringsgruppen kräver ett X.509-certifikat.

1. Öppna projektet **DeviceProvisioning** som du skapade ovan i Visual Studio Solution Explorer. 

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Lägg till följande fält i klassen **Program**. Byt platshållarvärdet till X509-certifikatplatsen.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Lägg till följande till **Progra.cs** för att implementera registreringen för gruppen:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Byt till sist ut följande kod i metoden **Main** för att öppna anslutningen till IoT-hubben och starta gruppregistreringen:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Kör .NET-enhetsappen **DeviceProvisiong**. Den bör konfigurera gruppetablering av enheten: 

    ![Körning av gruppregistrering](./media/tutorial-net-provision-device-to-hub/group.png)

    När enhetsgruppen har registrerats bör du se den i portalen på följande sätt:

   ![Lyckad gruppregistrering i portalen](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Starta enheten

Följande inställningar är nu klara för registrering av enheten:

1. Din enhet eller enhetsgrupp har registrerats till enhetsetableringstjänsten, och 
2. Din enhet är klar med säkerhetskonfigurationen och är åtkomlig via programmet med klient-SDK:n för enhetsetableringstjänsten.

Starta enheten för att låta klientprogrammet starta registreringen med din enhetsetableringstjänst.  


## <a name="verify-the-device-is-registered"></a>Kontrollera att enheten är registrerad

När enheten startar ska följande åtgärder utföras. Mer information finns i avsnittet om [etablering av enhetsklientexempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device). 

1. Enheten skickar en registreringsbegäran till enhetsetableringstjänsten.
2. För TPM-enheten skickar enhetsetableringstjänsten tillbaka en registreringskontroll som enheten svarar på. 
3. Om registringen har lyckats skickar enhetsetableringstjänsten tillbaka IoT-hubbens URI, enhets-ID och den krypterade nyckeln till enheten. 
4. IoT Hub-klientprogrammet på enheten ansluter därefter till hubben. 
5. Om anslutningen till hubben lyckas ska du se enheten i IoT-hubbens **Device Explorer**. 

    ![Lyckad anslutning till hubben i portalen](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Registrera enheten
> * Starta enheten
> * Kontrollera att enheten är registrerad

Gå vidare till nästa självstudiekurs för att lära dig att etablera flera enheter över belastningsutjämnade hubbar. 

> [!div class="nextstepaction"]
> [Etablera enheter till flera belastningsutjämnade IoT-hubbar](./tutorial-provision-multiple-hubs.md)