---
title: 'Snabbstart: Skapa en profil för hög åtkomst av program – Azure Portal – Azure Traffic Manager'
description: Den här snabbstartsartikeln beskriver hur du skapar en Traffic Manager-profil för att skapa en webbapp med hög Azure Portal.
services: traffic-manager
author: duongau
ms.author: duau
manager: twooley
ms.date: 04/19/2021
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-portal
ms.openlocfilehash: 13b5925310c615461424f78d90ba9849c9bf58c5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727986"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-the-azure-portal"></a>Snabbstart: Skapa en Traffic Manager med hjälp av Azure Portal

I den här snabbstarten beskrivs hur du skapar en Traffic Manager-profil som ger hög tillgänglighet för din webbapp.

I den här snabbstarten läser du om två instanser av en webbapp. Var och en av dem körs i olika Azure-regioner. Du skapar Traffic Manager-profil baserat på [slutpunktsprioritet](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profilen dirigerar användartrafik till den primära plats som kör webbappen. Traffic Manager övervakar kontinuerligt webbappen. Om den primära platsen inte är tillgänglig ger den automatisk redundans till säkerhetskopieringsplatsen.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Diagram över Traffic Manager distributionsmiljö." border="false":::

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten behöver du två instanser av en webbapp som distribuerats i två olika Azure-regioner (*USA, östra* och *Europa, västra*). Var och en av dessa kommer att fungera som primär- och redundansslutpunkter för Traffic Manager.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs.** Sök efter **Webbapp** och välj **Skapa**.

1. I **Skapa en webbapp** skriver eller väljer du följande värden på **fliken Grundläggande:**

    | Inställning                 | Värde |
    | ---                     | --- |
    | Prenumeration            | Välj din prenumeration. |    
    | Resursgrupp          | Välj **Skapa ny** och ange *myResourceGroupTM1* i textrutan.|
    | Name                    | Ange ett unikt **namn** för webbappen. I det här exemplet *används myWebAppEastUS*. |
    | Publicera                 | Välj **Kod**. |
    | Körningsstack           | Välj **ASP.NET V4.7**. |
    | Operativsystem        | Välj **Windows**. |
    | Region                  | Välj **USA, östra.** |
    | Windows-plan            | Välj **Skapa ny** och ange *myAppServicePlanEastUS* i textrutan. |
    | SKU och storlek            | Välj **Standard S1 100 totalt ACU, 1,75 GB minne.** |
   
1. Välj fliken **Övervakning** eller välj **Nästa: Övervakning.**  Under **Övervakning** anger du **Application Insights**  >  **Aktivera Application Insights** till **Nej.**

1. Välj **Granska och skapa**.

1. Granska inställningarna och välj sedan **Skapa**.  När webbappen har distribuerats skapar den en standardwebbplats.

1. Följ steg 1–6 för att skapa en andra webbapp med namnet *myWebAppWestEurope.* **Resursgruppens** namn är *myResourceGroupTM2,* med **regionen** Europa, västra och App Service **namnet** **myAppServicePlanWestEurope.**  Alla andra inställningar är samma som *myWebAppEastUS*.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil som dirigerar användartrafik baserat på slutpunktsprioritet.

1. Längst upp till vänster på skärmen väljer du **Skapa en resurs.** Sök sedan efter **Traffic Manager profil** och välj **Skapa**.
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

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-endpoint-menu.png" alt-text="Skärmbild av slutpunktsinställningar i Traffic Manager profil.":::

1. Ange eller välj de här inställningarna:

    | Inställning | Värde |
    | ------- | ------|
    | Typ | Välj **Azure-slutpunkt**. |
    | Name | Ange *myPrimaryEndpoint*. |
    | Målresurstyp | Välj **App Service**. |
    | Målresurs | Välj **Välj en apptjänst**  >  **USA, östra.** |
    | Prioritet | Välj **1**. All trafik skickas till den här slutpunkten när den är felfri. |

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/add-traffic-manager-endpoint.png" alt-text="Skärmbild av det ställe där du lägger till en slutpunkt i Traffic Manager-profilen.":::
    
1. Välj **OK**.
1. För att skapa en redundansslutpunkt för din andra Azure-region upprepar du steg 3 och 4 med de här inställningarna:

    | Inställning | Värde |
    | ------- | ------|
    | Typ | Välj **Azure-slutpunkt**. |
    | Name | Ange *myFailoverEndpoint*. |
    | Målresurstyp | Välj **App Service**. |
    | Målresurs | Välj **Välj en apptjänst,**  >  **Europa, västra.** |
    | Prioritet | Välj **2**. All trafik skickas till den här redundansslutpunkten om den primära slutpunkten är skadad. |

1. Välj **OK**.

När du är klar med att lägga till de två slutpunkterna visas de i **Traffic Manager-profilen**. Observera att deras övervakningsstatus är **Online** nu.

## <a name="test-traffic-manager-profile"></a>Testa Traffic Manager-profil

I det här avsnittet kontrollerar domännamnet för Traffic Manager-profilen. Du kan även konfigurera den primära slutpunkten till att inte vara tillgänglig. Slutligen ser du att webbappen fortfarande är tillgänglig. Det beror på att Traffic Manager skickar trafiken till redundansslutpunkten.

### <a name="check-the-dns-name"></a>Kontrollera DNS-namnet

1. I portalens sökfält söker du efter det **Traffic Manager-profil** namn som du skapade i föregående avsnitt.
1. Välj Traffic Manager-profilen. **Översikten** visas.
1. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil.
  
    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-dns-name.png" alt-text="Skärmbild av platsen för ditt Traffic Manager DNS-namn.":::

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken

1. I en webbläsare anger du DNS-namnet för din Traffic Manager-profil för att visa webbappens standardwebbplats.

    > [!NOTE]
    > I det här snabbstartsscenariot dirigeras alla begäranden till den primära slutpunkten. Den är inställd på **Prioritet 1**.

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-test.png" alt-text="Skärmbild av webbsidan för att bekräfta tillgängligheten för Traffic Manager profil.":::

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
