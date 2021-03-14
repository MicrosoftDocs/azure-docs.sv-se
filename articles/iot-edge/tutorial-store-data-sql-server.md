---
title: Självstudie – lagra data med SQL-modulen med hjälp av Azure IoT Edge
description: Den här självstudien visar hur du lagrar data lokalt på din IoT Edge enhet med en SQL Server modul
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e5aeda35ed4110807f17c11e93b31d0b3998846e
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461121"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Självstudie: Lagra data på gränsen med SQL Server-databaser

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Distribuera en SQL Server-modul för att lagra data på en Linux-enhet som kör Azure IoT Edge.

Använda Azure IoT Edge och SQL Server för att lagra och fråga efter data på gränsen. Azure IoT Edge har grundläggande lagringsfunktioner för att cachelagra meddelanden om en enhet tas offline och sedan vidarebefordra dem när anslutningen återupprättas. Du kanske behöver mer avancerade funktioner, som t.ex. att kunna fråga efter data lokalt. Dina IoT Edge enheter kan använda lokala databaser för att utföra mer komplexa data behandling utan att behöva ha en anslutning till IoT Hub.

Den här artikeln innehåller instruktioner för hur man distribuerar en SQL Server-databas till en IoT Edge-enhet. Azure Functions körs på IoT Edge-enheten och strukturerar inkommande data och skickar dem sedan till databasen. Stegen i den här artikeln kan också tillämpas på andra databaser som fungerar i containrar, t.ex. MySQL eller PostgreSQL.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Använd Visual Studio Code för att skapa en Azure-funktion
> * Distribuera en SQL-databas till din IoT Edge-enhet
> * Använd Visual Studio Code för att bygga moduler och distribuera dem till din IoT Edge-enhet
> * Visa genererade data

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här självstudien bör du ha gått igenom den föregående kursen för att konfigurera din utvecklings miljö för att utveckla Linux-behållare: [utveckla IoT Edge moduler för Linux-enheter](tutorial-develop-for-linux.md). När du har slutfört den här självstudien bör du ha följande krav på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En AMD64-enhet som kör Azure IoT Edge. Du kan använda snabb starterna för att konfigurera en [Linux-enhet](quickstart-linux.md) eller [Windows-enhet](quickstart.md).
  * ARM-enheter, t. ex. Raspberry Pis, kan inte köra SQL Server. Om du vill använda SQL på en ARM-enhet kan du registrera dig för att prova [Azure SQL Edge](https://azure.microsoft.com/services/sql-edge/) i för hands versionen.
* Ett behållar register som [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio-kod](https://code.visualstudio.com/) som kon figurer ATS med [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) konfigurerat för att köra Linux-behållare.

I den här självstudien används en Azure Functions-modul för att skicka data till SQL Server. Om du vill utveckla en IoT Edge-modul med Azure Functions installerar du följande ytterligare krav på utvecklings datorn:

* [C# för Visual Studio Code-tillägg (Powered by OmniSharp) för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt

Om du vill skicka data till en databas behöver du en modul som kan strukturera data korrekt och lagra den i en tabell.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Följande steg visar hur du skapar en IoT Edge-funktion med Visual Studio Code och Azure IoT-verktygen.

1. Öppna Visual Studio Code.

2. Öppna kommando paletten vs Code genom att välja **Visa**  >  **kommando palett**.

3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) i kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning:

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen, till exempel **SqlSolution**, eller acceptera standardnamnet. |
   | Välj modulmall | Välj **Azure Functions - C#**. |
   | Ange ett modulnamn | Ge modulen namnet **sqlFunction**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost: 5000** med **inloggnings serverns** värde från Azure Container Registry. Du kan hämta inloggnings servern från sidan Översikt i behållar registret i Azure Portal. <br><br>Den sista strängen ser ut som \<registry name\> . azurecr.io/sqlfunction. |

   VS Code läser in arbetsytan för IoT Edge-lösningen.

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

IoT Edge-tillägget försöker hämta dina autentiseringsuppgifter för behållar registret från Azure och fylla dem i miljö filen. Kontrol lera om dina autentiseringsuppgifter redan ingår. Om inte, lägger du till dem nu:

1. Öppna .env-filen i VS Code-utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara den här filen.

### <a name="select-your-target-architecture"></a>Välj din mål arkitektur

Du måste välja vilken arkitektur du vill använda för varje lösning, eftersom behållaren har skapats och körs på olika sätt för varje arkitektur typ. Standardvärdet är Linux AMD64.

1. Öppna paletten kommando och Sök efter **Azure IoT Edge: Ange standard plattform för Edge-lösning** eller Välj gen vägs ikonen i sido fältet längst ned i fönstret.

2. I paletten kommando väljer du mål arkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så behåller standard- **amd64**.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. I vs Code-Utforskaren öppnar du **modules**  >  **sqlFunction**  >  **sqlFunction. CSPROJ**.

2. Hitta gruppen med paket referenser och Lägg till en ny för att ta med SqlClient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

3. Spara filen **sqlFunction.csproj**.

4. Öppna filen **sqlFunction.cs** .

5. Ersätt hela innehållet i filen med följande kod:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");";
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}
       }
   }
   ```

6. Ersätt strängen **\<sql connection string\>** med följande sträng i rad 35. **Data källans** egenskap refererar till SQL Server container, som ännu inte finns. Du kommer att skapa den med namnet **SQL** i nästa avsnitt.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Spara filen **sqlFunction.cs**.

## <a name="add-the-sql-server-container"></a>Lägg till SQL Server container

Ett [distributionsmanifest](module-composition.md) deklarerar vilka moduler IoT Edge-körningen kommer installera på din IoT Edge-enhet. Du angav koden för att göra en anpassad funktions modul i föregående avsnitt, men SQL Server modulen är redan inbyggd och tillgänglig på Azure Marketplace. Du behöver bara tala om för IoT Edge-körningen att inkludera den och sedan konfigurera den på din enhet.

1. Öppna paletten kommando i Visual Studio Code genom att välja **Visa**  >  **kommando palett**.

2. I paletten kommando skriver du och kör kommandot **Azure IoT Edge: Lägg till IoT Edge-modul**. I paletten kommando anger du följande information för att lägga till en ny modul:

   | Fält | Värde |
   | ----- | ----- |
   | Välj distributionsmallfil | Paletten Command visar **deployment.template.jspå** filen i din aktuella Solution-mapp. Välj den filen.  |
   | Välj modulmall | Välj **modul från Azure Marketplace**. |

3. I Azure IoT Edge module Marketplace söker du efter och väljer **SQL Server modul**.

4. Ändra modulnamnet till **SQL**, alla gemener. Det här namnet matchar behållar namnet som deklareras i anslutnings strängen i sqlFunction.cs-filen.

5. Välj **Importera** för att lägga till modulen i din lösning.

6. Öppna den **deployment.template.js** filen i Solution-mappen.

7. Leta upp avsnittet **modules** (moduler). Du bör se tre moduler. Modulen *SimulatedTemperatureSensor* ingår som standard i nya lösningar och ger test data som ska användas med dina andra moduler. Modulen *sqlFunction* är den modul som du ursprungligen skapade och uppdaterade med ny kod. Slutligen har modulen *SQL* importer ATS från Azure Marketplace.

   >[!Tip]
   >Modulen SQL Server innehåller ett standard lösen ord som anges i miljövariablerna i distributions manifestet. Varje gång du skapar en SQL Server-container i en produktionsmiljö bör du [ändra standardlösenord för systemadministratören](/sql/linux/quickstart-install-connect-docker).

8. Stäng **deployment.template.jspå** filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en lösning med en modul och lade sedan till en annan till distributionsmanifestet. SQL Server modulen är offentligt av Microsoft, men du måste Använd koden i functions-modulen. I det här avsnittet skapar du lösningen, skapar behållar avbildningar för sqlFunction-modulen och push-överför avbildningen till behållar registret.

1. Öppna den integrerade terminalen i Visual Studio Code genom att välja **Visa**  >  **Terminal**.  

1. Logga in på ditt containerregister i Visual Studio Code så att du kan push-överföra avbildningarna till registret. Använd samma Azure Container Registry-autentiseringsuppgifter (ACR) som du har lagt till i. miljö-filen. Ange följande kommando i den integrerade terminalen:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Du kan se en säkerhets varning som rekommenderar att parametern--Password-STDIN används. Även om användning av denna ligger utanför vad som tas upp i denna artikel rekommenderar vi att du följer denna bästa metod. Mer information finns i kommando referensen [Docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

1. I VS Code-Utforskaren högerklickar du på **deployment.template.jspå** filen och väljer **Build och push IoT Edge-lösning**.

   Kommandot build och push startar tre åtgärder. Först skapar den en ny mapp i lösningen som heter **config** som innehåller det fullständiga distributions manifestet, som bygger på information i distributions mal len och andra lösningsfiler. Sedan körs den `docker build` för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. Sedan körs den `docker push` för att skicka avbildnings lagrings platsen till behållar registret.

   Den här processen kan ta flera minuter första gången, men går snabbare nästa gång du kör kommandona.

   Du kan kontrol lera att modulen sqlFunction har skickats till behållar registret. I Azure Portal navigerar du till behållar registret. Välj **databaser** och Sök efter **sqlFunction**. De andra två modulerna, SimulatedTemperatureSensor och SQL, kommer inte att flyttas till behållar registret eftersom deras databaser redan finns i Microsofts register.

## <a name="deploy-the-solution-to-a-device"></a>Distribuera lösningen till en enhet

Du kan ange moduler på en enhet via IoT Hub, men du kan också komma åt din IoT Hub och enheter via Visual Studio Code. I det här avsnittet kan du konfigurera åtkomst till din IoT Hub och sedan använda VS Code för att distribuera din lösning till IoT Edge-enheten.

1. I Visual Studio Code Explorer, under avsnittet **Azure IoT Hub** , expanderar du **enheter** för att se listan med IoT-enheter.

2. Högerklicka på den enhet som du vill använda som mål för distributionen och välj **skapa distribution för en enskild enhet**.

3. Välj **deployment.amd64.js** filen i mappen **config** och klicka sedan på **Välj gräns distributions manifest**. Använd inte filen deployment.template.json.

4. Under din enhet expanderar du **moduler** för att se en lista över distribuerade och aktiva moduler. Klicka på uppdateringsknappen. Du bör se de nya **SQL** -och **sqlFunction** -modulerna som körs tillsammans med **SimulatedTemperatureSensor** -modulen och **$edgeAgent** och **$edgeHub**.

    Du kan också kontrollera att alla moduler är igång på enheten. Kör följande kommando på IoT Edge-enheten för att se status för modulerna.

   ```cmd/sh
   iotedge list
   ```

    Det kan ta några minuter innan modulerna startar. Den IoT Edge körnings miljön måste ta emot sitt nya distributions manifest, Hämta modulens avbildningar från container körningen och sedan starta varje ny modul.

## <a name="create-the-sql-database"></a>Skapa SQL-databasen

När du applicerar distributionsmanifestet på din enhet körs tre moduler. SimulatedTemperatureSensor-modulen genererar simulerade miljö data. Modulen sqlFunction hämtar data och formaterar dem för en databas. Det här avsnittet hjälper dig att konfigurera SQL-databasen för lagring av temperaturdata.

Kör följande kommandon på din IoT Edge-enhet. De kommandona ansluter till den **sql**-modul som körs på enheten och skapar en databas och en tabell som ska innehålla temperaturdata som skickas till den.

1. I ett kommandoradsverktyg på IoT Edge-enheten ansluter du till din databas.

      ```bash
      sudo docker exec -it sql bash
      ```

2. Öppna SQL-kommandoverktyget.

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Skapa databasen:

      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Definiera tabellen.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

Du kan anpassa din SQL Server Docker-fil så att SQL Server automatiskt konfigureras för distribuering till flera IoT Edge-enheter. Mer information finns i [demoprojektet för Microsoft SQL Server-containern](https://github.com/twright-msft/mssql-node-docker-demo-app).

## <a name="view-the-local-data"></a>Visa lokala data

När din tabell har skapats börjar modulen sqlFunction lagra data i en lokal SQL Server 2017-databas på IoT Edge-enheten.

Kör följande kommando från SQL-kommandoverktyget för att visa formaterade tabelldata:

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Visa innehållet i en lokal databas](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du har skapat i den här artikeln för att därigenom undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

I den här självstudien skapade du en Azure Functions-modul som innehåller kod för att filtrera rådata som genereras av din IoT Edge-enhet. När du är redo att skapa egna moduler kan du läsa mer om hur du [utvecklar Azure Functions med Azure IoT Edge för Visual Studio Code](./how-to-vs-code-develop-module.md).

## <a name="next-steps"></a>Nästa steg

Om du vill prova en annan lagrings metod på gränsen läser du om hur du använder Azure Blob Storage på IoT Edge.

> [!div class="nextstepaction"]
> [Lagra data på gränsen med Azure Blob Storage på IoT Edge](how-to-store-data-blob.md)