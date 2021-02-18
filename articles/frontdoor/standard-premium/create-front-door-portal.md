---
title: 'Snabb start: skapa en Azure frontend-dörr standard/Premium-profil – Azure Portal'
description: Den här snabb starten visar hur du använder Azures standard-/premium-tjänst för hög tillgänglighet och globala webb program med hög prestanda genom att använda Azure Portal.
services: frontdoor
author: duongau
manager: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 18956948a32a79c1435bf4fc1554b09480c9010c
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100920"
---
# <a name="quickstart-create-an-azure-front-door-standardpremium-profile---azure-portal"></a>Snabb start: skapa en Azure frontend-dörr standard/Premium-profil – Azure Portal

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [Azures front dörrs dokument](../front-door-overview.md).

I den här snabb starten får du lära dig hur du skapar en Azure frontend-standard/Premium-profil med hjälp av Azure Portal. Du kan skapa standard-/Premium-profilen för Azure-dörren genom att *snabbt skapa* med grundläggande konfigurationer eller genom att *skapa anpassade* med mer avancerade konfigurationer. Med *anpassad skapande* distribuerar du två Web Apps. Därefter skapar du en profil för standard/Premium för Azure-dörren med hjälp av de två Web Apps som ditt ursprung. Sedan verifierar du anslutningen till din Web Apps med hjälp av klient-hostname för standard-/Premium-frontend i Azure.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-front-door-profile---quick-create"></a>Skapa profil för front dörren – snabb registrering

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På Start sidan eller på Azure-menyn väljer du **+ skapa en resurs**. Sök efter *Standard/Premium för front dörren (för hands version)*. Välj sedan **Skapa**.

1. På sidan **jämför erbjudanden** väljer du **snabb registrering**. Välj sedan **Fortsätt för att skapa en front dörr**.

   :::image type="content" source="../media/create-front-door-portal/front-door-quick-create.png" alt-text="Skärm bild av jämförelse av erbjudanden.":::

1. På sidan **skapa en profil för en front dörr** anger du eller väljer följande inställningar.

    :::image type="content" source="../media/create-front-door-portal/front-door-quick-create-2.png" alt-text="Skärm bild av sidan snabb registrering för front dörr.":::    

    | Inställningar | Värde |
    | --- | --- |
    | **Prenumeration**  | Välj din prenumeration. |
    | **Resursgrupp**  | Välj **Skapa nytt** och ange *contoso-AppService* i text rutan.|
    | **Namn** | Ge profilen ett namn. I det här exemplet används **contoso-AFD-quickcreate**. |
    | **Nivå** | Välj antingen standard-eller Premium-SKU. Standard-SKU: n är innehålls leverans optimerad. Premium SKU bygger på standard-SKU och fokuserar på säkerhet. Se [jämförelse av nivåer](tier-comparison.md).  |
    | **Slut punkts namn** | Ange ett globalt unikt namn för din slut punkt. |
    | **Ursprungstyp** | Välj typ av resurs för ditt ursprung. I det här exemplet väljer vi en app service som det ursprung som har privat länk aktive rad. |
    | **Ursprungs värd namn** | Ange värd namnet för ditt ursprung. |
    | **Aktivera privat länk** | Om du vill ha en privat anslutning mellan din Azure-front dörr och ditt ursprung. Mer information finns i [vägledning för privat länk](concept-private-link.md) och [aktivera privat länk](how-to-enable-private-link.md).
    | **Cachelagring** | Markera kryss rutan om du vill cachelagra innehåll närmare användare som globalt använder Azures frontend-pop och Microsoft Network. |
    | **WAF-princip** | Välj **Skapa ny** eller Välj en befintlig WAF-princip i list rutan om du vill aktivera den här funktionen. |

    > [!NOTE]
    > När du skapar en standard-/Premium-profil för Azure-dörren måste du välja ett ursprung från samma prenumeration som den första dörren skapas i.

1. Välj **Granska + skapa** för att få fram din profil för din frontend-dörr igång.

   > [!NOTE]
   > Det kan ta några minuter innan konfigurationerna sprids till alla Edge-pop.

1. Klicka sedan på **skapa** för att få din profil för front dörren distribuerad och körs.

1. Om du har aktiverat privat länk går du till ditt ursprung (App Service i det här exemplet). Välj **nätverk**  >  **Konfigurera privat länk**. Välj sedan den väntande begäran från Azures front dörr och klicka på Godkänn. Efter några sekunder kommer ditt program att vara tillgängligt via Azures front dörr på ett säkert sätt.

## <a name="create-front-door-profile---custom-create"></a>Skapa profil för front dörren – anpassad skapa

### <a name="create-a-web-app-with-two-instances-as-the-origin"></a>Skapa en webbapp med två instanser som ursprung

Om du redan har ett ursprung eller en ursprungs grupp har kon figurer ATS, kan du gå vidare till skapa en front dörr standard/Premium (för hands version) för programmet.

I det här exemplet skapar vi ett webb program med två instanser som körs i olika Azure-regioner. Båda instanserna av webb programmet körs i *aktivt/aktivt* läge, vilket innebär att det kan ta trafik. Den här konfigurationen skiljer sig från en *aktiv/fristående* konfiguration, där en fungerar som en redundansväxling.

Om du inte redan har en webbapp kan du använda följande steg för att konfigurera ett exempel på en webbapp.

1. Logga in på Azure Portal på https://portal.azure.com.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **webapp**.

1. På fliken **grundläggande** på sidan **skapa webbapp** anger eller väljer du följande information.

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | **Prenumeration**               | Välj din prenumeration. |
    | **Resursgrupp**       | Välj **Skapa nytt** och ange *FrontDoorQS_rg1* i text rutan.|
    | **Namn**                   | Ange ett unikt **namn** för din webbapp. I det här exemplet används *WebAppContoso-001*. |
    | **Publicera** | Välj **Kod**. |
    | **Körningsstack**         | Välj **.net Core 2,1 (LTS)**. |
    | **Operativsystem**          | Välj **Windows**. |
    | **Region**           | Välj **Central USA**. |
    | **Windows-plan** | Välj **Skapa nytt** och ange *myAppServicePlanCentralUS* i text rutan. |
    | **SKU och storlek** | Välj **standard S1 100 totalt ACU, 1,75 GB minne**. |

     :::image type="content" source="../media/create-front-door-portal/create-web-app.png" alt-text="Snabb registrering av SKU för front dörr Premium i Azure Portal":::

1. Välj **Granska + skapa**, Granska sammanfattningen och välj sedan **skapa**. Det kan ta flera minuter att distribuera till en

När distributionen är klar skapar du en annan webbapp. Använd samma inställningar som ovan, förutom följande inställningar:

| Inställning          | Värde     |
| ---              | ---  |
| **Resursgrupp**   | Välj **Skapa nytt** och ange *FrontDoorQS_rg2*. |
| **Namn**             | Ange ett unikt namn för din webbapp, i det här exemplet *WebAppContoso-002*.  |
| **Region**           | En annan region, i det här exemplet, *södra centrala USA* |
| **App Service plan**  >  **Windows-plan**         | Välj **ny** och ange *myAppServicePlanSouthCentralUS* och välj sedan **OK**. |

### <a name="create-a-front-door-standardpremium-preview-for-your-application"></a>Skapa en front dörr standard/Premium (för hands version) för ditt program

Konfigurera Azure front dörr standard/Premium (för hands version) för att dirigera användar trafik baserat på den lägsta svars tiden mellan de två Web Apps-servrarna. Skydda även din frontend-dörr med brand vägg för webbaserade program. 

1. Logga in på [Azure-portalen](https://portal.azure.com).
 
1. På Start sidan eller på Azure-menyn väljer du **+ skapa en resurs**. Sök efter *Standard/Premium för front dörren (för hands version)*. Välj sedan **Skapa**.

1. På sidan **jämför erbjudanden** väljer du **anpassad skapa**. Välj sedan **Fortsätt för att skapa en front dörr**.

1. På fliken **grundläggande**   anger eller väljer du följande information och väljer sedan **Nästa: hemlighet**. 

    | Inställning | Värde |
    | --- | --- |
    | **Prenumeration** | Välj din prenumeration. |
    | **Resursgrupp** | Välj **Skapa nytt** och ange *FrontDoorQS_rg0* i text rutan. |
    | **Resursgruppsplats** | Välj **USA, östra** |
    | **Profilnamn** | Ange ett unikt namn i den här prenumerationen **webapp-contoso-AFD** |
    | **Nivå** | Välj **Premium**. |
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-2.png" alt-text="Skapa profil för front dörr":::

1. *Valfritt*: **hemligheter**. Det här steget är valfritt om du planerar att använda hanterade certifikat. Om du har en befintlig Key Vault i Azure som du planerar att använda för att ta med ditt eget certifikat för den anpassade domänen väljer du **Lägg till ett certifikat**. Du kan också lägga till certifikat i hanterings miljön när du har skapat.

    > [!NOTE]
    > Du måste ha rätt behörighet för att lägga till certifikatet från Azure Key Vault som en användare. 

    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-secret.png" alt-text="Skärm bild av Lägg till en hemlighet i anpassad skapa.":::

1. På fliken **slut punkt** väljer du **Lägg till en slut punkt** och ger din slut punkt ett globalt unikt namn. Du kan skapa flera slut punkter i din Azures standard-/Premium-profil när du har slutfört skapande upplevelsen. I det här exemplet används *contoso-frontend*. Lämna timeout för ursprungs svar (i sekunder) och status som standard. Välj **Lägg till** för att lägga till slut punkten.
    
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-endpoint.png" alt-text="Skärm bild av Lägg till en slut punkt.":::

1. Lägg sedan till en ursprungs grupp som innehåller dina två webbappar. Välj **+ Lägg** till   för att öppna sidan **Lägg till en start grupp** . Som namn anger du *myOrignGroup* och väljer sedan **+ Lägg till ett ursprung**.
 
     :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group.png" alt-text="Skärm bild av Lägg till en ursprungs grupp.":::

1. På sidan **Lägg till ett ursprung**   anger eller väljer du informationen nedan. Välj **Lägg till**.

    | Inställning | Värde |
    | --- | --- |
    | **Namn** | Ange **webapp1** |
    | **Ursprungstyp** | Välj **app Services** |
    | **Värdnamn** | Välj `WebAppContoso-001.azurewebsites.net` |
    | **Ursprungsvärdadress** | Välj `WebAppContoso-001.azurewebsites.net` |
    | **Andra fält** | Lämna alla andra fält som standard. |

    > [!NOTE]
    > När du skapar en standard-/Premium-profil för Azure-dörren måste du välja ett ursprung från samma prenumeration som standard/Premium för Azure frontend-dörren skapas i.
   
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-1.png" alt-text="Skärm bild av Lägg till fler ursprung.":::

1. Upprepa steg 8 för att lägga till det andra ursprungs webapp002. Välj `webappcontoso-002.azurewebsite.net` som **ursprungs värd namn** och **ursprungs värd huvud**.

1. På sidan **Lägg till en ursprungs grupp** visas två tillägg som har lagts till, lämna alla andra fält som standard.
  
   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-origin-group-2.png" alt-text="Skärm bild av sidan Lägg till en start grupp.":::

1. Lägg sedan till en väg för att mappa klient delens slut punkt till ursprungs gruppen. Den här vägen vidarebefordrar begär Anden från slut punkten till myOriginGroup. Konfigurera en väg genom att välja **+ Lägg till** i väg.  

1. På sidan **Lägg till en väg** anger eller väljer du informationen nedan. Välj **Lägg till**.
  
    :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-route-without-caching.png" alt-text="Lägg till väg utan cachelagring":::

    | Inställning | Värde |
    | --- | --- |
    | **Namn** | Ange **väg** |    
    | **Domän** | Välj `contoso-frontend.z01.azurefd.net` | 
    | **Värdnamn** | Välj `WebAppContoso-001.azurewebsites.net` |
    | **Mönster som ska matchas** | Lämna som standard. |
    | **Godkända protokoll** | Lämna som standard. | 
    | **Omdirigera** | Lämna standardvärdet för **omdirigera all trafik för att använda https**. | 
    | **Ursprungs grupp** | Välj **MyOriginGroup**. | 
    | **Sökväg till ursprung** | Lämna som standard. | 
    | **Protokoll för vidarebefordran** | Välj **matcha inkommande begäran**. | 
    | **Cachelagring** | Lämna avmarkerat i den här snabb starten. Om du vill att innehållet ska cachelagras på kanter markerar du kryss rutan **Aktivera cachelagring**. |
    | **Regler** | Lämna som standard. När du har skapat din profil för din front dörr kan du skapa anpassade regler och tillämpa dem på vägar. |  
       
    >[!WARNING]
    > **Se till** att det finns en väg för varje slut punkt. Frånvaro av en väg kan orsaka att en slut punkt slutar fungera.

1. Välj sedan **+ Lägg till** säkerhet för att lägga till en WAF-princip. Välj **Lägg till** ny och ge principen ett unikt namn. Markera kryss rutan för **Lägg till bot-skydd**. Välj slut punkten i **domänerna** och välj sedan **Lägg till**.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-add-waf-policy-2.png" alt-text="Lägg till WAF-princip":::

1. Välj **Granska + skapa** och sedan **skapa**. Det tar några minuter för konfigurationerna att spridas till alla Edge-pop. Nu har du din första profil och slut punkt för front dörren.

   :::image type="content" source="../media/create-front-door-portal/front-door-custom-create-review.png" alt-text="Granska anpassad skapande":::

## <a name="verify-azure-front-door"></a>Verifiera Azures front dörr

När du skapar en profil för Azures främre dörr standard/Premium tar det några minuter innan konfigurationen distribueras globalt. När du är klar kan du komma åt klient dels värden som du skapade. Gå till i en webbläsare `contoso-frontend.z01.azurefd.net` . Din begäran skickas automatiskt till närmaste server från de angivna servrarna i gruppen ursprung.

Om du har skapat de här apparna i den här snabb starten visas en informations sida.

Vi använder följande steg för att testa en omedelbar global redundans:

1. Öppna en webbläsare, enligt beskrivningen ovan, och gå till klient delens adress: `contoso-frontend.azurefd.net` .

1. Sök efter och välj *app Services* i Azure Portal. Rulla ned för att hitta en av dina webbappar, **WebAppContoso – 001** i det här exemplet.

1. Välj din webbapp och välj sedan **stoppa** och **Ja** för att verifiera.

1. Uppdatera webbläsaren. Du bör se samma informations sida.

   >[!TIP]
   >Det finns lite fördröjning för dessa åtgärder. Du kan behöva uppdatera igen.

1. Hitta den andra webbappen och stoppa den också.

1. Uppdatera webbläsaren. Den här gången bör du se ett fel meddelande.

    :::image type="content" source="../media/create-front-door-portal/web-app-stopped-message.png" alt-text="Båda instanserna av webbappen stoppades":::

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
> [Lägga till en anpassad domän](how-to-add-custom-domain.md)
