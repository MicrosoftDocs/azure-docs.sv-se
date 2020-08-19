---
title: Hantera anpassade modeller
titleSuffix: Azure Digital Twins
description: Se hur du skapar, redigerar och tar bort en modell i Azures digitala dubbla.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 919db9338917a9c2bedd7806eb251a2e5ef6187b
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510002"
---
# <a name="manage-azure-digital-twins-models"></a>Hantera Azure Digitals dubbla modeller

Du kan hantera de [modeller](concepts-models.md) som Azure Digitals-instansen är medveten om att använda [**DigitalTwinsModels-API: er**](how-to-use-apis-sdks.md), [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)eller [Azure Digitals flätat CLI](how-to-use-cli.md). 

Hanterings åtgärder omfattar överföring, validering, hämtning och borttagning av modeller. 

## <a name="create-models"></a>Skapa modeller

Modeller för digitala Azure-dubblare skrivs i DTDL och sparas som *. JSON* -filer. Det finns också ett [DTDL-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) som är tillgängligt för [Visual Studio Code](https://code.visualstudio.com/), som innehåller syntax för autentisering och andra funktioner som underlättar skrivning av DTDL-dokument.

Överväg ett exempel där ett sjukhus vill kunna återge sina rum digitalt. Varje rum innehåller en smart SOAP-dispens för att övervaka hand tvättning och sensorer för att övervaka trafik genom rummet.

Det första steget i lösningen är att skapa modeller som visar aspekter av sjukhus. Ett patient rum i det här scenariot kan se ut så här:

```json
{
  "@id": "dtmi:com:contoso:PatientRoom;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "displayName": "Patient Room",
  "contents": [
    {
      "@type": "Property",
      "name": "visitorCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashCount",
      "schema": "double"
    },
    {
      "@type": "Property",
      "name": "handWashPercentage",
      "schema": "double"
    },
    {
      "@type": "Relationship",
      "name": "hasDevices"
    }
  ]
}
```

> [!NOTE]
> Detta är en exempel text för en. JSON-fil där en modell definieras och sparas, som ska laddas upp som en del av ett klient projekt. REST API-anropet använder å andra sidan en matris med modell definitioner som ovan (som är mappad till en `IEnumerable<string>` i .NET SDK). Om du vill använda den här modellen i REST API direkt omger du den med hakparenteser.

Den här modellen definierar ett namn och ett unikt ID för patient rummet och egenskaper för att representera antal besökare och status för manuell tvättning (dessa räknare kommer att uppdateras från rörelse sensorer och smarta SOAP-behållare och används tillsammans för att beräkna en *handwash procents ATS* -egenskap). Modellen definierar också en Relations- *hasDevices*, som kommer att användas för att ansluta alla [digitala](concepts-twins-graph.md) enheter baserat på den här *rums* modellen till de faktiska enheterna.

Efter den här metoden kan du gå vidare till för att definiera modeller för sjukhuss rapporter, zoner eller själva sjukhus.

### <a name="validate-syntax"></a>Validera syntax

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="manage-models-with-apis"></a>Hantera modeller med API: er.

I följande avsnitt visas hur du utför olika modell hanterings åtgärder med hjälp av [Azure Digitals dubbla API: er och SDK: er](how-to-use-apis-sdks.md).

> [!NOTE]
> I exemplen nedan ingår inte fel hantering för det kortfattat. Vi rekommenderar dock att du i dina projekt kan figursätta tjänst anrop i try/catch-block.

> [!TIP] 
> Kom ihåg att alla SDK-metoder ingår i synkrona och asynkrona versioner. För växlings anrop returneras asynkrona metoder `AsyncPageable<T>` när de synkrona versionerna returneras `Pageable<T>` .

### <a name="upload-models"></a>Ladda upp modeller

När modeller har skapats kan du ladda upp dem till Azure Digitals-instansen.

> [!TIP]
> Vi rekommenderar att du validerar dina modeller offline innan du laddar upp dem till din Azure Digital-instansen. Du kan använda [DTDL för klient sidans parser](https://nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser/) och [DTDL](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator) som beskrivs i [*instruktionen att parsa och validera modeller*](how-to-parse-models.md) för att kontrol lera dina modeller innan du överför dem till tjänsten.

När du är redo att ladda upp en modell kan du använda följande kodfragment:

```csharp
// 'client' is an instance of DigitalTwinsClient
// Read model file into string (not part of SDK)
StreamReader r = new StreamReader("MyModelFile.json");
string dtdl = r.ReadToEnd(); r.Close();
string[] dtdls = new string[] { dtdl };
client.CreateModels(dtdls);
```

Observera att `CreateModels` metoden accepterar flera filer i en enda transaktion. Här är ett exempel som illustrerar:

```csharp
var dtdlFiles = Directory.EnumerateFiles(sourceDirectory, "*.json");

List<string> dtdlStrings = new List<string>();
foreach (string fileName in dtdlFiles)
{
    // Read model file into string (not part of SDK)
    StreamReader r = new StreamReader(fileName);
    string dtdl = r.ReadToEnd(); r.Close();
    dtdlStrings.Add(dtdl);
}
client.CreateModels(dtdlStrings);
```

Model-filer kan innehålla mer än en enskild modell. I det här fallet måste modellerna placeras i en JSON-matris. Ett exempel:

```json
[
  {
    "@id": "dtmi:com:contoso:Planet",
    "@type": "Interface",
    //...
  },
  {
    "@id": "dtmi:com:contoso:Moon",
    "@type": "Interface",
    //...
  }
]
```
 
Vid uppladdning verifieras Model-filer av tjänsten.

### <a name="retrieve-models"></a>Hämta modeller

Du kan visa och hämta modeller som är lagrade på din Azure Digital-instansen. 

Här är dina alternativ:
* Hämta alla modeller
* Hämta en enskild modell
* Hämta en enskild modell med beroenden
* Hämta metadata för modeller

Här följer några exempel på anrop:

```csharp
// 'client' is a valid DigitalTwinsClient object

// Get a single model, metadata and data
ModelData md1 = client.GetModel(id);

// Get a list of the metadata of all available models
Pageable<ModelData> pmd2 = client.GetModels();

// Get a list of metadata and full model definitions
Pageable<ModelData> pmd3 = client.GetModels(null, true);

// Get models and metadata for a model ID, including all dependencies (models that it inherits from, components it references)
Pageable<ModelData> pmd4 = client.GetModels(new string[] { modelId }, true);
```

API-anrop för att hämta modeller med alla retur `ModelData` objekt. `ModelData` innehåller metadata om modellen som lagras i Azure Digitals-instansen, till exempel namn, DTMI och skapande datum för modellen. `ModelData`Objektet kan också innehålla själva modellen. Beroende på parametrarna kan du därför använda Hämta anrop för att antingen hämta bara metadata (vilket är användbart i scenarier där du vill visa en GRÄNSSNITTs lista med tillgängliga verktyg, till exempel) eller hela modellen.

`RetrieveModelWithDependencies`Anropet returnerar inte bara den begärda modellen, utan även alla modeller som den begärda modellen är beroende av.

Modeller returneras inte nödvändigt vis i exakt det dokument formulär de överfördes. Azure Digitals dubblare garanterar bara att RETUR formuläret kommer att vara semantiskt likvärdigt. 

### <a name="remove-models"></a>Ta bort modeller

Modeller kan också tas bort från tjänsten på ett av två sätt:
* **Avställning** : när en modell har inaktiverats kan du inte längre använda den för att skapa nya digitala dubbla. Befintliga digitala dubbla objekt som redan använder den här modellen påverkas inte, så du kan fortfarande uppdatera dem med saker som egenskaps ändringar och lägga till eller ta bort relationer.
* **Borttagning** : modellen tas bort helt från lösningen. Alla dubbla som använder den här modellen är inte längre kopplade till någon giltig modell, så de behandlas som om de inte har någon modell alls. Du kan fortfarande läsa dessa dubbla, men du kan inte göra några uppdateringar på dem förrän de har omtilldelats till en annan modell.

Dessa är separata funktioner och de påverkar inte varandra, men de kan användas tillsammans för att ta bort en modell gradvis. 

#### <a name="decommissioning"></a>Ställa

Här är koden för att inaktivera en modell:

```csharp
// 'client' is a valid DigitalTwinsClient  
client.DecommissionModel(dtmiOfPlanetInterface);
// Write some code that deletes or transitions digital twins
//...
```

En modells inaktive rings status ingår i `ModelData` posterna som returneras av modell hämtnings-API: erna.

#### <a name="deletion"></a>Borttagning

Du kan ta bort alla modeller i din instans på samma gång, eller så kan du göra det på individuell basis.

Ett exempel på hur du tar bort alla modeller får du genom att hämta den exempel app som används i [*självstudien: utforska grunderna med ett exempel på ett klient program*](tutorial-command-line-app.md). *CommandLoop.cs* -filen gör detta i en `CommandDeleteAllModels` funktion.

Resten av det här avsnittet delar ned modell borttagning i närmare detalj, och visar hur du gör det för en enskild modell.

##### <a name="before-deletion-deletion-requirements"></a>Före borttagning: borttagnings krav

Modeller kan vanligt vis tas bort när som helst.

Undantaget är modeller som andra modeller är beroende av, antingen med en `extends` relation eller som en komponent. Om en *ConferenceRoom* -modell exempelvis utökar en *rums* modell och har en *ACUnit* -modell som en komponent, kan du inte ta bort *rummet* eller *ACUnit* förrän *ConferenceRoom* tar bort de respektive referenserna. 

Du kan göra detta genom att uppdatera den beroende modellen för att ta bort beroenden eller ta bort den beroende modellen helt.

##### <a name="during-deletion-deletion-process"></a>Under borttagning: borttagnings process

Även om en modell uppfyller kraven för att ta bort den direkt, kan det vara bra att gå igenom några steg för att undvika oönskade konsekvenser för de dubblare som ligger kvar. Här följer några steg som kan hjälpa dig att hantera processen:
1. Börja med att inaktivera modellen
2. Vänta några minuter för att se till att tjänsten har bearbetat några sammanställda begär Anden om att skapa en sista minut som skickats före inaktive ring
3. Fråga dubbla efter modell för att se alla dubbla som använder den nu-inaktiverade modellen
4. Ta bort de dubbla objekten om du inte längre behöver dem eller korrigera dem till en ny modell om det behövs. Du kan också välja att låta dem vara ensamma, vilket innebär att de blir sammanflätade utan modeller när modellen har tagits bort. Se nästa avsnitt för detta tillstånds konsekvenser.
5. Vänta några minuter och se till att ändringarna har percolated
6. Ta bort modellen 

Om du vill ta bort en modell använder du det här anropet:
```csharp
// 'client' is a valid DigitalTwinsClient
await client.DeleteModelAsync(IDToDelete);
```

##### <a name="after-deletion-twins-without-models"></a>Efter borttagning: sammanflätade utan modeller

När en modell har tagits bort, anses alla digitala, dubbla, som använde modellen, vara utan en modell. Observera att det inte finns någon fråga som kan ge dig en lista över alla dubbla i det här läget, även om du fortfarande *kan* fråga de dubblarna efter den borttagna modellen för att veta vad som påverkas.

Här är en översikt över vad du kan och inte kan göra med ett garn som inte har en modell.

Saker du **kan** göra:
* Fråga den dubbla
* Läsa egenskaper
* Läs utgående relationer
* Lägga till och ta bort inkommande relationer (som i kan andra dubbla, fortfarande bilda relationer *till* denna dubbla)
  - `target`I Relations definitionen kan fortfarande Visa DTMI för den borttagna modellen. En relation utan definierade mål kan också fungera här.
* Ta bort relationer
* Ta bort den dubbla

Saker som du **inte kan** göra:
* Redigera utgående relationer (som i, relationer *från* den här dubbla till andra dubbla)
* Redigera egenskaper

##### <a name="after-deletion-re-uploading-a-model"></a>Efter borttagning: laddar upp en modell igen

När en modell har tagits bort kan du senare välja att ladda upp en ny modell med samma ID som den som du tog bort. Det här är vad som händer i detta fall.
* Från lösnings lagrets perspektiv är det samma som att ladda upp en helt ny modell. Tjänsten kommer inte ihåg att den gamla har laddats upp tidigare.   
* Om det finns några återstående dubbla i grafen som refererar till den borttagna modellen, är de inte längre överblivna. modell-ID: t är giltigt igen med den nya definitionen. Men om den nya definitionen för modellen skiljer sig från modell definitionen som har tagits bort, kan dessa dubbla, ha egenskaper och relationer som matchar den borttagna definitionen och som inte är giltiga med den nya.

Azures digitala dubblare förhindrar inte det här läget, så var noga med att korrigera korrigeringen på lämpligt sätt för att se till att de är giltiga via modell definitions växeln.

## <a name="manage-models-with-cli"></a>Hantera modeller med CLI

Modeller kan också hanteras med hjälp av Azure Digitals flätade CLI. Kommandona finns i [*anvisningar: använda Azure Digitals flätade CLI*](how-to-use-cli.md).

## <a name="next-steps"></a>Nästa steg

Se hur du skapar och hanterar digitala dubbla, baserade på dina modeller:
* [*Anvisningar: hantera digitala dubbla*](how-to-manage-twin.md)