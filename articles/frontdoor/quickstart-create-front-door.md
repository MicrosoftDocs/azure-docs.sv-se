---
title: 'Snabbstart: Konfigurera hög tillgänglighet med Azure Front Door Service – Azure Portal'
description: Den här snabbstarten visar hur du använder Azure Front Door Service för din globala webbapp med hög åtkomst och höga prestanda med hjälp av Azure Portal.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 09/16/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-portal
ms.openlocfilehash: 2cf52d30c5658e73c55944bdfb7d424425fa4507
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538944"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>Snabbstart: Skapa en Front Door för en global webbapp med hög tillgänglighet

Kom igång med Azure Front Door med hjälp av Azure Portal för att konfigurera hög tillgänglighet för en webbapp.

I den här snabbstarten Azure Front Door två instanser av ett webbprogram som körs i olika Azure-regioner. Du skapar en Front Door-konfiguration baserat på samma viktade och samma prioritetsbackends. Den här konfigurationen dirigerar trafik till den närmaste platsen som kör programmet. Azure Front Door övervakar webbappen kontinuerligt. Tjänsten tillhandahåller automatisk redundans till nästa tillgängliga plats när den närmaste platsen inte är tillgänglig.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-two-instances-of-a-web-app"></a>Skapa två instanser av en webbapp

Den här snabbstarten kräver två instanser av ett webbprogram som körs i olika Azure-regioner. Båda webbprograminstanserna körs i *aktivt/aktivt* läge, så att någon av dem kan ta trafik. Den här konfigurationen skiljer sig från *en aktiv/fristående* konfiguration, där en fungerar som en redundans.

Om du inte redan har en webbapp kan du använda följande steg för att konfigurera exempelwebbappar.

1. Logga in på Azure Portal på https://portal.azure.com.

1. Längst upp till vänster på skärmen väljer du Skapa **en resurs**  >   **WebApp**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-create-web-app.png" alt-text="Skapa en webbapp i Azure-portalen":::

1. På fliken **Grundläggande inställningar** på sidan **Skapa webbapp** anger eller väljer du följande information.

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | **Prenumeration**               | Välj din prenumeration. |    
    | **Resursgrupp**       | Välj **Skapa ny** och *FrontDoorQS_rg1* i textrutan.|
    | **Namn**                   | Ange ett unikt **namn** för webbappen. I det här *exemplet används WebAppContoso-1*. |
    | **Publicera** | Välj **Kod**. |
    | **Körningsstack**         | Välj **.NET Core 2.1 (LTS).** |
    | **Operativsystem**          | Välj **Windows**. |
    | **Region**           | Välj **USA, centrala.** |
    | **Windows-plan** | Välj **Skapa ny** och ange *myAppServicePlanCentralUS* i textrutan. |
    | **SKU och storlek** | Välj **Standard S1 100 total ACU, 1,75 GB minne**. |

1. Välj **Granska + skapa,** granska **sammanfattningen** och välj sedan **Skapa.** Det kan ta flera minuter för distributionen att slutföras.

    :::image type="content" source="media/quickstart-create-front-door/create-web-app.png" alt-text="Granskningssammanfattning för webbapp":::

När distributionen är klar skapar du en andra webbapp. Använd samma procedur med samma värden, förutom följande värden:

| Inställning          | Värde     |
| ---              | ---  |
| **Resursgrupp**   | Välj **Skapa ny** och ange *FrontDoorQS_rg2* |
| **Namn**             | Ange ett unikt namn för webbappen, i det här exemplet *WebAppContoso-2*  |
| **Region**           | En annan region, i det här exemplet USA, *södra centrala* |
| **App Service plan**  >  **Windows-plan**         | Välj **Ny** och ange *myAppServicePlanSouthCentralUS* och välj sedan **OK** |

## <a name="create-a-front-door-for-your-application"></a>Skapa en Front Door för programmet

Konfigurera Azure Front Door att dirigera användartrafik baserat på den lägsta svarstiden mellan de två webbappservrarna. Börja med att lägga till en värd för Azure Front Door.

1. Välj Skapa en resurs på startsidan eller **Azure-menyn.** Välj **Nätverk**  >  **Se alla**  >  **Front Door**.

1. På fliken **Grundläggande inställningar** på sidan **Skapa Front Door** anger eller väljer du följande information och väljer sedan **Nästa: Konfiguration.**

    | Inställning | Värde |
    | --- | --- |
    | **Prenumeration** | Välj din prenumeration. |    
    | **Resursgrupp** | Välj **Skapa ny** och *FrontDoorQS_rg0* i textrutan.|
    | **Resursgruppsplats** | Välj **USA, centrala.** |

1. I **Frontends/domains** väljer du **+** för att öppna Lägg till en **frontend-värd**.

1. I **Värdnamn** anger du ett globalt unikt värdnamn. I det här *exemplet används contoso-frontend*. Välj **Lägg till**.

    :::image type="content" source="media/quickstart-create-front-door/add-frontend-host-azure-front-door.png" alt-text="Lägga till en frontend-värd för Azure Front Door":::

Skapa sedan en backend-pool som innehåller dina två webbappar.

1. I Skapa **en Front Door i** **Backend-pooler** väljer du för **+** att öppna Lägg till en **backend-pool**.

1. För **Namn** anger du *myBackendPool och* väljer sedan Lägg till en **backend**.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool.png" alt-text="Lägga till en backend-pool":::

1. På **bladet Lägg till en backend** väljer du följande information och sedan Lägg **till**.

    | Inställning | Värde |
    | --- | --- |
    | **Typ av backend-värd** | Välj **App Service**. |   
    | **Prenumeration** | Välj din prenumeration. |    
    | **Värdnamn för backend** | Välj den första webbappen som du skapade. I det här exemplet var webbappen *WebAppContoso-1*. |

    **Låt alla andra fält vara kvar som standard.*

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-backend.png" alt-text="Lägg till en backend-värd till Front Door":::

1. Välj **Lägg till en backend** igen. välj följande information och välj Lägg **till**.

    | Inställning | Värde |
    | --- | --- |
    | **Värdtyp för backend** | Välj **App Service**. |   
    | **Prenumeration** | Välj din prenumeration. |    
    | **Värdnamn för backend** | Välj den andra webbappen som du skapade. I det här exemplet var webbappen *WebAppContoso-2*. |

    **Låt alla andra fält vara kvar som standard.*

1. Välj **Lägg** till på **bladet Lägg till en serverdelspool** för att slutföra konfigurationen av serverdelspoolen.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-backend-pool-complete.png" alt-text="Lägga till en backend-pool för Azure Front Door":::

Slutligen lägger du till en routningsregel. En routningsregel mappar din värd på serversidan till serverpoolen. Regeln vidarebefordrar en begäran för `contoso-frontend.azurefd.net` till **myBackendPool**.

1. I Skapa **en Front Door** i **Routningsregler väljer du** för att konfigurera **+** en routningsregel.

1. I **Lägg till en regel** för **Namn** anger du *LocationRule*. Acceptera alla standardvärden och välj sedan Lägg till **för att** lägga till routningsregeln.

    :::image type="content" source="media/quickstart-create-front-door/front-door-add-a-rule.png" alt-text="Lägga till en regel i Front Door":::

   >[!WARNING]
   > Du **måste** se till att varje värd i din Front Door har en routningsregel med en associerad standardsökväg ( `\*` ). Det innebär att i alla dina routningsregler måste det finnas minst en routningsregel för var och en av dina värdar på serversidan som definierats på standardsökvägen ( `\*` ). Om du inte gör det kan det leda till att slutanvändartrafiken inte dirigeras korrekt.

1. Välj **Granska + skapa** och sedan **Skapa.**

    :::image type="content" source="media/quickstart-create-front-door/configuration-azure-front-door.png" alt-text="Konfigurerade Azure Front Door":::

## <a name="view-azure-front-door-in-action"></a>Visa Azure Front Door i praktiken

När du har Front Door en konfiguration tar det några minuter innan konfigurationen distribueras globalt. När du är klar öppnar du den frontend-värd som du skapade. I en webbläsare går du till `contoso-frontend.azurefd.net` . Din begäran dirigeras automatiskt till den närmaste servern från de angivna servrarna i serverpoolen.

Om du har skapat dessa appar i den här snabbstarten visas en informationssida.

Prova följande steg för att testa omedelbar global redundans i praktiken:

1. Öppna en webbläsare enligt beskrivningen ovan och gå till adressen till frontend: `contoso-frontend.azurefd.net` .

1. I Azure Portal du efter och väljer *App Services*. Rulla ned för att hitta en av dina webbappar, **WebAppContoso-1** i det här exemplet.

1. Välj din webbapp och välj sedan Stoppa **och** Ja **för att** verifiera.

1. Uppdatera webbläsaren. Du bör se samma informationssida.

   >[!TIP]
   >Det finns en liten fördröjning för de här åtgärderna. Du kan behöva uppdatera igen.

1. Hitta den andra webbappen och stoppa den också.

1. Uppdatera webbläsaren. Den här gången bör du se ett felmeddelande.

   :::image type="content" source="media/quickstart-create-front-door/web-app-stopped-message.png" alt-text="Båda instanserna av webbappen stoppades":::

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar kan du ta bort alla objekt som du har skapat. När du tar bort en resursgrupp tas även dess innehåll bort. Om du inte planerar att använda den här Front Door bör du ta bort resurser för att undvika onödiga kostnader.

1. I Azure Portal du efter och väljer **Resursgrupper,** eller väljer **Resursgrupper** på Azure Portal menyn.

1. Filtrera eller rulla ned för att hitta en resursgrupp, till exempel **FrontDoorQS_rg0**.

1. Välj resursgruppen och välj sedan Ta **bort resursgrupp.**

   >[!WARNING]
   >Den här åtgärden kan inte användas.

1. Skriv namnet på resursgruppen som du vill verifiera och välj sedan Ta **bort.**

Upprepa proceduren för de andra två grupperna.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du lägger till en anpassad domän i Front Door.
> [!div class="nextstepaction"]
> [Lägga till en anpassad domän](front-door-custom-domain.md)
