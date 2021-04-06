---
title: 'Självstudie: Konfigurera prioriterad trafik routning med Azure Traffic Manager'
description: I den här självstudien beskrivs hur du konfigurerar routningsmetoden för prioritets trafik i Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: duau
ms.openlocfilehash: 1835377f4690097c8390957bf7d897242ba7aace
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92208064"
---
# <a name="tutorial-configure-priority-traffic-routing-method-in-traffic-manager"></a>Självstudie: Konfigurera prioritet för trafik cirkulation i Traffic Manager

I den här självstudien beskrivs hur du använder Azure Traffic Manager för att dirigera användar trafik till vissa slut punkter med hjälp av metoden för prioritets dirigering. I den här routningsmetod definierar du ordningen för varje slut punkt som hamnar i Traffic Manager profil konfiguration. Trafik från användare dirigeras till slut punkten i den ordning de visas. Den här metoden för routning är användbar när du vill konfigurera för redundansväxling av tjänster. Den primära slut punkten får ett prioritets nummer på 1 och kommer att betjäna alla inkommande begär Anden. Medan slut punkter med lägre prioritet fungerar som säkerhets kopieringar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> - Skapa en Traffic Manager profil med prioriterad routning.
> - Lägg till slut punkter.
> - Konfigurera prioritet för slut punkter.
> - Använd Traffic Manager profilen.
> - Ta bort Traffic Managers profil.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="to-configure-the-priority-traffic-routing-method"></a>Så här konfigurerar du metoden för prioritets väg för trafik
1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare.

1. Välj **+ skapa en resurs** på den vänstra sidan. Sök efter **Traffic Manager profil** och välj **skapa**.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-priority-profile.png" alt-text="Skapa en profil för Traffic Manager prioritet":::

1. Definiera följande inställningar på sidan *skapa Traffic Manager profil* :

    | Inställning         | Värde                                              |
    | ---             | ---                                                |
    | Namn            | Ange ett namn för din profil. Det här namnet måste vara unikt inom trafficmanager.net-zonen. För att få åtkomst till din Traffic Manager-profil använder du DNS-namnet `<profilename>.trafficmanager.net` . |    
    | Routningsmetod  | Välj **Prioritet**. |
    | Prenumeration    | Välj din prenumeration. |
    | Resursgrupp   | Använd en befintlig resurs grupp eller skapa en ny resurs grupp för att placera profilen under. Om du väljer att skapa en ny resurs grupp använder du List rutan *resurs grupps plats* för att ange platsen för resurs gruppen. Den här inställningen refererar till platsen för resurs gruppen och har ingen inverkan på den Traffic Manager profil som distribueras globalt. |

1. Välj **skapa** för att distribuera din Traffic Manager-profil.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/create-traffic-manager-profile-priority.png" alt-text="Skapa en Traffic Manager profil prioritet":::

## <a name="add-endpoints"></a>Lägg till slut punkter

1. Välj Traffic Manager profilen i listan.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-list.png" alt-text="Lista med Traffic Manager profil":::

1. Välj **slut punkter** under *Inställningar* och välj **+ Lägg** till för att lägga till en ny slut punkt.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-add-endpoints.png" alt-text="Traffic Manager lägga till slut punkter":::

1. Välj eller ange följande inställningar: 

    | Inställning                | Värde                                              |
    | ---                    | ---                                                |
    | Typ                   | Välj typ av slut punkt. |    
    | Name                   | Ange ett namn för att identifiera slut punkten. |
    | Målresurstyp   | Välj resurs typ för målet. |
    | Målresurs        | Välj resursen i listan. |
    | Prioritet               | Ange ett prioritets nummer för den här slut punkten. 1 är den högsta prioriteten. |


1. Välj **Lägg till** för att lägga till slut punkten. Upprepa steg 2 och 3 för att lägga till ytterligare slut punkter. Kom ihåg att ange rätt prioritets nummer.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/add-endpoint.png" alt-text="Lägg till prioritet 1-slutpunkt":::

1. På sidan **slut punkter** granskar du prioritets ordningen för dina slut punkter. När du väljer metoden **prioriterad** trafikroutning är ordningen för de valda slut punkterna. Kontrol lera prioritetsordningen för slut punkter.  Den primära slut punkten är överst. Dubbel kontroll över den ordning som den visas. Alla begär Anden dirigeras till den första slut punkten och om Traffic Manager identifierar att den inte är felfri växlar trafiken automatiskt över till nästa slut punkt. 

    :::image type="content" source="./media/traffic-manager-priority-routing-method/endpoints-list.png" alt-text="Lista över prioritets slut punkter":::

1. Om du vill ändra slut punktens prioritets ordning väljer du slut punkten, ändrar prioritet svärdet och väljer **Spara** för att spara slut punkts inställningarna.

## <a name="use-the-traffic-manager-profile"></a>Använd Traffic Manager profilen

1.  I portalens sökfält söker du efter namnet på **Traffic Manager profilen** som du skapade i föregående avsnitt och väljer i Traffic Manager-profilen i resultaten som visas.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/search-traffic-manager-profile.png" alt-text="Sök Traffic Manager profil":::

1.  På sidan Översikt över **Traffic Manager profil** visas DNS-namnet för din nyligen skapade Traffic Manager-profil. Detta kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till den högra slut punkten som fastställs av typen av routning. I det här fallet dirigeras alla begär anden till den första slut punkten och om Traffic Manager identifierar att den inte är felfri växlar trafiken automatiskt över till nästa slut punkt.

    :::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-profile-dns-name.png" alt-text="DNS-namn för Traffic Manager":::

1. När din Traffic Manager-profil fungerar redigerar du DNS-posten på den auktoritativa DNS-servern för att peka ditt företags domän namn till Traffic Manager domän namnet.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver Traffic Manager profilen letar du reda på profilen och väljer **ta bort profil**.

:::image type="content" source="./media/traffic-manager-priority-routing-method/traffic-manager-delete-priority-profile.png" alt-text="Ta bort Traffic Manager prioritets profil":::

## <a name="next-steps"></a>Nästa steg

Mer information om metoden för prioritets dirigering finns i:

> [!div class="nextstepaction"]
> [Metod för prioritets cirkulation](traffic-manager-routing-methods.md#priority-traffic-routing-method)
