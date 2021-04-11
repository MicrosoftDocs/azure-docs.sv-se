---
title: 'Självstudie: skapa en belastningsutjämnare för flera regioner med hjälp av Azure Portal'
titleSuffix: Azure Load Balancer
description: Kom igång med den här självstudien för att distribuera en Azure Load Balancer över flera regioner med Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 02/24/2021
ms.openlocfilehash: 65d85f51afef36aa618868e2fda1d2bbf583ea21
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221134"
---
# <a name="tutorial-create-a-cross-region-azure-load-balancer-using-the-azure-portal"></a>Självstudie: skapa en Azure Load Balancer över flera regioner med hjälp av Azure Portal

En belastningsutjämnare för flera regioner säkerställer att en tjänst är tillgänglig globalt över flera Azure-regioner. Om en region Miss lyckas dirigeras trafiken till nästa närmaste felfria regionala belastnings utjämning.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en belastningsutjämnare för flera regioner.
> * Skapa en backend-pool som innehåller två regionala belastningsutjämnare.
> * Skapa en lastbalanseringsregel.
> * Testa belastningsutjämnaren.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!IMPORTANT]
> Azure Load Balancer över flera regioner är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration.
- Två **standardiserade** SKU: er i Azure med backend-pooler distribuerade i två olika Azure-regioner.
    - Information om hur du skapar en regional standard belastningsutjämnare och virtuella datorer för backend-pooler finns i [snabb start: skapa en offentlig belastningsutjämnare för att belastningsutjämna virtuella datorer med hjälp av Azure Portal](quickstart-load-balancer-standard-public-portal.md).
        - Lägg till namnet på belastningsutjämnare, virtuella datorer och andra resurser i varje region med a **-R1** och **-R2**. 

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

[Logga in](https://portal.azure.com) i Azure-portalen.

## <a name="create-cross-region-load-balancer"></a>Skapa en belastningsutjämnare för flera regioner

I det här avsnittet ska du skapa en belastningsutjämnare för flera regioner och en offentlig IP-adress.

1. Välj **Skapa en resurs**. 
2. I rutan Sök anger du **Load Balancer**. Välj **Load Balancer** i Sök resultaten.
3. På sidan **belastnings utjämning** väljer du **skapa**.
4. På fliken **grundläggande** på sidan **skapa belastnings utjämning** anger eller väljer du följande information: 

    | Inställning                 | Värde                                              |
    | ---                     | ---                                                |
    | Prenumeration               | Välj din prenumeration.    |    
    | Resursgrupp         | Välj **Skapa ny** och ange **CreateCRLBTutorial-RG** i text rutan.|
    | Name                   | Ange **myLoadBalancer – CR**                                   |
    | Region         | Välj **(US) västra USA**.                                        |
    | Typ          | Välj **Offentlig**.                                        |
    | SKU           | Låt standardvärdet vara **standard.** |
    | Nivå           | Välj **Global** |
    | Offentlig IP-adress | Välj **Skapa ny**.|
    | Namn på offentlig IP-adress | Skriv **myPublicIP – CR** i text rutan.|
    | Routningsprioritet| Välj **Microsoft-nätverk**. </br> Mer information om inställningar för routning finns i [Vad är cirkulations inställningar (för hands version)?](../virtual-network/routing-preference-overview.md). |

    > [!NOTE]
    > Belastnings utjämning för flera regioner kan bara distribueras i följande hem regioner: **östra USA 2, västra USA, Västeuropa, Sydostasien, centrala USA, norra Europa, Asien, östra**. Mer information finns i **https://aka.ms/homeregionforglb**.


3. Acceptera standardinställningarna för återstående inställningar och välj sedan **Granska + skapa**.

4. På fliken **Granska och skapa** väljer du **skapa**.   

    :::image type="content" source="./media/tutorial-cross-region-portal/create-cross-region.png" alt-text="Skapa en belastningsutjämnare för flera regioner" border="true":::

## <a name="create-backend-pool"></a>Skapa serverdelspool

I det här avsnittet ska du lägga till två regionala standard belastningsutjämnare till backend-poolen för belastningsutjämnaren över flera regioner.

> [!IMPORTANT]
> För att slutföra de här stegen ser du till att två regionala belastningsutjämnare med backend-pooler har distribuerats i din prenumeration.  Mer information finns i, **[snabb start: skapa en offentlig belastningsutjämnare för att belastningsutjämna virtuella datorer med hjälp av Azure Portal](quickstart-load-balancer-standard-public-portal.md)**.

Skapa backend-adresspoolen **myBackendPool – CR** för att inkludera regionala standard belastningsutjämnare.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer-CR** i listan resurser.

2. Under **Inställningar** väljer du **backend-pooler** och väljer sedan **Lägg till**.

3. Skriv **myBackendPool-CR** som namn på sidan **Lägg till en backend-pool** .

4. Välj **Lägg till**.

4. Välj **myBackendPool – CR**.

5. Under **belastningsutjämnare** väljer du den nedrullningsbara rutan under **belastnings utjämning**.

5. Välj **myLoadBalancer-R1** eller namnet på belastningsutjämnaren i region 1.

6. Välj den nedrullningsbara List rutan under **klient delens IP-konfiguration**. Välj **LoadBalancerFrontEnd**.

7. Upprepa steg 4-6 för att lägga till **myLoadBalancer-R2**.

8. Välj **Lägg till**.

    :::image type="content" source="./media/tutorial-cross-region-portal/add-to-backendpool.png" alt-text="Lägg till regionala belastningsutjämnare till backendpool" border="true":::

## <a name="create-a-load-balancer-rule"></a>Skapa en lastbalanseringsregel

I det här avsnittet ska du skapa en belastnings Utjämnings regel:

* Med namnet **myHTTPRule**.
* I klient delen med namnet **LoadBalancerFrontEnd**.
* Lyssnar på **Port 80**.
* Dirigerar belastnings bal anse rad trafik till Server delen med namnet **myBackendPool-CR** på **port 80**.

    > [!NOTE]
    > Frontend-porten måste matcha backend-porten och frontend-porten för de regionala belastnings utjämningarna i backend-poolen.

1. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myLoadBalancer-CR** i listan resurser.

2. Välj **belastnings Utjämnings regler** under **Inställningar** och välj sedan **Lägg till**.

3. Använd de här värdena för att konfigurera belastnings Utjämnings regeln:
    
    | Inställning | Värde |
    | ------- | ----- |
    | Namn | Ange **myHTTPRule**. |
    | IP-version | Välj **IPv4** |
    | IP-adress för klient del | Välj **LoadBalancerFrontEnd** |
    | Protokoll | Välj **TCP**. |
    | Port | Ange **80**.|
    | Serverdelsport | Ange **80**. |
    | Serverdelspool | Välj **myBackendPool**.|
    | Hälsoavsökning | Välj **myHealthProbe**. |
    | Tids gräns för inaktivitet (minuter) | Flytta skjutreglaget till **15**. |
    | TCP-återställning | Välj **Aktiverad**. |

4. Lämna resten av standardinställningarna och välj sedan **OK**.

    :::image type="content" source="./media/tutorial-cross-region-portal/create-lb-rule.png" alt-text="Skapa belastnings Utjämnings regel" border="true":::

## <a name="test-the-load-balancer"></a>Testa lastbalanseraren

I det här avsnittet ska du testa belastningsutjämnaren för flera regioner. Du ansluter till den offentliga IP-adressen i en webbläsare.  Du kommer att stoppa de virtuella datorerna i en av de regionala Server dels poolerna för belastnings utjämning och kontrol lera redundansväxlingen.

1. Hitta den offentliga IP-adressen för belastningsutjämnaren på **översikts** skärmen. Välj **alla tjänster** i den vänstra menyn, Välj **alla resurser** och välj sedan **myPublicIP-CR**.

2. Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält. IIS-webbserverns standardsida visas i webbläsaren.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-1.png" alt-text="Testa lastbalanseraren" border="true":::

3. Stoppa de virtuella datorerna i backend-poolen för en av de regionala belastnings utjämningen.

4. Uppdatera webbläsaren och kontrol lera att anslutningen till den andra regionala belastningsutjämnaren är redundansväxling.

    :::image type="content" source="./media/tutorial-cross-region-portal/test-cr-lb-2.png" alt-text="Testa belastnings utjämning efter redundans" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, lastbalanseraren och alla relaterade resurser när de inte längre behövs. 

Det gör du genom att markera resurs gruppen **CreateCRLBTutorial-RG** som innehåller resurserna och sedan välja **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

* En belastningsutjämnare för flera regioner har skapats.
* Regionala belastningsutjämnare har lagts till i backend-poolen för belastningsutjämnaren i flera regioner.
* En regel för belastnings utjämning har skapats.
* Belastnings utjämning har testats.

Mer information om belastnings utjämning över flera regioner finns i:
> [!div class="nextstepaction"]
> [Belastningsutjämnare för flera regioner (för hands version)](cross-region-overview.md)
