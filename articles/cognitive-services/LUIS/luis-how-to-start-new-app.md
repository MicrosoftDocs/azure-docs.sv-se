---
title: Skapa en ny app-LUIS
titleSuffix: Azure Cognitive Services
description: Skapa och hantera dina program på sidan Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2020
ms.openlocfilehash: 2dd06a7b4c8e6296cda747d17fd3d5be5db0af6b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018896"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Skapa en ny LUIS-app i LUIS-portalen
Det finns ett par olika sätt att skapa en LUIS-app. Du kan skapa en LUIS-app i LUIS-portalen eller via LUIS redigerings- [API: er](developer-reference-resource.md).

## <a name="using-the-luis-portal"></a>Använda LUIS-portalen

Du kan skapa en ny app i portalen på flera sätt:

* Börja med en tom app och skapa avsikter, yttranden och entiteter.
* Börja med en tom app och Lägg till en [fördefinierad domän](./howto-add-prebuilt-models.md).
* Importera en LUIS-app från en- `.lu` eller- `.json` fil som redan innehåller avsikter, yttranden och entiteter.

## <a name="using-the-authoring-apis"></a>Använda API: erna för redigering
Du kan skapa en ny app med API: erna för redigering på ett par olika sätt:

* [Lägg till program](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) – starta med en tom app och skapa avsikter, yttranden och entiteter.
* [Lägg till fördefinierad applikations](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) start med en fördefinierad domän, inklusive avsikter, yttranden och entiteter.


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>


[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Skapa en ny app i LUIS

1. På sidan **Mina appar** väljer du din **prenumeration** och  **redigerar resurs** och sedan **skapa**. 

> [!div class="mx-imgBorder"]
> ![Lista över LUIS-appar](./media/create-app-in-portal.png)

1. I dialog rutan anger du namnet på programmet, till exempel `Pizza Tutorial` .

    ![Dialog rutan skapa ny app](./media/create-pizza-tutorial-app-in-portal.png)

1. Välj din program kultur och välj sedan **slutförd**. En beskrivning och en förutsägelse resurs är valfria i det här läget. Du kan ange när som helst i avsnittet **Hantera** i portalen.

    > [!NOTE]
    > Kulturen kan inte ändras när appen har skapats.

    När appen har skapats visar LUIS-portalen listan över **avsikter** med den `None` avsikt som redan har skapats. Nu har du en tom app.

    > [!div class="mx-imgBorder"]
    > ![Listan över intenta med ingen avsikt skapad utan exempel yttranden.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available-on-my-apps-page"></a>Andra åtgärder som är tillgängliga på sidan Mina appar

Verktygsfältet kontext innehåller andra åtgärder:

* Byt namn på App
* Importera från fil med `.lu` eller `.json`
* Exportera app som `.lu` (för [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), `.json` eller `.zip` (för [Luis-behållare](luis-container-howto.md))
* Importera slut punkts loggar för behållare för att granska slut punkts yttranden
* Exportera slut punkts loggar, som `.csv` för offline-analys
* Ta bort app

## <a name="next-steps"></a>Nästa steg

Om din app-design innehåller identifiering av avsikter, [skapa nya avsikter](luis-how-to-add-intents.md)och Lägg till exempel yttranden. Om din app design bara är data extrahering, lägger du till exempel yttranden till none-avsikten och [skapar sedan entiteter](./luis-how-to-add-entities.md)och förser exempel yttranden med dessa entiteter.