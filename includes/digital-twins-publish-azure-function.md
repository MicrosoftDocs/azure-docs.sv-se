---
author: baanders
description: include file for the process of publishing an Azure function from Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: ddc56ab05a087c9e86d67a13aebcfb8e65fbd78f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480746"
---
Om du vill publicera projektet till en funktionsapp i Azure börjar du Solution Explorer. Högerklicka på projektet och välj sedan **Publicera.**

> [!IMPORTANT] 
> Publicering till en funktionsapp i Azure medför ytterligare avgifter för din prenumeration, oberoende av Azure Digital Twins.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Skärmbild av Visual Studio visar snabbmenyn för lösningen. På menyn är Publicera markerat.":::

På sidan **Publicera** som öppnas lämnar du standardvalet av Azure som **mål.** Välj sedan **Nästa**. 

För ett specifikt mål väljer du **Azure-funktionsapp (Windows)** och sedan **Nästa.**

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Skärmbild av Visual Studio med dialogrutan Publicera Azure-funktion. På sidan Specifikt mål är valet Azure-funktionsapp (Windows).":::

Välj din **prenumeration på** fliken Functions-instans. Välj sedan plusikonen (+) för att skapa en ny funktion.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Skärmbild av Visual Studio med dialogrutan Publicera Azure-funktion. Plusikonen är markerad.":::

I fönstret **Funktionsapp (Windows) – Skapa** ny fyller du i följande fält:
* **Namnet** är namnet på den förbrukningsplan som Azure ska använda som värd för din Azure Functions app. Det här namnet gäller även för funktionsappen som innehåller den faktiska funktionen. Du kan välja ett unikt värde eller lämna standardförslaget.
* Kontrollera att Prenumeration **matchar** den prenumeration som du vill använda. 
* Kontrollera att **det är** resursgruppen som du vill använda.
* Lämna valet **Plantyp** som **Förbrukning.**
* Välj **platsen för** resursgruppen.
* Skapa en ny **Azure Storage** resurs genom att välja **länken** Nytt. Ange platsen så att den matchar din resursgrupp, använd de andra standardvärdena och välj sedan **OK.**

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Skärmbild av Visual Studio som visar dialogrutan Publicera Azure-funktion. Information om en ny funktionsapp fylls i, inklusive Namn, Prenumeration, Resursgrupp, Plantyp, Plats och Azure Storage.":::

Välj sedan **Skapa**.

När apptjänsten har skapats öppnas **fliken Functions-instans.** Den nya funktionsappen visas i **området Funktionsappar** under resursgruppen. Välj **Slutför**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Skärmbild av Visual Studio som visar dialogrutan Publicera Azure-funktion. Fliken Functions-instans är markerad. Den nya functions-appen visas under resursgruppen.":::

I fönstret **Publicera** som öppnas i huvudfönstret Visual Studio kontrollerar du att all information ser korrekt ut. Välj sedan **Publicera**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Skärmbild av Visual Studio som visar fönstret Publicera. Knappen Publicera är markerad.":::

> [!NOTE]
> Om du ser ett popup-fönster som i följande exempel väljer du **Försök att hämta autentiseringsuppgifter från Azure** och väljer sedan **Spara**.
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Skärmbild av Visual Studio som visar ett popup-fönster med namnet Publicera autentiseringsuppgifter. Den innehåller fält för användarnamn och lösenord. Den innehåller också en knapp för Att försöka hämta autentiseringsuppgifter från Azure." border="false":::
>
> Om du ser någon av följande varningar följer du anvisningarna för att uppgradera till den senaste Azure Functions-körningsversionen:
> * "Upgrade Functions version on Azure".
> * "Din version av Functions Runtime matchar inte den version som körs i Azure."
>
> Dessa varningar kan visas om du använder en gammal version av Visual Studio.

Funktionsappen har nu publicerats till Azure.
