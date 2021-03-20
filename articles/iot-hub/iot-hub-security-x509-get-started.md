---
title: Självstudie för X. 509-säkerhet i Azure IoT Hub | Microsoft Docs
description: Kom igång med X. 509-baserad säkerhet i din Azure IoT Hub i en simulerad miljö.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: a3e328418a0f111cd0b985310ea6dc497999772d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92909802"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Konfigurera säkerhet för X.509 i din Azure IoT Hub

Den här självstudien visar de steg du behöver för att skydda din Azure IoT Hub med hjälp av *509 för X.*. För illustrationen använder vi verktyget OpenSSL med öppen källkod för att skapa certifikat lokalt på din Windows-dator. Vi rekommenderar att du bara använder den här självstudien i test syfte. För en produktions miljö bör du köpa certifikaten från en *rot certifikat utfärdare (ca)*. I produktion kontrollerar du också att du har en strategi för att hantera certifikat förnyelse när ett enhets certifikat eller CA-certifikat upphör att gälla.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien krävs att du har följande resurser klara:

* Du har skapat en IoT-hubb med din Azure-prenumeration. Detaljerade anvisningar finns i [skapa en IoT-hubb via portalen](iot-hub-create-through-portal.md) .

* Du har installerat [Visual studio 2017 eller Visual studio 2019](https://www.visualstudio.com/vs/) .

## <a name="get-x509-ca-certificates"></a>Hämta X. 509 CA-certifikat

Den 509 säkerheten IoT Hub i X. kräver att du börjar med en [x. 509-certifikat kedja](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), som innehåller rot certifikatet samt alla mellanliggande certifikat fram till löv certifikatet.

Du kan välja något av följande sätt för att få dina certifikat:

* Köp X. 509-certifikat från en *rot certifikat utfärdare (ca)*. Den här metoden rekommenderas för produktions miljöer.

* Skapa dina egna X. 509-certifikat med hjälp av ett verktyg från tredje part, till exempel [openssl](https://www.openssl.org/). Den här tekniken är bra i test-och utvecklings syfte. Se [Hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) om du vill ha information om hur du genererar test CA-certifikat med PowerShell eller bash. Resten av den här självstudien använder test CA-certifikat som genereras genom att följa anvisningarna i [Hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

* Generera ett [X. 509-mellanliggande CA-certifikat](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) signerat av ett befintligt rot certifikat för certifikat utfärdare och ladda upp det till hubben. När det mellanliggande certifikatet har laddats upp och verifierats enligt anvisningarna nedan, kan det användas i stället för ett rot certifikat för certifikat utfärdare som nämns nedan. Verktyg som OpenSSL ([openssl REQ](https://www.openssl.org/docs/man1.1.0/man1/req.html) och [openssl ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) kan användas för att skapa och signera ett mellanliggande CA-certifikat.

> [!NOTE]
> Ladda inte upp roten från tredje part om den inte är unik för dig eftersom det skulle göra det möjligt för andra användare av den tredje parten att ansluta sina enheter till din IoT Hub.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Registrera X. 509 CA-certifikat i IoT Hub

De här stegen visar hur du lägger till en ny certifikat utfärdare till din IoT-hubb via portalen. När du använder X. 509-certifikatets CA-autentisering, se till att du registrerar ditt nya certifikat innan det befintliga upphör att gälla som en del av din strategi för certifikat förnyelse.

> [!NOTE]
> Det maximala antalet X. 509 CA-certifikat som kan registreras till en IoT-hubb är 25. Mer information finns i [Azure IoT Hub kvoter och begränsning](iot-hub-devguide-quotas-throttling.md).

1. I Azure Portal navigerar du till din IoT-hubb och väljer **Inställningar**  >  **certifikat** för hubben.

1. Välj **Lägg** till för att lägga till ett nytt certifikat.

1. I **certifikat namn** anger du ett eget visnings namn och väljer den certifikat fil som du skapade i föregående avsnitt på datorn.

1. När du får ett meddelande om att certifikatet har laddats upp väljer du **Spara**.

    ![Överför certifikat](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Ditt certifikat visas i listan certifikat med statusen **overifierad**.

1. Välj det certifikat som du just har lagt till för att visa **certifikat information** och välj sedan **generera verifierings kod**.

   ![Verifiera certifikat](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Kopiera **verifierings koden** till Urklipp. Du använder den för att verifiera certifikat ägarskapet.

1. Följ steg 3 i [Hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Den här processen signerar verifierings koden med den privata nyckeln som associeras med ditt X. 509-CA-certifikat, vilket genererar en signatur. Det finns verktyg som är tillgängliga för att utföra den här signerings processen, till exempel OpenSSL. Den här processen kallas för [innehavets bevis](https://tools.ietf.org/html/rfc5280#section-3.1).

1. I **certifikat information**, under **verifiering Certificate. pem eller. cer**, letar du upp och öppnar filen signatur. Välj sedan **Verifiera**.

   Statusen för ditt certifikat ändras till **verifierad**. Välj **Uppdatera** om certifikatet inte uppdateras automatiskt.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Skapa en X. 509-enhet för din IoT Hub

1. I Azure Portal navigerar du till din IoT-hubb och väljer sedan **Explorer**  >  **IoT-enheter**.

1. Välj **nytt** om du vill lägga till en ny enhet.

1. I **enhets-ID** anger du ett eget visnings namn. För **Autentiseringstyp** väljer du **X. 509 ca signerad** och väljer sedan **Spara**.

   ![Skapa X. 509-enhet i portalen](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Autentisera din X. 509-enhet med X. 509-certifikaten

För att autentisera din X. 509-enhet måste du först signera enheten med CA-certifikatet. Signering av löv enheter görs vanligt vis vid tillverknings anläggningen, där tillverknings verktyg har Aktiver ATS i enlighet med detta. När enheten flyttas från en tillverkare till en annan, samlas varje tillverkares signerings åtgärd som ett mellanliggande certifikat i kedjan. Resultatet är en certifikat kedja från CA-certifikatet till enhetens löv certifikat. Steg 4 i [Hantera test certifikat utfärdares certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) genererar ett enhets certifikat.

Sedan visar vi dig hur du skapar ett C#-program för att simulera X. 509-enheten som är registrerad för IoT Hub. Vi kommer att skicka temperatur-och fuktighets värden från den simulerade enheten till hubben. I den här självstudien skapar vi endast enhets programmet. Den lämnas som en övning för läsarna att skapa IoT Hub tjänst programmet som ska skicka svar till de händelser som skickas av den här simulerade enheten. C#-programmet förutsätter att du har följt stegen i [Hantera test CA-certifikat för exempel och självstudier](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. Öppna Visual Studio, Välj **skapa ett nytt projekt** och välj sedan projekt mal len **konsol program (.NET Framework)** . Välj **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du projektet *SimulateX509Device* och väljer sedan **skapa**.

   ![Skapa X. 509-enhets projekt i Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. I Solution Explorer högerklickar du på projektet **SimulateX509Device** och väljer sedan **Hantera NuGet-paket**.

1. I **NuGet Package Manager** väljer du **Bläddra** och söker efter och väljer **Microsoft. Azure. devices. client**. Välj **installera**.

   ![Lägg till enhets-SDK NuGet-paketet i Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    I det här steget hämtas, installeras och läggs en referens till i Azure IoT Device SDK NuGet-paketet och dess beroenden.

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Lägg till följande fält i **program** klassen:

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Använd det egna enhets namnet som du använde i föregående avsnitt i stället för _<your_device_id>_.

1. Lägg till följande funktion för att skapa slumpmässiga siffror för temperatur och fuktighet och skicka dessa värden till hubben:

    ```csharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. Slutligen lägger du till följande rader med kod i **huvud** funktionen och ersätter plats hållarna _enhets-ID_, _ditt-IoT-Hub-Name_ och _absolut-Path-till-Your-Device-PFX-fil_ som krävs i konfigurationen.

    ```csharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

   Den här koden ansluter till din IoT-hubb genom att skapa anslutnings strängen för din X. 509-enhet. När anslutningen är klar skickar den temperatur-och fuktighets händelser till hubben och väntar på sitt svar.

1. Kör appen. Eftersom det här programmet har åtkomst till en *. pfx* -fil kan du behöva köra den här appen som administratör.

   1. Bygg Visual Studio-lösningen.

   1. Öppna ett nytt kommando tolks fönster med hjälp av **Kör som administratör**.  

   1. Navigera till den mapp som innehåller din lösning och navigera sedan till sökvägen för *bin/debug* i mappen Solution.

   1. Kör programmet **SimulateX509Device.exe** från kommando tolken.

   Du bör se att enheten ansluter till hubben och skickar händelser.

   ![Kör enhets program](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skyddar din IoT-lösning finns i:

* [Metod tips för IoT-säkerhet](../iot-fundamentals/iot-security-best-practices.md)

* [IoT-säkerhetsarkitektur](../iot-fundamentals/iot-security-architecture.md)

* [Skydda distributionen av IoT](../iot-fundamentals/iot-security-deployment.md)

För att ytterligare utforska funktionerna i IoT Hub, se:

* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/quickstart-linux.md)