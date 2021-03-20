---
title: 'Självstudie: Konfigurera viktad trafik för resursallokering med Azure Traffic Manager'
description: I den här självstudien lär du dig att belastningsutjämna trafik med en Round-Robin-metod i Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: abcfce43b90c7371d5b38aa5b7a6d478e9d6a0dd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92207847"
---
# <a name="tutorial-configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Självstudie: Konfigurera den viktade routningsmetoden för trafik i Traffic Manager

Ett gemensamt metod mönster för trafik cirkulation är att tillhandahålla en uppsättning identiska slut punkter, bland annat moln tjänster och webbplatser, och skicka trafik till var och en. Följande steg beskriver hur du konfigurerar den här typen av metod för trafik dirigering.

> [!NOTE]
> Azure Web App tillhandahåller redan Round Robin-funktioner för belastnings utjämning för webbplatser i en Azure-region (som kan bestå av flera data Center). Med Traffic Manager kan du distribuera trafik mellan webbplatser i olika data Center.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Skapa en Traffic Manager profil med viktad routning.
> - Använd Traffic Manager profilen.
> - Ta bort Traffic Managers profil.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

## <a name="configure-the-weighted-traffic-routing-method"></a>Konfigurera den viktade trafikroutningsmetoden

1. Logga in på [Azure Portal](https://portal.azure.com) från en webbläsare.

1. I portalens sökfält söker du efter namnet på **Traffic Manager profilen** som du skapade i föregående avsnitt och väljer i Traffic Manager-profilen i resultaten som visas.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/search-traffic-manager-weighted-profile.png" alt-text="Sök efter Traffic Manager profil":::

1. Välj **konfiguration** och välj eller ange följande inställningar:

    | Inställning         | Värde                                              |
    | ---             | ---                                                |
    | Routningsmetod            | Välj **viktad**. |    
    | TTL-värde (Time to Live) för DNS | Det här värdet styr hur ofta klientens lokala namnserver för cachelagring ska fråga Traffic Manager systemet efter uppdaterade DNS-poster. Alla ändringar som sker med Traffic Manager, till exempel trafikflödets metod ändringar eller ändringar i tillgängligheten för tillagda slut punkter, tar den här tids perioden att uppdateras i hela det globala systemet med DNS-servrar. |
    | Protokoll    | Välj ett protokoll för slut punkts övervakning. *Alternativ: HTTP, HTTPS och TCP* |
    | Port | Ange port numret. |
    | Sökväg | Om du vill övervaka slut punkter måste du ange en sökväg och ett fil namn. Ett snedstreck "/" är en giltig post för den relativa sökvägen och innebär att filen finns i rot katalogen (standard). |
    | Anpassade huvud inställningar | Konfigurera de anpassade rubrikerna i formatet Host: contoso. com, newheader: newValue. Det högsta paret som stöds är 8. Gäller för http-och HTTPS-protokoll. Gäller för alla slut punkter i profilen |
    | Förväntade status kod intervall (standard: 200) | Konfigurera status kod intervallen i formatet 200-299301-301. Det maximala intervallet som stöds är 8. Gäller för http-och HTTPS-protokoll. Gäller för alla slut punkter i profilen |
    | Kontrollintervall | Konfigurera tidsintervallet mellan slut punkts hälso avsökningar. Du kan välja 10 eller 30 sekunder. |
    | Tolerera antal felaktiga försök | Konfigurera antalet hälso avsöknings fel som tolereras innan ett slut punkts fel utlöses. Du kan ange ett tal mellan 0 och 9. | 
    | Timeout för avsökning | Konfigurera tiden som krävs innan tids gränsen uppnås för en slut punkt för hälso avsökning. Värdet måste vara minst 5 och mindre än värdet för avsöknings intervall. |

1. Välj **Spara** att slutföra konfigurationen.

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-configuration.png" alt-text="Traffic Manager viktad konfiguration"::: 

1. Välj **slut punkt** och konfigurera vikten för varje slut punkt. Vikten kan vara mellan 1-1000. Ju högre vikt, desto högre prioritet.  

    :::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-configure-endpoints-weighted.png" alt-text="Konfiguration av Traffic Manager viktad slut punkt"::: 

## <a name="use-the-traffic-manager-profile"></a>Använd Traffic Manager profilen

**Traffic Manager-profilen** visar DNS-namnet på din nyligen skapade Traffic Manager-profil. Namnet kan användas av alla klienter (till exempel genom att navigera till den med hjälp av en webbläsare) för att dirigeras till den högra slut punkten som fastställs av routningstjänsten. I det här fallet dirigeras alla begär anden till varje slut punkt i en Round-Robin.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-overview.png" alt-text="Traffic Manager viktad översikt"::: 

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver Traffic Manager profilen letar du reda på profilen och väljer **ta bort profil**.

:::image type="content" source="./media/traffic-manager-weighted-routing-method/delete-traffic-manager-weighted-profile.png" alt-text="Ta bort Traffic Manager viktad profil":::

## <a name="next-steps"></a>Nästa steg

Mer information om viktad routningsmetod finns i:

> [!div class="nextstepaction"]
> [Metod för viktad trafik cirkulation](traffic-manager-routing-methods.md#weighted)