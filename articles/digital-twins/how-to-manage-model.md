---
title: Hantera anpassade modeller
titleSuffix: Azure Digital Twins
description: Se hur du skapar, redigerar och tar bort en modell i Azures digitala dubbla.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c193565035435b29f77177086c535fae0903ccd4
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737786"
---
# <a name="manage-azure-digital-twins-models"></a>Hantera Azure Digitals dubbla modeller

Du kan hantera de [modeller](concepts-models.md) som Azure Digitals-instansen är medveten om att använda [**DigitalTwinModels-API: er**](/rest/api/digital-twins/dataplane/models), [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)eller [Azure Digitals flätat CLI](how-to-use-cli.md). 

Hanterings åtgärder omfattar överföring, validering, hämtning och borttagning av modeller. 

## <a name="prerequisites"></a>Krav

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

## <a name="ways-to-manage-models"></a>Sätt att hantera modeller

[!INCLUDE [digital-twins-ways-to-manage.md](../../includes/digital-twins-ways-to-manage.md)]

## <a name="create-models"></a>Skapa modeller

Modeller för digitala Azure-dubblare skrivs i DTDL och sparas som *. JSON* -filer. Det finns också ett [DTDL-tillägg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) som är tillgängligt för [Visual Studio Code](https://code.visualstudio.com/), som innehåller syntax för autentisering och andra funktioner som underlättar skrivning av DTDL-dokument.

Överväg ett exempel där ett sjukhus vill kunna återge sina rum digitalt. Varje rum innehåller en smart SOAP-dispens för att övervaka hand tvättning och sensorer för att övervaka trafik genom rummet.

Det första steget i lösningen är att skapa modeller som visar aspekter av sjukhus. Ett patient rum i det här scenariot kan se ut så här:

:::code language="json" source="~/digital-twins-docs-samples/models/PatientRoom.json":::

> [!NOTE]
> Detta är en exempel text för en. JSON-fil där en modell definieras och sparas, som ska laddas upp som en del av ett klient projekt. REST API-anropet använder å andra sidan en matris med modell definitioner som ovan (som är mappad till en `IEnumerable<string>` i .NET SDK). Om du vill använda den här modellen i REST API direkt omger du den med hakparenteser.

Den här modellen definierar ett namn och ett unikt ID för patient rummet och egenskaper för att representera antal besökare och status för manuell tvättning (dessa räknare kommer att uppdateras från rörelse sensorer och smarta SOAP-behållare och används tillsammans för att beräkna en *handwash procents ATS* -egenskap). Modellen definierar också en Relations- *hasDevices*, som kommer att användas för att ansluta alla [digitala](concepts-twins-graph.md) enheter baserat på den här *rums* modellen till de faktiska enheterna.

Efter den här metoden kan du gå vidare till för att definiera modeller för sjukhuss rapporter, zoner eller själva sjukhus.

### <a name="validate-syntax"></a>Validera syntax

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="upload-models"></a>Ladda upp modeller

När modeller har skapats kan du ladda upp dem till Azure Digitals-instansen.

När du är redo att ladda upp en modell kan du använda följande kodfragment:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Observera att `CreateModels` metoden accepterar flera filer i en enda transaktion. Här är ett exempel som illustrerar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModels_multi":::

Model-filer kan innehålla mer än en enskild modell. I det här fallet måste modellerna placeras i en JSON-matris. Ett exempel:

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Moon.json":::

Vid uppladdning verifieras Model-filer av tjänsten.

## <a name="retrieve-models"></a>Hämta modeller

Du kan visa och hämta modeller som är lagrade på din Azure Digital-instansen. 

Här är dina alternativ:
* Hämta en enskild modell
* Hämta alla modeller
* Hämta metadata och beroenden för modeller

Här följer några exempel på anrop:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

API-anrop för att hämta modeller med alla retur `DigitalTwinsModelData` objekt. `DigitalTwinsModelData` innehåller metadata om modellen som lagras i Azure Digitals-instansen, till exempel namn, DTMI och skapande datum för modellen. `DigitalTwinsModelData`Objektet kan också innehålla själva modellen. Beroende på parametrarna kan du därför använda Hämta anrop för att antingen hämta bara metadata (vilket är användbart i scenarier där du vill visa en GRÄNSSNITTs lista med tillgängliga verktyg, till exempel) eller hela modellen.

`RetrieveModelWithDependencies`Anropet returnerar inte bara den begärda modellen, utan även alla modeller som den begärda modellen är beroende av.

Modeller returneras inte nödvändigt vis i exakt det dokument formulär de överfördes. Azure Digitals dubblare garanterar bara att RETUR formuläret kommer att vara semantiskt likvärdigt. 

## <a name="update-models"></a>Uppdatera modeller

När en modell har laddats upp till din Azure Digital-instansen är hela modell gränssnittet oföränderligt. Det innebär att det inte finns någon traditionell "redigering" av modeller. Azure Digitals dubbla, tillåter inte heller åter överföring av samma modell.

Om du i stället vill göra ändringar i en modell, t. ex. uppdatering `displayName` eller `description` – hur du gör detta, så är att ladda upp en **nyare version** av modellen. 

### <a name="model-versioning"></a>Versionshantering för modell

Om du vill skapa en ny version av en befintlig modell börjar du med DTDL för den ursprungliga modellen. Uppdatera, Lägg till eller ta bort de fält som du vill ändra.

Markera sedan detta som en nyare version av modellen genom att uppdatera `id` fältet i modellen. Det sista avsnittet i modell-ID: t, efter `;` , representerar modell numret. Om du vill visa att det nu är en mer uppdaterad version av den här modellen ökar du talet i slutet av `id` värdet till ett tal som är större än det aktuella versions numret.

Till exempel om ditt tidigare modell-ID såg ut så här:

```json
"@id": "dtmi:com:contoso:PatientRoom;1",
```

version 2 av den här modellen kan se ut så här:

```json
"@id": "dtmi:com:contoso:PatientRoom;2",
```

Ladda sedan upp den nya versionen av modellen till din instans. 

Den här versionen av modellen kommer sedan att vara tillgänglig i din instans för att användas för digitala dubbla. Tidigare versioner av modellen skrivs **inte** över, så flera versioner av modellen kan finnas i din instans tills du [tar bort dem](#remove-models).

### <a name="impact-on-twins"></a>Påverkan på dubbla

När du skapar en ny dubbla, eftersom den nya modell versionen och den gamla modell versionen finns, kan den nya, dubbla, använda antingen den nya versionen av modellen eller den äldre versionen.

Det innebär också att uppladdning av en ny version av en modell inte automatiskt påverkar befintliga dubbla. De befintliga dubbla är helt enkelt fortfarande instanser av den gamla modell versionen.

Du kan uppdatera de här befintliga dubblarna till den nya modell versionen genom att uppdatera dem, enligt beskrivningen i avsnittet [*Uppdatera en digital-enhets modell*](how-to-manage-twin.md#update-a-digital-twins-model) i *instruktionen så här: hantera digitala dubbla*. I samma korrigering måste du uppdatera båda **modell-ID: t** (till den nya versionen) och **alla fält som måste ändras på den dubbla för att den ska överensstämma med den nya modellen**.

## <a name="remove-models"></a>Ta bort modeller

Modeller kan också tas bort från tjänsten på ett av två sätt:
* **Avställning** : när en modell har inaktiverats kan du inte längre använda den för att skapa nya digitala dubbla. Befintliga digitala dubbla objekt som redan använder den här modellen påverkas inte, så du kan fortfarande uppdatera dem med saker som egenskaps ändringar och lägga till eller ta bort relationer.
* **Borttagning** : modellen tas bort helt från lösningen. Alla dubbla som använder den här modellen är inte längre kopplade till någon giltig modell, så de behandlas som om de inte har någon modell alls. Du kan fortfarande läsa dessa dubbla, men du kan inte göra några uppdateringar på dem förrän de har omtilldelats till en annan modell.

Dessa är separata funktioner och de påverkar inte varandra, men de kan användas tillsammans för att ta bort en modell gradvis. 

### <a name="decommissioning"></a>Ställa

Här är koden för att inaktivera en modell:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DecommissionModel":::

En modells inaktive rings status ingår i `ModelData` posterna som returneras av modell hämtnings-API: erna.

### <a name="deletion"></a>Borttagning

Du kan ta bort alla modeller i din instans på samma gång, eller så kan du göra det på individuell basis.

Ett exempel på hur du tar bort alla modeller får du genom att hämta den exempel app som används i [*självstudien: utforska grunderna med ett exempel på ett klient program*](tutorial-command-line-app.md). *CommandLoop.cs* -filen gör detta i en `CommandDeleteAllModels` funktion.

Resten av det här avsnittet delar ned modell borttagning i närmare detalj, och visar hur du gör det för en enskild modell.

#### <a name="before-deletion-deletion-requirements"></a>Före borttagning: borttagnings krav

Modeller kan vanligt vis tas bort när som helst.

Undantaget är modeller som andra modeller är beroende av, antingen med en `extends` relation eller som en komponent. Om en *ConferenceRoom* -modell exempelvis utökar en *rums* modell och har en *ACUnit* -modell som en komponent, kan du inte ta bort *rummet* eller *ACUnit* förrän *ConferenceRoom* tar bort de respektive referenserna. 

Du kan göra detta genom att uppdatera den beroende modellen för att ta bort beroenden eller ta bort den beroende modellen helt.

#### <a name="during-deletion-deletion-process"></a>Under borttagning: borttagnings process

Även om en modell uppfyller kraven för att ta bort den direkt, kan det vara bra att gå igenom några steg för att undvika oönskade konsekvenser för de dubblare som ligger kvar. Här följer några steg som kan hjälpa dig att hantera processen:
1. Börja med att inaktivera modellen
2. Vänta några minuter för att se till att tjänsten har bearbetat några sammanställda begär Anden om att skapa en sista minut som skickats före inaktive ring
3. Fråga dubbla efter modell för att se alla dubbla som använder den nu-inaktiverade modellen
4. Ta bort de dubbla objekten om du inte längre behöver dem eller korrigera dem till en ny modell om det behövs. Du kan också välja att låta dem vara ensamma, vilket innebär att de blir sammanflätade utan modeller när modellen har tagits bort. Se nästa avsnitt för detta tillstånds konsekvenser.
5. Vänta några minuter och se till att ändringarna har percolated
6. Ta bort modellen 

Om du vill ta bort en modell använder du det här anropet:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="DeleteModel":::

#### <a name="after-deletion-twins-without-models"></a>Efter borttagning: sammanflätade utan modeller

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

#### <a name="after-deletion-re-uploading-a-model"></a>Efter borttagning: laddar upp en modell igen

När en modell har tagits bort kan du senare välja att ladda upp en ny modell med samma ID som den som du tog bort. Det här är vad som händer i detta fall.
* Från lösnings lagrets perspektiv är det samma som att ladda upp en helt ny modell. Tjänsten kommer inte ihåg att den gamla har laddats upp tidigare.   
* Om det finns några återstående dubbla i grafen som refererar till den borttagna modellen, är de inte längre överblivna. modell-ID: t är giltigt igen med den nya definitionen. Men om den nya definitionen för modellen skiljer sig från modell definitionen som har tagits bort, kan dessa dubbla, ha egenskaper och relationer som matchar den borttagna definitionen och som inte är giltiga med den nya.

Azures digitala dubblare förhindrar inte det här läget, så var noga med att korrigera korrigeringen på lämpligt sätt för att se till att de är giltiga via modell definitions växeln.

## <a name="next-steps"></a>Nästa steg

Se hur du skapar och hanterar digitala dubbla, baserade på dina modeller:
* [*Anvisningar: hantera digitala dubbla*](how-to-manage-twin.md)