---
title: Självstudie – Testa möjligheten för X.509-certifikat att autentisera enheter till en Azure IoT Hub | Microsoft Docs
description: Självstudie – Testa dina X.509-certifikat för att autentisera till Azure IoT Hub
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
ms.openlocfilehash: 7d1900782fce6b84ed79014e985393f3626d171b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379442"
---
# <a name="tutorial-testing-certificate-authentication"></a>Självstudie: Testa certifikatautentisering

Du kan använda följande C#-kodexempel för att testa att certifikatet kan autentisera enheten till din IoT Hub. Observera att du måste göra följande innan du kör testkoden:

* Skapa en rotcertifikatutfärdare eller ett underordnat CA-certifikat.
* Ladda upp ca-certifikatet till IoT Hub.
* Bevisa att du har CA-certifikatet.
* Lägg till en enhet i IoT Hub.
* Skapa ett enhetscertifikat med samma enhets-ID som din enhet.

## <a name="code-example"></a>Kodexempel

I följande kodexempel visas hur du skapar ett C#-program för att simulera X.509-enheten som är registrerad för din IoT Hub. Exemplet skickar temperatur- och luftfuktighetsvärden från den simulerade enheten till hubben. I den här självstudien skapar vi bara enhetsprogrammet. Det är kvar som en övning för läsarna att skapa IoT Hub-tjänstprogrammet som skickar svar på de händelser som skickas av den här simulerade enheten.

1. Öppna Visual Studio, välj **Skapa ett nytt** projekt och välj sedan **projektmallen Konsolapp (.NET Framework).** Välj **Nästa**.

1. I **Konfigurera det nya projektet** ger du projektet namnet *SimulateX509Device* och väljer sedan **Skapa**.

   ![Skapa X.509-enhetsprojekt i Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. I Solution Explorer högerklickar du på **projektet SimulateX509Device** och väljer sedan **Hantera NuGet-paket.**

1. I **NuGet Package Manager väljer** du **Bläddra** och sök efter och väljer **Microsoft.Azure.Devices.Client.** Välj **installera**.

   ![Lägga till NuGet-paketet för enhets-SDK i Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Det här steget hämtar, installerar och lägger till en referens till NuGet-paketet sdk för Azure IoT-enheter och dess beroenden.

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