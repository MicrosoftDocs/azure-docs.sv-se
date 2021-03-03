---
title: Migrera dina ansikts data över prenumerationer – ansikte
titleSuffix: Azure Cognitive Services
description: Den här guiden visar hur du migrerar dina lagrade ansikts data från en ansikts prenumeration till en annan.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: nitinme
ms.custom: devx-track-csharp
ms.openlocfilehash: c8d3c5b10c670e7aa4f1fd00f47ef47e772416cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706868"
---
# <a name="migrate-your-face-data-to-a-different-face-subscription"></a>Migrera dina ansikts data till en annan ansikts prenumeration

Den här guiden visar hur du flyttar ansikts data, till exempel ett sparat PersonGroup-objekt med ansikten, till en annan Azure Cognitive Services Face-prenumeration. Om du vill flytta data använder du ögonblicks bild funktionen. På så sätt undviker du att upprepade gånger skapa och träna ett PersonGroup-eller FaceList-objekt när du flyttar eller utökar dina åtgärder. Exempelvis kanske du har skapat ett PersonGroup-objekt med en kostnads fri prenumeration och nu vill migrera det till den betalda prenumerationen. Eller så kanske du behöver synkronisera ansikts data mellan prenumerationer i olika regioner för en stor företags åtgärd.

Samma migrations strategi gäller även för LargePersonGroup-och LargeFaceList-objekt. Om du inte är bekant med begreppen i den här hand boken kan du se deras definitioner i rikt linjer för [ansikts igenkänning](../concepts/face-recognition.md) . Den här guiden använder sitt ansikte .NET-klient bibliotek med C#.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande objekt:

- Två ansikts prenumerations nycklar, en med befintliga data och en att migrera till. Följ instruktionerna i [skapa ett Cognitive Services-konto](../../cognitive-services-apis-create-account.md)om du vill prenumerera på ansikts tjänsten och hämta din nyckel.
- ID-strängen för ansikts prenumeration som motsvarar mål prenumerationen. Välj **Översikt** i Azure Portal för att hitta den. 
- Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Skapa Visual Studio-projektet

I den här guiden används en enkel konsol app för att köra ansikts data-migreringen. En fullständig implementering finns i exemplet på [ansikts ögonblicks bild](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) på GitHub.

1. Skapa ett nytt konsol program .NET Framework projekt i Visual Studio. Ge den namnet **FaceApiSnapshotSample**.
1. Hämta de NuGet-paket som behövs. Högerklicka på ditt projekt i Solution Explorer och välj **Hantera NuGet-paket**. Välj fliken **Bläddra** och välj inkludera för **hands version**. Sök efter och installera följande paket:
    - [Microsoft. Azure. CognitiveServices. vision. Face 2.3.0 – för hands version](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="create-face-clients"></a>Skapa ansikts klienter

I **main** -metoden i *program.cs* skapar du två [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) -instanser för dina käll-och mål prenumerationer. I det här exemplet används en ansikts prenumeration i Asien, östra region som källa och en västra USA-prenumeration som mål. Det här exemplet visar hur du migrerar data från en Azure-region till en annan. 

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

```csharp
var FaceClientEastAsia = new FaceClient(new ApiKeyServiceClientCredentials("<East Asia Subscription Key>"))
    {
        Endpoint = "https://southeastasia.api.cognitive.microsoft.com/>"
    };

var FaceClientWestUS = new FaceClient(new ApiKeyServiceClientCredentials("<West US Subscription Key>"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com/"
    };
```

Fyll i prenumerations nyckel värden och slut punkts-URL: er för dina käll-och mål prenumerationer.


## <a name="prepare-a-persongroup-for-migration"></a>Förbereda en PersonGroup för migrering

Du behöver ID: t för PersonGroup i din käll prenumeration för att migrera den till mål prenumerationen. Använd metoden [PersonGroupOperationsExtensions. ListAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperationsextensions.listasync) för att hämta en lista över dina PersonGroup-objekt. Hämta sedan egenskapen [PersonGroup. PersonGroupId](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.persongroup.persongroupid#Microsoft_Azure_CognitiveServices_Vision_Face_Models_PersonGroup_PersonGroupId) . Den här processen ser olika ut beroende på vilka PersonGroup-objekt du har. I den här hand boken lagras käll-PersonGroup-ID: t i `personGroupId` .

> [!NOTE]
> [Exempel koden](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample) skapar och tågen en ny PersonGroup som kan migreras. I de flesta fall bör du redan ha en PersonGroup att använda.

## <a name="take-a-snapshot-of-a-persongroup"></a>Ta en ögonblicks bild av en PersonGroup

En ögonblicks bild är tillfällig Fjärrlagring för vissa typer av ansikts data. Den fungerar som en typ av Urklipp för att kopiera data från en prenumeration till en annan. Först tar du en ögonblicks bild av data i käll prenumerationen. Sedan tillämpar du den på ett nytt data objekt i mål prenumerationen.

Använd käll prenumerationens FaceClient-instans för att ta en ögonblicks bild av PersonGroup. Använd [TakeAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperationsextensions.takeasync) med PersonGroup-ID och mål prenumerationens ID. Om du har flera mål prenumerationer lägger du till dem som mat ris poster i den tredje parametern.

```csharp
var takeSnapshotResult = await FaceClientEastAsia.Snapshot.TakeAsync(
    SnapshotObjectType.PersonGroup,
    personGroupId,
    new[] { "<Azure West US Subscription ID>" /* Put other IDs here, if multiple target subscriptions wanted */ });
```

> [!NOTE]
> Processen att ta och använda ögonblicks bilder stör inte några vanliga anrop till käll-eller mål PersonGroups eller FaceLists. Gör inte samtidiga anrop som ändrar källobjektet, t. ex. [FaceList hanterings samtal](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations) eller [PersonGroup Train](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations) -anrop, till exempel. Ögonblicks bild åtgärden kan köras före eller efter dessa åtgärder eller kan uppstå fel.

## <a name="retrieve-the-snapshot-id"></a>Hämta ögonblicks bild-ID

Den metod som används för att ta ögonblicks bilder är asynkron, så du måste vänta tills den är klar. Ögonblicks bild åtgärder kan inte avbrytas. I den här koden `WaitForOperation` övervakar metoden det asynkrona anropet. Den kontrollerar status var 100 MS. När åtgärden har slutförts hämtar du ett åtgärds-ID genom att parsa `OperationLocation` fältet. 

```csharp
var takeOperationId = Guid.Parse(takeSnapshotResult.OperationLocation.Split('/')[2]);
var operationStatus = await WaitForOperation(FaceClientEastAsia, takeOperationId);
```

Ett typiskt `OperationLocation` värde ser ut så här:

```csharp
"/operations/a63a3bdd-a1db-4d05-87b8-dbad6850062a"
```

`WaitForOperation`Hjälp metoden är här:

```csharp
/// <summary>
/// Waits for the take/apply operation to complete and returns the final operation status.
/// </summary>
/// <returns>The final operation status.</returns>
private static async Task<OperationStatus> WaitForOperation(IFaceClient client, Guid operationId)
{
    OperationStatus operationStatus = null;
    do
    {
        if (operationStatus != null)
        {
            Thread.Sleep(TimeSpan.FromMilliseconds(100));
        }

        // Get the status of the operation.
        operationStatus = await client.Snapshot.GetOperationStatusAsync(operationId);

        Console.WriteLine($"Operation Status: {operationStatus.Status}");
    }
    while (operationStatus.Status != OperationStatusType.Succeeded
            && operationStatus.Status != OperationStatusType.Failed);

    return operationStatus;
}
```

När åtgärds statusen visas `Succeeded` hämtar du ögonblicks bildens ID genom att parsa `ResourceLocation` fältet för den returnerade OperationStatus-instansen.

```csharp
var snapshotId = Guid.Parse(operationStatus.ResourceLocation.Split('/')[2]);
```

Ett typiskt `resourceLocation` värde ser ut så här:

```csharp
"/snapshots/e58b3f08-1e8b-4165-81df-aa9858f233dc"
```

## <a name="apply-a-snapshot-to-a-target-subscription"></a>Använd en ögonblicks bild för en mål prenumeration

Skapa sedan den nya PersonGroup i mål prenumerationen genom att använda ett slumpmässigt genererat ID. Använd sedan mål prenumerationens FaceClient-instans för att tillämpa ögonblicks bilden på den här PersonGroup. Skicka in ögonblicks bild-ID: t och det nya PersonGroup-ID: t.

```csharp
var newPersonGroupId = Guid.NewGuid().ToString();
var applySnapshotResult = await FaceClientWestUS.Snapshot.ApplyAsync(snapshotId, newPersonGroupId);
```


> [!NOTE]
> Ett ögonblicks bild objekt är giltigt endast för 48 timmar. Ta bara en ögonblicks bild om du tänker använda den för datamigrering strax efter.

En ögonblicks bild tillämpa begäran returnerar ett annat åtgärds-ID. Hämta det här ID: t genom att parsa `OperationLocation` fältet för den returnerade applySnapshotResult-instansen. 

```csharp
var applyOperationId = Guid.Parse(applySnapshotResult.OperationLocation.Split('/')[2]);
```

Processen för ögonblicks bild programmet är också asynkron och används sedan `WaitForOperation` för att vänta tills den har slutförts.

```csharp
operationStatus = await WaitForOperation(FaceClientWestUS, applyOperationId);
```

## <a name="test-the-data-migration"></a>Testa datamigreringen

När du har tillämpat ögonblicks bilden fyller den nya PersonGroup i mål prenumerationen med de ursprungliga ansikts data. Som standard kopieras också utbildnings resultat. Den nya PersonGroup är redo för ansikts identifierings anrop utan att behöva omträna.

Testa datamigreringen genom att köra följande åtgärder och jämföra de resultat som de skriver ut till-konsolen:

```csharp
await DisplayPersonGroup(FaceClientEastAsia, personGroupId);
await IdentifyInPersonGroup(FaceClientEastAsia, personGroupId);

await DisplayPersonGroup(FaceClientWestUS, newPersonGroupId);
// No need to retrain the person group before identification,
// training results are copied by snapshot as well.
await IdentifyInPersonGroup(FaceClientWestUS, newPersonGroupId);
```

Använd följande hjälp metoder:

```csharp
private static async Task DisplayPersonGroup(IFaceClient client, string personGroupId)
{
    var personGroup = await client.PersonGroup.GetAsync(personGroupId);
    Console.WriteLine("Person Group:");
    Console.WriteLine(JsonConvert.SerializeObject(personGroup));

    // List persons.
    var persons = await client.PersonGroupPerson.ListAsync(personGroupId);

    foreach (var person in persons)
    {
        Console.WriteLine(JsonConvert.SerializeObject(person));
    }

    Console.WriteLine();
}
```

```csharp
private static async Task IdentifyInPersonGroup(IFaceClient client, string personGroupId)
{
    using (var fileStream = new FileStream("data\\PersonGroup\\Daughter\\Daughter1.jpg", FileMode.Open, FileAccess.Read))
    {
        var detectedFaces = await client.Face.DetectWithStreamAsync(fileStream);

        var result = await client.Face.IdentifyAsync(detectedFaces.Select(face => face.FaceId.Value).ToList(), personGroupId);
        Console.WriteLine("Test identify against PersonGroup");
        Console.WriteLine(JsonConvert.SerializeObject(result));
        Console.WriteLine();
    }
}
```

Nu kan du använda den nya PersonGroup i mål prenumerationen. 

Om du vill uppdatera mål PersonGroup igen i framtiden skapar du en ny PersonGroup för att ta emot ögonblicks bilden. Det gör du genom att följa stegen i den här hand boken. Ett enda PersonGroup-objekt kan endast ha en ögonblicks bild som tillämpas på den en gång.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört migreringen av ansikts data tar du bort objektet Snapshot manuellt.

```csharp
await FaceClientEastAsia.Snapshot.DeleteAsync(snapshotId);
```

## <a name="next-steps"></a>Nästa steg

Gå sedan till relevant API-referens dokumentation, utforska en exempel-app som använder ögonblicks bild funktionen eller följ en instruktion för att börja använda andra API-åtgärder som nämns här:

- [Dokumentation för ögonblicks bild referens (.NET SDK)](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.snapshotoperations)
- [Exempel på ansikts ögonblicks bild](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceApiSnapshotSample/FaceApiSnapshotSample)
- [Lägg till ansikten](how-to-add-faces.md)
- [Identifiera ansikten i en bild](HowtoDetectFacesinImage.md)
