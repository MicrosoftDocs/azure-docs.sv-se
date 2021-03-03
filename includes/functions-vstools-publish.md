---
title: ta med fil
description: ta med fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/30/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5b537b88052ce4042e346732f3dc63aaec6621cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "100529723"
---
1. I **Solution Explorer** högerklickar du på projektet och väljer **publicera** och i **mål** väljer du **Azure** och sedan **Nästa**.

1. För det **specifika målet** väljer du **Azure Funktionsapp (Windows)**, som skapar en Function-app som körs i Windows.

1. I **funktions instans** väljer du **skapa en ny Azure-funktion...** 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-new-resource.png" alt-text="Skapa en ny function-serverinstans":::

1. Skapa en ny instans med de värden som anges i följande tabell:

    | Inställning      | Värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Globalt unikt namn | Namn som unikt identifierar din nya funktionsapp. Acceptera det här namnet eller ange ett nytt namn. Giltiga tecken är: `a-z` , `0-9` , och `-` . |
    | **Prenumeration** | Din prenumeration | Den Azure-prenumeration som ska användas. Godkänn denna prenumeration eller Välj en ny i list rutan. |
    | **[Resurs grupp](../articles/azure-resource-manager/management/overview.md)** | Namnet på din resurs grupp |  Resurs gruppen där du vill skapa din Function-app. Välj en befintlig resurs grupp i den nedrullningsbara listan eller Välj **ny** för att skapa en ny resurs grupp.|
    | **[Typ av plan](../articles/azure-functions/functions-scale.md)** | Förbrukning | När du publicerar projektet till en Function-app som körs i en [förbruknings plan](../articles/azure-functions/consumption-plan.md)betalar du bara för körningar av Functions-appen. Andra värd planer ådrar sig högre kostnader. |
    | **Plats** | Platsen för App Service | Välj en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller andra tjänster som dina funktioner har åtkomst till. |
    | **[Azure Storage](../articles/azure-functions/storage-considerations.md)** | Allmänt lagrings konto | Ett Azure Storage-konto krävs av Functions-körningen. Välj **nytt** om du vill konfigurera ett allmänt lagrings konto. Du kan också välja ett befintligt konto som uppfyller [kraven för lagrings kontot](../articles/azure-functions/storage-considerations.md#storage-account-requirements).  |

    ![Dialogrutan Skapa App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Välj **skapa** för att skapa en Function-app och dess relaterade resurser i Azure. Status för att skapa resurser visas längst ned till vänster i fönstret. 

1. Tillbaka i **Functions-instansen**, kontrol lera att **Kör från paket filen** är markerat. Din Function-app distribueras med hjälp av [zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) med läget [Kör-från-Package](../articles/azure-functions/run-functions-from-deployment-package.md) aktiverat. Detta är den rekommenderade distributions metoden för ditt Functions-projekt, eftersom den resulterar i bättre prestanda. 

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile-step-4.png" alt-text="Slutför skapande av profil":::

1. Välj **Slutför** och på sidan publicera väljer du **publicera** för att distribuera paketet som innehåller dina projektfiler till din nya Function-app i Azure. 

    När distributionen är klar visas rot-URL: en för Function-appen i Azure på fliken **publicera** . 
    
1.  På fliken publicera väljer du **Hantera i Cloud Explorer**. Då öppnas Azure-resursen ny function-app i Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Meddelande som anger att publiceringen lyckades":::
    
    Med Cloud Explorer kan du använda Visual Studio för att visa innehållet på platsen, starta och stoppa-appen och bläddra direkt för att söka efter app-resurser i Azure och i Azure Portal. 
