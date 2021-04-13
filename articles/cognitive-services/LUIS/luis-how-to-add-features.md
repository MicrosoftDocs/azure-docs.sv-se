---
title: Använda funktioner för att förbättra LUIS ord lista
titleSuffix: Azure Cognitive Services
description: Använd Language Understanding (LUIS) för att lägga till app-funktioner som kan förbättra identifieringen eller förutsägelsen av avsikter och entiteter som kategorier och mönster
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c1a0fb15be92e25b576cd29e671eed3c6a5b948f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313274"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Använd funktioner för att förstärka signalen av ord listan

Du kan lägga till funktioner i LUIS-appen för att förbättra noggrannheten. Funktioner hjälper LUIS genom att tillhandahålla tips om att vissa ord och fraser är en del av en app Domain-vokabulär.

Granska [begreppen](luis-concept-feature.md) för att förstå när och varför du ska använda en funktion.

## <a name="add-phrase-list-as-a-feature"></a>Lägg till fras lista som en funktion

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. Välj **bygge** och välj sedan **funktioner** i appens vänstra panel.

1. På sidan **funktioner** väljer du **+ skapa**.

1. I dialog rutan **Skapa ny fras List funktion** anger du ett namn som t `Cities` . ex.. I rutan **värde** anger du exempel på städer, till exempel `Seattle` . Du kan ange ett värde i taget eller en uppsättning värden avgränsade med kommatecken och sedan trycka på **RETUR**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar hur du lägger till funktioner (fras lista) i städer](./media/luis-add-features/add-phrase-list-cities.png)

    När du har angett tillräckligt många värden för LUIS visas förslag. Du kan **lägga till alla** föreslagna värden eller välja enskilda villkor.

1. Behåll **dessa värden är utbytbara** om fraserna kan användas utbytbart. En funktions lista med utbytbara fraser fungerar som en lista över synonymer för utbildning. En lista med icke-utbytbara fraser fungerar som separata funktioner för utbildning (vilket innebär att funktionerna är likartade men avsikten med ändringar när du byter fraser).

1. Fras listan kan tillämpas på hela appen med den **globala** inställningen eller till en speciell modell (avsikt eller entitet). Om du skapar fras listan, som en _funktion_ från ett avsikt eller en entitet, är växlingen inte inställd för global. I det här fallet är syftet med växlingen att funktionen bara är lokal för den modellen, vilket innebär att den _inte är global_ för programmet.

1. Välj **Klar**. Den nya funktionen läggs till på sidan med **ml-funktioner** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> Du kan ta bort eller inaktivera en fras lista från kontext verktygsfältet på sidan med **ml-funktioner** .

## <a name="global-phrase-list-applies-to-entire-app"></a>Global fras lista gäller för hela appen

En fras lista ska tillämpas på avsikten eller entiteten som den är avsedd att hjälpa, men det kan finnas tillfällen när en fras lista ska tillämpas på hela appen som en **Global** funktion.

På sidan ML-funktioner markerar du listan fras och väljer sedan **gör global** i det översta kontext verktygsfältet.

## <a name="model-as-a-feature"></a>Modell som en funktion

En entitet kan vara en [funktion i ett avsikts-eller entitets](luis-concept-feature.md)-objekt.

Om du vill lägga till en entitet som en funktion i avsikten väljer du avsikten från sidan avsikter och väljer sedan **+ Lägg till funktion** ovanför kontext verktygsfältet. Listan innehåller alla fras listor och entiteter som kan användas som funktioner.

Om du vill lägga till en entitet som en funktion i en annan entitet kan du lägga till funktionen antingen på sidan för avsikts information med hjälp av [entiteten entitet](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) eller så kan du [lägga till funktionen](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) på entitetens detalj sida.

## <a name="next-steps"></a>Nästa steg

När du har lagt till, redigerat, tagit bort eller inaktiverat en funktion kan du [träna och testa appen](luis-interactive-test.md) igen för att se om prestandan förbättras.
