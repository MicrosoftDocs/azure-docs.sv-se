---
title: inkludera fil
description: inkludera fil
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 07/30/2020
ms.openlocfilehash: fdae79912e6fe3bf2f7d55b7405cb7883e484c47
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602520"
---
[Referens dokumentation](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer?view=azure-dotnet-preview)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer)  |  [Paket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).

## <a name="using-this-quickstart"></a>Använd den här snabb starten

Det finns flera steg för att använda den här snabb starten:

* I Azure Portal skapar du en personanpassar-resurs
* I Azure Portal för personanpassa resursen, på sidan **konfiguration** , ändrar du modell uppdaterings frekvensen till ett mycket kort intervall
* Skapa en kod fil i en kod redigerare och redigera kod filen
* På kommando raden eller terminalen installerar du SDK från kommando raden
* Kör kod filen i kommando raden eller terminalen

[!INCLUDE [Create Azure resource for Personalizer](create-personalizer-resource.md)]

[!INCLUDE [!Change model frequency](change-model-frequency.md)]

## <a name="create-a-new-c-application"></a>Skapa ett nytt C#-program

Skapa ett nytt .NET Core-program i önskat redigerings program eller IDE.

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `new` kommandot dotNet för att skapa en ny konsol app med namnet `personalizer-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: `Program.cs` .

```console
dotnet new console -n personalizer-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla varningar eller fel.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

## <a name="install-the-sdk"></a>Installera SDK:n

I program katalogen installerar du ett installations program för personanpassa klient bibliotek för .NET med följande kommando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

Om du använder Visual Studio IDE är klient biblioteket tillgängligt som ett nedladdnings Bart NuGet-paket.

## <a name="object-model"></a>Objekt modell

Personanpassa klienten är ett [PersonalizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient?view=azure-dotnet) -objekt som autentiserar till Azure med hjälp av Microsoft. rest. ServiceClientCredentials, som innehåller din nyckel.

Om du vill be om det enda bästa objektet i innehållet skapar du en [RankRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest?view=azure-dotnet-preview)och skickar det sedan till [klienten. Rangordnings](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank?view=azure-dotnet-preview) metod. Rangordnings metoden returnerar en RankResponse.

Om du vill skicka en belönings poäng till Personanpassaren skapar du en [RewardRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest?view=azure-dotnet-preview)och skickar den till [klienten. Belönings](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward?view=azure-dotnet-preview) metod.

Att fastställa belönings poängen i den här snabb starten är trivial. I ett produktions system kan du bestämma vad som påverkar [belönings poängen](../concept-rewards.md) och hur mycket som kan vara en komplicerad process som du kan välja att ändra med tiden. Det här design beslutet bör vara ett av de viktigaste besluten i din personanpassa arkitektur.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med personanpassa klient biblioteket för .NET:

* [Skapa en personanpassa klient](#create-a-personalizer-client)
* [Rang-API](#request-the-best-action)
* [Belönings-API](#send-a-reward)

## <a name="add-the-dependencies"></a>Lägg till beroenden

Från projekt katalogen öppnar du **program.cs** -filen i önskat redigerings program eller IDE. Ersätt den befintliga `using` koden med följande `using` direktiv:

[!code-csharp[Using statements](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=Dependencies)]

## <a name="add-personalizer-resource-information"></a>Lägg till information om personanpassa resurser

I **program** -klassen redigerar du Key-och Endpoint-variablerna överst i kod filen för resursens Azure-nyckel och slut punkt. 

[!code-csharp[Create variables to hold the Personalizer resource key and endpoint values found in the Azure portal.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=classVariables)]

## <a name="create-a-personalizer-client"></a>Skapa en personanpassa klient

Skapa sedan en metod för att returnera en personanpassa klient. Parametern till-metoden är `PERSONALIZER_RESOURCE_ENDPOINT` och ApiKey är `PERSONALIZER_RESOURCE_KEY` .

[!code-csharp[Create the Personalizer client](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=authorization)]

## <a name="get-food-items-as-rankable-actions"></a>Hämta mat objekt som ranknings bara åtgärder

Åtgärder representerar de innehålls val som du vill att en Personanpassare ska välja det bästa innehålls objektet från. Lägg till följande metoder i program-klassen för att representera uppsättningen med åtgärder och deras funktioner. 

[!code-csharp[Food items as actions](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createAction)]

## <a name="get-user-preferences-for-context"></a>Hämta användar inställningar för kontext

Lägg till följande metoder i program-klassen för att få en användares indata från kommando raden för tid på dag och aktuell kost preferens. Dessa kommer att användas som Sammanhangs beroende funktioner.

[!code-csharp[Present time out day preference to the user](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createUserFeatureTimeOfDay)]

[!code-csharp[Present food taste preference to the user](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=createUserFeatureTastePreference)]

Båda metoderna använder `GetKey` metoden för att läsa användarens val från kommando raden.

[!code-csharp[Read user's choice from the command line](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=readCommandLine)]

## <a name="create-the-learning-loop"></a>Skapa inlärnings slingan

Inlärnings-loopen för inlärning är en cykel av [rang](#request-the-best-action) -och [belönings](#send-a-reward) samtal. I den här snabb starten, som varje rang anrop, för att anpassa innehållet, följs av ett belönings samtal för att berätta för personanpassa hur väl tjänsten utförs.

Följande kod går igenom en cykel som ber användaren att ange sina inställningar på kommando raden, vilket innebär att informationen skickas till Personanpassare för att välja den bästa åtgärden, vilket innebär att valet av kund kan välja bland listan och sedan skicka en belönings poäng till en personlig signalering av hur väl tjänsten gjorde sitt val.

[!code-csharp[Learning loop](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=mainLoop)]

Lägg till följande metoder, som [hämtar innehålls valen](#get-food-items-as-rankable-actions), innan du kör kod filen:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Begär den bästa åtgärden

För att slutföra ranknings förfrågan ställer programmet till användarens inställningar för att skapa ett `currentContext` av innehålls valen. Processen kan skapa innehåll som ska undantas från åtgärder, som visas som `excludeActions` . Ranknings förfrågan behöver åtgärder och deras funktioner, currentContext-funktioner, excludeActions och ett unikt händelse-ID för att få svaret.

Den här snabb starten har enkla Sammanhangs funktioner i tid på dygnet och användarens mat preferenser. I produktions system kan det vara en icke-trivial sak att fastställa och [utvärdera](../concept-feature-evaluation.md) [åtgärder och funktioner](../concepts-features.md) .

[!code-csharp[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=rank)]

## <a name="send-a-reward"></a>Skicka en belöning

För att få belönings poängen att skicka i belönings förfrågan får programmet användarens val från kommando raden, tilldelar ett numeriskt värde till markeringen och skickar sedan det unika händelse-ID: t och belönings poängen som det numeriska värdet till belönings-API: et.

Den här snabb starten tilldelar ett enkelt tal som en belönings poäng, antingen noll eller 1. I produktions system kan du fastställa när och vad som ska skickas till [belönings](../concept-rewards.md) anropet som en icke-trivial fråga, beroende på dina behov.

[!code-csharp[The Personalizer learning loop ranks the request.](~/cognitive-services-quickstart-code/dotnet/Personalizer/Program.cs?name=reward)]

## <a name="run-the-program"></a>Köra programmet

Kör programmet med `run` kommandot dotNet från program katalogen.

```console
dotnet run
```

![Snabb start programmet ber några frågor om att samla in användar inställningar, kallas funktioner, och ger sedan den främsta åtgärden.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

[Käll koden för den här snabb](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer) starten är tillgänglig.
