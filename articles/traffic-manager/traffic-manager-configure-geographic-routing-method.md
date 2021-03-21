---
title: 'Självstudie: Konfigurera geografisk trafik routning med Azure Traffic Manager'
description: I den här självstudien beskrivs hur du konfigurerar metoden för geografisk trafik cirkulation med hjälp av Azure Traffic Manager
services: traffic-manager
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2020
ms.author: duau
ms.openlocfilehash: 29b3cdde328a994e5806df810db15b529a6da9af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96188674"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Självstudie: Konfigurera den geografiska Traffic routing-metoden med hjälp av Traffic Manager

Med metoden för geografisk trafik cirkulation kan du dirigera trafik till specifika slut punkter baserat på den geografiska plats där begär Anden kommer. Den här självstudien visar hur du skapar en Traffic Manager-profil med den här routningsmetod och konfigurerar slut punkterna för att ta emot trafik från vissa geografiska områden.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Skapa en Traffic Manager profil med geografisk routning.
> - Konfigurera en kapslad slut punkt.
> - Använd Traffic Manager profilen.
> - Ta bort Traffic Managers profil.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare.

1. Välj **+ skapa en resurs** på den vänstra sidan. Sök efter **Traffic Manager profil** och välj **skapa**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager.png" alt-text="Skapa en Traffic Manager-profil":::

1. Definiera följande inställningar på sidan *skapa Traffic Manager profil* :

    | Inställning         | Värde                                              |
    | ---             | ---                                                |
    | Namn            | Ange ett namn för din profil. Det här namnet måste vara unikt inom trafficmanager.net-zonen. För att få åtkomst till din Traffic Manager-profil använder du DNS-namnet `<profilename>.trafficmanager.net` . |    
    | Routningsmetod  | Välj **geografisk**. |
    | Prenumeration    | Välj din prenumeration. |
    | Resursgrupp   | Använd en befintlig resurs grupp eller skapa en ny resurs grupp för att placera profilen under. Om du väljer att skapa en ny resurs grupp använder du List rutan *resurs grupps plats* för att ange platsen för resurs gruppen. Den här inställningen refererar till platsen för resurs gruppen och har ingen inverkan på den Traffic Manager profil som distribueras globalt. |

1. Välj **skapa** för att distribuera din Traffic Manager-profil.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png" alt-text="Skapa en profil sida för Traffic Manager":::

## <a name="add-endpoints"></a>Lägg till slut punkter

1. Välj Traffic Manager profilen i listan.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-list-geographic.png" alt-text="Traffic Manager geografisk lista":::

1. Välj **slut punkter** under *Inställningar* och välj **+ Lägg** till för att lägga till en ny slut punkt.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/add-geographic-endpoint.png" alt-text="Lägg till slut punkter":::

1. Välj eller ange följande inställningar: 

    | Inställning                | Värde                                              |
    | ---                    | ---                                                |
    | Typ                   | Välj typ av slut punkt. För geografiska routnings profiler som används i produktion rekommenderar vi att du använder kapslade slut punkts typer som innehåller en underordnad profil med fler än en slut punkt. Mer information finns i [vanliga frågor och svar om metoder för geografisk trafik cirkulation](traffic-manager-FAQs.md). |    
    | Name                   | Ange ett namn för att identifiera slut punkten. |
    | Målresurstyp   | Välj resurs typ för målet. |
    | Målresurs        | Välj resursen i listan. |

    > [!Note]
    > Vissa fält på den här sidan beror på vilken typ av slut punkt du lägger till:
    > 1. Om du lägger till en Azure-slutpunkt väljer du **mål resurs typ** och **mål** baserat på den resurs som du vill dirigera trafiken till
    > 1. Om du lägger till en **extern** slut punkt anger du slut punktens **fullständigt kvalificerade domän namn (FQDN)** .
    > 1. Om du lägger till en **kapslad slut punkt** väljer du den **mål resurs** som motsvarar den underordnade profil som du vill använda och anger **antalet lägsta underordnade slut punkter**.

1. I avsnittet *geo-mappning* använder du List rutan för att lägga till de regioner där du vill att trafiken ska skickas till den här slut punkten. Minst en region måste läggas till. Du kan ha mappat flera regioner.

1. Upprepa det sista steget för alla slut punkter som du vill lägga till under profilen och välj sedan **Spara**.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-add-endpoint.png" alt-text="Lägga till en Traffic Manager-slutpunkt":::

## <a name="use-the-traffic-manager-profile"></a>Använd Traffic Manager profilen

1.  I portalens sökfält söker du efter namnet på **Traffic Manager profilen** som du skapade i föregående avsnitt och väljer i Traffic Manager-profilen i resultaten som visas.
    
    :::image type="content" source="./media/traffic-manager-geographic-routing-method/search-traffic-manager-profile.png" alt-text="Sök Traffic Manager-profil":::

1. **Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. Namnet kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till den högra slut punkten som fastställs av routningstjänsten. Med geografisk routning, Traffic Manager tittar på källans IP-adress för inkommande begäran och bestämmer den region från vilken den har sitt ursprung. Om regionen mappas till en slut punkt dirigeras trafiken till där. Om den här regionen inte är mappad till en slut punkt returnerar Traffic Manager svar på en nodata-fråga.

    :::image type="content" source="./media/traffic-manager-geographic-routing-method/traffic-manager-geographic-overview.png" alt-text="Översikt över geografiska Traffic Manager":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver Traffic Manager profilen letar du reda på profilen och väljer **ta bort profil**.

:::image type="content" source="./media/traffic-manager-geographic-routing-method/delete-traffic-manager-profile.png" alt-text="Ta bort Traffic Manager profil":::

## <a name="next-steps"></a>Nästa steg

Mer information om geografisk routningsmetod finns i:

> [!div class="nextstepaction"]
> [Routningsmetod för geografisk trafik](traffic-manager-routing-methods.md#geographic)
