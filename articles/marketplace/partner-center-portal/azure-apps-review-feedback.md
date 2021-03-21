---
title: Granska feedback för Azure Apps-erbjudanden – Microsoft Commercial Marketplace
description: Hur du hanterar feedback för ditt Azure-programerbjudande från Microsoft Azure Marketplace gransknings teamet. Du kan komma åt feedback i Azure DevOps med dina autentiseringsuppgifter för ditt partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mingshen
author: mingshen-ms
ms.openlocfilehash: ede4e5dd781851c781407a8acea640e3e501f259
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96436393"
---
# <a name="handling-review-feedback-for-azure-application-offers"></a>Hantera gransknings feedback för Azure-programerbjudanden

Den här artikeln förklarar hur du får åtkomst till feedback från Microsoft Azure Marketplace gransknings teamet i [Azure DevOps](https://azure.microsoft.com/services/devops/). Om det finns kritiska problem i ditt erbjudande om Azure-program under **Microsoft gransknings** steg kan du logga in på systemet för att visa detaljerad information om de här problemen (granska feedback). När du har åtgärdat alla problem måste du skicka erbjudandet igen för att fortsätta att publicera det på Azure Marketplace. Följande diagram illustrerar hur den här återställnings processen relaterar till publicerings processen.

![Granska feedback process](./media/review-feedback-process.png)

Normalt refereras gransknings problem som en pull-begäran (PR). Varje PR är länkat till ett Azure DevOps-objekt online som innehåller information om problemet. Följande bild visar ett exempel på Partner Center-upplevelsen om problem påträffas under granskningar. 

![Publiceringsstatus](./media/publishing-status.png)

PR som innehåller detaljerad information om sändningen visas i länken "Visa certifierings rapport". I komplexa situationer kan gransknings-och support team också e-posta dig.

## <a name="azure-devops-access"></a>Åtkomst till Azure DevOps

Alla användare med åtkomst till rollen "utvecklare" i Partner Center har åtkomst för att se PR-objekten som refereras till i granska feedback.

## <a name="reviewing-the-pull-request"></a>Granska pull-begäran

Använd följande procedur för att granska problem som dokumenteras i pull-begäran.

1. I formuläret **gransknings** avsnitt i publicerings steg väljer du en PR-länk för att starta webbläsaren och gå till sidan **Översikt** (start) för den här PR. Följande bild visar ett exempel på Start sidan för kritiska problem för erbjudandet för Contoso-exempelprogrammet. Den här sidan innehåller användbar sammanfattnings information om de gransknings problem som finns i Azure-appen.

    [![Start sida för pull-begäran](./media/pr-home-page-thumb.png)](./media/pr-home-page.png)
    <br/> *Klicka på bilden för att expandera.*

1. Valfritt På höger sida av fönstret, i avsnittet **principer**, väljer du ärende meddelandet (i det här exemplet: **princip valideringen misslyckades**) för att undersöka lågnivå information om problemet, inklusive de associerade loggfilerna. Fel visas vanligt vis längst ned i loggfilerna.

1. På menyn på vänster sida av start sidan väljer du **filer** för att visa de filer som utgör de tekniska till gångarna för det här erbjudandet. Microsoft-granskarna bör ha lagt till kommentarer som beskriver de identifierade kritiska problemen. I följande exempel har två problem identifierats.

    [![Skärm bild som markerar filer och de två problem som har identifierats.](./media/pr-files-page-thumb.png)](./media/pr-files-page.png)
    <br/> *Klicka på bilden för att expandera.*

1. Välj varje kommentar-nod i det vänstra trädet för att navigera till kommentaren i kontexten för den omgivande koden. Åtgärda problemet som beskrivs i kommentaren genom att korrigera käll koden i teamets projekt.

>[!Note]
>Du kan inte redigera ditt erbjudandes tekniska till gångar i gransknings teamets Azure DevOps-miljö. För utgivare är detta en skrivskyddad miljö för den inneslutna käll koden. Du kan dock lämna svar på kommentarer till Microsoft gransknings teamets fördel.

   I följande exempel har utgivaren granskat, korrigerat och besvarat det första problemet.

   ![Första korrigering och kommentar svar](./media/first-comment-reply.png)

## <a name="next-steps"></a>Nästa steg

När du har korrigerat de kritiska problem som dokumenterats i gransknings PR (s), måste du [Publicera om erbjudandet för Azure-appen](../create-new-azure-apps-offer.md).
