---
title: Koda en klientapp
titleSuffix: Azure Digital Twins
description: Självstudie för att skriva den minimala koden för en klient app med hjälp av .NET (C#) SDK.
author: cschormann
ms.author: cschorm
ms.date: 05/05/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d063418bb8e78bf306dda8fc558bb0e3005dbd0a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88584904"
---
# <a name="coding-with-the-azure-digital-twins-apis"></a>Koda med Azure Digitals dubbla API: er

Det är vanligt för utvecklare som arbetar med Azure Digitals dubblare att skriva ett klient program för att interagera med sin instans av Azure Digitals dubbla tjänster. Den här själv studie kursen för utvecklare ger en introduktion till programmering mot Azure Digitals-tjänsten med [Azure IoT Digital-klient biblioteket för .net (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Den vägleder dig genom att skriva en C#-konsol klient program steg för steg, som börjar från grunden.

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien används kommando raden för installation och projekt arbete. Därför kan du använda valfri kod redigerare för att gå igenom övningarna.

Vad du behöver för att börja:
* Valfri kod redigerare
* **.Net Core 3,1** på din utvecklings dator. Du kan hämta den här versionen av .NET Core SDK för flera plattformar från [Hämta .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

[!INCLUDE [Azure Digital Twins tutorials: instance prereq](../../includes/digital-twins-tutorial-prereq-instance.md)]

## <a name="set-up-project"></a>Konfigurera projekt

När du är redo att ansluta till din Azure Digital-instansen börjar du konfigurera klientens program projekt. 

Öppna en kommando tolk eller ett annat konsol fönster på datorn och skapa en tom projekt katalog där du vill lagra ditt arbete under den här självstudien. Namnge katalogen oavsett om du vill (till exempel *DigitalTwinsCodeTutorial*).

Navigera till den nya katalogen.

Skapa ett tomt program projekt för .NET-konsolen en gång i projekt katalogen. I kommando fönstret kör du följande kommando för att skapa ett minimalt C#-projekt för-konsolen:

```cmd/sh
dotnet new console
```

Detta skapar flera filer i katalogen, inklusive en som kallas *program.cs* där du skriver merparten av koden.

Lägg sedan till två nödvändiga beroenden för att arbeta med Azure Digitals dubbla:

```cmd/sh
dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
dotnet add package Azure.identity --version 1.1.1
```

Det första beroendet är [Azure IoT Digital-klient biblioteket för .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). Det andra sambandet innehåller verktyg som hjälper dig med autentisering mot Azure.

Se till att kommando fönstret är öppet, eftersom du fortsätter att använda det i självstudien.

## <a name="get-started-with-project-code"></a>Kom igång med projekt kod

I det här avsnittet ska du skriva koden för att ditt nya app-projekt ska fungera med Azure Digitals. De åtgärder som omfattas är:
* Autentisering mot tjänsten
* Laddar upp en modell
* Fånga fel
* Skapa digitala dubbla
* Skapar relationer
* Fråga digitala dubbla

Det finns också ett avsnitt som visar den fullständiga koden i slutet av självstudien. Du kan använda den som en referens för att kontrol lera programmet när du går.

Börja genom att öppna filen *program.cs* i valfri kod redigerare. En minimal kod mall visas som ser ut så här:

```csharp
using System;

namespace DigitalTwinsCodeTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine("Hello World!");
        }
    }
}
```

Lägg först till några `using` rader överst i koden för att hämta nödvändiga beroenden.

```csharp
using Azure.DigitalTwins.Core;
using Azure.Identity;
```

Sedan lägger du till kod i den här filen för att fylla i vissa funktioner. 

### <a name="authenticate-against-the-service"></a>Autentisera mot tjänsten

Det första du behöver göra är att autentisera mot tjänsten Azure Digitals dubbla. Sedan kan du skapa en tjänst klient klass för att få åtkomst till SDK-funktionerna.

Du behöver tre delar av information för att kunna autentisera:
* *Katalog-ID* för din prenumeration
* Det *program-ID (klient)* som skapades när du konfigurerade Azure Digitals-instansen tidigare
* *Värd namnet* för Azure Digitals dubbla instanser

>[!TIP]
> Om du inte känner till ditt *katalog-ID*kan du hämta det genom att köra det här kommandot i [Azure Cloud Shell](https://shell.azure.com):
> 
> ```azurecli-interactive
> az account show --query tenantId
> ```

I *program.cs*klistrar du in följande kod under "Hello, World!" Skriv ut rad i `Main` metoden. Ange värdet för `adtInstanceUrl` till ditt Azure Digital-instansen *hostname*, `clientId` till ditt *program-ID*och `tenantId` till ditt *katalog-ID*.

```csharp
string clientId = "<your-application-ID>";
string tenantId = "<your-directory-ID>";
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
Console.WriteLine($"Service client created – ready to go");
```

Spara filen. 

Observera att det här exemplet använder en interaktiv webbläsares autentiseringsuppgifter:
```csharp
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
```

Den här typen av autentiseringsuppgift kommer att göra att ett webbläsarfönster öppnas där du uppmanas att ange dina autentiseringsuppgifter för Azure. 

>[!NOTE]
> Information om andra typer av autentiseringsuppgifter finns i dokumentationen för [Microsoft Identity Platform Authentication libraries](../active-directory/develop/reference-v2-libraries.md).

Kör koden med det här kommandot i kommando fönstret: 

```cmd/sh
dotnet run
```

Detta kommer att återställa beroendena vid första körningen och sedan köra programmet. 
* Om inget fel uppstår skrivs *service-klienten som skapats – redo att gå*.
* Eftersom det ännu inte finns någon fel hantering i det här projektet visas ett undantags fel som orsakas av koden.

### <a name="upload-a-model"></a>Ladda upp en modell

Azure Digital-dubbla har ingen inbyggd vokabulär i domänen. De typer av element i din miljö som du kan representera i Azure Digitals-enheter definieras av dig med hjälp av **modeller**. [Modeller](concepts-models.md) liknar klasser i objektorienterade programmeringsspråk. de ger användardefinierade mallar för [digitala dubbla](concepts-twins-graph.md) och kan följa och instansiera senare. De är skrivna i ett JSON-liknande språk som kallas **DTDL (Digital Definition Language)**.

Det första steget i att skapa en Azure digital-lösning med dubbla lösningar är att definiera minst en modell i en DTDL-fil.

Skapa en ny *. JSON* -fil med namnet *SampleModel.jspå*i katalogen där du skapade projektet. Klistra in följande fil text: 

```json
{
  "@id": "dtmi:com:contoso:SampleModel;1",
  "@type": "Interface",
  "displayName": "SampleModel",
  "contents": [
    {
      "@type": "Relationship",
      "name": "contains"
    },
    {
      "@type": "Property",
      "name": "data",
      "schema": "string"
    }
  ],
  "@context": "dtmi:dtdl:context;2"
}
```

> [!TIP]
> Om du använder Visual Studio för den här självstudien kanske du vill välja den nyss skapade JSON-filen och ange egenskapen *Kopiera till utdata-katalog* i egenskapsinspektören för att *Kopiera om* det är nyare eller *Kopiera Always*. Detta gör att Visual Studio kan hitta JSON-filen med standard Sök vägen när du kör programmet med **F5** under resten av självstudien.

> [!TIP] 
> Det finns ett språk-oberoende [DTDL-verifierings exempel](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) som du kan använda för att kontrol lera modell dokument för att kontrol lera att DTDL är giltig. Det bygger på DTDL parser-biblioteket, som du kan läsa mer om i [*instruktion: parsa och validera modeller*](how-to-parse-models.md).

Lägg sedan till ytterligare kod i *program.cs* för att ladda upp den modell som du precis har skapat i Azure Digitals-instansen.

Lägg först till några `using` instruktioner överst i filen:

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
```

Förbered sedan att använda de asynkrona metoderna i C#-tjänst-SDK: n genom `Main` att ändra Metodsignaturen till att tillåta asynkron körning. 

```csharp
static async Task Main(string[] args)
```

> [!NOTE]
> `async`Att använda är inte absolut nödvändigt eftersom SDK även tillhandahåller synkrona versioner av alla anrop. Den här själv studie praxis som använder `async` .

Härnäst visas den första bit koden som interagerar med Azure Digitals dubbla-tjänster. Den här koden läser in den DTDL-fil som du skapade från disken och laddar sedan upp den till din Azure Digitals dubbla tjänst instans. 

Klistra in följande kod under den auktoriseringskod som du lade till tidigare.

```csharp
Console.WriteLine();
Console.WriteLine($"Upload a model");
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
// Upload the model to the service
await client.CreateModelsAsync(typeList);
```

Kör programmet med det här kommandot i kommando fönstret: 

```cmd/sh
dotnet run
```
"Överför en modell" skrivs ut i utdata, men det finns inga utdata än att ange om modeller har överförts eller inte.

Om du vill lägga till en Print-instruktion som anger om modeller faktiskt har laddats upp, lägger du till följande kod direkt efter föregående avsnitt:

```csharp
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Innan du kör programmet igen för att testa den nya koden ska du återkalla den senaste gången du körde programmet. du laddade upp din modell redan. Med Azure Digital-dubbla kan du inte ladda upp samma modell två gånger, så förväntar dig att se ett undantag när du kör programmet igen.

Kör nu programmet igen med det här kommandot i kommando fönstret:

```cmd/sh
dotnet run
```

Programmet ska utlösa ett undantag. När du försöker överföra en modell som redan har laddats upp returnerar tjänsten fel meddelandet "felaktig begäran" via REST API. Det innebär att Azure Digitals klient-SDK: n i sin tur genererar ett undantag för varje annan tjänst returkod än slutförd. 

Nästa avsnitt pratar om undantag som detta och hur du hanterar dem i din kod.

### <a name="catch-errors"></a>Catch-fel

Om du vill förhindra att programmet kraschar kan du lägga till undantags kod runt modell överförings koden. Packa upp det befintliga klient anropet `client.CreateModelsAsync` i en try/catch-hanterare, så här:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

Om du kör programmet med `dotnet run` i kommando fönstret nu ser du att du får tillbaka en felkod. Utdata ser ut ungefär så här:

```cmd/sh
Hello World!
Service client created - ready to go

Upload a model
Load model: 409:Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"DocumentAlreadyExists","message":"A document with same identifier already exists.","details":[]}}

Headers:
api-supported-versions: REDACTED
Date: Tue, 05 May 2020 01:57:51 GMT
Content-Length: 115
Content-Type: application/json; charset=utf-8

Type name: : dtmi:com:contoso:SampleModel;1
```

Från och med nu kommer kursen att figursättas alla anrop till tjänst metoder i try/catch-hanterare.

### <a name="create-digital-twins"></a>Skapa digitala dubbla

Nu när du har laddat upp en modell till Azure Digitals, kan du använda den här modell definitionen för att skapa **digitala dubbla**. [Digitala dubbla](concepts-twins-graph.md) är instanser av en modell och representerar entiteterna i din affärs miljö – saker som sensorer i en grupp, rum i en byggnad eller lampor i en bil. Det här avsnittet skapar några digitala dubbla, baserat på den modell som du laddade upp tidigare.

Lägg till en ny `using` instruktion överst eftersom du behöver den inbyggda .net JSON-serialiseraren i `System.Text.Json` :

```csharp
using System.Text.Json;
using Azure.DigitalTwins.Core.Serialization;
```

Lägg sedan till följande kod i slutet av- `Main` metoden för att skapa och initiera tre digitala dubbla, baserade på den här modellen.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

Kör programmet med i kommando fönstret `dotnet run` . Upprepa sedan för att köra programmet igen. 

Observera att det inte uppstår något fel när de dubblarna skapas den andra gången, även om de finns redan efter den första körningen. Till skillnad från skapande av modell är skapandet *av ett anrop* med *upsert* -semantik på rest-nivån. Det innebär att om det redan finns en dubbla, försöker du att skapa den igen och ersätter den bara. Inget fel krävs.

### <a name="create-relationships"></a>Skapa relationer

Sedan kan du skapa **relationer** mellan de dubbla som du har skapat, för att ansluta dem till ett **dubbel diagram**. [Dubbla grafer](concepts-twins-graph.md) används för att representera hela miljön.

Om du vill kunna skapa relationer lägger du till en `using` instruktion för Relations bastypen i SDK: hoppa över detta om det redan har lagts till.
```csharp
using Azure.DigitalTwins.Core.Serialization;
```

Lägg sedan till en ny statisk metod till `Program` -klassen under `Main` metoden:
```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Lägg sedan till följande kod i slutet av- `Main` metoden för att anropa `CreateRelationship` koden:
```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

Kör programmet med i kommando fönstret `dotnet run` .

Observera att Azure Digital multipler inte tillåter att du skapar en relation om det redan finns en med samma ID, så om du kör programmet flera gånger visas undantag vid skapande av relationer. Den här koden fångar undantagen och ignorerar dem. 

### <a name="list-relationships"></a>List relationer

Nästa kod som du lägger till gör att du kan se listan över relationer som du har skapat.

Lägg till följande nya metod till `Program`-klassen:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (string rel in results)
        {
            var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
            Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
        }
    } catch (RequestFailedException rex) {
        Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
    }
}
```

Lägg sedan till följande kod i slutet av- `Main` metoden för att anropa `ListRelationships` koden:

```csharp
//List the relationships
await ListRelationships(client, "sampleTwin-0");
```

Kör programmet med i kommando fönstret `dotnet run` . Du bör se en lista över alla relationer som du har skapat.

### <a name="query-digital-twins"></a>Fråga digitala dubbla

En huvud funktion i Azure Digitals flätas är möjligheten att [fråga](concepts-query-language.md) ditt dubbla diagram enkelt och effektivt att besvara frågor om din miljö.

Det sista avsnittet av kod som ska läggas till i den här självstudien kör en fråga mot Azure Digitals-instansen. Frågan som används i det här exemplet returnerar alla digitala dubbla i-instansen.

Lägg till följande kod i slutet av- `Main` metoden:

```csharp
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    Console.WriteLine("---------------");
}
```

Kör programmet med i kommando fönstret `dotnet run` . Du bör se alla digitala dubbla i den här instansen i utdata.

## <a name="complete-code-example"></a>Fullständigt kod exempel

I den här kursen har du en komplett klient-app som kan utföra grundläggande åtgärder mot digitala Azure-enheter. Den fullständiga koden för programmet i *program.cs* visas som referens nedan:

```csharp
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Models;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string clientId = "<your-application-ID>";
            string tenantId = "<your-directory-ID>";
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>";
            var credentials = new InteractiveBrowserCredential(tenantId, clientId);
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine();
            Console.WriteLine($"Upload a model");
            var typeList = new List<string>();
            string dtdl = File.ReadAllText("SampleModel.json");
            typeList.Add(dtdl);

            // Upload the model to the service
            try {
                await client.CreateModelsAsync(typeList);
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
            }
            // Read a list of models back from the service
            AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
            await foreach (ModelData md in modelDataList)
            {
                Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
            twinData.CustomProperties.Add("data", $"Hello World!");
    
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateDigitalTwinAsync($"{prefix}{i}", JsonSerializer.Serialize(twinData));
                    Console.WriteLine($"Created twin: {prefix}{i}");
                } catch(RequestFailedException rex) {
                    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
                }
            }

            // Connect the twins with relationships
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
            await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");

            //List the relationships
            await ListRelationships(client, "sampleTwin-0");

            // Run a query    
            AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
            await foreach (string twin in result)
            {
                object jsonObj = JsonSerializer.Deserialize<object>(twin);
                string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
                Console.WriteLine(prettyTwin);
                Console.WriteLine("---------------");
            }
        }

        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = "contains"
            };
        
            try
            {
                string relId = $"{srcId}-contains->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<string> results = client.GetRelationshipsAsync(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (string rel in results)
                {
                    var brel = JsonSerializer.Deserialize<BasicRelationship>(rel);
                    Console.WriteLine($" -{brel.Name}->{brel.TargetId}");
                }
            } catch (RequestFailedException rex) {
                Console.WriteLine($"Relationship retrieval error: {rex.Status}:{rex.Message}");   
            }
        }

    }
}
```
## <a name="clean-up-resources"></a>Rensa resurser
 
Den instans som används i den här självstudien kan återanvändas i nästa självstudie, [*Självstudier: utforska grunderna med ett exempel på en klient-app*](tutorial-command-line-app.md). Om du planerar att fortsätta till nästa självstudie kan du behålla Azure Digitals-instansen som du ställer in här.
 
Om du inte längre behöver resurserna som skapas i den här självstudien kan du ta bort dem genom att följa stegen nedan.

Med hjälp av [Azure Cloud Shell](https://shell.azure.com)kan du ta bort alla Azure-resurser i en resurs grupp med kommandot [AZ Group Delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) . Detta tar bort resurs gruppen och Azures digitala dubbla instansen.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

Öppna en Azure Cloud Shell och kör följande kommando för att ta bort resurs gruppen och allt den innehåller.

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ta sedan bort den Azure Active Directory app-registrering som du skapade för din klient app med det här kommandot:

```azurecli
az ad app delete --id <your-application-ID>
```

Slutligen tar du bort projektmappen som du skapade på den lokala datorn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett .NET-konsol klient program från grunden. Du har skrivit kod för den här klient appen för att utföra grundläggande åtgärder på en digital Azure-instans.

Fortsätt till nästa självstudie för att utforska de saker du kan göra med en sådan exempel klient app: 

> [!div class="nextstepaction"]
> [*Självstudie: utforska grunderna med en exempel klient program*](tutorial-command-line-app.md)

Du kan också lägga till koden som du skrev i den här självstudien genom att lära dig mer om hanterings åtgärder i instruktions artiklarna eller börja titta på begrepps dokumentationen för att lära dig mer om element som du har arbetat med i självstudien.
* [*Anvisningar: Hantera anpassade modeller*](how-to-manage-model.md)
* [*Begrepp: anpassade modeller*](concepts-models.md)
