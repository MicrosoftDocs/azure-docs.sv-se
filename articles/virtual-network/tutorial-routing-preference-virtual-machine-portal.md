---
title: Konfigurera Dirigerings inställningar för en virtuell dator – Azure Portal
description: Lär dig hur du skapar en virtuell dator med en offentlig IP-adress med inställnings alternativ för routning med hjälp av Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 0559d02ec603d12578fa46d9790d0711fde5e38b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670898"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Konfigurera Dirigerings inställningar för en virtuell dator med hjälp av Azure Portal

Den här artikeln visar hur du konfigurerar cirkulations inställningar för en virtuell dator. Internet bindnings trafik från den virtuella datorn kommer att dirigeras via Internet leverantörens nätverk när du väljer **Internet** som alternativ för routning av inställningar. Standardroutningen är via Microsofts globala nätverk.

Den här artikeln visar hur du skapar en virtuell dator med en offentlig IP-adress som är inställd på att dirigera trafik via det offentliga Internet med hjälp av Azure Portal.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute (beräkna**) och välj sedan **Windows Server 2016 VM** eller något annat operativ system som du väljer.
3. Ange eller Välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVM|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösen ordet måste vara minst 12 tecken långt och uppfylla de [definierade komplexitets kraven](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Använd befintlig** och sedan **myResourceGroup**.|
    |Location| Välj **USA, östra**|

4. Välj en storlek för den virtuella datorn och sedan **Välj**.
5. Under fliken **nätverk** klickar du på **Skapa ny** för **offentlig IP-adress**.
6. Ange *myPublicIpAddress*, Välj SKU som **standard** och välj sedan dirigera inställningar **Internet** och tryck sedan på **OK**, som du ser i följande bild:

   ![Välj statisk](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Välj en port eller inga portar under **Välj offentliga inkommande portar**. Portal 3389 är markerad, för att aktivera fjärråtkomst till den virtuella Windows Server-datorn från Internet. Det rekommenderas inte att öppna port 3389 från Internet för produktions arbets belastningar.

   ![Välj en port](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Godkänn de återstående standardinställningarna och välj **OK**.
8. På sidan **Sammanfattning** väljer du **Skapa**. Det tar några minuter att distribuera den virtuella datorn.
9. När den virtuella datorn har distribuerats anger du *myPublicIpAddress* i sökrutan längst upp i portalen. När **myPublicIpAddress** visas i Sök resultaten väljer du det.
10. Du kan visa den offentliga IP-adress som har tilldelats och att adressen är tilldelad den virtuella **myVM** -datorn, som du ser i följande bild:

    ![Skärm bild som visar NÄTVERKSKORTets offentliga I P för nätverks gränssnittet mynic.](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Välj **nätverk** och klicka sedan på NIC- **mynic** och välj sedan den offentliga IP-adressen för att bekräfta att cirkulations inställningen tilldelas som **Internet**.

    ![Skärm bild som visar inställningarna i P-adress och routning för en offentlig I P-adress.](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen, skalningsuppsättningen och alla resurser som den innehåller:

1. Skriv *myResourceGroup* i **sökrutan** överst i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* i **SKRIV RESURSGRUPPSNAMNET:** och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [offentlig IP-adress med inställningar för routning](routing-preference-overview.md).
- Läs mer om [offentliga IP-adresser](./public-ip-addresses.md#public-ip-addresses) i Azure.
- Läs mer om [Inställningar för offentliga IP-adresser](virtual-network-public-ip-address.md#create-a-public-ip-address).