---
title: 'JavaScript-självstudie: Distribuera Sök aktive rad webbplats'
titleSuffix: Azure Cognitive Search
description: Distribuera search-aktiverad webbplats till Azures statiska webbapp.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726958"
---
# <a name="3---deploy-the-search-enabled-website"></a>3 – distribuera den Sök aktiverade webbplatsen

Distribuera den Sök aktiverade webbplatsen som en statisk Azure-webbapp. Den här distributionen inkluderar både den reagera appen och Function-appen.  

Den statiska webbappen hämtar information och filer för distribution från GitHub med hjälp av din förgrening av exempel lagrings platsen.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Skapa en statisk webbapp i Visual Studio Code

1. Välj **Azure** från aktivitets fältet och välj sedan **statisk Web Apps** från sido fältet. 
1. Högerklicka på prenumerationens namn och välj sedan **skapa statisk webbapp (avancerat)**.    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="Högerklicka på prenumerationens namn och välj sedan * * skapa en statisk webbapp (avancerat) * *.":::

1. Följ anvisningarna för att ange följande information:

    |Prompt|Ange|
    |--|--|
    |Hur vill du skapa en statisk webbapp?|Använd befintlig GitHub-lagringsplats|
    |Välj organisation|Välj ditt _eget_ GitHub-alias som organisation.|
    |Välj databas|Välj **Azure-Search-JavaScript-samples** i listan. |
    |Välj gren av lagrings plats|Välj **original** i listan. |
    |Ange namnet på den nya statiska webb appen.|Skapa ett unikt namn för din resurs. Du kan till exempel lägga ditt namn till namnet på lagrings platsen, till exempel, `joansmith-azure-search-javascript-samples` . |
    |Välj en resurs grupp för nya resurser.|Använd den resurs grupp som du skapade för den här självstudien.|
    |Välj Bygg för inställning för att konfigurera standard projekt strukturen.|Välj **anpassad**|
    |Välj platsen för din program kod|`search-website`|
    |Välj platsen för din Azure Function-kod|`search-website/api`|
    |Ange sökvägen till din build-utdata...|skapa|
    |Välj en plats för nya resurser.|Välj en region nära dig.|

1. Resursen har skapats och välj **Öppna åtgärder i GitHub** från meddelandena. Då öppnas ett webbläsarfönster som pekar på din förgrenade lagrings platsen. 

    I listan med åtgärder anges att din webbapp, både klienten och funktionerna, skickades till din Azure-statiska webbapp. 

    Vänta tills bygget och distributionen har slutförts innan du fortsätter. Det kan ta en minut eller två att avsluta.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Hämta Kognitiv söknings frågans nyckel i Visual Studio Code

1. Öppna [aktivitets fältet](https://code.visualstudio.com/docs/getstarted/userinterface)i Visual Studio Code och välj Azure-ikonen. 

1. I sido fältet väljer du din Azure-prenumeration under avsnittet **Azure: kognitiv sökning** och högerklicka sedan på din Sök resurs och välj **Kopiera frågegrupp**. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="I sido fältet väljer du din Azure-prenumeration under * * Azure: Kognitiv sökning * * och högerklickar sedan på Sök resursen och väljer * * Kopiera frågesträng * *.":::

1. Behåll den här fråge nyckeln, du måste använda den i nästa avsnitt. Frågeparametern kan fråga ditt index. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Lägg till konfigurations inställningar i Visual Studio Code

Azure Function-appen returnerar inte Sök data förrän Sök hemligheterna är i inställningar. 

1. Välj **Azure** från aktivitets fältet och välj sedan **statisk Web Apps** från sido fältet. 
1. Expandera din nya statiska webbapp tills **program inställningarna** visas.
1. Högerklicka på **program inställningar** och välj sedan **Lägg till ny inställning**.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="Högerklicka på * * program inställningar * * och välj sedan * * Lägg till ny inställning * *.":::

1. Lägg till följande inställningar:

    |Inställning|Ditt Sök resurs värde|
    |--|--|
    |SearchApiKey|Sök frågans nyckel|
    |SearchServiceName|Ditt Sök resurs namn|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>Använda Sök i din statiska webbapp

1. Öppna [aktivitets fältet](https://code.visualstudio.com/docs/getstarted/userinterface)i Visual Studio Code och välj Azure-ikonen.
1. I sido fältet **högerklickar du på din Azure-prenumeration** under avsnittet `Static web apps` och letar reda på den statiska webbapp som du skapade för den här kursen.
1. Högerklicka på namnet på den statiska webb appen och välj **Bläddra webbplats**.
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="Högerklicka på namnet på den statiska webbappen och välj * * Bläddra till webbplatsen * *.":::    

1. Välj **Öppna** i popup-dialogrutan.
1. I Sök fältet webbplats anger du en Sök fråga som till exempel `code` _långsamt_ så att den föreslår funktionen föreslår bok titlar. Välj ett förslag eller Fortsätt att ange en egen fråga. Tryck på RETUR när du har slutfört din Sök fråga. 
1. Granska resultaten och välj sedan en av böckerna för att se mer information. 

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen om du vill rensa de resurser som skapats i den här självstudien.

1. Öppna [aktivitets fältet](https://code.visualstudio.com/docs/getstarted/userinterface)i Visual Studio Code och välj Azure-ikonen. 

1. I sido fältet **högerklickar du på din Azure-prenumeration** under avsnittet `Resource Groups` och letar upp resurs gruppen som du skapade för den här kursen.
1. Högerklicka på resurs gruppens namn och välj sedan **ta bort**.
    Detta tar bort både Sök-och statiska webbappens resurser.
1. Kom ihåg att ta bort det på GitHub om du inte längre vill ha GitHub-delen av exemplet. Gå till **inställningarna** för din förgrening och ta sedan bort grenen. 


## <a name="next-steps"></a>Nästa steg

* [Förstå Sök integrering för den Sök aktiverade webbplatsen](tutorial-javascript-search-query-integration.md)
