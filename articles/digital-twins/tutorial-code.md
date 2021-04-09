---
title: 'Självstudie: koda en klient app'
titleSuffix: Azure Digital Twins
description: Självstudie för att skriva den minimala koden för en klient app med hjälp av .NET (C#) SDK.
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: bd3ba88650161bd11a24697b4ff8575d307120e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102424469"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Självstudie: koda med Azure Digitals dubbla API: er

Det är vanligt för utvecklare som arbetar med Azure Digitals dubblare att skriva ett klient program för att interagera med sin instans av Azure Digitals dubbla tjänster. Den här själv studie kursen om utvecklare är en introduktion till programmering mot Azure Digitals-tjänsten med [Azure Digitals-SDK för .net (C#)](/dotnet/api/overview/azure/digitaltwins/client). Den vägleder dig genom att skriva en C#-konsol klient program steg för steg, som börjar från grunden.

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

Detta skapar flera filer i katalogen, inklusive en som kallas *program. cs* där du skriver merparten av koden.

Se till att kommando fönstret är öppet, eftersom du fortsätter att använda det i självstudien.

Lägg sedan **till två beroenden i projektet** som behövs för att arbeta med Azure Digital-dubbla. Det första är paketet för [Azure Digitals-SDK: n för .net](/dotnet/api/overview/azure/digitaltwins/client), den andra innehåller verktyg som hjälper dig att autentisera mot Azure.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
```

## <a name="get-started-with-project-code"></a>Kom igång med projekt kod

I det här avsnittet ska du skriva koden för att ditt nya app-projekt ska fungera med Azure Digitals. De åtgärder som omfattas är:
* Autentisering mot tjänsten
* Laddar upp en modell
* Fånga fel
* Skapa digitala dubbla
* Skapar relationer
* Fråga digitala dubbla

Det finns också ett avsnitt som visar den fullständiga koden i slutet av självstudien. Du kan använda den som en referens för att kontrol lera programmet när du går.

Starta genom att öppna filen *program. cs* i valfri kod redigerare. En minimal kod mall visas som ser ut ungefär så här:

:::row:::
    :::column:::
        :::image type="content" source="media/tutorial-code/starter-template.png" alt-text="Ett fragment med exempel kod. Det finns en using system;-instruktion, ett namn område med namnet DigitalTwinsCodeTutorial; en klass i namn området som heter program; och en main-metod i-klassen med en standardsignatur för &quot;static void main (String [] args)&quot;. Main-metoden innehåller en Hello World Print-instruktion." lightbox="media/tutorial-code/starter-template.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Lägg först till några `using` rader överst i koden för att hämta nödvändiga beroenden.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Azure_Digital_Twins_dependencies":::

Sedan lägger du till kod i den här filen för att fylla i vissa funktioner. 

### <a name="authenticate-against-the-service"></a>Autentisera mot tjänsten

Det första du behöver göra är att autentisera mot tjänsten Azure Digitals dubbla. Sedan kan du skapa en tjänst klient klass för att få åtkomst till SDK-funktionerna.

För att kunna autentisera behöver du *värd namnet* för din Azure Digital-instansen.

I *program. cs* klistrar du in följande kod under "Hello, World!" Skriv ut rad i `Main` metoden. Ange värdet för `adtInstanceUrl` till ditt Azure Digital-instansen *värdnamn*.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Authentication_code":::

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

:::code language="json" source="~/digital-twins-docs-samples/models/SampleModel.json":::

> [!TIP]
> Om du använder Visual Studio för den här självstudien kanske du vill välja den nyss skapade JSON-filen och ange egenskapen *Kopiera till utdata-katalog* i egenskapsinspektören för att *Kopiera om* det är nyare eller *Kopiera Always*. Detta gör att Visual Studio kan hitta JSON-filen med standard Sök vägen när du kör programmet med **F5** under resten av självstudien.

> [!TIP] 
> Det finns ett språk-oberoende [DTDL-verifierings exempel](/samples/azure-samples/dtdl-validator/dtdl-validator) som du kan använda för att kontrol lera modell dokument för att kontrol lera att DTDL är giltig. Det bygger på DTDL parser-biblioteket, som du kan läsa mer om i [*instruktion: parsa och validera modeller*](how-to-parse-models.md).

Lägg sedan till ytterligare kod i *program. cs* för att ladda upp den modell som du precis har skapat i Azure Digitals-instansen.

Lägg först till några `using` instruktioner överst i filen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_dependencies":::

Förbered sedan att använda de asynkrona metoderna i C#-tjänst-SDK: n genom `Main` att ändra Metodsignaturen till att tillåta asynkron körning. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Async_signature":::

> [!NOTE]
> `async`Att använda är inte absolut nödvändigt eftersom SDK även tillhandahåller synkrona versioner av alla anrop. Den här själv studie praxis som använder `async` .

Härnäst visas den första bit koden som interagerar med Azure Digitals dubbla-tjänster. Den här koden läser in den DTDL-fil som du skapade från disken och laddar sedan upp den till din Azure Digitals dubbla tjänst instans. 

Klistra in följande kod under den auktoriseringskod som du lade till tidigare.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp_excerpt_model.cs":::

Kör programmet med det här kommandot i kommando fönstret: 

```cmd/sh
dotnet run
```
"Överför en modell" skrivs ut i utdata, vilket indikerar att den här koden har uppnåtts, men det finns inga utdata än att ange om uppladdningen lyckades.

Lägg till en Print-instruktion som visar alla modeller som har laddats upp till instansen genom att lägga till följande kod direkt efter föregående avsnitt:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Print_model":::

**Innan du kör programmet igen för att testa den nya koden** ska du återkalla den senaste gången du körde programmet. du laddade upp din modell redan. Med Azure Digital-dubbla kan du inte överföra samma modell två gånger, så om du försöker överföra samma modell igen, ska programmet utlösa ett undantag.

Med detta i åtanke kan du köra programmet igen med det här kommandot i kommando fönstret:

```cmd/sh
dotnet run
```

Programmet ska utlösa ett undantag. När du försöker överföra en modell som redan har laddats upp returnerar tjänsten fel meddelandet "felaktig begäran" via REST API. Det innebär att Azure Digitals klient-SDK: n i sin tur genererar ett undantag för varje annan tjänst returkod än slutförd. 

Nästa avsnitt pratar om undantag som detta och hur du hanterar dem i din kod.

### <a name="catch-errors"></a>Catch-fel

Om du vill förhindra att programmet kraschar kan du lägga till undantags kod runt modell överförings koden. Packa upp det befintliga klient anropet `await client.CreateModelsAsync(typeList)` i en try/catch-hanterare, så här:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_try_catch":::

Om du nu kör programmet med `dotnet run` i ditt kommando fönster, kommer du att se att du får tillbaka felkoden. Utdata från modellen för skapande av modeller visar det här felet:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Programutdata, som visar ett meddelande som säger &quot;409: tjänstbegäran misslyckades. Status: 409 (konflikt)., följt av en fel utskrift som indikerar att dtmi: exempel: SampleModel; 1 redan finns":::

Från och med nu kommer kursen att figursättas alla anrop till tjänst metoder i try/catch-hanterare.

### <a name="create-digital-twins"></a>Skapa digitala tvillingar

Nu när du har laddat upp en modell till Azure Digitals, kan du använda den här modell definitionen för att skapa **digitala dubbla**. [Digitala dubbla](concepts-twins-graph.md) är instanser av en modell och representerar entiteterna i din affärs miljö – saker som sensorer i en grupp, rum i en byggnad eller lampor i en bil. Det här avsnittet skapar några digitala dubbla, baserat på den modell som du laddade upp tidigare.

Lägg till följande kod i slutet av- `Main` metoden för att skapa och initiera tre digitala dubbla, baserade på den här modellen.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Initialize_twins":::

Kör programmet med i kommando fönstret `dotnet run` . I utdata söker du efter de utskrifts meddelanden som *sampleTwin-0*, *sampleTwin-1* och *sampleTwin-2* har skapats. 

Kör sedan programmet igen. 

Observera att det inte uppstår något fel när de dubblarna skapas den andra gången, även om de finns redan efter den första körningen. Till skillnad från skapande av modell är skapandet *av ett anrop* med *upsert* -semantik på rest-nivån. Det innebär att om en dubbla redan finns, kommer ett försök att skapa samma dubbla igen att bara ersätta den ursprungliga. Inget fel har utlösts.

### <a name="create-relationships"></a>Skapa relationer

Sedan kan du skapa **relationer** mellan de dubbla som du har skapat, för att ansluta dem till ett **dubbel diagram**. [Dubbla grafer](concepts-twins-graph.md) används för att representera hela miljön.

Lägg till en **ny statisk metod** i `Program` -klassen under `Main` metoden (koden har nu två metoder):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Create_relationship":::

Lägg sedan till följande kod i slutet av `Main` -metoden för att anropa- `CreateRelationship` metoden och använda koden som du nyss skrev:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_create_relationship":::

Kör programmet med i kommando fönstret `dotnet run` . I utdata söker du efter utskrifts uttryck som säger att de två relationerna har skapats.

Observera att Azure Digital multipler inte tillåter att du skapar en relation om det redan finns en annan relation med samma ID, så om du kör programmet flera gånger visas undantag vid skapande av relationer. Den här koden fångar undantagen och ignorerar dem. 

### <a name="list-relationships"></a>List relationer

Nästa kod som du lägger till gör att du kan se listan över relationer som du har skapat.

Lägg till följande **nya metod** i- `Program` klassen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="List_relationships":::

Lägg sedan till följande kod i slutet av- `Main` metoden för att anropa `ListRelationships` koden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_list_relationships":::

Kör programmet med i kommando fönstret `dotnet run` . Du bör se en lista över alla relationer som du har skapat i en output-instruktion som ser ut så här:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Programutdata, som visar ett meddelande som säger att &quot;dubbla sampleTwin-0 är anslutet till: innehåller->sampleTwin-1,-contains->sampleTwin-2&quot;" lightbox="media/tutorial-code/list-relationships.png":::

### <a name="query-digital-twins"></a>Fråga digitala dubbla

En huvud funktion i Azure Digitals flätas är möjligheten att [fråga](concepts-query-language.md) ditt dubbla diagram enkelt och effektivt att besvara frågor om din miljö.

Det sista avsnittet av kod som ska läggas till i den här självstudien kör en fråga mot Azure Digitals-instansen. Frågan som används i det här exemplet returnerar alla digitala dubbla i-instansen.

Lägg till den här `using` instruktionen för att aktivera användning av `JsonSerializer` klassen för att presentera den digitala dubbla informationen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_dependencies":::

Lägg sedan till följande kod i slutet av- `Main` metoden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_twins":::

Kör programmet med i kommando fönstret `dotnet run` . Du bör se alla digitala dubbla i den här instansen i utdata.

## <a name="complete-code-example"></a>Fullständigt kod exempel

I den här kursen har du en komplett klient-app som kan utföra grundläggande åtgärder mot digitala Azure-enheter. Den fullständiga koden för programmet i *program. cs* visas i listan nedan:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs":::

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört den här självstudien kan du välja vilka resurser du vill ta bort, beroende på vad du vill göra härnäst.

* **Om du planerar att fortsätta till nästa självstudie** kan du återanvända den instans som används i den här självstudien i nästa. Du kan behålla Azures digitala dubbla resurser som du konfigurerar här och hoppa över resten av det här avsnittet.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Du kanske också vill ta bort projektmappen från den lokala datorn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat ett .NET-konsol klient program från grunden. Du har skrivit kod för den här klient appen för att utföra grundläggande åtgärder på en digital Azure-instans.

Fortsätt till nästa självstudie för att utforska de saker du kan göra med en sådan exempel klient app: 

> [!div class="nextstepaction"]
> [*Självstudie: utforska grunderna med en exempel klient program*](tutorial-command-line-app.md)
