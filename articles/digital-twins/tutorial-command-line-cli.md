---
title: 'Självstudie: skapa en graf i Azure Digitals flätas (CLI)'
titleSuffix: Azure Digital Twins
description: Självstudie för att bygga ett digitalt Azure-scenario med Azure CLI
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d155d0c4a18b254f66ff5fb58ea91dbee22d2c34
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496617"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Självstudie: skapa ett digitalt Azure-diagram med Azure CLI

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

I den här självstudien skapar du en graf i Azure Digitals, med modeller, dubbla och relationer. Verktyget för den här självstudien är [Azure Digitals-kommandot som har angetts för **Azure CLI**](how-to-use-cli.md). 

Du kan använda CLI-kommandona för att utföra viktiga Azure Digital-åtgärder, till exempel överföra modeller, skapa och ändra dubbla och skapa relationer. Du kan också titta i [referens dokumentationen för *AZ DT* -kommando](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest) för att se en fullständig uppsättning CLI-kommandon.

I den här självstudien kommer du att...
> [!div class="checklist"]
> * Modellera en miljö
> * Skapa digitala tvillingar
> * Lägga till relationer för att skapa ett diagram
> * Fråga grafen för att besvara frågor

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här självstudien måste du först uppfylla följande krav.

Om du inte har en Azure-prenumeration kan du **skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** innan du börjar.

### <a name="download-the-sample-models"></a>Ladda ned exempel modellerna

I självstudien används två fördefinierade modeller som ingår i C#-exempelprojektet från [slut punkt till slut punkt](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) för digitala Azure-enheter. Model-filerna finns här: 
* [*Room.jspå*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [*Floor.jspå*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

Hämta filerna på datorn genom att använda navigerings länkarna ovan och kopiera fil kroppar till lokala filer på datorn med samma namn (*Room.jspå* och *Floor.jspå*).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Konfigurera Cloud Shell-session
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Förbered en digital Azure-instans

Om du vill arbeta med digitala Azure-objekt i den här artikeln måste du först **Konfigurera en digital Azure-instans** och de behörigheter som krävs för att använda den. Om du redan har en Azure Digital-instans som har kon figurer ATS från föregående arbete kan du använda den instansen.

Annars följer du anvisningarna i [*instruktion: Konfigurera en instans och autentisering*](how-to-set-up-instance-cli.md). Instruktionerna innehåller också anvisningar för att kontrol lera att du har slutfört varje steg och att du är redo att gå vidare till att använda den nya instansen.

När du har konfigurerat Azure Digitals-instansen ska du anteckna följande värden som du behöver för att ansluta till instansen senare:
* instansens **_värdnamn_**
* den **Azure-prenumeration** som du använde för att skapa instansen. 

Du kan hämta båda värdena för din instans i resultatet av följande Azure CLI-kommando: 

```azurecli-interactive
az dt show -n <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Skärm bild av Cloud Shell webbläsarfönster som visar utdata från kommandot AZ DT show. HostName-fältet och prenumerations-ID: t (en del av ID-fältet) är markerade.":::

## <a name="model-a-physical-environment-with-dtdl"></a>Modellera en fysisk miljö med DTDL

Nu när CLI-och Azure Digitals-instansen har kon figurer ATS kan du börja skapa ett diagram över ett scenario. 

Det första steget i att skapa en Azure digital-lösning med dubbla lösningar är att definiera dubbla [**modeller**](concepts-models.md) för din miljö. 

Modeller liknar klasser i objektorienterade programmeringsspråk. de ger användardefinierade mallar för [digitala dubbla](concepts-twins-graph.md) och kan följa och instansiera senare. De är skrivna i ett JSON-liknande språk som kallas **DTDL (Digital Endefinierad Definition Language)** och kan definiera ett dubbelt *Egenskaper*, *telemetri*, *relationer* och *komponenter*.

> [!NOTE]
> DTDL kan också användas för definition av *kommandon* på digitala dubbla. Men det finns för närvarande inte stöd för kommandon i Azures digitala dubbla-tjänster.

Navigera på din dator till den *Room.jspå* filen som du skapade i avsnittet [krav](#prerequisites) . Öppna den i en kod redigerare och ändra den på följande sätt:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Ladda upp modeller till Azure Digitals dubbla

När du har utformat modeller måste du ladda upp dem till din Azure Digital-instansen. Detta konfigurerar din Azure Digitals dubbla tjänst instans med din egen anpassade domän vokabulär. När du har laddat upp modellerna kan du skapa dubbla instanser som använder dem.

1. Om du vill lägga till modeller med Cloud Shell måste du ladda upp dina modellvariabler till Cloud Shell lagrings utrymme så att filerna blir tillgängliga när du kör Cloud Shell kommandot som använder dem. Det gör du genom att välja ikonen "Ladda upp/ladda ned filer" och välja "Ladda upp".

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Skärm bild av Cloud Shell webbläsarfönster med val av uppladdnings ikonen.":::
    
    Navigera till *Room.jspå* filen på din dator och välj "öppna". Upprepa sedan det här steget för *Floor.jspå*.

1. Använd sedan kommandot [**AZ DT Model Create**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_create) enligt beskrivningen nedan för att ladda upp din uppdaterade *rums* modell till din Azure Digital-instansen. Det andra kommandot laddar upp en annan modell, *våning*, som du också använder i nästa avsnitt för att skapa olika typer av skärnings punkter.

    ```azurecli-interactive
    az dt model create -n <ADT_instance_name> --models Room.json
    az dt model create -n <ADT_instance_name> --models Floor.json
    ```
    
    Utdata från varje kommando visar information om en modell som har laddats upp.

    >[!TIP]
    >Du kan också ladda upp alla modeller i en katalog på samma tid genom att använda `--from-directory` alternativet för kommandot Skapa modell. Mer information finns i [valfria parametrar för *AZ DT Model Create*](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_create-optional-parameters).

1. Kontrol lera att modellerna har skapats med kommandot [**AZ DT Model List**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_list) som visas nedan. Då skrivs en lista över alla modeller som har överförts till den digitala Azure-instansen med fullständig information. 

    ```azurecli-interactive
    az dt model list -n <ADT_instance_name> --definition
    ```
    
    Leta efter den redigerade *rums* modellen i resultaten:
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Skärm bild av Cloud Shell visar resultatet av kommandot modell lista, som innehåller den uppdaterade rums modellen." lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>Fel

CLI hanterar även fel från tjänsten. 

Kör `az dt model create` kommandot på nytt för att försöka ladda upp en av samma modeller som du just har laddat upp, under en andra tid:

```azurecli-interactive
az dt model create -n <ADT_instance_name> --models Room.json
```

Eftersom modeller inte kan skrivas över returnerar detta nu felkoden `ModelIdAlreadyExists` .

## <a name="create-digital-twins"></a>Skapa digitala tvillingar

Nu när vissa modeller har laddats upp till din Azure Digital-instansen, kan du skapa [**digitala**](concepts-twins-graph.md) delar baserade på modell definitionerna. Digitala delar representerar entiteterna i din affärs miljö – saker som sensorer i en grupp, rum i en byggnad eller lampor i en bil. 

Om du vill skapa en digital-delad använder du kommandot [**AZ DT dubbla Create**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_create) . Du måste referera till modellen som den dubbla är baserad på och kan även definiera startvärden för alla egenskaper i modellen. Du behöver inte skicka någon Relations information i det här skedet.

1. Kör den här koden i Cloud Shell för att skapa flera dubbla, baserat på den *rums* modell som du uppdaterade tidigare och en annan modell, *våning*. Kom ihåg att *rummet* har tre egenskaper, så du kan ange argument med de ursprungliga värdena för dessa. (Det är valfritt att initiera egenskaps värden i allmänhet, men de behövs för den här självstudien.)

    ```azurecli-interactive
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > Om du använder Cloud Shell i PowerShell-miljön kan du behöva undanta citat tecken för att det ska gå att `--properties` parsa JSON-värdet korrekt. Med den här redigeringen, ser kommandona för att skapa rummet att se ut så här:
    >
    > ```azurecli-interactive
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > Detta visas i skärm bilden nedan.
    
    Utdata från varje kommando visar information om det har skapats dubbla (inklusive egenskaper för rummets dubbla som initierades med dem).

1. Du kan kontrol lera att de dubblarna har skapats med kommandot [**AZ DT dubbla Query**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_query) enligt nedan. Frågan som visas hittar alla digitala dubbla i din Azure Digital-instansen.
    
    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```
    
    Leta efter *room0*, *room1*, *floor0* och *floor1* -dubbla i resultaten. Här är ett utdrag som visar en del av resultatet av den här frågan.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Skärm bild av Cloud Shell visar partiellt resultat av en delad fråga, inklusive room0 och room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>Ändra en digital delad

Du kan också ändra egenskaperna för en som du har skapat. 

1. Kör det här [**AZ DT, dubbla uppdaterings**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_update) kommando för att ändra *room0*-RoomName från *room0* till *PresidentialSuite*:

    ```azurecli-interactive
    az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > Om du använder Cloud Shell i PowerShell-miljön kan du behöva undanta citat tecken för att det ska gå att `--json-patch` parsa JSON-värdet korrekt. Med den här redigeringen ser kommandot för att uppdatera den dubbla ser ut så här:
    >
    > ```azurecli-interactive
    > az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > Detta visas i skärm bilden nedan.
    
    Utdata från det här kommandot visar den dubblans aktuella information och du bör se det nya värdet för `RoomName` i resultatet.

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Skärm bild av Cloud Shell visar resultatet av kommandot Update, som innehåller en RoomName av PresidentialSuite." lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. Du kan kontrol lera att uppdateringen har slutförts genom att köra kommandot [**AZ DT dubbla show**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_show) för att se *room0* information:

    ```azurecli-interactive
    az dt twin show -n <ADT_instance_name> --twin-id room0
    ```
    
    Resultatet ska återspegla det uppdaterade namnet.

## <a name="create-a-graph-by-adding-relationships"></a>Skapa en graf genom att lägga till relationer

Sedan kan du skapa några **relationer** mellan dessa dubbla, för att ansluta dem till ett [**dubbel diagram**](concepts-twins-graph.md). Dubbla grafer används för att representera en hel miljö. 

De typer av relationer som du kan skapa från en till en annan definieras i de [modeller](#model-a-physical-environment-with-dtdl) som du laddade upp tidigare. [Modell definitionen för *basyta*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) anger att golv kan ha en typ av relation som kallas *innehåller*. Detta gör det möjligt att skapa en relation av typen *contains* från varje *golv* till motsvarande rum som den innehåller.

Om du vill lägga till en relation använder du kommandot [**AZ DT dubbla Relations skapande**](/cli/azure/ext/azure-iot/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_relationship_create) . Ange den dubbla som relationen kommer från, typen av relation och den dubbla som relationen ansluter till. Till sist ska du ge relationen ett unikt ID. Om en relation har definierats för att ha egenskaper kan du även initiera Relations egenskaperna i det här kommandot.

1. Kör följande kod för att lägga till en *contains*-Type-relation från var och en av de *golv* som du skapade tidigare till motsvarande *rum* , dubbla. Relationerna heter *relationship0* och *relationship1*.

    ```azurecli-interactive
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >Relationen *contains* i [ *våningsplanet*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) har också definierats med två egenskaper, `ownershipUser` och `ownershipDepartment` du kan också ange argument med de ursprungliga värdena för dessa när du skapar relationerna.
    > Om du vill skapa en relation med dessa egenskaper initierad lägger du till `--properties` alternativet till något av ovanstående kommandon, så här:
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > Om du använder Cloud Shell i PowerShell-miljön kan du behöva undanta citat tecken för att det ska gå att `--properties` parsa JSON-värdet korrekt.
    
    Utdata från varje kommando visar information om den korrekt skapade relationen.

1. Du kan kontrol lera relationerna med något av följande kommandon, som frågar relationerna i din Azure Digital-instansen.
    * Om du vill se alla relationer som kommer från varje våning (som visar relationerna från ena sidan):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor0
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor1
        ```
    * För att se alla relationer som kommer till varje rum (som visar relationen från den andra sidan):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list -n <ADT_instance_name> --twin-id room1 --incoming
        ```
    * För att leta efter dessa relationer individuellt, efter ID:
        ```azurecli-interactive
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

De dubbla och relationer som du har skapat i den här självstudien utgör följande konceptuella diagram:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Ett diagram som visar ett konceptuellt diagram. floor0 är ansluten via relationship0 till room0 och floor1 är ansluten via relationship1 till room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Fråga den dubbla grafen för att svara på frågor om miljön

En huvud funktion i Azure Digitals flätas är möjligheten att [fråga](concepts-query-language.md) ditt dubbla diagram enkelt och effektivt att besvara frågor om din miljö. I Azure CLI görs detta med kommandot [**AZ DT dubbla frågor**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_query) .

Kör följande frågor i Cloud Shell för att svara på några frågor om exempel miljön.

1. **Vilka är alla entiteter från min miljö som representeras i Azure Digitals dubbla?** (fråga alla)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```

    På så sätt kan du snabbt ta en titt på din miljö och se till att allt är representerat på det sätt som du vill att det ska vara i Azure Digitals. Resultatet av detta är en utmatning som innehåller varje digital enhet med information. Här är ett utdrag:

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Skärm bild av Cloud Shell visar partiellt resultat av en delad fråga, inklusive room0 och room1." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >Du kanske upptäcker att det här är samma kommando som du använde i avsnittet [*skapa digitala dubbla*](#create-digital-twins) avsnitt tidigare för att hitta alla Azure Digital-dubbla i-instansen.

1. **Vad är alla rum i min miljö?** (fråga efter modell)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    Du kan begränsa frågan till flera av en viss typ, för att få mer specifik information om vad som visas. Resultatet av detta är *room0* och *room1*, men visar **inte** *floor0* eller *floor1* (eftersom de är golv, inte rum).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Skärm bild av Cloud Shell visar resultatet av modell frågan, som endast innehåller room0 och room1." lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **Vilka är alla rum på *floor0*?** (fråga efter relation)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    Du kan fråga baserat på relationer i diagrammet för att få information om hur dubbla anslutningar är anslutna eller begränsa din fråga till ett visst område. Endast *room0* är på *floor0*, så det är det enda rummet i resultatet.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Skärm bild av Cloud Shell visar resultatet av Relations frågan, som innehåller room0." lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > Lägg märke till att ett nummer med dubbla ID (t. ex. *floor0* i frågan ovan) frågas med hjälp av fältet metadata `$dtId` . 
    >
    >När du använder Cloud Shell för att köra en fråga med metadatafält som denna som börjar med `$` , bör du kringgå `$` med ett baktick för att göra det möjligt för Cloud Shell veta att det inte är en variabel och bör användas som en literal i frågetexten. Detta visas i skärm bilden ovan.

1. **Vad är alla dubbla i min miljö med en temperatur över 75?** (fråga efter egenskap)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    Du kan fråga diagrammet utifrån egenskaper för att besvara en rad olika frågor, inklusive att hitta avvikande extrem värden i din miljö som kan behöva åtgärdas. Andra jämförelse operatorer ( *<* ,, *>* *=* eller *! =*) stöds också. *room1* visas i resultatet, eftersom det har en temperatur på 80.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Skärm bild av Cloud Shell visar resultatet av egenskaps frågan, som endast innehåller room1." lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **Vilka är alla rum på *floor0* med en temperatur över 75?** (sammansatt fråga)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    Du kan också kombinera de tidigare frågorna som du skulle göra i SQL, med kombinations operatorer som `AND` , `OR` , `NOT` . Den här frågan använder `AND` för att göra den föregående frågan om dubbla temperaturer mer detaljerad. Resultatet innehåller nu bara rum med temperaturer över 75 som finns på *floor0*, vilket i det här fallet inte är något av dem. Resultat uppsättningen är tom.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Skärm bild av Cloud Shell visar resultatet av en sammansatt fråga som inte innehåller några objekt." lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört den här självstudien kan du välja vilka resurser du vill ta bort, beroende på vad du vill göra härnäst.

* **Om du planerar att fortsätta till nästa själv studie kurs** kan du behålla de resurser som du har konfigurerat här och återanvända Azures digitala dubbla instansen utan att avmarkera något i mellan.

* **Om du vill fortsätta att använda Azures digitala dubbla instansen, men ta bort alla dess modeller, delar och relationer**, kan du använda [**AZ DT dubbla relationer ta bort**](/cli/azure/ext/azure-iot/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_relationship_delete), [**AZ DT dubbla Delete**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_delete)och [**AZ DT modell Delete**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_delete) -kommandon för att rensa relationer, dubbla och modeller i din instans.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Du kanske också vill ta bort de modell filer som du har skapat på den lokala datorn.

## <a name="next-steps"></a>Nästa steg 

I den här självstudien kommer du igång med Azure Digitals dubbla, genom att skapa en graf i din instans med hjälp av Azure CLI. Du har skapat modeller, digitala garn och relationer för att bilda ett diagram. Du körde också några frågor i grafen för att få en uppfattning om vilka typer av frågor som Azure Digitals kan svara på en miljö.

Fortsätt till nästa självstudie för att kombinera Azure Digitals dubbla steg med andra Azure-tjänster för att slutföra ett data drivet, komplett scenario:
> [!div class="nextstepaction"]
> [*Självstudie: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md)