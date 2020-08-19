---
title: inkludera fil
description: inkludera fil
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/06/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: db6f5927e1cbdb3527014ccbb277c7ec20b02600
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84231602"
---
1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. I **Välj ett publicerings mål**använder du de publicerings alternativ som anges i följande tabell: 

    | Alternativ      | Beskrivning                                |
    | ------------ |  -------------------------------------------------- |
    | **Azure Functions förbruknings plan** | Skapa en Function-app i en Azure-molnbaserad miljö som körs i en [förbruknings plan](../articles/azure-functions/functions-scale.md#consumption-plan). När du använder en förbruknings plan betalar du bara för körningar av Functions-appen. Andra värd planer ådrar sig högre kostnader. Om du kör i en annan plan än en förbruknings plan måste du hantera [skalningen av din Function-app](../articles/azure-functions/functions-scale.md).| 
    | **Skapa ny** | En ny function-app med relaterade resurser skapas i Azure. <br/>Om du väljer **Välj befintlig**skrivs alla filer i den befintliga Function-appen i Azure över av filer från det lokala projektet. Använd bara det här alternativet när du publicerar om uppdateringar till en befintlig Function-app. |
    | **Kör från paketfil** | Din Function-app distribueras med hjälp av [zip Deploy](../articles/azure-functions/functions-deployment-technologies.md#zip-deploy) med läget [Kör-från-Package](../articles/azure-functions/run-functions-from-deployment-package.md) aktiverat. Den här distributionen, som ger bättre prestanda, är det rekommenderade sättet att köra funktionerna. <br/>Om du inte använder det här alternativet, se till att du stoppar ditt program projekt från att köras lokalt innan du publicerar till Azure. |

    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-profile.png" alt-text="Skapa en publicerings profil":::


1. Välj **Skapa profil**. Om du inte redan har loggat in på ditt Azure-konto från Visual Studio väljer du **Logga in**. Du kan också skapa ett kostnads fritt Azure-konto.

1. I **App Service: skapa ny**, Använd de värden som anges i följande tabell:

    | Inställning      | Värde  | Beskrivning                                |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Namn** | Globalt unikt namn | Namn som unikt identifierar din nya funktionsapp. Acceptera det här namnet eller ange ett nytt namn. Giltiga tecken är: `a-z` , `0-9` , och `-` . |
    | **Prenumeration** | Din prenumeration | Den Azure-prenumeration som ska användas. Godkänn denna prenumeration eller Välj en ny i list rutan. |
    | **[Resursgrupp](../articles/azure-resource-manager/management/overview.md)** | Namnet på din resurs grupp |  Resurs gruppen där du vill skapa din Function-app. Välj en befintlig resurs grupp i den nedrullningsbara listan eller Välj **ny** för att skapa en ny resurs grupp.|
    | **[Värdplan](../articles/azure-functions/functions-scale.md)** | Namn på din värd plan | Välj **nytt** om du vill konfigurera en server lös plan. Se till att välja **förbrukningen** under **storlek**. När du publicerar projektet till en Function-app som körs i en [förbruknings plan](../articles/azure-functions/functions-scale.md#consumption-plan)betalar du bara för körningar av Functions-appen. Andra värd planer ådrar sig högre kostnader. Om du kör i en annan plan än **förbrukningen**måste du hantera [skalningen av din Function-app](../articles/azure-functions/functions-scale.md).  |
    | **Plats** | Platsen för App Service | Välj en **plats** i en [region](https://azure.microsoft.com/regions/) nära dig eller andra tjänster som dina funktioner har åtkomst till. |
    | **[Azure Storage](../articles/storage/common/storage-account-create.md)** | Allmänt lagrings konto | Ett Azure Storage-konto krävs av Functions-körningen. Välj **nytt** om du vill konfigurera ett allmänt lagrings konto. Du kan också välja ett befintligt konto som uppfyller [kraven för lagrings kontot](../articles/azure-functions/functions-scale.md#storage-account-requirements).  |

    ![Dialogrutan Skapa App Service](./media/functions-vstools-publish/functions-visual-studio-publish.png)

1. Välj **skapa** för att skapa en Function-app och dess relaterade resurser i Azure med de här inställningarna och distribuera din funktions projekt kod. 

1. Välj **publicera** och vänta tills distributionen har slutförts. 

    När distributionen är klar visas rot-URL: en för Function-appen i Azure på fliken **publicera** . 
    
1.  På fliken publicera väljer du **Hantera i Cloud Explorer**. Då öppnas Azure-resursen ny function-app i Cloud Explorer. 
    
    :::image type="content" source="media/functions-vstools-publish/functions-visual-studio-publish-complete.png" alt-text="Meddelande som anger att publiceringen lyckades":::
    
    Med Cloud Explorer kan du använda Visual Studio för att visa innehållet på platsen, starta och stoppa-appen och bläddra direkt för att söka efter app-resurser i Azure och i Azure Portal. 