---
title: Konfigurera routning av Multivärdes trafik – Azure Traffic Manager
description: Den här artikeln förklarar hur du konfigurerar Traffic Manager för att dirigera trafik till en/AAAA-slutpunkter.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 85e088dda767a6f6c80ac0a9f6eed84e8802e5ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994935"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Konfigurera routningsmetod för multivärde i Traffic Manager

Den här artikeln beskriver hur du konfigurerar routningsmetod för Multivärdes trafik. Med routnings metoden för **Multivärdes** trafik kan du returnera flera felfria slut punkter och öka tillförlitligheten för ditt program eftersom klienterna har fler alternativ för att försöka igen utan att behöva göra en annan DNS-sökning. Multivärdes-routning är bara aktiverat för profiler som har alla deras slut punkter angivna med IPv4-eller IPv6-adresser. När en fråga tas emot för den här profilen returneras alla felfria slut punkter baserat på det konfigurerbara Max antalet returnerade antal. 

>[!NOTE]
> Vid det här tillfället stöds inte att lägga till slut punkter med IPv4-eller IPv6-adresser för slut punkter av typen **extern** och därför stöds multivärde-routning också endast för sådana slut punkter.

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.
## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resurs grupp för Traffic Manager profilen.
1. I den vänstra rutan i Azure Portal väljer du **resurs grupper**.
2. Välj **Lägg till** i **resurs grupper** överst på sidan.
3. I **resurs grupp namn** skriver du ett namn *myResourceGroupTM1*. För **resurs gruppens plats** väljer du **östra USA** och väljer sedan **OK**.

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil
Skapa en Traffic Manager profil som dirigerar användar trafiken genom att skicka dem till slut punkten med lägsta latens.

1. På den övre vänstra sidan av skärmen väljer du **skapa en resurs**  >  **nätverk**  >  **Traffic Manager profil**  >  **skapa**.
2. I **skapa Traffic Manager profil**, ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **skapa**:
    
    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Namn                   | Namnet måste var unikt inom trafficmanager.net-zonen och generera DNS-namnet, trafficmanager.net, som används för att öppna din Traffic Manager-profil.                                   |
    | Routningsmetod          | Välj routningsmetod för **multivärde** .                                       |
    | Prenumeration            | Välj din prenumeration.                          |
    | Resursgrupp          | Välj *myResourceGroupTM1*. |
    | Location                | Inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som distribueras globalt.                              |
   |        |           | 
  
   ![Skapa en Traffic Manager-profil](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

Lägg till två IP-adresser som externa slut punkter i Traffic Manager profilen för multivärde som du skapade i föregående steg.

1. I portalens sökfält söker du efter det Traffic Manager-profilnamn som du skapade i föregående avsnitt och väljer profilen i det resultat som visas.
2. I **Traffic Manager-profilen** går du till avsnittet **Inställningar** och klickar på **Slutpunkter** och klickar sedan på **Lägg till**.
3. Ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Typ                    | Extern slut punkt                                   |
    | Name           | myEndpoint1                                        |
    | Fullständigt kvalificerat domän namn (FQDN) eller IP           | Ange den offentliga IP-adressen för den slut punkt som du vill lägga till i den här Traffic Manager profilen                         |
    |        |           |

4. Upprepa steg 2 och 3 för att lägga till en annan slut punkt med namnet *myEndpoint2*, för **fullständigt kvalificerat domän namn (FQDN) eller IP**, anger du den offentliga IP-adressen för den andra slut punkten.
5. När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

   ![Lägga till en Traffic Manager-slutpunkt](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [routningsmetoden för viktad trafik](traffic-manager-configure-weighted-routing-method.md).
- Lär dig mer om [prioritetsroutningsmetoden](traffic-manager-configure-priority-routing-method.md).
- Läs mer om [routningsmetod för prestanda](traffic-manager-configure-performance-routing-method.md)
- Lär dig mer om den [geografiska routningsmetoden](traffic-manager-configure-geographic-routing-method.md).


