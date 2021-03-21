---
title: 'Självstudie: skapa en graf i Azure Digitals flätas (klient program)'
titleSuffix: Azure Digital Twins
description: Självstudie för att bygga ett digitalt Azure-scenario med ett exempel på kommando rads program
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c18366fd4bc510f32ac0ef255b27709797a3b626
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493720"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Självstudie: skapa ett digitalt Azure-diagram med en exempel klient

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

I den här självstudien skapar du en graf i Azure Digitals, med modeller, dubbla och relationer. Verktyget för den här självstudien är ett **exempel på kommando rads klient program** för interaktion med en digital Azure-instans. Klient programmet liknar den som skrevs i [*Självstudier: koda en klient app*](tutorial-code.md).

Du kan använda det här exemplet för att utföra viktiga Azure Digital-åtgärder, till exempel överföra modeller, skapa och ändra dubbla och skapa relationer. Du kan också titta på [koden för exemplet](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) för att lära dig om Azures digitala dubbla API: er och öva på att implementera dina egna kommandon genom att ändra det exempel projekt som du vill.

I den här självstudien kommer du att...
> [!div class="checklist"]
> * Modellera en miljö
> * Skapa digitala tvillingar
> * Lägga till relationer för att skapa ett diagram
> * Fråga grafen för att besvara frågor

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>Kör exempelprojektet

Nu när appen och autentiseringen har kon figurer ATS kör du projektet med den här knappen i verktygsfältet:

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Skärm bild av Start knappen i Visual Studio (SampleClientApp-projekt)." lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Ett konsol fönster öppnas, utför autentisering och vänta på ett kommando. 
* Autentiseringen hanteras via webbläsaren: din standard webbläsare öppnas med en autentiserings-prompt. Använd den här frågan för att logga in med dina Azure-autentiseringsuppgifter. Sedan kan du stänga webbläsarens flik eller fönster.

Här är en skärm bild av hur projekt konsolen ser ut:

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Skärm bild av välkomst meddelandet från kommando rads appen." lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> En lista över alla möjliga kommandon som du kan använda med det här projektet får du genom `help` att skriva i projekt konsolen och trycka på RETUR.

Se till att projekt konsolen körs för resten av stegen i den här självstudien.

## <a name="model-a-physical-environment-with-dtdl"></a>Modellera en fysisk miljö med DTDL

Nu när Azure Digital-instansen och exempel appen har kon figurer ATS kan du börja skapa ett diagram över ett scenario. 

Det första steget i att skapa en Azure digital-lösning med dubbla lösningar är att definiera dubbla [**modeller**](concepts-models.md) för din miljö. 

Modeller liknar klasser i objektorienterade programmeringsspråk. de ger användardefinierade mallar för [digitala dubbla](concepts-twins-graph.md) och kan följa och instansiera senare. De är skrivna i ett JSON-liknande språk som kallas **DTDL (Digital Endefinierad Definition Language)** och kan definiera ett dubbelt *Egenskaper*, *telemetri*, *relationer* och *komponenter*.

> [!NOTE]
> DTDL kan också användas för definition av *kommandon* på digitala dubbla. Men det finns för närvarande inte stöd för kommandon i Azures digitala dubbla-tjänster.

I Visual Studio-fönstret där _**AdtE2ESample**_ -projektet är öppet använder du fönstret *Solution Explorer* för att navigera till *mappen AdtSampleApp\SampleClientApp\Models*. Den här mappen innehåller exempel modeller.

Välj *Room.jspå* för att öppna den i redigerings fönstret och ändra den på följande sätt:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Ladda upp modeller till Azure Digitals dubbla

När du har utformat modeller måste du ladda upp dem till din Azure Digital-instansen. Detta konfigurerar din Azure Digitals dubbla tjänst instans med din egen anpassade domän vokabulär. När du har laddat upp modellerna kan du skapa dubbla instanser som använder dem.

1. I fönstret projekt konsol kör du följande kommando för att ladda upp din uppdaterade *rums* modell och en *våningsplan* som du också använder i nästa avsnitt för att skapa olika typer av sammanslagna.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    Utdatan anger att modellerna har skapats.

1. Kontrol lera att modellerna har skapats genom att köra kommandot `GetModels true` . Detta kommer att fråga Azure Digitals-instansen för alla modeller som har laddats upp och skriva ut all information. Leta efter den redigerade *rums* modellen i resultaten:

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Skärm bild av resultatet från GetModels, som visar den uppdaterade rums modellen." lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>Fel

Exempel programmet hanterar även fel från tjänsten. 

Kör `CreateModels` kommandot på nytt för att försöka ladda upp en av samma modeller som du just har laddat upp, under en andra tid:

```cmd/sh
CreateModels Room
```

Eftersom det inte går att skriva över modeller kommer det nu att returnera ett tjänst fel.
Information om hur du tar bort befintliga modeller finns [*här: hantera DTDL-modeller*](how-to-manage-model.md).
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

## <a name="create-digital-twins"></a>Skapa digitala tvillingar

Nu när vissa modeller har laddats upp till din Azure Digital-instansen, kan du skapa [**digitala**](concepts-twins-graph.md) delar baserade på modell definitionerna. Digitala delar representerar entiteterna i din affärs miljö – saker som sensorer i en grupp, rum i en byggnad eller lampor i en bil. 

Om du vill skapa en digital-delad använder du `CreateDigitalTwin` kommandot. Du måste referera till modellen som den dubbla är baserad på och kan även definiera startvärden för alla egenskaper i modellen. Du behöver inte skicka någon Relations information i det här skedet.

1. Kör den här koden i projekt konsolen som körs för att skapa flera dubbla, baserat på den *rums* modell som du uppdaterade tidigare och en annan modell, *våning*. Kom ihåg att *rummet* har tre egenskaper, så du kan ange argument med de ursprungliga värdena för dessa. (Det är valfritt att initiera egenskaps värden i allmänhet, men de behövs för den här självstudien.)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    Utdata från de här kommandona ska ange att de dubbla värdena har skapats. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Skärm bild som visar ett utdrag från resultatet av CreateDigitalTwin-kommandon, som innehåller floor0, floor1, room0 och room1." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. Du kan kontrol lera att de dubblarna har skapats genom att köra `Query` kommandot. Det här kommandot frågar din Azure Digital-instansen för alla digitala dubbla som den innehåller. Leta efter *room0*, *room1*, *floor0* och *floor1* -dubbla i resultaten.

### <a name="modify-a-digital-twin"></a>Ändra en digital delad

Du kan också ändra egenskaperna för en som du har skapat. 

> [!NOTE]
> Den underliggande REST API använder [JSON](http://jsonpatch.com/) -format för att definiera uppdateringar till en dubbel. Kommando rads appen använder också det här formatet för att ge en sann upplevelse med vad de underliggande API: erna förväntar sig.

1. Kör det här kommandot om du vill ändra *room0*-RoomName från *room0* till *PresidentialSuite*:
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    Resultatet bör indikera att den dubbla uppdateringen har uppdaterats.

1. Du kan kontrol lera att uppdateringen är klar genom att köra det här kommandot för att se *room0* information:

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    Resultatet ska återspegla det uppdaterade namnet.


## <a name="create-a-graph-by-adding-relationships"></a>Skapa en graf genom att lägga till relationer

Sedan kan du skapa några **relationer** mellan dessa dubbla, för att ansluta dem till ett [**dubbel diagram**](concepts-twins-graph.md). Dubbla grafer används för att representera en hel miljö. 

De typer av relationer som du kan skapa från en till en annan definieras i de [modeller](#model-a-physical-environment-with-dtdl) som du laddade upp tidigare. [Modell definitionen för *basyta*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) anger att golv kan ha en typ av relation som kallas *innehåller*. Detta gör det möjligt att skapa en relation av typen *contains* från varje *golv* till motsvarande rum som den innehåller.

Om du vill lägga till en relation använder du `CreateRelationship` kommandot. Ange den dubbla som relationen kommer från, typen av relation och den dubbla som relationen ansluter till. Till sist ska du ge relationen ett unikt ID.

1. Kör följande kod för att lägga till en "innehåller"-relation från var och en av de *golv* som du skapade tidigare till ett motsvarande *rum* med dubbla. Relationerna heter *relationship0* och *relationship1*.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >Relationen *contains* i [ *golv* modellen](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) definierades också med två sträng egenskaper, `ownershipUser` och `ownershipDepartment` du kan också ange argument med de ursprungliga värdena för dessa när du skapar relationerna.
    > Här är en alternativ version av kommandot ovan för att skapa *relationship0* som också anger startvärden för dessa egenskaper:
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    Utdata från de här kommandona bekräftar att relationerna har skapats:
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Skärm bild av ett utdrag från resultatet av CreateRelationship-kommandona, som innehåller relationship0 och relationship1." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. Du kan kontrol lera relationerna med något av följande kommandon, som frågar relationerna i din Azure Digital-instansen.
    * Om du vill se alla relationer som kommer från varje våning (som visar relationerna från ena sidan):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * För att se alla relationer som kommer till varje rum (som visar relationen från den andra sidan):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * För att leta efter dessa relationer individuellt, efter ID:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

De dubbla och relationer som du har skapat i den här självstudien utgör följande konceptuella diagram:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Ett diagram som visar ett konceptuellt diagram. floor0 är ansluten via relationship0 till room0 och floor1 är ansluten via relationship1 till room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Fråga den dubbla grafen för att svara på frågor om miljön

En huvud funktion i Azure Digitals flätas är möjligheten att [fråga](concepts-query-language.md) ditt dubbla diagram enkelt och effektivt att besvara frågor om din miljö. 

Kör följande kommandon i projekt konsolen som körs för att svara på frågor om exempel miljön.

1. **Vilka är alla entiteter från min miljö som representeras i Azure Digitals dubbla?** (fråga alla)

    ```cmd/sh
    Query
    ```

    På så sätt kan du snabbt ta en titt på din miljö och se till att allt är representerat på det sätt som du vill att det ska vara i Azure Digitals. Resultatet av detta är en utmatning som innehåller varje digital enhet med information. Här är ett utdrag:

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="Skärm bild som visar ett partiellt resultat från den dubbla frågan, inklusive room0 och floor1.":::

    >[!NOTE]
    >Kommandot `Query` utan några ytterligare argument är detsamma som i exempelprojektet `Query SELECT * FROM DIGITALTWINS` . Om du vill fråga alla dubbla i din instans med hjälp av [fråge-API: erna](/rest/api/digital-twins/dataplane/query) eller [CLI-kommandona](how-to-use-cli.md)använder du frågan längre (slutförd).

1. **Vad är alla rum i min miljö?** (fråga efter modell)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    Du kan begränsa frågan till flera av en viss typ, för att få mer specifik information om vad som visas. Resultatet av detta är *room0* och *room1*, men visar **inte** *floor0* eller *floor1* (eftersom de är golv, inte rum).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Skärm bild av resultatet från modell frågan, som endast visar room0 och room1.":::

1. **Vilka är alla rum på *floor0*?** (fråga efter relation)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    Du kan fråga baserat på relationer i diagrammet för att få information om hur dubbla anslutningar är anslutna eller begränsa din fråga till ett visst område. Endast *room0* är på *floor0*, så det är det enda rummet i resultatet.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="Skärm bild av resultatet från Relations frågan som visar room0.":::

1. **Vad är alla dubbla i min miljö med en temperatur över 75?** (fråga efter egenskap)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    Du kan fråga diagrammet utifrån egenskaper för att besvara en rad olika frågor, inklusive att hitta avvikande extrem värden i din miljö som kan behöva åtgärdas. Andra jämförelse operatorer ( *<* ,, *>* *=* eller *! =*) stöds också. *room1* visas i resultatet, eftersom det har en temperatur på 80.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Skärm bild av resultatet från egenskaps frågan, som endast visar room1.":::

1. **Vilka är alla rum på *floor0* med en temperatur över 75?** (sammansatt fråga)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    Du kan också kombinera de tidigare frågorna som du skulle göra i SQL, med kombinations operatorer som `AND` , `OR` , `NOT` . Den här frågan använder `AND` för att göra den föregående frågan om dubbla temperaturer mer detaljerad. Resultatet innehåller nu bara rum med temperaturer över 75 som finns på *floor0*, vilket i det här fallet inte är något av dem. Resultat uppsättningen är tom.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Skärm bild av resultatet från den sammansatta frågan och visar inga resultat." lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört den här självstudien kan du välja vilka resurser du vill ta bort, beroende på vad du vill göra härnäst.

* **Om du planerar att fortsätta till nästa självstudie** kan du behålla de resurser som du har ställt in här för att fortsätta använda den här Azure Digital-instansen och den konfigurerade exempel appen för nästa självstudie

* **Om du vill fortsätta använda Azure Digitals-instansen, men ta bort alla dess modeller, delar och relationer**, kan du använda exempel appens `DeleteAllTwins` och `DeleteAllModels` -kommandon för att ta bort de dubbla och modellerna i din instans.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Du kanske också vill ta bort projektmappen från den lokala datorn.

## <a name="next-steps"></a>Nästa steg 

I den här självstudien kommer du igång med Azure Digitals dubbla, genom att skapa en graf i din instans med ett exempel klient program. Du har skapat modeller, digitala garn och relationer för att bilda ett diagram. Du körde också några frågor i grafen för att få en uppfattning om vilka typer av frågor som Azure Digitals kan svara på en miljö.

Fortsätt till nästa självstudie för att kombinera Azure Digitals dubbla steg med andra Azure-tjänster för att slutföra ett data drivet, komplett scenario:
> [!div class="nextstepaction"]
> [*Självstudie: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md)