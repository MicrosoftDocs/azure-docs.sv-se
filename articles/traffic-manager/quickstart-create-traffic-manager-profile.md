---
title: 'Snabb start: skapa en profil för HA med program – Azure Portal – Azure Traffic Manager'
description: Den här snabb starten beskriver hur du skapar en Traffic Manager-profil för att skapa ett webb program med hög tillgänglighet med hjälp av Azure Portal.
services: traffic-manager
author: duongau
manager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 45489d3500a4a744f2aeb34dc21122d180797133
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92101336"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Snabb start: skapa en Traffic Manager profil med hjälp av Azure Portal

I den här snabbstarten beskrivs hur du skapar en Traffic Manager-profil som ger hög tillgänglighet för din webbapp.

I den här snabbstarten läser du om två instanser av en webbapp. Var och en av dem körs i olika Azure-regioner. Du skapar Traffic Manager-profil baserat på [slutpunktsprioritet](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profilen dirigerar användartrafik till den primära plats som kör webbappen. Traffic Manager övervakar kontinuerligt webbappen. Om den primära platsen inte är tillgänglig ger den automatisk redundans till säkerhetskopieringsplatsen.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten behöver du två instanser av en webbapp som distribuerats i två olika Azure-regioner (*USA, östra* och *Europa, västra*). Var och en av dessa kommer att fungera som primär- och redundansslutpunkter för Traffic Manager.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**. Sök efter **webbapp** och välj **skapa**.

1. I **skapa en webbapp** skriver eller väljer du följande värden på fliken **grundläggande** :

    | Inställning                 | Värde |
    | ---                     | --- |
    | Prenumeration            | Välj din prenumeration. |    
    | Resursgrupp          | Välj **Skapa nytt** och ange *myResourceGroupTM1* i text rutan.|
    | Name                    | Ange ett unikt **namn** för din webbapp. I det här exemplet används *myWebAppEastUS*. |
    | Publicera                 | Välj **Kod**. |
    | Körningsstack           | Välj **ASP.net v 4.7**. |
    | Operativsystem        | Välj **Windows**. |
    | Region                  | Välj **USA, östra**. |
    | Windows-plan            | Välj **Skapa nytt** och ange *myAppServicePlanEastUS* i text rutan. |
    | SKU och storlek            | Välj **standard S1 100 totalt ACU, 1,75 GB minne**. |
   
1. Välj fliken **övervakning** eller Välj **Nästa: övervakning**.  Under **övervakning**, ange **Application Insights**  >  **Aktivera Application Insights** till **Nej**.

1. Välj **Granska och skapa**.

1. Granska inställningarna och välj sedan **Skapa**.  När webbappen har distribuerats skapar den en standardwebbplats.

1. Följ steg 1-6 för att skapa en andra webbapp med namnet *myWebAppWestEurope*. **Resurs gruppens** namn är *MyResourceGroupTM2*, med **regionen** *Västeuropa* och **App Service plan** namn för **myAppServicePlanWestEurope**. Alla andra inställningar är samma som *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil som dirigerar användartrafik baserat på slutpunktsprioritet.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**. Sök sedan efter **Traffic Manager profil** och välj **skapa**.
1. I **Skapa Traffic Manager-profil** anger eller väljer du de här inställningarna:

    | Inställning | Värde |
    | --------| ----- |
    | Namn | Ange ett unikt namn för din Traffic Manager-profil.|
    | Routningsmetod | Välj **Prioritet**.|
    | Prenumeration | Välj den prenumeration som du vill att Traffic Manager-profilen ska gälla för. |
    | Resursgrupp | Välj *myResourceGroupTM1*.|
    | Location |Den här inställningen refererar till platsen för resursgruppen. Den har ingen effekt på den Traffic Manager-profil som ska distribueras globalt.|

1. Välj **Skapa**.

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till webbplatsen i *USA, östra* som primär slutpunkt för att dirigera all användartrafik. Lägg till webbplatsen i *Europa, västra* som en redundansslutpunkt. När den primära slutpunkten inte är tillgänglig dirigeras trafiken automatiskt till redundansslutpunkten.

1. I portalens sökfält anger du det Traffic Manager-profilnamn som du skapade i föregående avsnitt.
1. Välj profilen bland sökresultatet.
1. I **Traffic Manager-profil** går du till avsnittet **Inställningar** och väljer **Slutpunkter** följt av **Lägg till**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Slut punkts inställningar i Traffic Managers profil":::

1. Ange eller välj de här inställningarna:

    | Inställning | Värde |
    | ------- | ------|
    | Typ | Välj **Azure-slutpunkt**. |
    | Name | Ange *myPrimaryEndpoint*. |
    | Målresurstyp | Välj **App Service**. |
    | Målresurs | Välj **Välj en app service**  >  **östra USA**. |
    | Prioritet | Välj **1**. All trafik skickas till den här slutpunkten när den är felfri. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Skärm bild av var du lägger till en slut punkt till din Traffic Manager-profil":::
    
1. Välj **OK**.
1. För att skapa en redundansslutpunkt för din andra Azure-region upprepar du steg 3 och 4 med de här inställningarna:

    | Inställning | Värde |
    | ------- | ------|
    | Typ | Välj **Azure-slutpunkt**. |
    | Name | Ange *myFailoverEndpoint*. |
    | Målresurstyp | Välj **App Service**. |
    | Målresurs | Välj **Välj en app service**  >  **Västeuropa**. |
    | Prioritet | Välj **2**. All trafik skickas till den här redundansslutpunkten om den primära slutpunkten är skadad. |

1. Välj **OK**.

När du är klar med att lägga till de två slutpunkterna visas de i **Traffic Manager-profilen**. Observera att deras övervakningsstatus är **Online** nu.

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil

I det här avsnittet kontrollerar domännamnet för Traffic Manager-profilen. Du kan även konfigurera den primära slutpunkten till att inte vara tillgänglig. Slutligen ser du att webbappen fortfarande är tillgänglig. Det beror på att Traffic Manager skickar trafiken till redundansslutpunkten.

### <a name="check-the-dns-name"></a>Kontrollera DNS-namnet

1. I portalens sökfält söker du efter det **Traffic Manager-profil** namn som du skapade i föregående avsnitt.
1. Välj Traffic Manager-profilen. **Översikten** visas.
1. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Skärmbild av platsen för ditt Traffic Manager DNS-namn":::

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken

1. I en webbläsare anger du DNS-namnet för din Traffic Manager-profil för att visa webbappens standardwebbplats.

    > [!NOTE]
    > I det här snabbstartsscenariot dirigeras alla begäranden till den primära slutpunkten. Den är inställd på **Prioritet 1**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Skärmbild av webbplatsen för att bekräfta tillgängligheten för Traffic Manager-profil":::

1. För att se hur Traffic Manager-redundans fungerar i praktiken inaktiverar du din primära plats:
    1. På Traffic Manager-profilsidan går du till avsnittet **Översikt** och väljer **myPrimaryEndpoint**.
    1. I *myPrimaryEndpoint* väljer du **Inaktiverad** > **Spara**.
    1. Stäng **myPrimaryEndpoint**. Lägg märke till att statusen är **Inaktiverad** nu.
1. Kopiera DNS-namnet för din Traffic Manager-profil från det föregående steget för att visa webbplatsen i en ny webbläsarsession.
1. Kontrollera att webbappen fortfarande är tillgänglig.

Den primära slutpunkten är inte tillgänglig, så du dirigerades till redundansslutpunkten.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgrupperna, webbapparna och alla relaterade resurser när du är klar. Det gör du genom att markera varje enskilt objekt instrumentpanelen och välja **Ta bort** längst upp på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en Traffic Manager-profil. Med den kan du dirigera användartrafik för webbappar med hög tillgänglighet. Om du vill veta mer om att dirigera trafik kan du gå vidare till Traffic Manager-självstudierna.

> [!div class="nextstepaction"]
> [Självstudier för Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
