---
title: Konfigurera skalnings uppsättning för virtuell dator med en befintlig Azure Load Balancer-Azure Portal
description: Lär dig hur du konfigurerar en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer med hjälp av Azure Portal.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: cb7bfb9ac4b10b807ac186d087b0037953abd559
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91439526"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-portal"></a>Konfigurera en skalnings uppsättning för en virtuell dator med en befintlig Azure Load Balancer med hjälp av Azure Portal

I den här artikeln får du lära dig hur du konfigurerar en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer. 

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration.
- En befintlig SKU för standard-SKU i prenumerationen där den virtuella datorns skalnings uppsättning ska distribueras.
- En Azure-Virtual Network för skalnings uppsättningen för den virtuella datorn.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).



## <a name="deploy-virtual-machine-scale-set-with-existing-load-balancer"></a>Distribuera skalnings uppsättning för virtuell dator med befintlig belastningsutjämnare

I det här avsnittet ska du skapa en skalnings uppsättning för virtuella datorer i Azure Portal med en befintlig Azure Load Balancer.

> [!NOTE]
> Följande steg förutsätter att ett virtuellt nätverk med namnet **myVNet** och en Azure-belastningsutjämnare med namnet **myLoadBalancer** har distribuerats tidigare.

1. Klicka på **skapa en**  >    >  **skalnings uppsättning för virtuell dator** på den övre vänstra sidan på skärmen eller Sök efter skalnings uppsättning för **virtuella datorer** i Marketplace-sökningen.

2. Välj **Skapa**.

3. I **skapa en skalnings uppsättning för virtuell dator** anger eller väljer du den här informationen på fliken **grundläggande** :

    | Inställning                        | Värde                                                                                                 |
    |--------------------------------|-------------------------------------------------------------------------------------------------------|
    | **Projektinformation**            |                                                                                                       |
    | Prenumeration                   | Välj din Azure-prenumeration                                                                        |
    | Resursgrupp                 | Välj Skapa ny, ange **myResourceGroup**, välj OK eller Välj en befintlig resurs grupp. |
    | **Information om skalnings uppsättning**          |                                                                                                       |
    | Namn på VM-skalningsuppsättning | Ange **myVMSS**                                                                                      |
    | Region                         | Välj **USA, östra 2**                                                                                    |
    | Tillgänglighetszon              | Välj **ingen**                                                                                       |
    | **Instansinformation**           |                                                                                                       |
    | Bild                          | Välj **Ubuntu Server 18,04 LTS**                                                                    |
    | Azure Spot-instans            | Välj **Nej**                                                                                         |
    | Storlek                           | Lämna kvar som standard                                                                                      |
    | **Administratörs konto**      |                                                                                                       |
    | Autentiseringstyp            | Välj **lösen ord**                                                                                   |
    | Användarnamn                       | Ange ditt administratörs användar namn        |
    | Lösenord                       | Ange ditt administratörs lösen ord    |
    | Bekräfta lösenordet               | Ange administratörs lösen ordet igen |


    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-01.png" alt-text="Skärm bild som visar fliken för att skapa grundläggande skalnings uppsättningar för virtuella datorer." border="true":::

4. Välj fliken **Nätverk**.

5. Ange eller Välj den här informationen på fliken **nätverk** :

     Inställning                           | Värde                                                    |
    |-----------------------------------|----------------------------------------------------------|
    | **Konfigurera virtuella nätverk** |                                                          |
    | Virtuellt nätverk                   | Välj **myVNet** eller ditt befintliga virtuella nätverk.      |
    | **Belastningsutjämning**                |                                                          |
    | Använd en belastningsutjämnare               | Välj **Ja**                                           |
    | **Inställningar för belastnings utjämning**       |                                                          |
    | Alternativ för belastnings utjämning            | Välj **Azure Load Balancer**                           |
    | Välj en belastningsutjämnare            | Välj **myLoadBalancer** eller din befintliga belastningsutjämnare |
    | Välj en backend-pool             | Välj **myBackendPool** eller din befintliga backend-pool.  |

    :::image type="content" source="./media/vm-scale-sets/create-vm-scale-set-02.png" alt-text="Skärm bild som visar fliken Skapa virtuell dator skalnings uppsättning nätverk." border="true":::

6. Välj fliken **hantering** .

7. På fliken **hantering** ställer du in **startdiagnostik** på **av**.

8. Välj knappen blå **granskning + skapa** .

9. Granska inställningarna och välj knappen **skapa** .

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du distribuerat en skalnings uppsättning för virtuella datorer med en befintlig Azure Load Balancer.  Mer information om skalnings uppsättningar för virtuella datorer och belastningsutjämnare finns i:

- [Vad är Azure Load Balancer?](load-balancer-overview.md)
- [Vad är skalningsuppsättningar för virtuella datorer?](../virtual-machine-scale-sets/overview.md)
