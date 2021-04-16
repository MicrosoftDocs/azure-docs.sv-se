---
title: Självstudie – Lagra data med SQL-modulen med Azure IoT Edge
description: Den här självstudien visar hur du lagrar data lokalt på IoT Edge enhet med en SQL Server modul
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 709c7bf352635ffb1c17f6cb24aee59dcae2e033
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479771"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Självstudie: Lagra data på gränsen med SQL Server-databaser

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Distribuera en SQL Server för att lagra data på en enhet som kör Azure IoT Edge linux-containrar.

Använda Azure IoT Edge och SQL Server för att lagra och fråga efter data på gränsen. Azure IoT Edge har grundläggande lagringsfunktioner för att cachelagra meddelanden om en enhet tas offline och sedan vidarebefordra dem när anslutningen återupprättas. Du kanske behöver mer avancerade funktioner, som t.ex. att kunna fråga efter data lokalt. Dina IoT Edge-enheter kan använda lokala databaser för att utföra mer komplex databehandling utan att behöva ha en anslutning till IoT Hub.

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

Innan du påbörjar den här självstudien bör du ha gått igenom den tidigare självstudien för att konfigurera utvecklingsmiljön för Linux-containerutveckling: [Utveckla IoT Edge moduler för Linux-enheter](tutorial-develop-for-linux.md). När du har slutfört den här självstudien bör du ha följande förutsättningar på plats:

* En [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) på kostnadsfri nivå eller standardnivå i Azure.
* En AMD64-enhet som kör Azure IoT Edge Linux-containrar. Du kan använda snabbstarterna för att konfigurera en [Linux-enhet eller](quickstart-linux.md) [Windows-enhet.](quickstart.md)
  * ARM-enheter, som Raspberry Pis, kan inte SQL Server. Om du vill använda SQL på en ARM-enhet kan du använda [Azure SQL Edge](../azure-sql-edge/overview.md).
* Ett containerregister, till [exempel Azure Container Registry](../container-registry/index.yml).
* [Visual Studio-kod](https://code.visualstudio.com/) som konfigurerats med [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE har konfigurerats](https://docs.docker.com/install/) för att köra Linux-containrar.

Den här självstudien använder Azure Functions-modul för att skicka data till SQL Server. Om du vill IoT Edge en Azure Functions måste du installera följande ytterligare förutsättningar på utvecklingsdatorn:

* [C# för Visual Studio Code-tillägget (drivs av OmniSharp) för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Skapa ett funktionsprojekt

Om du vill skicka data till en databas behöver du en modul som kan strukturera data korrekt och lagra den i en tabell.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt

Följande steg visar hur du skapar en IoT Edge-funktion med Visual Studio Code och Azure IoT-verktygen.

1. Öppna Visual Studio Code.

2. Öppna kommandopaletten i VS Code genom att **välja Visa**  >  **kommandopalett.**

3. Skriv och kör kommandot **Azure IoT Edge: New IoT Edge solution** (Ny IoT Edge-lösning) i kommandopaletten. Ange följande information i kommandopaletten för att skapa din lösning:

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen, till exempel **SqlSolution**, eller acceptera standardnamnet. |
   | Välj modulmall | Välj **Azure Functions - C#**. |
   | Ange ett modulnamn | Ge modulen namnet **sqlFunction**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen har fyllts i från föregående steg. Ersätt **localhost:5000 med** värdet **för inloggningsservern** från ditt Azure-containerregister. Du kan hämta inloggningsservern från översiktssidan för ditt containerregister i Azure Portal. <br><br>Den slutliga strängen ser ut \<registry name\> som .azurecr.io/sqlfunction. |

   VS Code läser in arbetsytan för IoT Edge-lösningen.

### <a name="add-your-registry-credentials"></a>Lägg till autentiseringsuppgifter för registret

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina privata avbildningar till IoT Edge-enheten.

Tillägget IoT Edge försöker hämta dina autentiseringsuppgifter för containerregistret från Azure och fylla i dem i miljöfilen. Kontrollera om dina autentiseringsuppgifter redan ingår. Annars lägger du till dem nu:

1. Öppna .env-filen i VS Code-utforskaren.
2. Uppdatera fälten med det **användarnamn** och **lösenord** som du kopierade från Azure Container-registret.
3. Spara den här filen.

### <a name="select-your-target-architecture"></a>Välj målarkitektur

Du måste välja vilken arkitektur du riktar in dig på med varje lösning, eftersom containern har skapats och körs på olika sätt för varje arkitekturtyp. Standardvärdet är Linux AMD64.

1. Öppna kommandopaletten och **sök efter Azure IoT Edge:** Ange standardmålplattform för Edge-lösning, eller välj genvägsikonen i sidofältet längst ned i fönstret.

2. I kommandopaletten väljer du målarkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så vi behåller standardinställningen **amd64**.

### <a name="update-the-module-with-custom-code"></a>Uppdatera modulen med anpassad kod

1. I VS Code-utforskaren öppnar **du**  >  **modulerna sqlFunction**  >  **sqlFunction.csproj**.

2. Leta upp gruppen med paketreferenser och lägg till en ny för att inkludera SqlClient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

3. Spara filen **sqlFunction.csproj**.

4. Öppna filen **sqlFunction.cs.**

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

6. Ersätt strängen med följande sträng på rad **\<sql connection string\>** 35. Egenskapen **Datakälla** refererar till SQL Server containern, som inte finns ännu. Du skapar den med namnet **SQL** i nästa avsnitt.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Spara filen **sqlFunction.cs**.

## <a name="add-the-sql-server-container"></a>Lägga till SQL Server containern

Ett [distributionsmanifest](module-composition.md) deklarerar vilka moduler IoT Edge-körningen kommer installera på din IoT Edge-enhet. Du angav koden för att skapa en anpassad funktionsmodul i föregående avsnitt, men SQL Server-modulen är redan skapad och tillgänglig i Azure Marketplace. Du behöver bara tala om för IoT Edge-körningen att inkludera den och sedan konfigurera den på din enhet.

1. I Visual Studio Code öppnar du kommandopaletten genom att välja **Visa**  >  **kommandopalett.**

2. I kommandopaletten skriver och kör du kommandot **Azure IoT Edge: Lägg IoT Edge modul**. I kommandopaletten anger du följande information för att lägga till en ny modul:

   | Fält | Värde |
   | ----- | ----- |
   | Välj distributionsmallfil | Kommandopaletten visar **deployment.template.jspå** filen i den aktuella lösningsmappen. Välj filen.  |
   | Välj modulmall | Välj **Modul från Azure Marketplace**. |

3. I marketplace Azure IoT Edge-modulen söker du efter och väljer **SQL Server Modul**.

4. Ändra modulnamnet till **sql**, med gemener. Det här namnet matchar det containernamn som deklareras i anslutningssträngen i filen sqlFunction.cs.

5. Välj **Importera** för att lägga till modulen i din lösning.

6. I lösningsmappen öppnar du **deployment.template.jspå** filen.

7. Leta upp avsnittet **modules** (moduler). Du bör se tre moduler. Modulen *SimulatedTemperatureSensor* ingår som standard i nya lösningar och tillhandahåller testdata som du kan använda med dina andra moduler. Modulen *sqlFunction är* den modul som du ursprungligen skapade och uppdaterade med ny kod. Slutligen importerades *modulen sql* från Azure Marketplace.

   >[!Tip]
   >Modulen SQL Server levereras med ett standardlösenord som angetts i miljövariablerna för distributionsmanifestet. Varje gång du skapar en SQL Server-container i en produktionsmiljö bör du [ändra standardlösenord för systemadministratören](/sql/linux/quickstart-install-connect-docker).

8. Stäng **deployment.template.jspå** filen.

## <a name="build-your-iot-edge-solution"></a>Skapa din IoT Edge-lösning

I föregående avsnitt skapade du en lösning med en modul och lade sedan till en annan till distributionsmanifestet. Modulen SQL Server värddator offentligt av Microsoft, men du måste containerisera koden i Functions-modulen. I det här avsnittet skapar du lösningen, skapar containeravbildningar för modulen sqlFunction och push-pushar avbildningen till containerregistret.

1. I Visual Studio Code öppnar du den integrerade terminalen genom att välja **Visa**  >  **terminal.**  

1. Logga in på ditt containerregister i Visual Studio Code så att du kan push-överföra avbildningarna till registret. Använd samma autentiseringsuppgifter Azure Container Registry autentiseringsuppgifter (ACR) som du lade till i .env-filen. Ange följande kommando i den integrerade terminalen:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Du kan se en säkerhetsvarning som rekommenderar användning av parametern --password-stdin. Även om användning av denna ligger utanför vad som tas upp i denna artikel rekommenderar vi att du följer denna bästa metod. Mer information finns i [kommandoreferensen docker login.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)

1. I VS Code-utforskaren högerklickar du på **filendeployment.template.js** och väljer Build and Push IoT Edge solution (Skapa och **push-IoT Edge).**

   Build- och push-kommandot startar tre åtgärder. Först skapas en ny mapp i lösningen med namnet **config** som innehåller det fullständiga distributionsmanifestet, som är byggt på information i distributionsmallen och andra lösningsfiler. För det andra körs den `docker build` för att skapa containeravbildningen baserat på lämplig dockerfile för målarkitekturen. Sedan körs den för `docker push` att push-skicka avbildningsdatabasen till containerregistret.

   Den här processen kan ta flera minuter första gången, men går snabbare nästa gång du kör kommandona.

   Du kan kontrollera att modulen sqlFunction har push-skickats till containerregistret. I Azure Portal navigerar du till containerregistret. Välj **lagringsplatsen och** sök efter **sqlFunction**. De andra två modulerna, SimulatedTemperatureSensor och sql, skickas inte till containerregistret eftersom deras databaser redan finns i Microsoft-registren.

## <a name="deploy-the-solution-to-a-device"></a>Distribuera lösningen till en enhet

Du kan ange moduler på en enhet via IoT Hub, men du kan också komma åt din IoT Hub och enheter via Visual Studio Code. I det här avsnittet kan du konfigurera åtkomst till din IoT Hub och sedan använda VS Code för att distribuera din lösning till IoT Edge-enheten.

1. I Visual Studio Code-utforskaren under **Azure IoT Hub** expanderar du **Enheter** för att se din lista över IoT-enheter.

2. Högerklicka på den enhet som du vill använda för distributionen och välj **Skapa distribution för enskild enhet.**

3. Välj filen **deployment.amd64.jskonfigurationsmappen** **och** klicka sedan på Välj **Distributionsmanifest för Edge.** Använd inte filen deployment.template.json.

4. Under din enhet expanderar du **Moduler** för att se en lista över distribuerade och körande moduler. Klicka på uppdateringsknappen. Du bör se de nya **modulerna sql** och **sqlFunction** köras tillsammans med **modulen SimulatedTemperatureSensor** och **modulen $edgeAgent** och **$edgeHub**.

    Du kan också kontrollera att alla moduler är igång på enheten. Kör följande kommando på IoT Edge-enheten för att se status för modulerna.

   ```cmd/sh
   iotedge list
   ```

    Det kan ta några minuter för modulerna att starta. Den IoT Edge körningen måste ta emot sitt nya distributionsmanifest, hämta modulavbildningarna från containerkörningen och sedan starta varje ny modul.

## <a name="create-the-sql-database"></a>Skapa SQL-databasen

När du applicerar distributionsmanifestet på din enhet körs tre moduler. Modulen SimulatedTemperatureSensor genererar simulerade miljödata. Modulen sqlFunction hämtar data och formaterar dem för en databas. Det här avsnittet hjälper dig att konfigurera SQL-databasen för lagring av temperaturdata.

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

Om du vill prova en annan lagringsmetod vid gränsen kan du läsa om hur du använder Azure Blob Storage på IoT Edge.

> [!div class="nextstepaction"]
> [Lagra data på gränsen med Azure Blob Storage på IoT Edge](how-to-store-data-blob.md)