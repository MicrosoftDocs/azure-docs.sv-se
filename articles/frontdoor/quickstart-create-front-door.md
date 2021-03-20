---
title: 'Snabb start: Konfigurera hög tillgänglighet med Azure-tjänsten för front dörr – Azure Portal'
description: Den här snabb starten visar hur du använder Azures frontend-tjänst för hög tillgänglig global webbapp med hög prestanda genom att använda Azure Portal.
services: front-door
documentationcenter: na
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2020
ms.author: duau
ms.openlocfilehash: 4846438f8479fe622570aa515a4d8b40cccc57b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91252314"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Snabbstart: Skapa en Front Door för en global webbapp med hög tillgänglighet

Kom igång med Azures front dörr genom att använda Azure Portal för att konfigurera hög tillgänglighet för ett webb program.

I den här snabb starten har Azures frontend-pooler två instanser av ett webb program som körs i olika Azure-regioner. Du skapar en konfiguration för frontend-dörren baserat på lika viktad och samma prioritets Server del. Den här konfigurationen dirigerar trafik till den närmaste platsen som kör programmet. Azures front dörr övervakar kontinuerligt webb programmet. Tjänsten ger automatisk redundans till nästa tillgängliga plats när den närmaste platsen inte är tillgänglig.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-two-instances-of-a-web-app"></a>Skapa två instanser av en webbapp

Den här snabb starten kräver två instanser av ett webb program som körs i olika Azure-regioner. Båda instanserna av webb programmet körs i *aktivt/aktivt* läge, vilket innebär att det kan ta trafik. Den här konfigurationen skiljer sig från en *aktiv/fristående* konfiguration, där en fungerar som en redundansväxling.

Om du inte redan har en webbapp använder du följande steg för att ställa in exempel-webbappar.

1. Logga in på Azure Portal på https://portal.azure.com.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >   **webapp**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-create-web-app.png" alt-text="Skapa en webbapp i Azure-portalen":::

1. På fliken **grundläggande** på sidan **skapa webb program** anger eller väljer du följande information.

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | **Prenumeration**               | Välj din prenumeration. |    
    | **Resursgrupp**       | Välj **Skapa nytt** och ange *FrontDoorQS_rg1* i text rutan.|
    | **Namn**                   | Ange ett unikt **namn** för din webbapp. I det här exemplet används *WebAppContoso-1*. |
    | **Publicera** | Välj **Kod**. |
    | **Körningsstack**         | Välj **.net Core 2,1 (LTS)**. |
    | **Operativsystem**          | Välj **Windows**. |
    | **Region**           | Välj **Central USA**. |
    | **Windows-plan** | Välj **Skapa nytt** och ange *myAppServicePlanCentralUS* i text rutan. |
    | **SKU och storlek** | Välj **standard S1 100 totalt ACU, 1,75 GB minne**. |

1. Välj **Granska + skapa**, granska **sammanfattningen** och välj sedan **skapa**. Det kan ta flera minuter innan distributionen har slutförts.

    :::image type="content" source="media/quickstart-create-front-door/create-web-app.png" alt-text="Översikt över översikten över webbapp":::

När distributionen är klar skapar du en annan webbapp. Använd samma procedur med samma värden, förutom följande värden:

| Inställning          | Värde     |
| ---              | ---  |
| **Resursgrupp**   | Välj **Skapa nytt** och ange *FrontDoorQS_rg2* |
| **Namn**             | Ange ett unikt namn för din webbapp, i det här exemplet *WebAppContoso-2*  |
| **Region**           | En annan region, i det här exemplet, *södra centrala USA* |
| **App Service plan**  >  **Windows-plan**         | Välj **ny** och ange *myAppServicePlanSouthCentralUS* och välj sedan **OK** |

## <a name="create-a-front-door-for-your-application"></a>Skapa en Front Door för programmet

Konfigurera Azure-frontend för att dirigera användar trafik baserat på den lägsta svars tiden mellan de två webbappar-servrarna. Börja genom att lägga till en klient dels värd för Azures frontend-dörr.

1. Välj **skapa en resurs** på Start sidan eller Azure-menyn. Välj **nätverk**  >  **Se alla**  >  **frontend-fack**.

1. På fliken **grundläggande** på sidan **skapa en frontend-dörr** anger eller väljer du följande information och väljer sedan **Nästa: konfiguration**.

    | Inställning | Värde |
    | --- | --- |
    | **Prenumeration** | Välj din prenumeration. |    
    | **Resursgrupp** | Välj **Skapa nytt** och ange *FrontDoorQS_rg0* i text rutan.|
    | **Resursgruppsplats** | Välj **Central USA**. |

1. I **klient delar/domäner** väljer **+** du att öppna **Lägg till en klient dels värd**.

1. Ange ett globalt unikt värdnamn för **värd namn**. I det här exemplet används *contoso-frontend*. Välj **Lägg till**.

    :::image type="content" source="media/quickstart-create-front-door/add-frontend-host-azure-front-door.png" alt-text="Lägg till en klient dels värd för Azure-front dörr":::

Skapa sedan en backend-pool som innehåller dina två webbappar.

1. Fortfarande i **skapa en front dörr**, i **backend-pooler**, väljer **+** du att öppna **Lägg till en backend-pool**.

1. Som **namn** anger du *myBackendPool* och väljer sedan **Lägg till en server** del.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool.png" alt-text="Lägg till en backend-pool":::

1. I bladet **Lägg till en server** del väljer du följande information och väljer **Lägg till**.

    | Inställning | Värde |
    | --- | --- |
    | **Server dels värd typ** | Välj **App Service**. |   
    | **Prenumeration** | Välj din prenumeration. |    
    | **Server dels värd namn** | Välj den första webbapp som du skapade. I det här exemplet var webbappen *WebAppContoso-1*. |

    **Lämna alla andra fält som standard.*

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-backend.png" alt-text="Lägg till en backend-värd i din front dörr":::

1. Välj **Lägg till en server** del igen. Välj följande information och välj **Lägg till**.

    | Inställning | Värde |
    | --- | --- |
    | **Server dels värd typ** | Välj **App Service**. |   
    | **Prenumeration** | Välj din prenumeration. |    
    | **Server dels värd namn** | Välj den andra webbapp som du skapade. I det här exemplet var webbappen *WebAppContoso-2*. |

    **Lämna alla andra fält som standard.*

1. Välj **Lägg till** på bladet **Lägg till en server dels grupp** för att slutföra konfigurationen av backend-poolen.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool-complete.png" alt-text="Lägg till en backend-pool för Azures frontend-dörr":::

Slutligen lägger du till en regel för routning. En Routningstjänst mappar klient dels värden till backend-poolen. Regeln vidarebefordrar en begäran om `contoso-frontend.azurefd.net` till **myBackendPool**.

1. Fortfarande i **skapa en front dörr**, i **regler för routning**, väljer **+** du att konfigurera en regel för routning.

1. I **Lägg till en regel** anger du *LocationRule* som **namn**. Acceptera alla standardvärden och välj sedan **Lägg** till för att lägga till regeln för routning.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-rule.png" alt-text="Lägg till en regel till din front dörr":::

   >[!WARNING]
   > Du **måste** se till att var och en av klient dels värdarna i din front dörr har en regel för routning med en standard Sök väg ( `\*` ) kopplad till den. Det vill säga att alla regler för routning måste finnas minst en routningsprincip för var och en av de klient dels värdar som definierats på standard Sök vägen ( `\*` ). Om du inte gör det kan det leda till att din slut användar trafik inte dirigeras korrekt.

1. Välj **Granska + skapa** och sedan **skapa**.

    :::image type="content" source="media/quickstart-create-front-door/configuration-azure-front-door.png" alt-text="Konfigurerad Azure-front dörr":::

## <a name="view-azure-front-door-in-action"></a>Visa Azures front dörr i praktiken

När du har skapat en frontend-dörr tar det några minuter innan konfigurationen distribueras globalt. När du är klar kan du komma åt klient dels värden som du skapade. Gå till i en webbläsare `contoso-frontend.azurefd.net` . Din begäran dirigeras automatiskt till närmaste server från de angivna servrarna i backend-poolen.

Om du har skapat de här apparna i den här snabb starten visas en informations sida.

Prova följande steg för att testa den globala redundansväxlingen i praktiken:

1. Öppna en webbläsare, enligt beskrivningen ovan, och gå till klient delens adress: `contoso-frontend.azurefd.net` .

1. Sök efter och välj *app Services* i Azure Portal. Rulla ned för att hitta en av dina webbappar, **WebAppContoso-1** i det här exemplet.

1. Välj din webbapp och välj sedan **stoppa** och **Ja** för att verifiera.

1. Uppdatera webbläsaren. Du bör se samma informations sida.

   >[!TIP]
   >Det finns lite fördröjning för dessa åtgärder. Du kan behöva uppdatera igen.

1. Hitta den andra webbappen och stoppa den också.

1. Uppdatera webbläsaren. Den här gången bör du se ett fel meddelande.

   :::image type="content" source="media/quickstart-create-front-door/web-app-stopped-message.png" alt-text="Båda instanserna av webbappen stoppades":::

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar kan du ta bort alla objekt som du har skapat. När du tar bort en resurs grupp raderas även dess innehåll. Om du inte tänker använda den här frontend-dörren bör du ta bort resurser för att undvika onödiga kostnader.

1. I Azure Portal kan du söka efter och välja **resurs grupper** eller välja **resurs grupper** på Azure Portal-menyn.

1. Filtrera eller rulla nedåt för att hitta en resurs grupp, till exempel **FrontDoorQS_rg0**.

1. Välj resurs gruppen och välj sedan **ta bort resurs grupp**.

   >[!WARNING]
   >Den här åtgärden är irreversable.

1. Skriv namnet på den resurs grupp som ska verifieras och välj sedan **ta bort**.

Upprepa proceduren för de andra två grupperna.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du lägger till en anpassad domän i din front dörr.
> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](front-door-custom-domain.md)
