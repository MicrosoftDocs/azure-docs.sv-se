---
title: Lägg till entiteter – LUIS
description: Skapa entiteter för att extrahera nyckel data från User yttranden i Language Understanding-appar (LUIS). Extraherade enhets data används av klient programmet för att fullfil kund förfrågningar.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5c6836c2d68036bf2b9c5abe191943537349b8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91540973"
---
# <a name="add-entities-to-extract-data"></a>Lägga till entiteter för att extrahera data

Skapa entiteter för att extrahera nyckel data från User yttranden i Language Understanding-appar (LUIS). Extraherade enhets data används av klient programmet för att fullfil kund förfrågningar.

Entiteten representerar ett ord eller en fras i uttryck som du vill extrahera. Entiteter beskriver information som är relevant för avsikten och de är ibland nödvändiga för att din app ska kunna utföra sitt uppdrag. Du kan skapa entiteter när du lägger till ett exempel-uttryck till ett avsikts sätt eller förutom (före eller efter) lägger till ett exempel uttryck till ett avsikts sätt.

## <a name="plan-entities-then-create-and-label"></a>Planera entiteter, sedan skapa och etiketter

enheter för maskin inlärning kan skapas från exemplet yttranden eller skapas från sidan **entiteter** .

I allmänhet är det en bra idé att ägna tid åt att planera entiteter innan du skapar en enhet för maskin inlärning i portalen. Skapa sedan enheten för maskin inlärning från exemplet uttryck med så mycket information som möjligt i de underentiteter och funktioner du känner till vid tidpunkten. [Självstudien om desammansättnings bara entiteter](tutorial-machine-learned-entity.md) visar hur du använder den här metoden.

Som en del av planeringen av entiteterna kanske du behöver text matchnings bara entiteter (till exempel färdiga entiteter, reguljära uttrycks enheter eller List enheter). Du kan skapa dessa från sidan **entiteter** innan de märks i exemplet yttranden.

När du markerar etiketter kan du antingen namnge enskilda entiteter och sedan skapa upp till en överordnad dator inlärnings enhet. Eller så kan du börja med en överordnad enhet för inlärning av datorer och dela upp underordnade entiteter.

> [!TIP]
>Märk alla ord som kan indikera en entitet, även om orden inte används när de extraheras i klient programmet.

## <a name="when-to-create-an-entity"></a>När du ska skapa en entitet

När du har planerat dina entiteter bör du skapa dina entiteter och underentiteter för maskin inlärning. Detta kan kräva att du lägger till fördefinierade entiteter eller text matchnings enheter för att tillhandahålla funktioner för dina maskin inlärnings enheter. Dessa bör göras innan du märker etiketter.

När du har börjat namnge exempel yttranden kan du skapa enheter som har lärts av enheten eller utöka List enheter.

Använd följande tabell för att lära dig om hur du skapar eller lägger till varje entitetstyp i appen.

|Entitetstyp|Var du ska skapa entiteten i LUIS-portalen|
|--|--|
|enhet för maskin inlärning|Information om entiteter eller avsikter|
|Lista entitet|Information om entiteter eller avsikter|
|Entitet för reguljära uttryck|Entiteter|
|Entiteten Pattern.any|Entiteter|
|Fördefinierad entitet|Entiteter|
|Fördefinierad domän entitet|Entiteter|

Du kan skapa alla entiteter från sidan **entiteter** , eller så kan du skapa ett par entiteter som en del av etiketten på entiteten i exemplet uttryck på sidan **information om avsikt** . Du kan bara _namnge_ en entitet i ett exempel på en uttryck på sidan för **avsikts information** .



## <a name="how-to-create-a-new-custom-entity"></a>Så här skapar du en ny anpassad entitet

Den här processen fungerar för enheter som har lärts in, lista entiteter och entiteter med reguljära uttryck.

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. Välj sidan **entiteter** .
1. Välj **+ skapa** och välj sedan enhets typ.
1. Fortsätt att konfigurera entiteten och välj sedan **skapa** när du är färdig.

## <a name="create-a-machine-learned-entity"></a>Skapa en enhet som har lärts in

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. I avsnittet **skapa** väljer du **entiteter** i den vänstra panelen och väljer sedan **+ skapa**.
1. I dialog rutan **skapa en entitetstyp** anger du namnet på entiteten och väljer den dator som har **lärts**, väljer. Om du vill lägga till underentiteter väljer du **Lägg till struktur**. Välj **Skapa**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar hur du skapar en enhet som har lärts.](media/add-entities/machine-learned-entity-with-structure.png)

1. I **Lägg till underentiteter** lägger du till en underordnad entitet genom att välja **+** på raden för den överordnade entiteten.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar hur du lägger till underentiteter.](media/add-entities/machine-learned-entity-with-subentities.png)

1. Slutför skapande processen genom att välja **skapa** .

## <a name="add-a-feature-to-a-machine-learned-entity"></a>Lägg till en funktion till en enhet som har lärts in

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. I avsnittet **build** väljer du **entiteter** i den vänstra panelen och väljer sedan den enhet som har lärts.
1. Lägg till en funktion genom att välja **+ Lägg till funktion** på raden entitet eller underentitet.
1. Välj från listorna befintliga entiteter och fraser.
1. Om entiteten bara ska extraheras om funktionen hittas väljer du asterisken `*` för den funktionen.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar hur du lägger till funktionen i entiteten.](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>Skapa en entitet för reguljära uttryck

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. I avsnittet **skapa** väljer du **entiteter** i den vänstra panelen och väljer sedan **+ skapa**.

1. I dialog rutan **skapa en entitetstyp** skriver du namnet på entiteten och väljer **regex**, anger det reguljära uttrycket i **regex** -fältet och väljer **skapa**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av skapa en entitet för reguljära uttryck.](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>Skapa en lista entitet

List entiteter representerar en fast, avslutad uppsättning relaterade ord. Medan du, som författare, kan ändra listan, kommer LUIS inte att utöka eller krympa listan. Du kan också importera till en befintlig lista entitet med en [list Entity. JSON-format](reference-entity-list.md#example-json-to-import-into-list-entity).

I följande lista visas kanoniskt namn och synonymer.

|Objekt namn för färg lista|Färg synonymer|
|--|--|
|Red|Crimson, blod, äpple, eld-motor|
|Blue|Himmelsblå, kobolt|
|Green|Kelly, kalk|

Använd proceduren för att skapa en lista med entiteter. När entiteten lista har skapats behöver du inte märka exempel yttranden i ett avsikts syfte. List objekt och synonymer matchas med hjälp av exakt text.
1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. I avsnittet **skapa** väljer du **entiteter** i den vänstra panelen och väljer sedan **+ skapa**.

1. I dialog rutan **skapa en entitetstyp** anger du namnet på entiteten, t. ex `Colors` . och Select- **listan**.
1. I dialog rutan **skapa en lista entitet** i rutan **Lägg till ny under lista...**, anger du List objekt namnet, till exempel `Green` , och lägger till synonymer.

    > [!div class="mx-imgBorder"]
    > ![Skapa en lista med färger som en lista entitet på sidan entitets information.](media/how-to-add-entities/create-list-entity-of-colors.png)

1. När du är färdig med att lägga till List objekt och synonymer väljer du **skapa**.

    Kom ihåg att **träna** appen när du är färdig med en grupp ändringar i appen. Träna inte appen efter en enda ändring.

    > [!NOTE]
    > Den här proceduren visar hur du skapar och etiketterar en List-entitet från ett exempel på en uttryck på sidan **information om avsikt** . Du kan också skapa samma entitet från sidan **entiteter** .

## <a name="add-a-role-for-an-entity"></a>Lägg till en roll för en entitet

En roll är en namngiven undertyp till en entitet, baserat på kontext.

### <a name="add-a-role-to-distinguish-different-contexts"></a>Lägg till en roll för att skilja på olika kontexter

I följande uttryck finns det två platser, och varje anges semantiskt av orden runt den, till exempel `to` och `from` :

`Pick up the package from Seattle and deliver to New York City.`

I den här proceduren lägger `origin` du till och `destination` roller i en fördefinierad geographyV2-entitet.
1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. I avsnittet **build** väljer du **entiteter** i den vänstra panelen.

1. Välj **+ Lägg till en fördefinierad entitet**. Välj **geographyV2** och välj sedan **färdig**. Detta lägger till en fördefinierad entitet i appen.

    Om du tycker att mönstret extraherar entiteterna felaktigt när det innehåller en Pattern.any-entitet, kan du åtgärda problemet med hjälp av en [explicit lista](reference-pattern-syntax.md#explicit-lists).

1. Välj den nyligen tillagda förskapade geographyV2-entiteten från listan **entiteter i entiteter** .
1. Om du vill lägga till en ny roll väljer **+** du bredvid **inga roller tillagda**.
1. I text rutan **ange roll...** anger du namnet på rollen `Origin` och anger sedan. Lägg till ett andra roll namn `Destination` och ange sedan.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar hur du lägger till ursprungs rollen till plats entiteten](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    Rollen läggs till i den fördefinierade entiteten men läggs inte till i någon yttranden som använder den entiteten.

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Etikettext med en roll i ett exempel uttryck

> [!TIP]
> Roller kan ersättas med etiketter med underentiteter i en maskin inlärnings entiteter.

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. Gå till sidan information om avsikt, som har exempel yttranden som använder rollen.
1. Om du vill namnge med rollen väljer du enhets etiketten (heldragen linje under text) i exemplet uttryck och väljer sedan **Visa i entiteten** i list rutan.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar meny alternativet Visa i entitets fönstret markerat.](media/add-entities/view-in-entity-pane.png)

    Paletten entitet öppnas till höger.

1. Välj entiteten och gå sedan till slutet av paletten och välj rollen.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar var du kan välja rollen.](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>Skapa ett mönster. valfri entitet

**Mönstret. alla** entiteter är bara tillgängliga med [mönster](luis-how-to-model-intent-pattern.md).


## <a name="do-not-change-entity-type"></a>Ändra inte entitetstyp

Med LUIS kan du inte ändra entitetens typ eftersom den inte vet vad som ska läggas till eller tas bort för att konstruera den entiteten. För att kunna ändra typen är det bättre att skapa en ny entitet av rätt typ med ett något annat namn. När entiteten har skapats tar du bort det gamla etikett namnet och lägger till det nya entitetsnamnet i varje uttryck. Ta bort den gamla entiteten när alla yttranden har bytt namn.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använd fördefinierade modeller](howto-add-prebuilt-models.md)

Läs mer om:
* Hur man [tränar](luis-how-to-train.md)
* [Testa](luis-interactive-test.md)
* [Publicera](luis-how-to-publish-app.md)
* Skuggning
    * [Begrepp](luis-concept-patterns.md)
    * [Syntax](reference-pattern-syntax.md)
* [Fördefinierade entiteter GitHub-lagringsplats](https://github.com/Microsoft/Recognizers-Text)
* [Koncept för data extrahering](luis-concept-data-extraction.md)



