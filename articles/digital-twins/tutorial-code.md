---
title: 'Självstudie: koda en klient app'
titleSuffix: Azure Digital Twins
description: Självstudie för att skriva den minimala koden för en klient app med hjälp av .NET (C#) SDK.
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: 96533c1742e8ad5fde9796ea2073f6e402ac8dcf
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2021
ms.locfileid: "97862379"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Självstudie: koda med Azure Digitals dubbla API: er

Det är vanligt för utvecklare som arbetar med Azure Digitals dubblare att skriva ett klient program för att interagera med sin instans av Azure Digitals dubbla tjänster. Den här själv studie kursen om utvecklare är en introduktion till programmering mot Azure Digitals-tjänsten med [Azure Digitals-SDK för .net (C#)](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). Den vägleder dig genom att skriva en C#-konsol klient program steg för steg, som börjar från grunden.

> [!div class="checklist"]
> * Konfigurera projekt
> * Kom igång med projekt kod   
> * Fullständigt kod exempel
> * Rensa resurser
> * Nästa steg

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien används kommando raden för installation och projekt arbete. Därför kan du använda valfri kod redigerare för att gå igenom övningarna.

Vad du behöver för att börja:
* Valfri kod redigerare
* **.Net Core 3,1** på din utvecklings dator. Du kan hämta den här versionen av .NET Core SDK för flera plattformar från [Hämta .net Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

### <a name="prepare-an-azure-digital-twins-instance"></a>Förbered en digital Azure-instans

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Konfigurera projekt

När du är redo att ansluta till din Azure Digital-instansen börjar du konfigurera klientens program projekt. 

Öppna en kommando tolk eller ett annat konsol fönster på datorn och skapa en tom projekt katalog där du vill lagra ditt arbete under den här självstudien. Namnge katalogen oavsett om du vill (till exempel *DigitalTwinsCodeTutorial*).

Navigera till den nya katalogen.

**Skapa ett tomt program projekt för .net-konsolen** en gång i projekt katalogen. I kommando fönstret kan du köra följande kommando för att skapa ett minimalt C#-projekt för-konsolen:

```cmd/sh
dotnet new console
```

Detta skapar flera filer i katalogen, inklusive en som kallas *program.cs* där du skriver merparten av koden.

Se till att kommando fönstret är öppet, eftersom du fortsätter att använda det i självstudien.

Lägg sedan **till två beroenden i projektet** som behövs för att arbeta med Azure Digital-dubbla. Du kan använda länkarna nedan för att navigera till paketen på NuGet, där du kan hitta konsol kommandona (inklusive för .NET CLI) för att lägga till dem i projektet.
* [**Azure. DigitalTwins. Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Det här är paketet för [Azure Digitals dubbla SDK för .net](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). Lägg till den senaste versionen.
* [**Azure. Identity**](https://www.nuget.org/packages/Azure.Identity). Det här biblioteket innehåller verktyg som hjälper dig med autentisering mot Azure. Lägg till version 1.2.2.

>[!NOTE]
> Det finns för närvarande ett [känt problem](troubleshoot-known-issues.md#issue-with-default-azure-credential-authentication-on-azureidentity-130) som påverkar möjligheten att använda Azure. Identity-versionen 1.3.0 med den här självstudien. Använd version 1.2.2 när problemet kvarstår.

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

För att kunna autentisera behöver du *värd namnet* för din Azure Digital-instansen.

I *program.cs* klistrar du in följande kod under "Hello, World!" Skriv ut rad i `Main` metoden. Ange värdet för `adtInstanceUrl` till ditt Azure Digital-instansen *värdnamn*.

```csharp
string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>"; 
var credential = new DefaultAzureCredential();
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
Console.WriteLine($"Service client created – ready to go");
```

Spara filen. 

Kör koden med det här kommandot i kommando fönstret: 

```cmd/sh
dotnet run
```

Detta kommer att återställa beroendena vid första körningen och sedan köra programmet. 
* Om inget fel uppstår skrivs *service-klienten som skapats – redo att gå*.
* Eftersom det ännu inte finns någon fel hantering i det här projektet visas ett undantags fel som uppstår i koden.

### <a name="upload-a-model"></a>Ladda upp en modell

Azure Digital-dubbla har ingen inbyggd vokabulär i domänen. De typer av element i din miljö som du kan representera i Azure Digitals-enheter definieras av dig med hjälp av **modeller**. [Modeller](concepts-models.md) liknar klasser i objektorienterade programmeringsspråk. de ger användardefinierade mallar för [digitala dubbla](concepts-twins-graph.md) och kan följa och instansiera senare. De är skrivna i ett JSON-liknande språk som kallas **DTDL (Digital Definition Language)**.

Det första steget i att skapa en Azure digital-lösning med dubbla lösningar är att definiera minst en modell i en DTDL-fil.

Skapa en ny *. JSON* -fil med namnet *SampleModel.jspå* i katalogen där du skapade projektet. Klistra in följande fil text: 

```json
{
  "@id": "dtmi:example:SampleModel;1",
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
> Det finns ett språk-oberoende [DTDL-verifierings exempel](/samples/azure-samples/dtdl-validator/dtdl-validator) som du kan använda för att kontrol lera modell dokument för att kontrol lera att DTDL är giltig. Det bygger på DTDL parser-biblioteket, som du kan läsa mer om i [*instruktion: parsa och validera modeller*](how-to-parse-models.md).

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
"Överför en modell" skrivs ut i utdata, vilket indikerar att den här koden har uppnåtts, men det finns inga utdata än att ange om uppladdningen lyckades.

Lägg till en Print-instruktion som visar alla modeller som har laddats upp till instansen genom att lägga till följande kod direkt efter föregående avsnitt:

```csharp
// Read a list of models back from the service
Console.WriteLine("Models uploaded to the instance:");
AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
await foreach (DigitalTwinsModelData md in modelDataList)
{
    Console.WriteLine($"{md.Id}");
}
```

**Innan du kör programmet igen för att testa den nya koden** ska du återkalla den senaste gången du körde programmet. du laddade upp din modell redan. Med Azure Digital-dubbla kan du inte överföra samma modell två gånger, så om du försöker överföra samma modell igen, ska programmet utlösa ett undantag.

Med detta i åtanke kan du köra programmet igen med det här kommandot i kommando fönstret:

```cmd/sh
dotnet run
```

Programmet ska utlösa ett undantag. När du försöker överföra en modell som redan har laddats upp returnerar tjänsten fel meddelandet "felaktig begäran" via REST API. Det innebär att Azure Digitals klient-SDK: n i sin tur genererar ett undantag för varje annan tjänst returkod än slutförd. 

Nästa avsnitt pratar om undantag som detta och hur du hanterar dem i din kod.

### <a name="catch-errors"></a>Catch-fel

Om du vill förhindra att programmet kraschar kan du lägga till undantags kod runt modell överförings koden. Packa upp det befintliga klient anropet `await client.CreateModelsAsync(typeList)` i en try/catch-hanterare, så här:

```csharp
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
```

Om du nu kör programmet med `dotnet run` i ditt kommando fönster, kommer du att se att du får tillbaka felkoden. Utdata från modellen för skapande av modeller visar det här felet:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Programutdata, som visar ett meddelande som säger &quot;409: tjänstbegäran misslyckades. Status: 409 (konflikt)., följt av en fel utskrift som indikerar att dtmi: exempel: SampleModel; 1 redan finns":::

Från och med nu kommer kursen att figursättas alla anrop till tjänst metoder i try/catch-hanterare.

### <a name="create-digital-twins"></a>Skapa digitala dubbla

Nu när du har laddat upp en modell till Azure Digitals, kan du använda den här modell definitionen för att skapa **digitala dubbla**. [Digitala dubbla](concepts-twins-graph.md) är instanser av en modell och representerar entiteterna i din affärs miljö – saker som sensorer i en grupp, rum i en byggnad eller lampor i en bil. Det här avsnittet skapar några digitala dubbla, baserat på den modell som du laddade upp tidigare.

Lägg till följande kod i slutet av- `Main` metoden för att skapa och initiera tre digitala dubbla, baserade på den här modellen.

```csharp
// Initialize twin data
BasicDigitalTwin twinData = new BasicDigitalTwin();
twinData.Metadata.ModelId = "dtmi:example:SampleModel;1";
twinData.Contents.Add("data", $"Hello World!");

string prefix="sampleTwin-";
for(int i=0; i<3; i++) {
    try {
        twinData.Id = $"{prefix}{i}";
        await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twinData.Id, twinData);
        Console.WriteLine($"Created twin: {prefix}{i}");
    } catch(RequestFailedException rex) {
        Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
    }
}
```

Kör programmet med i kommando fönstret `dotnet run` . I utdata söker du efter de utskrifts meddelanden som *sampleTwin-0*, *sampleTwin-1* och *sampleTwin-2* har skapats. 

Kör sedan programmet igen. 

Observera att det inte uppstår något fel när de dubblarna skapas den andra gången, även om de finns redan efter den första körningen. Till skillnad från skapande av modell är skapandet *av ett anrop* med *upsert* -semantik på rest-nivån. Det innebär att om en dubbla redan finns, kommer ett försök att skapa samma dubbla igen att bara ersätta den ursprungliga. Inget fel har utlösts.

### <a name="create-relationships"></a>Skapa relationer

Sedan kan du skapa **relationer** mellan de dubbla som du har skapat, för att ansluta dem till ett **dubbel diagram**. [Dubbla grafer](concepts-twins-graph.md) används för att representera hela miljön.

Lägg till en **ny statisk metod** i `Program` -klassen under `Main` metoden (koden har nu två metoder):

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
        await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

Lägg sedan till följande kod i slutet av `Main` -metoden för att anropa- `CreateRelationship` metoden och använda koden som du nyss skrev:

```csharp
// Connect the twins with relationships
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-1");
await CreateRelationship(client, "sampleTwin-0", "sampleTwin-2");
```

Kör programmet med i kommando fönstret `dotnet run` . I utdata söker du efter utskrifts uttryck som säger att de två relationerna har skapats.

Observera att Azure Digital multipler inte tillåter att du skapar en relation om det redan finns en annan relation med samma ID, så om du kör programmet flera gånger visas undantag vid skapande av relationer. Den här koden fångar undantagen och ignorerar dem. 

### <a name="list-relationships"></a>List relationer

Nästa kod som du lägger till gör att du kan se listan över relationer som du har skapat.

Lägg till följande **nya metod** i- `Program` klassen:

```csharp
public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
{
    try {
        AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
        Console.WriteLine($"Twin {srcId} is connected to:");
        await foreach (BasicRelationship rel in results)
        {
            Console.WriteLine($" -{rel.Name}->{rel.TargetId}");
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

Kör programmet med i kommando fönstret `dotnet run` . Du bör se en lista över alla relationer som du har skapat i en output-instruktion som ser ut så här:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Programutdata, som visar ett meddelande som säger att &quot;dubbla sampleTwin-0 är anslutet till: innehåller->sampleTwin-1,-contains->sampleTwin-2&quot;":::

### <a name="query-digital-twins"></a>Fråga digitala dubbla

En huvud funktion i Azure Digitals flätas är möjligheten att [fråga](concepts-query-language.md) ditt dubbla diagram enkelt och effektivt att besvara frågor om din miljö.

Det sista avsnittet av kod som ska läggas till i den här självstudien kör en fråga mot Azure Digitals-instansen. Frågan som används i det här exemplet returnerar alla digitala dubbla i-instansen.

Lägg till den här `using` instruktionen för att aktivera användning av `JsonSerializer` klassen för att presentera den digitala dubbla informationen:

```csharp
using System.Text.Json;
```

Lägg sedan till följande kod i slutet av- `Main` metoden:

```csharp
// Run a query for all twins   
string query = "SELECT * FROM digitaltwins";
AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);

await foreach (BasicDigitalTwin twin in result)
{
    Console.WriteLine(JsonSerializer.Serialize(twin));
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
using System.Text.Json;

namespace minimal
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            
            string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-hostName>"; 
            
            var credential = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
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
            Console.WriteLine("Models uploaded to the instance:");
            AsyncPageable<DigitalTwinsModelData> modelDataList = client.GetModelsAsync();
            await foreach (DigitalTwinsModelData md in modelDataList)
            {
                Console.WriteLine($"{md.Id}");
            }

            // Initialize twin data
            BasicDigitalTwin twinData = new BasicDigitalTwin();
            twinData.Metadata.ModelId = "dtmi:example:SampleModel;1";
            twinData.Contents.Add("data", $"Hello World!");
            
            string prefix="sampleTwin-";
            for(int i=0; i<3; i++) {
                try {
                    twinData.Id = $"{prefix}{i}";
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twinData.Id, twinData);
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

            // Run a query for all twins   
            string query = "SELECT * FROM digitaltwins";
            AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
            
            await foreach (BasicDigitalTwin twin in result)
            {
                Console.WriteLine(JsonSerializer.Serialize(twin));
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
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine("Created relationship successfully");
            }
            catch (RequestFailedException rex) {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
        }
        
        public async static Task ListRelationships(DigitalTwinsClient client, string srcId)
        {
            try {
                AsyncPageable<BasicRelationship> results = client.GetRelationshipsAsync<BasicRelationship>(srcId);
                Console.WriteLine($"Twin {srcId} is connected to:");
                await foreach (BasicRelationship rel in results)
                {
                    Console.WriteLine($" -{rel.Name}->{rel.TargetId}");
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
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Slutligen tar du bort projektmappen som du skapade på den lokala datorn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett .NET-konsol klient program från grunden. Du har skrivit kod för den här klient appen för att utföra grundläggande åtgärder på en digital Azure-instans.

Fortsätt till nästa självstudie för att utforska de saker du kan göra med en sådan exempel klient app: 

> [!div class="nextstepaction"]
> [*Självstudie: utforska grunderna med en exempel klient program*](tutorial-command-line-app.md)
