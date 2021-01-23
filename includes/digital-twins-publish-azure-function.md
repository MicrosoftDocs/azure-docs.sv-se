---
author: baanders
description: inkludera fil för processen att publicera en Azure-funktion från Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 63b393f519ad29baa05fef046ee1e8ba9e5330d8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701174"
---
Om du vill publicera projektet i en Function-app i Azure, högerklickar du på projektet i *Solution Explorer* och väljer **publicera**.

> [!IMPORTANT] 
> Om du publicerar till en Function-app i Azure debiteras ytterligare avgifter för din prenumeration, oberoende av Azures digitala dubbla.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Skärm bild av Visual Studio som visar menyn för höger val av lösning. Publicera är markerat i menyn.":::

På sidan *publicera* som följer lämnar du standard valet av **Azure** och klickar *sedan på nästa*. 

För ett särskilt mål väljer du **Azure Funktionsapp (Windows)** och klickar *sedan på nästa*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Skärm bild av Visual Studio i dialog rutan publicera Azure-funktion. Azure Funktionsapp (Windows) har marker ATS på sidan för specifika mål.":::

På sidan *Functions instance* väljer du din prenumeration. Detta bör fylla i en ruta med *resurs grupperna* i din prenumeration.

Välj din instanss resurs grupp och tryck *+* för att skapa en ny Azure-funktion.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Skärm bild av Visual Studio i dialog rutan publicera Azure-funktion. Knappen + för att skapa en ny funktion är markerad på sidan funktions instanser.":::

I fönstret *Funktionsapp (Windows) – skapa nytt* , fyller du i fälten enligt följande:
* **Namn** är namnet på förbruknings planen som Azure använder för att vara värd för din Azure Functions-app. Detta kommer också att bli namnet på den Function-app som innehåller din faktiska funktion. Du kan välja ett eget unikt värde eller lämna standard förslaget.
* Se till att **prenumerationen** matchar den prenumeration som du vill använda 
* Kontrol lera att **resurs gruppen** till den resurs grupp som du vill använda
* Lämna **plan typen** till *förbrukning*
* Välj den **plats** som matchar resurs gruppens plats
* Skapa en ny **Azure Storage** resurs med hjälp av länken *New...* . Ange den plats som ska matcha din resurs grupp, Använd de andra standardvärdena och tryck på OK.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Skärm bild av Visual Studio i dialog rutan publicera Azure-funktion. Information om en ny function-app fylls i, inklusive namn, prenumeration, resurs grupp, plan typ, plats och Azure Storage.":::

Välj sedan **Skapa**.

På så sätt kan du gå tillbaka till sidan *funktions instanser* där din nya Function-app nu visas under din resurs grupp. Tryck på *Slutför*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Publicera Azure Function i Visual Studio: Functions-instans (efter Function-app)":::

I fönstret *publicera* som öppnas i huvud fönstret i Visual Studio kontrollerar du att all information ser korrekt ut och väljer **publicera**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Skärm bild av Visual Studio i dialog rutan publicera Azure-funktion. Den nya Function-appen visas i listan över funktions program och det finns en Slutför-knapp.":::

> [!NOTE]
> Om du ser ett popup-fönster som detta: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="skärm bild av popup-fönstret i Visual Studio kallas publicerings-autentiseringsuppgifter. Det innehåller fält för ett användar namn och lösen ord och en knapp för att försöka hämta autentiseringsuppgifter från Azure." border="false":::
> Välj **försök att hämta autentiseringsuppgifter från Azure** och **Spara**.
>
> Om du ser en varning om att *Uppgradera Functions-versionen på Azure* eller att *din version av Functions runtime inte matchar den version som körs i Azure*:
>
> Följ anvisningarna för att uppgradera till den senaste versionen av Azure Functions Runtime. Det här problemet kan uppstå om du använder en äldre version av Visual Studio.

För att din Function-app ska kunna komma åt Azures digitala dubbla, måste den ha en Systemhanterad identitet och ha behörighet att komma åt din Azure Digital-instansen. Nu ska du ställa in det här.
