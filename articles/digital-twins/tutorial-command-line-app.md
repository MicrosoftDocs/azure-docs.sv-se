---
title: 'Självstudie: utforska grunderna med en exempel klient program'
titleSuffix: Azure Digital Twins
description: Självstudie för att utforska de digitala Azure-distributionerna med ett exempel på kommando rads program
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: db85bf19c1b040fea1ed2f5d6dcf904288366c7f
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050246"
---
# <a name="tutorial-explore-azure-digital-twins-with-a-sample-client-app"></a>Självstudie: utforska digitala Azure-enheter med ett exempel på en klient

I den här självstudien introduceras ett exempel program som implementerar ett kommando rads klient program för att interagera med en digital Azure-instans. Klient programmet liknar den som skrevs i [*Självstudier: koda en klient app*](tutorial-code.md).

Du kan använda det här exemplet för att utföra viktiga Azure Digital-åtgärder, till exempel överföra modeller, skapa och ändra dubbla och skapa relationer. Du kan också titta på koden för exemplet för att lära dig om Azures digitala dubbla API: er och öva på att implementera dina egna kommandon genom att ändra det exempel projekt som du vill.

I den här självstudien kommer du att...
> [!div class="checklist"]
> * Konfigurera en digital Azure-instans
> * Konfigurera det exempel kommando rads program som ska samverka med instansen
> * Använd kommando rads appen för att utforska Azures digitala dubbla, inklusive **modeller**, **digitala dubbla**, **relationer** och **frågor**

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>Utforska med exempel lösningen

Nu när instansen och exempel appen har kon figurer ATS använder du exempelprojektet och en kod i förväg skriven exempel kod för att bygga ut och utforska en grundläggande Azure digital-lösning. De viktigaste lösnings komponenterna är **modeller**, **digitala, dubbla** och **relationer**, vilket resulterar i ett fråge bara, **dubbel diagram** över en miljö.

### <a name="model-a-physical-environment-with-dtdl"></a>Modellera en fysisk miljö med DTDL

Det första steget i att skapa en Azure digital-lösning med dubbla lösningar är att definiera dubbla [**modeller**](concepts-models.md) för din miljö. 

Modeller liknar klasser i objektorienterade programmeringsspråk. de ger användardefinierade mallar för [digitala dubbla](concepts-twins-graph.md) och kan följa och instansiera senare. De är skrivna i ett JSON-liknande språk som kallas **DTDL (Digital Endefinierad Definition Language)** och kan definiera ett dubbelt *Egenskaper*, *telemetri*, *relationer* och *komponenter*.

> [!NOTE]
> DTDL kan också användas för definition av *kommandon* på digitala dubbla. Men det finns för närvarande inte stöd för kommandon i Azures digitala dubbla-tjänster.

I Visual Studio-fönstret där _**AdtE2ESample**_ -projektet är öppet använder du fönstret *Solution Explorer* för att navigera till mappen *AdtSampleApp\SampleClientApp\Models* . Den här mappen innehåller exempel modeller.

Välj *Room.jspå* för att öppna den i redigerings fönstret och ändra den på följande sätt:

1. **Uppdatera versions numret** för att indikera att du tillhandahåller en mer uppdaterad version av den här modellen. Gör detta genom att ändra *1* i slutet av `@id` värdet till *2*. Alla tal som är större än det aktuella versions numret fungerar också.
1. **Redigera en egenskap**. Ändra namnet på `Humidity` egenskapen till *HumidityLevel* (eller något annat om du vill. Om du använder något annat än *HumidityLevel*, kom ihåg vad du använde och fortsätta att använda det i stället för *HumidityLevel* i hela kursen.
1. **Lägg till en egenskap**. Under den `HumidityLevel` egenskap som slutar på rad 15 klistrar du in följande kod för att lägga till en `RoomName` egenskap till rummet:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Lägg till en relation**. Under den `RoomName` egenskap som du just har lagt till klistrar du in följande kod för att lägga till möjligheten för den här typen av dubbla till formulär som *innehåller* relationer med andra dubbla:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

När du är färdig ska den uppdaterade modellen matcha detta:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Se till att spara filen innan du fortsätter.

> [!TIP]
> Om du vill prova att skapa en egen modell kan du klistra in *rums* modell koden i en ny fil som du sparar med tillägget *. JSON* i mappen *AdtSampleApp\SampleClientApp\Models* . Sedan kan du experimentera med att lägga till egenskaper och relationer som representerar vad du vill. Du kan också titta på de andra exempel modellerna i den här mappen för idéer.

> [!TIP] 
> Det finns ett språk-oberoende [DTDL-verifierings exempel](/samples/azure-samples/dtdl-validator/dtdl-validator) som du kan använda för att kontrol lera modell dokument för att kontrol lera att DTDL är giltig. Det bygger på DTDL parser-biblioteket, som du kan läsa mer om i [*instruktion: parsa och validera modeller*](how-to-parse-models.md).

### <a name="get-started-with-the-command-line-app"></a>Kom igång med kommando rads appen

Nu när du har definierat en modell kan de återstående stegen använda exempel appen för att interagera med din Azure Digital-instansen. Kör projektet med den här knappen i verktygsfältet:

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="Start knappen i Visual Studio (SampleClientApp-projekt)":::

Ett konsol fönster öppnas, utför autentisering och vänta på ett kommando. 
* Autentiseringen hanteras via webbläsaren: din standard webbläsare öppnas med en autentiserings-prompt. Använd den här frågan för att logga in med dina Azure-autentiseringsuppgifter. Sedan kan du stänga webbläsarens flik eller fönster.

Här är en skärm bild av hur projekt konsolen ser ut:

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="Välkomst meddelande från kommando rads appen":::

> [!TIP]
> En lista över alla möjliga kommandon som du kan använda med det här projektet får du genom `help` att skriva i projekt konsolen och trycka på RETUR.
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="Utdata från hjälp kommandot":::

Se till att projekt konsolen körs för resten av stegen i den här självstudien.

#### <a name="upload-models-to-azure-digital-twins"></a>Ladda upp modeller till Azure Digitals dubbla

När du har utformat modeller måste du ladda upp dem till din Azure Digital-instansen. Detta konfigurerar din Azure Digitals dubbla tjänst instans med din egen anpassade domän vokabulär. När du har laddat upp modellerna kan du skapa dubbla instanser som använder dem.

I fönstret projekt konsol kör du följande kommando för att ladda upp din uppdaterade *rums* modell och en *våningsplan* som du också använder i nästa avsnitt för att skapa olika typer av sammanslagna.

```cmd/sh
CreateModels Room Floor
```

Utdatan anger att modellerna har skapats.

> [!TIP]
> Om du har skapat en egen modell tidigare kan du också ladda upp den här genom att lägga till dess fil namn (du kan lämna tillägget) till `Room Floor` listan i kommandot ovan.

Kontrol lera att modellerna har skapats genom att köra kommandot `GetModels true` . Detta kommer att fråga Azure Digitals-instansen för alla modeller som har laddats upp och skriva ut all information. Leta efter den redigerade *rums* modellen i resultaten:

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="Resultat av GetModels, som visar den uppdaterade rums modellen":::

#### <a name="errors"></a>Fel

Exempel programmet hanterar även fel från tjänsten. 

Kör `CreateModels` kommandot på nytt för att försöka ladda upp en av samma modeller som du just har laddat upp, under en andra tid:

```cmd/sh
CreateModels Room
```

Eftersom det inte går att skriva över modeller kommer det nu att returnera ett tjänst fel.
Information om hur du tar bort befintliga modeller finns i [*så här gör du: Hantera anpassade modeller*](how-to-manage-model.md).
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

### <a name="create-digital-twins"></a>Skapa digitala dubbla

Nu när vissa modeller har laddats upp till din Azure Digital-instansen, kan du skapa [**digitala**](concepts-twins-graph.md) delar baserade på modell definitionerna. Digitala delar representerar entiteterna i din affärs miljö – saker som sensorer i en grupp, rum i en byggnad eller lampor i en bil. 

Om du vill skapa en digital-delad använder du `CreateDigitalTwin` kommandot. Du måste referera till modellen som den dubbla är baserad på och kan även definiera startvärden för alla egenskaper i modellen. Du behöver inte skicka någon Relations information i det här skedet.

Kör den här koden i projekt konsolen som körs för att skapa flera dubbla, baserat på den *rums* modell som du uppdaterade tidigare och en annan modell, *våning*. Kom ihåg att *rummet* har tre egenskaper, så du kan ange argument med de ursprungliga värdena för dessa.

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> Om du har överfört din egen modell tidigare kan du prova med att göra ditt eget `CreateDigitalTwin` kommando baserat på kommandona ovan för att lägga till en annan modell typ.

Utdata från de här kommandona ska ange att de dubbla värdena har skapats. 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="Utdrag från resultaten av CreateDigitalTwin-kommandon, som visar floor0, floor1, room0 och room1":::

Du kan också kontrol lera att de dubblarna har skapats genom att köra `Query` kommandot. Det här kommandot frågar din Azure Digital-instansen för alla digitala dubbla som den innehåller. Leta efter *floor0*, *floor1*, *room0* och *room1* -dubbla i resultaten.

#### <a name="modify-a-digital-twin"></a>Ändra en digital delad

Du kan också ändra egenskaperna för en som du har skapat. Försök att köra det här kommandot för att ändra *room0*-RoomName från *room0* till *PresidentialSuite*:

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

Resultatet bör indikera att den dubbla uppdateringen har uppdaterats.

Du kan också kontrol lera genom att köra det här kommandot för att se *room0* information:

```cmd/sh
GetDigitalTwin room0
```

Resultatet ska återspegla det uppdaterade namnet.

> [!NOTE]
> Den underliggande REST API använder JSON-korrigering för att definiera uppdateringar till en dubbel. Kommando rads programmet visar det här formatet, så att du kan experimentera med vad underliggande API: er faktiskt förväntar sig.

### <a name="create-a-graph-by-adding-relationships"></a>Skapa en graf genom att lägga till relationer

Sedan kan du skapa några **relationer** mellan dessa dubbla, för att ansluta dem till ett [**dubbel diagram**](concepts-twins-graph.md). Dubbla grafer används för att representera en hel miljö. 

Om du vill lägga till en relation använder du `CreateRelationship` kommandot. Ange den dubbla som relationen kommer från, typen av relation som ska läggas till och den dubbla som relationen ansluter till. Ange slutligen ett namn (ID) för relationen.

Kör följande kod för att lägga till en "innehåller"-relation från var och en av de *golv* som du skapade tidigare till ett motsvarande *rum* med dubbla. Observera att det måste finnas en *contains* -relation definierad i *golv* modellen för att det ska vara möjligt.

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

Utdata från de här kommandona bekräftar att relationerna har skapats:

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="Utdrag från resultaten av CreateRelationship-kommandon, som visar relationship0 och relationship1":::

Du kan också kontrol lera relationerna med något av följande kommandon, som frågar relationerna i din Azure Digital-instansen.
* Om du vill se alla relationer som kommer från varje våning (som visar relationerna från ena sidan),
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* Om du vill se alla relationer som kommer till varje rum (som visar relationen från den andra sidan),
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* För att fråga efter dessa relationer individuellt, 
    ```cmd/sh
    GetRelationship floor0 relationship0
    GetRelationship floor1 relationship1
    ```

De dubbla och relationer som du har skapat i den här självstudien utgör följande konceptuella diagram:

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="Ett diagram som visar floor0 som är anslutna via relationship0 till room0 och floor1 som är anslutna via relationship1 till room1" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>Fråga den dubbla grafen för att svara på frågor om miljön

En huvud funktion i Azure Digitals flätas är möjligheten att [fråga](concepts-query-language.md) ditt dubbla diagram enkelt och effektivt att besvara frågor om din miljö. Kör följande kommandon i projekt konsolen som körs för att få en uppfattning om vad det är som helst.

* **Vilka är alla entiteter i min miljö som representeras i Azure Digitals dubbla?** (fråga alla)

    ```cmd/sh
    Query
    ```

    På så sätt kan du snabbt ta en titt på din miljö och se till att allt är representerat på det sätt som du vill att det ska vara i Azure Digitals. Resultatet av detta är en utmatning som innehåller varje digital enhet med information. Här är ett utdrag:

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="Partiella resultat av en delad fråga som visar room0 och floor1":::

    >[!NOTE]
    >Kommandot `Query` utan några ytterligare argument är detsamma som i exempelprojektet `Query SELECT * FROM DIGITALTWINS` . Om du vill fråga alla dubbla i din instans med hjälp av [fråge-API: erna](/rest/api/digital-twins/dataplane/query) eller [CLI-kommandona](how-to-use-cli.md)använder du frågan längre (slutförd).

* **Vad är alla rum i min miljö?** (fråga efter modell)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Du kan begränsa frågan till flera av en viss typ, för att få mer specifik information om vad som visas. Resultatet av detta är *room0* och *room1*, men visar **inte** *floor0* eller *floor1* (eftersom de är golv, inte rum).
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="Resultat av modell frågan, som endast visar room0 och room1":::

* **Vilka är alla rum på *floor0*?** (fråga efter relation)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Du kan fråga baserat på relationer i diagrammet för att få information om hur dubbla anslutningar är anslutna eller begränsa din fråga till ett visst område. Endast *room0* är på *floor0*, så det är det enda rummet i resultatet.

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="Resultat av Relations frågan, som visar room0":::

* **Vad är alla dubbla i min miljö med en temperatur över 75?** (fråga efter egenskap)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Du kan fråga diagrammet utifrån egenskaper för att besvara en rad olika frågor, inklusive att hitta avvikande extrem värden i din miljö som kan behöva åtgärdas. Andra jämförelse operatorer ( *<* ,, *>* *=* eller *! =*) stöds också. *room1* visas i resultatet, eftersom det har en temperatur på 80.

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="Resultat för egenskaps fråga, som endast visar room1":::

* **Vilka är alla rum på *floor0* med en temperatur över 75?** (sammansatt fråga)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Du kan också kombinera de tidigare frågorna som du skulle göra i SQL, med kombinations operatorer som `AND` , `OR` , `NOT` . Den här frågan använder `AND` för att göra den föregående frågan om dubbla temperaturer mer detaljerad. Resultatet innehåller nu bara rum med temperaturer över 75 som finns på *floor0*, vilket i det här fallet inte är något av dem. Resultat uppsättningen är tom.

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="Resultat av sammansatt fråga som visar inga resultat":::

## <a name="clean-up-resources"></a>Rensa resurser

Projektet i den här självstudien utgör grunden för nästa självstudie, [*Självstudier: ansluta en komplett lösning*](tutorial-end-to-end.md). Om du planerar att fortsätta till nästa självstudie kan du behålla de resurser som du har ställt in här för att fortsätta använda den här Azure Digital-instansen och den konfigurerade exempel appen.
* I det här fallet kan du använda exempel appens `DeleteAllTwins` och- `DeleteAllModels` kommandon för att rensa de dubbla och modellerna i din instans. Detta ger dig en ren arbets gång för nästa självstudie.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Slutligen tar du bort exempel mappen Project som du laddade ned till din lokala dator.

## <a name="next-steps"></a>Nästa steg 

I den här självstudien kommer du igång med Azure Digitals dubbla, genom att konfigurera en instans och ett klient program som interagerar med instansen. Du använde klient appen för att utforska Azure Digitals, skapa modeller, digitala sammanflätade och relationer. Du körde också några frågor om lösningen, för att få en uppfattning om vilka typer av frågor som Azure Digitals, kan svara på en miljö.

Fortsätt till nästa självstudie om du vill använda exempel kommando rads appen i kombination med andra Azure-tjänster för att slutföra ett data drivet, slut punkt till slut punkts scenario:
> [!div class="nextstepaction"]
> [*Självstudie: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md)