---
title: 'Självstudie: Skapa en lastbalanserare mellan regioner med hjälp av Azure Portal'
titleSuffix: Azure Load Balancer
description: Kom igång med den här självstudien och distribuera en Azure Load Balancer med Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: 16320021ede4a4e285c4e1973c166d2cdf643c4a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529538"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Självstudie: Skapa en Azure Load Balancer med hjälp av Azure Portal

En lastbalanserare mellan regioner säkerställer att en tjänst är tillgänglig globalt i flera Azure-regioner. Om en region misslyckas dirigeras trafiken till nästa närmaste felfria regionala lastbalanserare.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa lastbalanserare mellan regioner.
> * Skapa en backend-pool som innehåller två regionala lastbalanserare.
> * Skapa en lastbalanseringsregel.
> * Testa lastbalanseraren.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!IMPORTANT]
> Flera regioner Azure Load Balancer för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration.
- Två  Standard-SKU:er för Azure Load Balancers med backend-pooler som distribuerats i två olika Azure-regioner.
    - Information om hur du skapar en regional standardlastbalanserare och virtuella datorer för serverpooler finns i [Snabbstart:](quickstart-load-balancer-standard-public-portal.md)Skapa en offentlig lastbalanserare för att belastningsutjämna virtuella datorer med hjälp av Azure Portal .
        - Lägg till namnet på lastbalanserare, virtuella datorer och andra resurser i varje region med **-R1** och **-R2**. 

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

[Logga in](https://portal.azure.com) i Azure-portalen.

## <a name="create-cross-region-load-balancer"></a>Skapa lastbalanserare mellan regioner

I det här avsnittet skapar du en lastbalanserare mellan regioner och en offentlig IP-adress.

1. Välj **Skapa en resurs**. 
2. I sökrutan anger du **Lastbalanserare**. Välj **Lastbalanserare** i sökresultatet.
3. På sidan **Lastbalanserare** väljer du **Skapa**.
4. På fliken **Grundläggande på** sidan **Skapa lastbalanserare** anger eller väljer du följande information: 

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och ange **CreateCRLBTutorial-rg** i textrutan.|
    | Name                   | Ange **myLoadBalancer-CR**                                   |
    | Region         | Välj **USA, västra.**                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Låt standardvärdet **Standard vara kvar.** |
    | Nivå           | Välj **Global** |
    | Offentlig IP-adress | Välj **Skapa ny**.|
    | Namn på offentlig IP-adress | Skriv **myPublicIP-CR** i textrutan.|
    | Routningsprioritet| Välj **Microsoft-nätverk**. </br> Mer information om routningsinställningar finns i [Vad är routningsinställning (förhandsversion)?](../virtual-network/routing-preference-overview.md). |

    > [!NOTE]
    > Lastbalanserare mellan regioner kan bara distribueras i följande hemregioner: USA, östra **2, USA, västra, Europa, västra, Sydostasien, USA, centrala, Europa, norra Asien, östra**. Mer information finns i **https://aka.ms/homeregionforglb**.


3. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa.**

4. På fliken **Granska + skapa** väljer du **Skapa.**   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Skapa en lastbalanserare mellan regioner" border="true":::

## <a name="create-backend-pool"></a>Skapa serverdelspool

I det här avsnittet lägger du till två regionala standardlastbalanserare i backend-poolen för lastbalanserare mellan regioner.

> [!IMPORTANT]
> För att slutföra de här stegen ser du till att två regionala lastbalanserare med backend-pooler har distribuerats i din prenumeration.  Mer information finns i **[Snabbstart: Skapa en offentlig lastbalanserare för att lastbalansera virtuella datorer med hjälp av Azure Portal](quickstart-load-balancer-standard-public-portal.md)**.

Skapa backend-adresspoolen **myBackendPool-CR för** att inkludera de regionala standardlastbalanserarna.

1. Välj **Alla tjänster** på den vänstra menyn, välj **Alla** resurser och välj **sedan myLoadBalancer-CR** i resurslistan.

2. Under **Inställningar** väljer du **Backend-pooler** och sedan Lägg **till**.

3. På sidan **Lägg till en backend-pool** skriver du **myBackendPool-CR** som namn.

4. Välj **Lägg till**.

4. Välj **myBackendPool-CR.**

5. Under **Lastbalanserare** väljer du den nedströmsrutan under **Lastbalanserare.**

5. Välj **myLoadBalancer-R1** eller namnet på lastbalanseraren i region 1.

6. Välj pull-down-rutan under **IP-konfiguration för frontend.** Välj **LoadBalancerFrontEnd**.

7. Upprepa steg 4–6 för att lägga **till myLoadBalancer-R2.**

8. Välj **Lägg till**.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Lägga till regionala lastbalanserare i backendpoolen" border="true":::

## <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

I det här avsnittet skapar du en lastbalanseringsregel:

* Med **namnet myHTTPRule.**
* I frontend med namnet **LoadBalancerFrontEnd**.
* Lyssnar på **port 80**.
* Dirigerar belastningsutjämnad trafik till backend med **namnet myBackendPool-CR** **på port 80**.

    > [!NOTE]
    > Frontend-porten måste matcha backend-porten och frontend-porten för de regionala lastbalanserarna i backend-poolen.

1. Välj **Alla tjänster** på den vänstra menyn, välj **Alla** resurser och välj **sedan myLoadBalancer-CR** i resurslistan.

2. Under **Inställningar** väljer du **Belastningsutjämningsregler** och sedan Lägg **till**.

3. Använd dessa värden för att konfigurera belastningsutjämningsregeln:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHTTPRule.** |
    | IP-version | Välj **IPv4** |
    | IP-adress för frontend | Välj **LoadBalancerFrontEnd** |
    | Protokoll | Välj **TCP**. |
    | Port | Ange **80**.|
    | Serverdelsport | Ange **80**. |
    | Serverdelspool | Välj **myBackendPool**.|
    | Tidsgräns för inaktivitet (minuter) | Flytta skjutreglaget **till 15**. |
    | TCP-återställning | Välj **Aktiverad**. |

4. Lämna resten av standardinställningarna och välj sedan **OK.**

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Skapa lastbalanseringsregel" border="true":::

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

I det här avsnittet testar du lastbalanserare mellan regioner. Du ansluter till den offentliga IP-adressen i en webbläsare.  Du stoppar de virtuella datorerna i en av de regionala serverpoolerna för lastbalanserare och observerar redundansen.

1. Hitta den offentliga IP-adressen för lastbalanseraren på **skärmen** Översikt. Välj **Alla tjänster** på den vänstra menyn, välj Alla **resurser** och välj sedan **myPublicIP-CR.**

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Testa lastbalanseraren" border="true":::

3. Stoppa de virtuella datorerna i serverpoolen för en av de regionala lastbalanserarna.

4. Uppdatera webbläsaren och observera redundans för anslutningen till den andra regionala lastbalanseraren.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Testa lastbalanserare efter redundans" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när de inte längre behövs. 

Det gör du genom att välja resursgruppen **CreateCRLBTutorial-rg** som innehåller resurserna och sedan ta **bort**.

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

* Skapat en lastbalanserare mellan regioner.
* Regionala lastbalanserare har lagts till i backend-poolen för lastbalanserare mellan regioner.
* Skapat en belastningsutjämningsregel.
* Testade lastbalanseraren.

Mer information om lastbalanserare mellan regioner finns i:
> [!div class="nextstepaction"]
> [Lastbalanserare mellan regioner (förhandsversion)](cross-region-overview.md)
