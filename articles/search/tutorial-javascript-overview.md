---
title: 'JavaScript-självstudie: översikt över Sök integrering'
titleSuffix: Azure Cognitive Search
description: Teknisk översikt och installation för att lägga till en sökning på en webbplats och distribuera till Azures statiska webbapp.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 03192b8a84b78682b53bf3d47e7de7b65eb8bceb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726946"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1 – Översikt över att lägga till en sökning på en webbplats

Den här självstudien skapar en webbplats för att söka igenom en katalog med böcker och distribuerar sedan webbplatsen till en statisk Azure-webbapp. 

Programmet är tillgängligt: 
* [Exempel](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [Demo webbplats – aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>Vad gör exemplet? 

Den här exempel webbplatsen ger till gång till en katalog med 10 000 böcker. En användare kan söka i katalogen genom att ange text i Sök fältet. När användaren anger text använder webbplatsen Sök indexets förslags funktion för att slutföra texten. När frågan har slutförts visas listan över böcker med en del av informationen. En användare kan välja en bok för att se all information som lagras i Sök indexet i boken. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="Den här exempel webbplatsen ger till gång till en katalog med 10 000 böcker. En användare kan söka i katalogen genom att ange text i Sök fältet. När användaren anger text använder webbplatsen Sök indexets förslags funktion för att slutföra texten. När sökningen är klar visas listan över böcker med en del av informationen. En användare kan välja en bok för att se all information som lagras i Sök indexet i boken.":::

Sök upplevelsen innehåller: 

* Search – tillhandahåller Sök funktioner för programmet.
* Föreslå – ger förslag när användaren skriver i Sök fältet.
* Dokuments ökning – söker efter ett dokument efter ID för att hämta innehållet på informations sidan.

## <a name="how-is-the-sample-organized"></a>Hur organiseras exemplet?

[Exemplet](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website) innehåller följande:

|App|Syfte|GitHub<br>Lagringsplats<br>Location|
|--|--|--|
|Klient|Reagera på appen (presentations lager) för att visa böcker, med Sök. Den anropar Azure Function-appen. |[/search-website/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Server|Azure Function-app (företags nivå) – anropar Azure Kognitiv sökning API med Java Script SDK |[/search-website/api](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Massinläsning|JavaScript-fil för att skapa indexet och lägga till dokument i den.|[/search-website/bulk-insert](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>Ställt in din utvecklingsmiljö

Installera följande för din lokala utvecklings miljö. 

- [Node.js 12 eller 14](https://nodejs.org/en/download)
    - Om du har en annan version av Node.js installerad på den lokala datorn kan du överväga att använda [Node version Manager](https://github.com/nvm-sh/nvm) (NVM) eller en Docker-behållare.  
- [Git](https://git-scm.com/downloads)
- [Visual Studio Code](https://code.visualstudio.com/) och följande tillägg
    - [Azure-resurser](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups)
    - [Azure Kognitiv sökning 0.2.0 +](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [Azures statiska webbapp](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- Valfritt:
    - Den här självstudien kör inte Azure Function API lokalt, men om du tänker köra den lokalt måste du installera [Azure-Functions Core-tools](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) globalt med följande bash-kommando: 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>Förgrena och klona Sök exemplet med git

Förgrening av exempel lagrings platsen är viktig för att kunna distribuera den statiska webbappen. Webbapparna fastställer Bygg åtgärder och distributions innehåll baserat på din egen GitHub-förgrenings plats. Kod körning i den statiska webbappen är fjärran sluten, med Azures statiska Web Apps att läsa från koden i ditt förgrenade exempel.

1. På GitHub delar vi in [exempel lagrings platsen](https://github.com/Azure-Samples/azure-search-javascript-samples). 

    Slutför processen för förgrening i webbläsaren med ditt GitHub-konto. I den här självstudien används din förgrening som en del av distributionen till en statisk Azure-webbapp. 

1. Ladda ned exempel programmet till den lokala datorn på en bash-Terminal. 

    Ersätt `YOUR-GITHUB-ALIAS` med ditt GitHub-alias. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. Öppna din lokala mapp i den klonade lagrings platsen i Visual Studio Code. Återstående uppgifter utförs från Visual Studio Code, om inget annat anges.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Skapa en resurs grupp för dina Azure-resurser

1. Öppna [aktivitets fältet](https://code.visualstudio.com/docs/getstarted/userinterface)i Visual Studio Code och välj Azure-ikonen. 
1. I sido fältet **högerklickar du på din Azure-prenumeration** under avsnittet `Resource Groups` och väljer **skapa resurs grupp**.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="I sido fältet, * * högerklicka på din Azure-prenumeration * * under avsnittet &quot;resurs grupper&quot; och välj * * Skapa resurs grupp * *.":::
1. Ange ett resurs grupp namn, till exempel `cognitive-search-website-tutorial` . 
1. Välj en plats nära dig.
1. När du skapar Kognitiv sökning och statiska resurser för webb program, senare i självstudien, använder du den här resurs gruppen. 

    Genom att skapa en resurs grupp får du en logisk enhet för att hantera resurserna, inklusive att ta bort dem när du är klar med dem.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Sök index och läsa in med dokument](tutorial-javascript-create-load-index.md)
* [Distribuera din statiska webbapp](tutorial-javascript-deploy-static-web-app.md)
