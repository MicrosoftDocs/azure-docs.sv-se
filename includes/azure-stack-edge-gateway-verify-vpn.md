---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/27/2020
ms.author: alkohli
ms.openlocfilehash: efa9e996cbfbc8954c294b4da8900b1d5bcbeeed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467388"
---
Du kan skapa ett lagrings konto som bara kan nås av det virtuella nätverk som du har skapat för att kontrol lera VPN. Följ de här stegen för att skapa och associera det här lagrings kontot med det virtuella nätverk som du skapade:

1. Skapa ett lagringskonto. Du kan använda det lagrings konto som du planerar att använda med din Azure Stack Edge-enhet. Försök att komma åt lagrings kontot från ett externt nätverk (utanför det valda nätverket). Lagrings kontot bör vara tillgängligt.
2. Gå till lagrings kontot i Azure Portal. 
3. Gå till **brand väggar och virtuella nätverk**. I den högra rutan för **Tillåt åtkomst från** väljer du **valda nätverk**. På sidan **skydda vårt lagrings konto med virtuella nätverk** väljer du **+ Lägg till befintligt virtuellt nätverk.**

    ![Verifiera VPN 1](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-1.png)

4. På bladet **Lägg till nätverk** :

    - Välj prenumerationen. Det här är samma prenumeration som är associerad med din Azure Stack Edge/Data Box Gateway-resurs. 
    - I list rutan väljer du det virtuella nätverk som du vill koppla till det här lagrings kontot. Välj det virtuella nätverk som du skapade i föregående steg.
    - I **undernät** väljer du **_standard_* _ och _GatewaySubnet *. Tjänstens slut punkter aktive ras för den här kombinationen av virtuella nätverk/undernät. Att aktivera åtkomst tar upp till 15 minuter att slutföra.
    - Välj **Aktivera**.

    ![Verifiera VPN 2](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-2.png)
    
4. Spara **Inställningar**.

    ![Verifiera VPN 3](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-3.png)

5. När inställningarna har sparats kan du se de undernät som det virtuella nätverket är aktiverat för.

    ![Verifiera VPN 4](../articles/databox-online/media/azure-stack-edge-pro-r-configure-vpn-powershell/verify-vpn-4.png)

5. Följ dessa steg om du vill kontrol lera att data bara kommer att gå via VPN: 
    - Försök att komma åt lagrings kontot från ett externt nätverk (utanför det valda nätverket). Lagrings kontot bör inte vara tillgängligt. 
    - Försök att komma åt lagrings kontot från virtuella nätverk/undernät som du har aktiverat i valda nätverk. Lagrings kontot bör vara tillgängligt. 
 
Du kan bara komma åt det här lagrings kontot om du har aktiverat VPN. Om du inaktiverar VPN måste du också justera nätverks inställningarna för lagrings kontot. 

Mer information finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../articles/storage/common/storage-network-security.md). 

