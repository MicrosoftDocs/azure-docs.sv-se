---
title: Konfigurera privata IP-adresser för virtuella datorer – Azure Portal
description: Lär dig hur du konfigurerar privata IP-adresser för virtuella datorer med hjälp av Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96015952"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Konfigurera en privat IP-adress för en virtuell dator med hjälp av Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Följande exempel steg förväntar sig en enkel miljö som redan har skapats. Om du vill köra stegen som de visas i det här dokumentet måste du först [skapa ett virtuellt nätverk](quick-create-portal.md#create-a-virtual-network). I steg 3 använder du dock dessa värden i stället:

| Inställning | Värde |
| ------- | ----- |
| Namn | *TestVNet* |
| Adressutrymme | *192.168.0.0/16* |
| Resursgrupp | **TestRG** (om det behövs väljer du **Skapa nytt** för att skapa den) |
| Undernät – Namn | *Delen* |
| Undernät – adressintervall | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Skapa en virtuell dator för att testa statiska privata IP-adresser
När du skapar en virtuell dator i distributions läge för Resource Manager kan du inte ange en statisk privat IP-adress med hjälp av Azure Portal. I stället skapar du den virtuella datorn först. Sedan kan du ange att den privata IP-adressen ska vara statisk.

Följ dessa steg om du vill skapa en virtuell dator med namnet *DNS01* i *klient delens* undernät i ett virtuellt nätverk med namnet *TestVNet*:

1. Från [Azure Portal](https://portal.azure.com) -menyn väljer du **skapa en resurs**.

    ![Skapa en resurs Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Välj den virtuella **Compute**-  >  **datorn**.

    ![Skapa virtuell dator, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. I **grunderna** anger du värden för objekt enligt beskrivningen i följande tabell. Välj sedan **Nästa &nbsp; : &nbsp; diskar** och **Nästa &nbsp; : &nbsp; nätverk**.

    | Objekt | Värde |
    | --- | --- |
    | **Prenumeration** | Din aktuella prenumeration |
    | **Resursgrupp** | **TestRG** (Välj från List rutan) |
    | **Namn på virtuell dator** | *DNS01* |
    | **Region** | **(USA) USA, östra** |
    | **Bild** | **Windows Server 2019 Datacenter** |
    | **Storlek** | **VM-storlek** på **B1ls**, **erbjudande** av **standard** |
    | **Användarnamn** | Användar namnet för ditt administratörs konto |
    | **Lösenord** | Lösen ordet för administratörs kontots användar namn |
    | **Bekräfta lösenord** | Lösen ordet igen |

    ![Fliken grundläggande, skapa en virtuell dator, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. I **nätverk** anger du värden för objekt enligt beskrivningen i följande tabell och väljer sedan **Nästa**.

    | Objekt | Värde |
    | --- | --- |
    | **Virtuellt nätverk** | **TestVNet** |
    | **Undernät** | **Delen** |

    ![Fliken nätverk, skapa en virtuell dator, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. Under **hantering**, under **diagnostik lagrings konto**, väljer du **vnetstorage**. Om lagrings kontot inte visas i listan väljer du **Skapa nytt**, anger ett **namn** på *vnetstorage* och väljer **OK**. Välj slutligen **Granska &nbsp; + &nbsp; skapa**.

    ![Fliken hantering, skapa en virtuell dator, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. Granska översikts informationen i **Granska och skapa** och välj sedan **skapa**.

    ![Granska + skapa-fliken, skapa en virtuell dator, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Följande meddelande visas när den virtuella datorn har skapats.

![Distributions slut för ande meddelande, skapa en virtuell dator Azure Portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Hämta information om privat IP-adress för en virtuell dator
Så här visar du information om den privata IP-adressen för den nya virtuella datorn:

1. Gå till [Azure Portal](https://portal.azure.com) för att hitta den virtuella datorn. Sök efter och välj **virtuella datorer**.

    ![Virtual Machines, search box Azure Portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Välj namnet på den nya virtuella datorn (**DNS01**).

    ![Lista över virtuella datorer Azure Portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Välj **nätverk** och välj det enda nätverks gränssnitt som anges.

    ![Nätverks gränssnitt, nätverk, virtuell dator, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Välj **IP-konfigurationer** och välj IP-konfigurationen som visas i tabellen.

    ![IP-konfiguration, nätverks gränssnitt, nätverk, virtuell dator, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. I **Inställningar för privat IP-adress** går du till det virtuella nätverket/under nätet i **TestVNet/klient delen** och noterar **tilldelning** svärdet (**dynamisk** eller **statisk**) och **IP-adressen**.

    ![Dynamisk eller statisk tilldelning, gamla inställningar för privata IP-adresser, IP-konfiguration, nätverks gränssnitt, nätverk, virtuell dator, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Lägga till en statisk privat IP-adress till en befintlig virtuell dator
Så här lägger du till en statisk privat IP-adress till den nya virtuella datorn:

1. På sidan IP-konfiguration anger du tilldelningen för din privata IP-adress till **statisk**.
2. Ändra din privata **IP-adress** till *192.168.1.101* och välj sedan **Spara**.
   
    ![Dynamisk eller statisk tilldelning, nya inställningar för privata IP-adresser, IP-konfiguration, nätverks gränssnitt, nätverk, virtuell dator, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Om du märker att du har valt **Spara** som tilldelningen fortfarande är inställt på **dynamisk**, används redan den angivna IP-adressen. Försök med en annan IP-adress.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator
Ta bort den statiska privata IP-adressen från den virtuella datorn:

På sidan IP-konfiguration anger du tilldelningen för din privata IP-adress till **dynamisk** och väljer sedan **Spara**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser inom operativ systemet

I operativ systemet för en virtuell dator bör du inte statiskt tilldela den *privata* IP-adress som är tilldelad den virtuella Azure-datorn. Använd bara statisk tilldelning av en privat IP-adress när det är nödvändigt, till exempel när [du tilldelar många IP-adresser till virtuella datorer](virtual-network-multiple-ip-addresses-portal.md). Om du anger den privata IP-adressen manuellt i operativ systemet kontrollerar du att den matchar den privata IP-adress som tilldelats Azure- [nätverks gränssnittet](virtual-network-network-interface-addresses.md#change-ip-address-settings). Annars kan du förlora anslutningen till den virtuella datorn. Läs mer om inställningar för [privata IP-adresser](virtual-network-network-interface-addresses.md#private) .

Dessutom bör du aldrig manuellt tilldela den *offentliga* IP-adress som tilldelats till en virtuell Azure-dator i den virtuella datorns operativ system.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att hantera [Inställningar för IP-adresser](virtual-network-network-interface-addresses.md).
