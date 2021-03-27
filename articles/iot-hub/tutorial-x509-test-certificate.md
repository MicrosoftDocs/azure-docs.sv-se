---
title: Självstudie – testa möjligheten för X. 509-certifikat för att autentisera enheter till en Azure-IoT Hub | Microsoft Docs
description: Självstudie – testa dina X. 509-certifikat för att autentisera till Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 91eea344914120a396ba9465ec504a37f5844d4e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630769"
---
# <a name="tutorial-testing-certificate-authentication"></a>Självstudie: testa certifikatautentisering

Du kan använda följande C#-kod exempel för att testa att ditt certifikat kan autentisera din enhet till din IoT Hub. Observera att du måste göra följande innan du kör test koden:

* Skapa en rot certifikat utfärdare eller ett underordnat CA-certifikat.
* Ladda upp certifikat utfärdarens certifikat till din IoT Hub.
* Bevisa att du innehar CA-certifikatet.
* Lägg till en enhet till din IoT Hub.
* Skapa ett enhets certifikat med samma enhets-ID som du är din enhet.

## <a name="code-example"></a>Kod exempel

I följande kod exempel visas hur du skapar ett C#-program för att simulera X. 509-enheten som är registrerad för IoT Hub. Exemplet skickar temperatur-och fuktighets värden från den simulerade enheten till hubben. I den här självstudien skapar vi endast enhets programmet. Den lämnas som en övning för läsarna att skapa det IoT Hub tjänst program som ska skicka svar till de händelser som skickas av den här simulerade enheten.

1. Öppna Visual Studio, Välj **skapa ett nytt projekt** och välj sedan projekt mal len **konsol program (.NET Framework)** . Välj **Nästa**.

1. I **Konfigurera ditt nya projekt** namnger du projektet *SimulateX509Device* och väljer sedan **skapa**.

   ![Skapa X. 509-enhets projekt i Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. I Solution Explorer högerklickar du på projektet **SimulateX509Device** och väljer sedan **Hantera NuGet-paket**.

1. I **NuGet Package Manager** väljer du **Bläddra** och söker efter och väljer **Microsoft. Azure. devices. client**. Välj **installera**.

   ![Lägg till enhets-SDK NuGet-paketet i Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    I det här steget hämtas, installeras och läggs en referens till i Azure IoT Device SDK NuGet-paketet och dess beroenden.

    Mata in och kör följande kod:

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

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
         }
    }
}
```